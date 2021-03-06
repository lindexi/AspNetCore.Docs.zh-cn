---
title: ASP.NET Core SignalR生产承载和扩展
author: bradygaster
description: 了解如何避免使用 ASP.NET Core SignalR的应用程序中的性能和缩放问题。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/scale
ms.openlocfilehash: 23ac2b1c80b9d73d6e9ac57f0ef774ac2ea54be4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775071"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>ASP.NET Core SignalR 托管和缩放

作者： [Andrew Stanton](https://twitter.com/anurse)、 [Brady Gaster](https://twitter.com/bradygaster)和[Tom Dykstra](https://github.com/tdykstra)

本文介绍了使用 ASP.NET Core SignalR 的高流量应用的托管和扩展注意事项。

## <a name="sticky-sessions"></a>粘滞会话

SignalR 要求对特定连接的所有 HTTP 请求都由同一服务器进程处理。 当 SignalR 在服务器场（多台服务器）上运行时，必须使用 "粘滞会话"。 某些负载均衡器也称为 "粘滞会话"。 Azure App Service 使用[应用程序请求路由](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview)（ARR）来路由请求。 启用 Azure App Service 中的 "ARR 相似性" 设置将启用 "粘滞会话"。 不需要手写会话的唯一情况是：

1. 在单个服务器上承载时，在单个进程中。
1. 使用 Azure SignalR 服务时。
1. 当所有客户端都配置为**仅**使用 websocket 时，**并且**在客户端配置中启用了[SkipNegotiation 设置](xref:signalr/configuration#configure-additional-options)。

在所有其他情况下（包括使用 Redis 底板时），必须为粘滞会话配置服务器环境。

有关为 SignalR 配置 Azure App Service 的指南，请<xref:signalr/publish-to-azure-web-app>参阅。

## <a name="tcp-connection-resources"></a>TCP 连接资源

Web 服务器可以支持的并发 TCP 连接数受到限制。 标准 HTTP 客户端使用*临时*连接。 当客户端进入空闲状态并在稍后重新打开时，可以关闭这些连接。 另一方面，SignalR 连接是*永久性*的。 即使客户端进入空闲状态，SignalR 连接仍保持打开状态。 在服务于多个客户端的高流量应用程序中，这些持久连接可能会导致服务器达到其最大连接数。

持久性连接还会占用一些额外的内存，用于跟踪每个连接。

SignalR 的连接相关资源的大量使用可能会影响托管在同一服务器上的其他 web 应用程序。 当 SignalR 打开并保存最近可用的 TCP 连接时，同一服务器上的其他 web 应用也不会有更多的可用连接。

如果服务器的连接用尽，你会看到随机套接字错误和连接重置错误。 例如：

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

若要防止 SignalR 资源使用导致其他 web 应用中出现错误，请在不同于其他 web 应用的服务器上运行 SignalR。

为了使 SignalR 的资源使用不会导致 SignalR 应用中出现错误，请向外扩展以限制服务器必须处理的连接数。

## <a name="scale-out"></a>横向扩展

使用 SignalR 的应用需要跟踪其所有连接，这会为服务器场带来问题。 添加服务器，并获取其他服务器不知道的新连接。 例如，在下图中的每个服务器上，SignalR 不知道其他服务器上的连接。 当某个服务器上的 SignalR 要向所有客户端发送消息时，该消息只会发送到连接到该服务器的客户端。

![无底板缩放 SignalR](scale/_static/scale-no-backplane.png)

解决此问题的方法是[Azure SignalR 服务](#azure-signalr-service)和[Redis 底板](#redis-backplane)。

## <a name="azure-signalr-service"></a>Azure SignalR 服务

Azure SignalR 服务是一种代理，而不是底板。 每次客户端启动与服务器的连接时，客户端都将被重定向以连接到服务。 下图说明了该过程：

![建立与 Azure SignalR 服务的连接](scale/_static/azure-signalr-service-one-connection.png)

因此，服务管理所有客户端连接，而每个服务器只需要与服务建立少量的固定连接，如下图所示：

![连接到服务的客户端，连接到服务的服务器](scale/_static/azure-signalr-service-multiple-connections.png)

与 Redis 底板替代方法相比，这种扩展方法具有多个优点：

* 粘滞会话（也称为[客户端关联](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)）不是必需的，因为客户端在连接时立即重定向到 Azure SignalR 服务。
* SignalR 应用可以根据发送的消息数进行扩展，而 Azure SignalR 服务会自动缩放以处理任意数量的连接。 例如，可能有数千个客户端，但如果每秒只发送了几条消息，则 SignalR 应用不需要向外扩展到多个服务器即可直接处理连接。
* SignalR 应用使用的连接资源比没有 SignalR 的 web 应用要大得多。

出于此原因，我们建议 azure SignalR 服务适用于在 Azure 上托管的所有 ASP.NET Core SignalR 应用，包括应用服务、Vm 和容器。

有关详细信息，请参阅[Azure SignalR 服务文档](/azure/azure-signalr/signalr-overview)。

## <a name="redis-backplane"></a>Redis 底板

[Redis](https://redis.io/)是内存中的键-值存储，它支持具有发布/订阅模型的消息传送系统。 SignalR Redis 底板使用发布/订阅功能将消息转发到其他服务器。 当客户端建立连接时，会将连接信息传递到底板。 当服务器要向所有客户端发送消息时，它会发送到底板。 底板知道所有连接的客户端和它们所在的服务器。 它通过各自的服务器将消息发送到所有客户端。 下图演示了此过程：

![Redis 底板，从一台服务器发送到所有客户端的消息](scale/_static/redis-backplane.png)

对于托管在你自己的基础结构上的应用，建议使用 Redis 底板。 如果在数据中心与 Azure 数据中心之间存在明显的连接延迟，则对于具有低延迟或高吞吐量要求的本地应用，Azure SignalR 服务可能不是可行的选择。

前面提到的 Azure SignalR 服务优点是 Redis 底板的缺点：

* 需要将粘滞会话（也称为[客户端关联](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)）用于以下**两**种情况：
  * 所有客户端都配置为**仅**使用 websocket。
  * 在客户端配置中启用了[SkipNegotiation 设置](xref:signalr/configuration#configure-additional-options)。 
   在服务器上启动连接后，连接必须停留在该服务器上。
* 即使SignalR发送的消息太少，应用程序也必须基于客户端数进行扩展。
* SignalR应用比 web 应用使用的连接资源明显更多SignalR。

## <a name="iis-limitations-on-windows-client-os"></a>Windows 客户端操作系统上的 IIS 限制

Windows 10 和 Windows 8.x 是客户端操作系统。 客户端操作系统上的 IIS 的并发连接数限制为10个。 SignalR的连接是：

* 暂时性并经常重新建立。
* 不再使用时**不会**立即释放。

上述情况可能导致在客户端操作系统上达到10个连接限制。 当客户端操作系统用于开发时，建议：

* 避免 IIS。
* 使用 Kestrel 或 IIS Express 作为部署目标。

## <a name="linux-with-nginx"></a>Linux 与 Nginx

对于SignalR websocket，请`Connection`将`Upgrade`代理和标头设置为以下内容：

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

有关详细信息，请参阅 [NGINX 作为 WebSocket 代理](https://www.nginx.com/blog/websocket-nginx/)。

## <a name="third-party-signalr-backplane-providers"></a>第三方SignalR底板提供程序

* [NCache](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* [Orleans](https://github.com/OrleansContrib/SignalR.Orleans)

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* [Azure SignalR服务文档](/azure/azure-signalr/signalr-overview)
* [设置 Redis 底板](xref:signalr/redis-backplane)
