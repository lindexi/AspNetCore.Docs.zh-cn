---
title: 创建和使用 ASP.NET Core Razor 组件
author: guardrex
description: 了解如何创建和使用 Razor 组件，包括如何绑定到数据、处理事件和管理组件生命周期。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/components
ms.openlocfilehash: e877abfb568f71046c3603cac5e888e99ffc8d15
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800418"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="445a1-103">创建和使用 ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="445a1-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="445a1-104">作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="445a1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="445a1-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="445a1-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="445a1-106">Blazor 应用是使用*组件*生成的。</span><span class="sxs-lookup"><span data-stu-id="445a1-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="445a1-107">组件是自包含的用户界面（UI）块，如页、对话框或窗体。</span><span class="sxs-lookup"><span data-stu-id="445a1-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="445a1-108">组件包含插入数据或响应 UI 事件所需的 HTML 标记和处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="445a1-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="445a1-109">组件非常灵活且轻型。</span><span class="sxs-lookup"><span data-stu-id="445a1-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="445a1-110">它们可以嵌套、重复使用和在项目之间共享。</span><span class="sxs-lookup"><span data-stu-id="445a1-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="445a1-111">组件类</span><span class="sxs-lookup"><span data-stu-id="445a1-111">Component classes</span></span>

<span data-ttu-id="445a1-112">在[razor](xref:mvc/views/razor)组件文件（*razor*）中，使用和 HTML 标记的C#组合实现了组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="445a1-113">Blazor 中的组件正式称为*Razor 组件*。</span><span class="sxs-lookup"><span data-stu-id="445a1-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="445a1-114">组件的名称必须以大写字符开头。</span><span class="sxs-lookup"><span data-stu-id="445a1-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="445a1-115">例如， *MyCoolComponent*是有效的，并且*MyCoolComponent*无效。</span><span class="sxs-lookup"><span data-stu-id="445a1-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="445a1-116">可以使用 *# 文件扩展名*编写组件，只要使用`_RazorComponentInclude` MSBuild 属性将这些文件标识为 Razor 组件文件即可。</span><span class="sxs-lookup"><span data-stu-id="445a1-116">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="445a1-117">例如，指定 "*页面*" 文件夹下的所有 *.* 文件都应被视为 Razor 组件文件的应用：</span><span class="sxs-lookup"><span data-stu-id="445a1-117">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

<span data-ttu-id="445a1-118">使用 HTML 定义组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="445a1-118">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="445a1-119">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="445a1-119">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="445a1-120">在编译应用程序时，会将 HTML 标记C#和呈现逻辑转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="445a1-120">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="445a1-121">生成的类的名称与文件的名称匹配。</span><span class="sxs-lookup"><span data-stu-id="445a1-121">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="445a1-122">组件类的成员在 `@code` 块中定义。</span><span class="sxs-lookup"><span data-stu-id="445a1-122">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="445a1-123">`@code`在块中，组件状态（"属性"、"字段"）通过事件处理方法或定义其他组件逻辑来指定。</span><span class="sxs-lookup"><span data-stu-id="445a1-123">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="445a1-124">允许多个 `@code` 块。</span><span class="sxs-lookup"><span data-stu-id="445a1-124">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="445a1-125">在以前的 ASP.NET Core 3.0 的预览`@functions`中，将块用于与 Razor 组件`@code`中的块相同的用途。</span><span class="sxs-lookup"><span data-stu-id="445a1-125">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="445a1-126">`@functions`块继续在 Razor 组件中运行，但我们建议使用`@code`块 ASP.NET Core 3.0 Preview 6 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="445a1-126">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="445a1-127">组件成员可以使用C#以开头`@`的表达式作为组件的呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="445a1-127">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="445a1-128">例如， C#字段通过在字段名称前面`@`加上来呈现。</span><span class="sxs-lookup"><span data-stu-id="445a1-128">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="445a1-129">下面的示例计算并呈现：</span><span class="sxs-lookup"><span data-stu-id="445a1-129">The following example evaluates and renders:</span></span>

* <span data-ttu-id="445a1-130">`_headingFontStyle`的 CSS 属性值`font-style`。</span><span class="sxs-lookup"><span data-stu-id="445a1-130">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="445a1-131">`_headingText``<h1>`元素的内容。</span><span class="sxs-lookup"><span data-stu-id="445a1-131">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="445a1-132">最初呈现组件后，组件会为响应事件而重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="445a1-132">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="445a1-133">然后，Blazor 将新的呈现树与上一个树进行比较，并将所有修改应用于浏览器的文档对象模型（DOM）。</span><span class="sxs-lookup"><span data-stu-id="445a1-133">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="445a1-134">组件是普通C#类，可以放置在项目中的任何位置。</span><span class="sxs-lookup"><span data-stu-id="445a1-134">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="445a1-135">生成网页的组件通常位于*Pages*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="445a1-135">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="445a1-136">非页组件通常放置在*共享*文件夹中或添加到项目的自定义文件夹中。</span><span class="sxs-lookup"><span data-stu-id="445a1-136">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="445a1-137">若要使用自定义文件夹，请将自定义文件夹的命名空间添加到父组件或应用程序的 *_Imports*文件。</span><span class="sxs-lookup"><span data-stu-id="445a1-137">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="445a1-138">例如，以下命名空间使*组件*文件夹中的组件在应用程序的根命名空间为`WebApplication`时可用：</span><span class="sxs-lookup"><span data-stu-id="445a1-138">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="445a1-139">将组件集成到 Razor Pages 和 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="445a1-139">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="445a1-140">将组件用于现有 Razor Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="445a1-140">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="445a1-141">无需重写现有页面或视图即可使用 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-141">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="445a1-142">呈现页面或视图时，组件将同时预呈现。</span><span class="sxs-lookup"><span data-stu-id="445a1-142">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="445a1-143">若要从页面或视图呈现组件，请使用`RenderComponentAsync<TComponent>` HTML 帮助器方法：</span><span class="sxs-lookup"><span data-stu-id="445a1-143">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

<span data-ttu-id="445a1-144">尽管页面和视图可以使用组件，但不是这样。</span><span class="sxs-lookup"><span data-stu-id="445a1-144">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="445a1-145">组件不能使用视图和特定于页的方案，如分部视图和节。</span><span class="sxs-lookup"><span data-stu-id="445a1-145">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="445a1-146">若要在组件中通过分部视图使用逻辑，请将分部视图逻辑分解为一个组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-146">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="445a1-147">有关如何呈现组件和在 Blazor 服务器端应用程序中管理组件状态的详细信息，请参阅<xref:blazor/hosting-models>文章。</span><span class="sxs-lookup"><span data-stu-id="445a1-147">For more information on how components are rendered and component state is managed in Blazor server-side apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="445a1-148">使用组件</span><span class="sxs-lookup"><span data-stu-id="445a1-148">Use components</span></span>

<span data-ttu-id="445a1-149">组件可以通过使用 HTML 元素语法声明组件来包含其他组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-149">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="445a1-150">使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。</span><span class="sxs-lookup"><span data-stu-id="445a1-150">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="445a1-151">属性绑定区分大小写。</span><span class="sxs-lookup"><span data-stu-id="445a1-151">Attribute binding is case sensitive.</span></span> <span data-ttu-id="445a1-152">例如， `@bind`是有效的，并且`@Bind`无效。</span><span class="sxs-lookup"><span data-stu-id="445a1-152">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="445a1-153">*Index*中的以下标记将呈现`HeadingComponent`实例：</span><span class="sxs-lookup"><span data-stu-id="445a1-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="445a1-154">*组件/HeadingComponent*：</span><span class="sxs-lookup"><span data-stu-id="445a1-154">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

<span data-ttu-id="445a1-155">如果某个组件包含一个 HTML 元素，该元素的首字母大写字母与组件名称不匹配，则会发出警告，指示该元素具有意外的名称。</span><span class="sxs-lookup"><span data-stu-id="445a1-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="445a1-156">为组件命名空间添加语句使组件可用，从而消除了警告。`@using`</span><span class="sxs-lookup"><span data-stu-id="445a1-156">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="445a1-157">组件参数</span><span class="sxs-lookup"><span data-stu-id="445a1-157">Component parameters</span></span>

<span data-ttu-id="445a1-158">组件可以具有*组件参数*，这些参数是使用组件类上的公共属性和`[Parameter]`属性定义的。</span><span class="sxs-lookup"><span data-stu-id="445a1-158">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="445a1-159">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-159">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="445a1-160">*组件/ChildComponent*：</span><span class="sxs-lookup"><span data-stu-id="445a1-160">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="445a1-161">在下面的示例中， `ParentComponent`设置的`Title` `ChildComponent`属性的值。</span><span class="sxs-lookup"><span data-stu-id="445a1-161">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="445a1-162">*Pages/ParentComponent*：</span><span class="sxs-lookup"><span data-stu-id="445a1-162">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="445a1-163">子内容</span><span class="sxs-lookup"><span data-stu-id="445a1-163">Child content</span></span>

<span data-ttu-id="445a1-164">组件可以设置另一个组件的内容。</span><span class="sxs-lookup"><span data-stu-id="445a1-164">Components can set the content of another component.</span></span> <span data-ttu-id="445a1-165">分配组件提供用于指定接收组件的标记之间的内容。</span><span class="sxs-lookup"><span data-stu-id="445a1-165">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="445a1-166">在下面的示例中， `ChildComponent`具有一个`ChildContent`表示`RenderFragment`的属性，该属性表示要呈现的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="445a1-166">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="445a1-167">的值`ChildContent`定位在应呈现内容的组件标记中。</span><span class="sxs-lookup"><span data-stu-id="445a1-167">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="445a1-168">的值`ChildContent`从父组件接收，并呈现在启动面板的`panel-body`中。</span><span class="sxs-lookup"><span data-stu-id="445a1-168">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="445a1-169">*组件/ChildComponent*：</span><span class="sxs-lookup"><span data-stu-id="445a1-169">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="445a1-170">必须按约定命名`RenderFragment` `ChildContent`接收内容的属性。</span><span class="sxs-lookup"><span data-stu-id="445a1-170">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="445a1-171">以下`ParentComponent`内容可以通过将`<ChildComponent>`内容放入`ChildComponent`标记中来提供用于呈现的内容。</span><span class="sxs-lookup"><span data-stu-id="445a1-171">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="445a1-172">*Pages/ParentComponent*：</span><span class="sxs-lookup"><span data-stu-id="445a1-172">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="445a1-173">Attribute 展开和任意参数</span><span class="sxs-lookup"><span data-stu-id="445a1-173">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="445a1-174">除了组件的声明参数外，组件还可以捕获和呈现附加属性。</span><span class="sxs-lookup"><span data-stu-id="445a1-174">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="445a1-175">其他属性可以在字典中捕获，然后在使用[@attributes](xref:mvc/views/razor#attributes) Razor 指令呈现组件时 splatted 到元素。</span><span class="sxs-lookup"><span data-stu-id="445a1-175">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="445a1-176">此方案在定义生成支持各种自定义项的标记元素的组件时非常有用。</span><span class="sxs-lookup"><span data-stu-id="445a1-176">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="445a1-177">例如，为支持多个参数的指定单独`<input>`定义属性可能会很繁琐。</span><span class="sxs-lookup"><span data-stu-id="445a1-177">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="445a1-178">在下面的示例中，第`<input>`一个元素`id="useIndividualParams"`（）使用单个组件参数，第二`<input>`个元素`id="useAttributesDict"`（）使用属性展开：</span><span class="sxs-lookup"><span data-stu-id="445a1-178">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```cshtml
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="445a1-179">参数的类型必须实现`IEnumerable<KeyValuePair<string, object>>`字符串键。</span><span class="sxs-lookup"><span data-stu-id="445a1-179">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="445a1-180">在`IReadOnlyDictionary<string, object>`此方案中，还可以选择使用。</span><span class="sxs-lookup"><span data-stu-id="445a1-180">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="445a1-181">使用这`<input>`两种方法的呈现元素相同：</span><span class="sxs-lookup"><span data-stu-id="445a1-181">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="445a1-182">若要接受任意属性，请使用`[Parameter]`属性设置为的特性`CaptureUnmatchedValues`来`true`定义组件参数：</span><span class="sxs-lookup"><span data-stu-id="445a1-182">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="445a1-183">`CaptureUnmatchedValues` 上`[Parameter]`的属性允许参数匹配与任何其他参数不匹配的所有属性。</span><span class="sxs-lookup"><span data-stu-id="445a1-183">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="445a1-184">组件只能使用`CaptureUnmatchedValues`定义一个参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-184">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="445a1-185">使用的属性类型`CaptureUnmatchedValues`必须可从`Dictionary<string, object>`和字符串键赋值。</span><span class="sxs-lookup"><span data-stu-id="445a1-185">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="445a1-186">`IEnumerable<KeyValuePair<string, object>>`在`IReadOnlyDictionary<string, object>`此方案中，或也是选项。</span><span class="sxs-lookup"><span data-stu-id="445a1-186">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="445a1-187">数据绑定</span><span class="sxs-lookup"><span data-stu-id="445a1-187">Data binding</span></span>

<span data-ttu-id="445a1-188">对组件和 DOM 元素的数据绑定都是通过[@bind](xref:mvc/views/razor#bind)属性实现的。</span><span class="sxs-lookup"><span data-stu-id="445a1-188">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="445a1-189">下面的示例将`_italicsCheck`字段绑定到复选框的选中状态：</span><span class="sxs-lookup"><span data-stu-id="445a1-189">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="445a1-190">选中并清除该复选框后，属性的值将分别更新为`true`和。 `false`</span><span class="sxs-lookup"><span data-stu-id="445a1-190">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="445a1-191">仅当呈现组件时，才会在 UI 中更新此复选框，而不会在响应属性值的更改时进行更新。</span><span class="sxs-lookup"><span data-stu-id="445a1-191">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="445a1-192">由于组件会在事件处理程序代码执行后呈现自身，因此属性更新通常会立即反映在 UI 中。</span><span class="sxs-lookup"><span data-stu-id="445a1-192">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="445a1-193">与属性（ `@bind` ）`<input @bind="CurrentValue" />`一起使用本质上等效于以下内容： `CurrentValue`</span><span class="sxs-lookup"><span data-stu-id="445a1-193">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="445a1-194">呈现组件时，输入元素`value` `CurrentValue`的将来自属性。</span><span class="sxs-lookup"><span data-stu-id="445a1-194">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="445a1-195">当用户在文本框中键入内容时， `onchange`将触发事件`CurrentValue` ，并将属性设置为更改的值。</span><span class="sxs-lookup"><span data-stu-id="445a1-195">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="445a1-196">事实上，由于`@bind`处理类型转换的几种情况，代码生成会稍微复杂一些。</span><span class="sxs-lookup"><span data-stu-id="445a1-196">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="445a1-197">原则上， `@bind`将表达式的当前值`value`与属性相关联，并使用注册的处理程序来处理更改。</span><span class="sxs-lookup"><span data-stu-id="445a1-197">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="445a1-198">除了使用`@bind`语法处理`onchange`事件之外，还可以通过使用`event`参数（[@bind-value:event](xref:mvc/views/razor#bind)）指定[@bind-value](xref:mvc/views/razor#bind)属性，使用其他事件来绑定属性或字段。</span><span class="sxs-lookup"><span data-stu-id="445a1-198">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="445a1-199">下面的示例绑定`CurrentValue` `oninput`事件的属性：</span><span class="sxs-lookup"><span data-stu-id="445a1-199">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="445a1-200">不同`onchange`于，当元素失去`oninput`焦点时，将在文本框的值更改时激发。</span><span class="sxs-lookup"><span data-stu-id="445a1-200">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="445a1-201">**全球化**</span><span class="sxs-lookup"><span data-stu-id="445a1-201">**Globalization**</span></span>

<span data-ttu-id="445a1-202">`@bind`值的格式设置为显示，并使用当前区域性的规则进行分析。</span><span class="sxs-lookup"><span data-stu-id="445a1-202">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="445a1-203">可从<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>属性访问当前区域性。</span><span class="sxs-lookup"><span data-stu-id="445a1-203">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="445a1-204">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)用于以下字段类型（`<input type="{TYPE}" />`）：</span><span class="sxs-lookup"><span data-stu-id="445a1-204">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="445a1-205">上述字段类型：</span><span class="sxs-lookup"><span data-stu-id="445a1-205">The preceding field types:</span></span>

* <span data-ttu-id="445a1-206">使用其基于浏览器的适当格式规则显示。</span><span class="sxs-lookup"><span data-stu-id="445a1-206">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="445a1-207">不能包含自由格式的文本。</span><span class="sxs-lookup"><span data-stu-id="445a1-207">Can't contain free-form text.</span></span>
* <span data-ttu-id="445a1-208">基于浏览器的实现提供用户交互特性。</span><span class="sxs-lookup"><span data-stu-id="445a1-208">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="445a1-209">以下字段类型具有特定的格式要求，Blazor 当前不支持它们，因为它们不受所有主要浏览器的支持：</span><span class="sxs-lookup"><span data-stu-id="445a1-209">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="445a1-210">`@bind`支持参数以<xref:System.Globalization.CultureInfo?displayProperty=fullName>提供用于分析值和设置值的格式。 `@bind:culture`</span><span class="sxs-lookup"><span data-stu-id="445a1-210">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="445a1-211">使用`date` 和`number`字段类型时不建议指定区域性。</span><span class="sxs-lookup"><span data-stu-id="445a1-211">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="445a1-212">`date`和`number`具有提供所需区域性的内置 Blazor 支持。</span><span class="sxs-lookup"><span data-stu-id="445a1-212">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="445a1-213">有关如何设置用户的区域性的信息，请参阅[本地化](#localization)部分。</span><span class="sxs-lookup"><span data-stu-id="445a1-213">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="445a1-214">**格式字符串**</span><span class="sxs-lookup"><span data-stu-id="445a1-214">**Format strings**</span></span>

<span data-ttu-id="445a1-215">数据绑定<xref:System.DateTime>使用[@bind:format](xref:mvc/views/razor#bind)格式字符串。</span><span class="sxs-lookup"><span data-stu-id="445a1-215">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="445a1-216">现在不能使用其他格式的表达式，如货币或数字格式。</span><span class="sxs-lookup"><span data-stu-id="445a1-216">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="445a1-217">在前面的代码中， `<input>`元素的字段类型（`type`）默认为`text`。</span><span class="sxs-lookup"><span data-stu-id="445a1-217">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="445a1-218">`@bind:format`支持绑定以下 .NET 类型：</span><span class="sxs-lookup"><span data-stu-id="445a1-218">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="445a1-219"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="445a1-219"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="445a1-220"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="445a1-220"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="445a1-221">特性指定要应用`value` `<input>`于元素的的日期格式。 `@bind:format`</span><span class="sxs-lookup"><span data-stu-id="445a1-221">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="445a1-222">当发生`onchange`事件时，该格式还用于分析值。</span><span class="sxs-lookup"><span data-stu-id="445a1-222">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="445a1-223">不建议为`date`字段类型指定格式，因为 Blazor 具有对日期格式的内置支持。</span><span class="sxs-lookup"><span data-stu-id="445a1-223">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="445a1-224">**组件参数**</span><span class="sxs-lookup"><span data-stu-id="445a1-224">**Component parameters**</span></span>

<span data-ttu-id="445a1-225">绑定可识别组件参数， `@bind-{property}`可在其中跨组件绑定属性值。</span><span class="sxs-lookup"><span data-stu-id="445a1-225">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="445a1-226">以下子组件（`ChildComponent`）具有一个`Year`组件参数和`YearChanged`回调：</span><span class="sxs-lookup"><span data-stu-id="445a1-226">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="445a1-227">`EventCallback<T>`在[EventCallback](#eventcallback)部分中进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="445a1-227">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="445a1-228">以下父组件使用`ChildComponent`并将`ParentYear`参数从父级绑定到子组件上`Year`的参数：</span><span class="sxs-lookup"><span data-stu-id="445a1-228">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="445a1-229">加载将`ParentComponent`生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="445a1-229">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="445a1-230">如果通过选择`ParentComponent`中的`ParentYear`按钮来更改属性的值， `Year`则将更新的`ChildComponent`属性。</span><span class="sxs-lookup"><span data-stu-id="445a1-230">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="445a1-231">在重新呈现`Year` `ParentComponent`时，的新值将呈现在 UI 中：</span><span class="sxs-lookup"><span data-stu-id="445a1-231">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="445a1-232">参数`Year`是可绑定的，因为它具有`YearChanged` `Year`与参数类型匹配的伴随事件。</span><span class="sxs-lookup"><span data-stu-id="445a1-232">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="445a1-233">按照约定， `<ChildComponent @bind-Year="ParentYear" />`本质上等效于编写：</span><span class="sxs-lookup"><span data-stu-id="445a1-233">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="445a1-234">通常，可以使用`@bind-property:event`属性将属性绑定到相应的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="445a1-234">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="445a1-235">例如，可以使用以下`MyProp`两个属性将`MyEventHandler`属性绑定到：</span><span class="sxs-lookup"><span data-stu-id="445a1-235">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="445a1-236">事件处理</span><span class="sxs-lookup"><span data-stu-id="445a1-236">Event handling</span></span>

<span data-ttu-id="445a1-237">Razor 组件提供事件处理功能。</span><span class="sxs-lookup"><span data-stu-id="445a1-237">Razor components provide event handling features.</span></span> <span data-ttu-id="445a1-238">对于带有委托类型值的`on{event}`名为（ `onclick`例如和`onsubmit`）的 HTML 元素特性，Razor 组件将属性的值视为事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="445a1-238">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="445a1-239">特性的名称始终为 "格式[ @on{事件}](xref:mvc/views/razor#onevent)"。</span><span class="sxs-lookup"><span data-stu-id="445a1-239">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="445a1-240">当在 UI 中选择`UpdateHeading`该按钮时，以下代码将调用方法：</span><span class="sxs-lookup"><span data-stu-id="445a1-240">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="445a1-241">如果 UI 中的复选`CheckChanged`框发生了更改，以下代码将调用方法：</span><span class="sxs-lookup"><span data-stu-id="445a1-241">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="445a1-242">事件处理程序也可以是异步的<xref:System.Threading.Tasks.Task>并返回。</span><span class="sxs-lookup"><span data-stu-id="445a1-242">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="445a1-243">无需手动调用`StateHasChanged()`。</span><span class="sxs-lookup"><span data-stu-id="445a1-243">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="445a1-244">异常在发生时进行记录。</span><span class="sxs-lookup"><span data-stu-id="445a1-244">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="445a1-245">在下面的示例中`UpdateHeading` ，当选择按钮时，将异步调用：</span><span class="sxs-lookup"><span data-stu-id="445a1-245">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a><span data-ttu-id="445a1-246">事件参数类型</span><span class="sxs-lookup"><span data-stu-id="445a1-246">Event argument types</span></span>

<span data-ttu-id="445a1-247">对于某些事件，允许使用事件参数类型。</span><span class="sxs-lookup"><span data-stu-id="445a1-247">For some events, event argument types are permitted.</span></span> <span data-ttu-id="445a1-248">如果不需要访问这些事件类型之一，则方法调用中不需要这样做。</span><span class="sxs-lookup"><span data-stu-id="445a1-248">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="445a1-249">下表显示了支持的[EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) 。</span><span class="sxs-lookup"><span data-stu-id="445a1-249">Supported [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) are shown in the following table.</span></span>

| <span data-ttu-id="445a1-250">Event</span><span class="sxs-lookup"><span data-stu-id="445a1-250">Event</span></span> | <span data-ttu-id="445a1-251">类</span><span class="sxs-lookup"><span data-stu-id="445a1-251">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="445a1-252">剪贴板</span><span class="sxs-lookup"><span data-stu-id="445a1-252">Clipboard</span></span>        | `ClipboardEventArgs` |
| <span data-ttu-id="445a1-253">入</span><span class="sxs-lookup"><span data-stu-id="445a1-253">Drag</span></span>             | <span data-ttu-id="445a1-254">`DragEventArgs`&ndash; 并保存`DataTransferItem`拖动的项数据。 `DataTransfer`</span><span class="sxs-lookup"><span data-stu-id="445a1-254">`DragEventArgs` &ndash; `DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="445a1-255">Error</span><span class="sxs-lookup"><span data-stu-id="445a1-255">Error</span></span>            | `ErrorEventArgs` |
| <span data-ttu-id="445a1-256">焦点</span><span class="sxs-lookup"><span data-stu-id="445a1-256">Focus</span></span>            | <span data-ttu-id="445a1-257">`FocusEventArgs`不包含对的`relatedTarget`支持。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="445a1-257">`FocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="445a1-258">`<input>` 已更改</span><span class="sxs-lookup"><span data-stu-id="445a1-258">`<input>` change</span></span> | `ChangeEventArgs` |
| <span data-ttu-id="445a1-259">键盘</span><span class="sxs-lookup"><span data-stu-id="445a1-259">Keyboard</span></span>         | `KeyboardEventArgs` |
| <span data-ttu-id="445a1-260">鼠标</span><span class="sxs-lookup"><span data-stu-id="445a1-260">Mouse</span></span>            | `MouseEventArgs` |
| <span data-ttu-id="445a1-261">鼠标指针</span><span class="sxs-lookup"><span data-stu-id="445a1-261">Mouse pointer</span></span>    | `PointerEventArgs` |
| <span data-ttu-id="445a1-262">鼠标滚轮</span><span class="sxs-lookup"><span data-stu-id="445a1-262">Mouse wheel</span></span>      | `WheelEventArgs` |
| <span data-ttu-id="445a1-263">进度</span><span class="sxs-lookup"><span data-stu-id="445a1-263">Progress</span></span>         | `ProgressEventArgs` |
| <span data-ttu-id="445a1-264">触控</span><span class="sxs-lookup"><span data-stu-id="445a1-264">Touch</span></span>            | <span data-ttu-id="445a1-265">`TouchEventArgs`&ndash; 表示触摸敏感设备上`TouchPoint`的单个联系点。</span><span class="sxs-lookup"><span data-stu-id="445a1-265">`TouchEventArgs` &ndash; `TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="445a1-266">有关上表中事件的属性和事件处理行为的信息，请参阅[引用源中的 EventArgs 类（aspnet/AspNetCore release/3.0-preview9 分支）](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="445a1-266">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0-preview9 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="445a1-267">Lambda 表达式</span><span class="sxs-lookup"><span data-stu-id="445a1-267">Lambda expressions</span></span>

<span data-ttu-id="445a1-268">还可以使用 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="445a1-268">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="445a1-269">通常可以很方便地关闭其他值，如在循环访问一组元素时。</span><span class="sxs-lookup"><span data-stu-id="445a1-269">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="445a1-270">下面的示例创建了三个按钮，每个`UpdateHeading`按钮在 UI 中选择`UIMouseEventArgs`时均调用传递事件参数`buttonNumber`（）和按钮号（）：</span><span class="sxs-lookup"><span data-stu-id="445a1-270">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="445a1-271">不要**直接**在 lambda 表达式中的`i` `for`循环中使用循环变量（）。</span><span class="sxs-lookup"><span data-stu-id="445a1-271">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="445a1-272">否则，所有 lambda 表达式都将使用相同的变量`i`，从而使所有 lambda 中的值都相同。</span><span class="sxs-lookup"><span data-stu-id="445a1-272">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="445a1-273">始终捕获其在本地变量中的值`buttonNumber` （在前面的示例中），然后使用它。</span><span class="sxs-lookup"><span data-stu-id="445a1-273">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="445a1-274">EventCallback</span><span class="sxs-lookup"><span data-stu-id="445a1-274">EventCallback</span></span>

<span data-ttu-id="445a1-275">使用嵌套组件的常见方案是，当发生&mdash;子组件事件时（例如， `onclick`当子组件发生在子组件中时）需要运行父组件的方法。</span><span class="sxs-lookup"><span data-stu-id="445a1-275">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="445a1-276">若要跨组件公开事件，请`EventCallback`使用。</span><span class="sxs-lookup"><span data-stu-id="445a1-276">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="445a1-277">父组件可将回调方法分配给子组件的`EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="445a1-277">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="445a1-278">示例`ChildComponent`应用中的演示如何`EventCallback`设置按钮的`onclick`处理程序，以接收来自示例的的`ParentComponent`委托。</span><span class="sxs-lookup"><span data-stu-id="445a1-278">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="445a1-279">使用进行类型化， `onclick`这适用于来自外围设备的事件： `UIMouseEventArgs` `EventCallback`</span><span class="sxs-lookup"><span data-stu-id="445a1-279">The `EventCallback` is typed with `UIMouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="445a1-280">将子级设置为其`ShowMessage`方法： `ParentComponent` `EventCallback<T>`</span><span class="sxs-lookup"><span data-stu-id="445a1-280">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="445a1-281">如果在中`ChildComponent`选择了该按钮：</span><span class="sxs-lookup"><span data-stu-id="445a1-281">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="445a1-282">调用`ParentComponent`的`ShowMessage`方法。</span><span class="sxs-lookup"><span data-stu-id="445a1-282">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="445a1-283">`messageText`会更新并显示在中`ParentComponent`。</span><span class="sxs-lookup"><span data-stu-id="445a1-283">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="445a1-284">在回调的`StateHasChanged`方法（`ShowMessage`）中不需要对的调用。</span><span class="sxs-lookup"><span data-stu-id="445a1-284">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="445a1-285">`StateHasChanged`自动调用以诸如此类`ParentComponent`，就像子事件在子中执行的事件处理程序中 rerendering。</span><span class="sxs-lookup"><span data-stu-id="445a1-285">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="445a1-286">`EventCallback`和`EventCallback<T>`允许异步委托。</span><span class="sxs-lookup"><span data-stu-id="445a1-286">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="445a1-287">`EventCallback<T>`为强类型，需要特定的参数类型。</span><span class="sxs-lookup"><span data-stu-id="445a1-287">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="445a1-288">`EventCallback`是弱类型，允许任何参数类型。</span><span class="sxs-lookup"><span data-stu-id="445a1-288">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="445a1-289">`EventCallback`调用或`EventCallback<T>` ，并等待<xref:System.Threading.Tasks.Task>： `InvokeAsync`</span><span class="sxs-lookup"><span data-stu-id="445a1-289">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="445a1-290">将`EventCallback` 和`EventCallback<T>`用于事件处理和绑定组件参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-290">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="445a1-291">优先使用强类型`EventCallback<T>`化`EventCallback`。</span><span class="sxs-lookup"><span data-stu-id="445a1-291">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="445a1-292">`EventCallback<T>`向组件的用户提供更好的错误反馈。</span><span class="sxs-lookup"><span data-stu-id="445a1-292">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="445a1-293">与其他 UI 事件处理程序类似，指定事件参数是可选的。</span><span class="sxs-lookup"><span data-stu-id="445a1-293">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="445a1-294">当`EventCallback`没有任何值传递到回调时使用。</span><span class="sxs-lookup"><span data-stu-id="445a1-294">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="445a1-295">捕获对组件的引用</span><span class="sxs-lookup"><span data-stu-id="445a1-295">Capture references to components</span></span>

<span data-ttu-id="445a1-296">组件引用提供了一种方法来引用组件实例，以便可以向该实例发出命令，如`Show`或。 `Reset`</span><span class="sxs-lookup"><span data-stu-id="445a1-296">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="445a1-297">捕获组件引用：</span><span class="sxs-lookup"><span data-stu-id="445a1-297">To capture a component reference:</span></span>

* <span data-ttu-id="445a1-298">向子组件添加[属性。@ref](xref:mvc/views/razor#ref)</span><span class="sxs-lookup"><span data-stu-id="445a1-298">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="445a1-299">定义与子组件类型相同的字段。</span><span class="sxs-lookup"><span data-stu-id="445a1-299">Define a field with the same type as the child component.</span></span>

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="445a1-300">呈现组件时， `loginDialog`将`MyLoginDialog`用子组件实例填充字段。</span><span class="sxs-lookup"><span data-stu-id="445a1-300">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="445a1-301">然后，可以在组件实例上调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="445a1-301">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="445a1-302">仅在呈现组件后填充`MyLoginDialog` 变量，并且它的输出包含元素。`loginDialog`</span><span class="sxs-lookup"><span data-stu-id="445a1-302">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="445a1-303">在该点之前，没有任何内容可供参考。</span><span class="sxs-lookup"><span data-stu-id="445a1-303">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="445a1-304">若要在组件完成呈现后操作组件引用，请使用`OnAfterRenderAsync`或`OnAfterRender`方法。</span><span class="sxs-lookup"><span data-stu-id="445a1-304">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="445a1-305">捕获组件引用时，请使用类似的语法来[捕获元素引用](xref:blazor/javascript-interop#capture-references-to-elements)，而不是[JavaScript 互操作](xref:blazor/javascript-interop)功能。</span><span class="sxs-lookup"><span data-stu-id="445a1-305">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="445a1-306">不向 JavaScript 代码&mdash;传递组件引用，它们仅用于 .net 代码。</span><span class="sxs-lookup"><span data-stu-id="445a1-306">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="445a1-307">不要**使用组件**引用来改变子组件的状态。</span><span class="sxs-lookup"><span data-stu-id="445a1-307">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="445a1-308">请改用常规声明性参数将数据传递给子组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-308">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="445a1-309">使用正常的声明性参数会导致子组件自动诸如此类正确的时间。</span><span class="sxs-lookup"><span data-stu-id="445a1-309">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="445a1-310">在外部调用组件方法以更新状态</span><span class="sxs-lookup"><span data-stu-id="445a1-310">Invoke component methods externally to update state</span></span>

<span data-ttu-id="445a1-311">Blazor 使用`SynchronizationContext`来强制执行单个逻辑线程。</span><span class="sxs-lookup"><span data-stu-id="445a1-311">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="445a1-312">对此`SynchronizationContext`执行 Blazor 引发的组件生命周期方法和任何事件回调。</span><span class="sxs-lookup"><span data-stu-id="445a1-312">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="445a1-313">如果组件必须根据外部事件（如计时器或其他通知）进行更新，请使用`InvokeAsync`方法，该方法将调度到 Blazor 的。 `SynchronizationContext`</span><span class="sxs-lookup"><span data-stu-id="445a1-313">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="445a1-314">例如，假设有一个*通告程序服务*可以通知已更新状态的任何侦听组件：</span><span class="sxs-lookup"><span data-stu-id="445a1-314">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Action<string, int, Task> Notify;
}
```

<span data-ttu-id="445a1-315">`NotifierService`使用更新组件：</span><span class="sxs-lookup"><span data-stu-id="445a1-315">Usage of the `NotifierService` to update a component:</span></span>

```cshtml
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="445a1-316">在前面的示例中`NotifierService` ，在 Blazor 的`OnNotify` `SynchronizationContext`外部调用该组件的方法。</span><span class="sxs-lookup"><span data-stu-id="445a1-316">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="445a1-317">`InvokeAsync`用于切换到正确的上下文，并将呈现器排队。</span><span class="sxs-lookup"><span data-stu-id="445a1-317">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="445a1-318">使用\@key 控制是否保留元素和组件</span><span class="sxs-lookup"><span data-stu-id="445a1-318">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="445a1-319">在呈现元素或组件的列表并且元素或组件随后发生变化时，Blazor 的比较算法必须决定哪些之前的元素或组件可以保留，以及模型对象应如何映射到它们。</span><span class="sxs-lookup"><span data-stu-id="445a1-319">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="445a1-320">通常，此过程是自动的，可以忽略，但在某些情况下，您可能需要控制该过程。</span><span class="sxs-lookup"><span data-stu-id="445a1-320">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="445a1-321">请看下面的示例：</span><span class="sxs-lookup"><span data-stu-id="445a1-321">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="445a1-322">`People`集合的内容可能会随插入、删除或重新排序条目而更改。</span><span class="sxs-lookup"><span data-stu-id="445a1-322">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="445a1-323">当组件 rerenders 时， `<DetailsEditor>`组件可能会更改以接收不同`Details`的参数值。</span><span class="sxs-lookup"><span data-stu-id="445a1-323">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="445a1-324">这可能导致比预期更复杂的 rerendering。</span><span class="sxs-lookup"><span data-stu-id="445a1-324">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="445a1-325">在某些情况下，rerendering 可能会导致可见行为差异，如失去元素焦点。</span><span class="sxs-lookup"><span data-stu-id="445a1-325">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="445a1-326">可以用`@key`指令特性来控制映射过程。</span><span class="sxs-lookup"><span data-stu-id="445a1-326">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="445a1-327">`@key`使比较算法根据键的值保证保留元素或组件：</span><span class="sxs-lookup"><span data-stu-id="445a1-327">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="445a1-328">当集合更改时，比较算法会保留实例和`person`实例`<DetailsEditor>`之间的关联： `People`</span><span class="sxs-lookup"><span data-stu-id="445a1-328">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="445a1-329">如果从列表中删除，则只会从 UI `<DetailsEditor>`中删除相应的实例。 `People` `Person`</span><span class="sxs-lookup"><span data-stu-id="445a1-329">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="445a1-330">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="445a1-330">Other instances are left unchanged.</span></span>
* <span data-ttu-id="445a1-331">如果在列表中的某个位置插入，则在相应位置`<DetailsEditor>`插入一个新实例。 `Person`</span><span class="sxs-lookup"><span data-stu-id="445a1-331">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="445a1-332">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="445a1-332">Other instances are left unchanged.</span></span>
* <span data-ttu-id="445a1-333">如果`Person`项是重新排序的，则相应`<DetailsEditor>`的实例会保留并在 UI 中重新排序。</span><span class="sxs-lookup"><span data-stu-id="445a1-333">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="445a1-334">在某些情况下，使用`@key`可最大程度地降低 rerendering 的复杂性，并避免在 DOM 的有状态部分发生变化时出现潜在问题，如焦点位置。</span><span class="sxs-lookup"><span data-stu-id="445a1-334">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="445a1-335">键对于每个容器元素或组件都是本地的。</span><span class="sxs-lookup"><span data-stu-id="445a1-335">Keys are local to each container element or component.</span></span> <span data-ttu-id="445a1-336">不会在整个文档中全局地比较键。</span><span class="sxs-lookup"><span data-stu-id="445a1-336">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="445a1-337">何时使用\@密钥</span><span class="sxs-lookup"><span data-stu-id="445a1-337">When to use \@key</span></span>

<span data-ttu-id="445a1-338">通常， `@key`每次呈现列表时（例如， `@foreach`在块中）以及定义的`@key`合适值都有意义。</span><span class="sxs-lookup"><span data-stu-id="445a1-338">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="445a1-339">当对象发生更改`@key`时，还可以使用阻止 Blazor 保留元素或组件子树：</span><span class="sxs-lookup"><span data-stu-id="445a1-339">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

<span data-ttu-id="445a1-340">如果`@currentPerson`发生更改， `@key`则 attribute 指令强制 Blazor 丢弃整个`<div>`及其子代，并通过新的元素和组件重新生成 UI 中的子树。</span><span class="sxs-lookup"><span data-stu-id="445a1-340">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="445a1-341">如果需要确保更改时`@currentPerson`不保留 UI 状态，这会很有用。</span><span class="sxs-lookup"><span data-stu-id="445a1-341">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="445a1-342">何时不使用\@密钥</span><span class="sxs-lookup"><span data-stu-id="445a1-342">When not to use \@key</span></span>

<span data-ttu-id="445a1-343">与进行`@key`比较时，会产生性能开销。</span><span class="sxs-lookup"><span data-stu-id="445a1-343">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="445a1-344">性能开销不大，但仅指定`@key`控制元素或组件保留规则是否会对应用进行权益。</span><span class="sxs-lookup"><span data-stu-id="445a1-344">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="445a1-345">`@key`即使未使用，Blazor 也会尽可能地保留子元素和组件实例。</span><span class="sxs-lookup"><span data-stu-id="445a1-345">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="445a1-346">使用`@key`的唯一优点是控制*如何*将模型实例映射到保留的组件实例，而不是选择映射的比较算法。</span><span class="sxs-lookup"><span data-stu-id="445a1-346">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="445a1-347">\@键要使用哪些值</span><span class="sxs-lookup"><span data-stu-id="445a1-347">What values to use for \@key</span></span>

<span data-ttu-id="445a1-348">通常，为提供以下类型的值`@key`有意义：</span><span class="sxs-lookup"><span data-stu-id="445a1-348">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="445a1-349">模型对象实例（例如， `Person`在前面的示例中为实例）。</span><span class="sxs-lookup"><span data-stu-id="445a1-349">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="445a1-350">这可确保基于对象引用相等性保存。</span><span class="sxs-lookup"><span data-stu-id="445a1-350">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="445a1-351">唯一标识符（例如，、或`int` `Guid`类型`string`的主键值）。</span><span class="sxs-lookup"><span data-stu-id="445a1-351">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="445a1-352">确保用于的`@key`值不冲突。</span><span class="sxs-lookup"><span data-stu-id="445a1-352">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="445a1-353">如果在同一父元素内检测到冲突值，则 Blazor 会引发异常，因为它无法确定将旧元素或组件映射到新元素或组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-353">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="445a1-354">仅使用非重复值，例如对象实例或主键值。</span><span class="sxs-lookup"><span data-stu-id="445a1-354">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="445a1-355">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="445a1-355">Lifecycle methods</span></span>

<span data-ttu-id="445a1-356">`OnInitializedAsync`并`OnInitialized`执行代码来初始化该组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-356">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="445a1-357">若要执行异步操作，请`OnInitializedAsync`在操作`await`中使用和关键字：</span><span class="sxs-lookup"><span data-stu-id="445a1-357">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="445a1-358">对于同步操作，请使用`OnInitialized`：</span><span class="sxs-lookup"><span data-stu-id="445a1-358">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="445a1-359">`OnParametersSetAsync`当`OnParametersSet`组件已从其父级接收参数并且为属性分配了值时，将调用和。</span><span class="sxs-lookup"><span data-stu-id="445a1-359">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="445a1-360">这些方法在组件初始化之后以及每次呈现组件时执行：</span><span class="sxs-lookup"><span data-stu-id="445a1-360">These methods are executed after component initialization and each time the component is rendered:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="445a1-361">`OnAfterRenderAsync`和`OnAfterRender`在组件完成呈现后被调用。</span><span class="sxs-lookup"><span data-stu-id="445a1-361">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="445a1-362">此时将填充元素和组件引用。</span><span class="sxs-lookup"><span data-stu-id="445a1-362">Element and component references are populated at this point.</span></span> <span data-ttu-id="445a1-363">使用此阶段，可以使用呈现的内容来执行其他初始化步骤，如激活在呈现的 DOM 元素上操作的第三方 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="445a1-363">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="445a1-364">`OnAfterRender`在*服务器上预呈现时不调用。*</span><span class="sxs-lookup"><span data-stu-id="445a1-364">`OnAfterRender` *is not called when prerendering on the server.*</span></span>

<span data-ttu-id="445a1-365">`firstRender` 和`OnAfterRender`的参数为： `OnAfterRenderAsync`</span><span class="sxs-lookup"><span data-stu-id="445a1-365">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender` is:</span></span>

* <span data-ttu-id="445a1-366">第一`true`次调用组件实例时设置为。</span><span class="sxs-lookup"><span data-stu-id="445a1-366">Set to `true` the first time that the component instance is invoked.</span></span>
* <span data-ttu-id="445a1-367">确保仅执行一次初始化工作。</span><span class="sxs-lookup"><span data-stu-id="445a1-367">Ensures that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="445a1-368">在呈现时处理未完成的异步操作</span><span class="sxs-lookup"><span data-stu-id="445a1-368">Handle incomplete async actions at render</span></span>

<span data-ttu-id="445a1-369">在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="445a1-369">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="445a1-370">当执行生命`null`周期方法时，对象可能会或未完全填充数据。</span><span class="sxs-lookup"><span data-stu-id="445a1-370">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="445a1-371">提供呈现逻辑以确认对象已初始化。</span><span class="sxs-lookup"><span data-stu-id="445a1-371">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="445a1-372">在对象为`null`时呈现占位符 UI 元素（例如，加载消息）。</span><span class="sxs-lookup"><span data-stu-id="445a1-372">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="445a1-373">在 Blazor 模板的`OnInitializedAsync` `forecasts`组件中，将重写为 asychronously 接收预测数据（）。 `FetchData`</span><span class="sxs-lookup"><span data-stu-id="445a1-373">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="445a1-374">如果`forecasts` 为`null`，则向用户显示一条加载消息。</span><span class="sxs-lookup"><span data-stu-id="445a1-374">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="445a1-375">完成`OnInitializedAsync`返回后，组件将重新呈现已更新状态。 `Task`</span><span class="sxs-lookup"><span data-stu-id="445a1-375">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="445a1-376">*Pages/FetchData.razor*：</span><span class="sxs-lookup"><span data-stu-id="445a1-376">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="445a1-377">在设置参数之前执行代码</span><span class="sxs-lookup"><span data-stu-id="445a1-377">Execute code before parameters are set</span></span>

<span data-ttu-id="445a1-378">`SetParameters`可以重写，以在设置参数之前执行代码：</span><span class="sxs-lookup"><span data-stu-id="445a1-378">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="445a1-379">如果`base.SetParameters`未调用，则自定义代码可以通过任何所需的方式解释传入参数值。</span><span class="sxs-lookup"><span data-stu-id="445a1-379">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="445a1-380">例如，无需将传入参数分配给类的属性。</span><span class="sxs-lookup"><span data-stu-id="445a1-380">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="445a1-381">禁止刷新 UI</span><span class="sxs-lookup"><span data-stu-id="445a1-381">Suppress refreshing of the UI</span></span>

<span data-ttu-id="445a1-382">`ShouldRender`可以重写以禁止刷新 UI。</span><span class="sxs-lookup"><span data-stu-id="445a1-382">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="445a1-383">如果实现返回`true`，则刷新 UI。</span><span class="sxs-lookup"><span data-stu-id="445a1-383">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="445a1-384">`ShouldRender`即使重写，组件也始终是最初呈现的。</span><span class="sxs-lookup"><span data-stu-id="445a1-384">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="445a1-385">用 IDisposable 进行的组件处置</span><span class="sxs-lookup"><span data-stu-id="445a1-385">Component disposal with IDisposable</span></span>

<span data-ttu-id="445a1-386">如果某个组件实现<xref:System.IDisposable>，则从 UI 中删除该组件时，将调用[Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="445a1-386">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="445a1-387">以下组件使用`@implements IDisposable` `Dispose`和方法：</span><span class="sxs-lookup"><span data-stu-id="445a1-387">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a><span data-ttu-id="445a1-388">路由</span><span class="sxs-lookup"><span data-stu-id="445a1-388">Routing</span></span>

<span data-ttu-id="445a1-389">通过向应用程序中的每个可访问组件提供路由模板，实现 Blazor 中的路由。</span><span class="sxs-lookup"><span data-stu-id="445a1-389">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="445a1-390">在编译包含`@page`指令的 Razor 文件时，将为<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>生成的类指定指定路由模板的。</span><span class="sxs-lookup"><span data-stu-id="445a1-390">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="445a1-391">在运行时，路由器将使用`RouteAttribute`查找组件类，并呈现哪个组件包含与请求的 URL 匹配的路由模板。</span><span class="sxs-lookup"><span data-stu-id="445a1-391">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="445a1-392">可以将多个路由模板应用于组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-392">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="445a1-393">以下组件响应和`/BlazorRoute` `/DifferentBlazorRoute`的请求：</span><span class="sxs-lookup"><span data-stu-id="445a1-393">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="445a1-394">路由参数</span><span class="sxs-lookup"><span data-stu-id="445a1-394">Route parameters</span></span>

<span data-ttu-id="445a1-395">组件可以从`@page`指令中提供的路由模板接收路由参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-395">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="445a1-396">路由器使用路由参数来填充相应的组件参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-396">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="445a1-397">*路由参数组件*：</span><span class="sxs-lookup"><span data-stu-id="445a1-397">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="445a1-398">不支持可选参数，因此上面`@page`的示例中应用了两个指令。</span><span class="sxs-lookup"><span data-stu-id="445a1-398">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="445a1-399">第一个允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-399">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="445a1-400">第二`@page`个指令`{text}`采用路由参数，并将值`Text`分配给属性。</span><span class="sxs-lookup"><span data-stu-id="445a1-400">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="445a1-401">"代码隐藏" 体验的基类继承</span><span class="sxs-lookup"><span data-stu-id="445a1-401">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="445a1-402">组件文件将 HTML 标记和C#处理代码组合到同一个文件中。</span><span class="sxs-lookup"><span data-stu-id="445a1-402">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="445a1-403">`@inherits`指令可用于通过 "代码隐藏" 体验来提供 Blazor 应用，这些应用将组件标记与处理代码分开。</span><span class="sxs-lookup"><span data-stu-id="445a1-403">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="445a1-404">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)演示组件如何继承基类， `BlazorRocksBase`以提供组件的属性和方法。</span><span class="sxs-lookup"><span data-stu-id="445a1-404">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="445a1-405">*Pages/BlazorRocks*：</span><span class="sxs-lookup"><span data-stu-id="445a1-405">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="445a1-406">*BlazorRocksBase.cs*：</span><span class="sxs-lookup"><span data-stu-id="445a1-406">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="445a1-407">基类应派生自`ComponentBase`。</span><span class="sxs-lookup"><span data-stu-id="445a1-407">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="445a1-408">导入组件</span><span class="sxs-lookup"><span data-stu-id="445a1-408">Import components</span></span>

<span data-ttu-id="445a1-409">使用 Razor 编写的组件的命名空间基于：</span><span class="sxs-lookup"><span data-stu-id="445a1-409">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="445a1-410">项目的`RootNamespace`。</span><span class="sxs-lookup"><span data-stu-id="445a1-410">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="445a1-411">从项目根到组件的路径。</span><span class="sxs-lookup"><span data-stu-id="445a1-411">The path from the project root to the component.</span></span> <span data-ttu-id="445a1-412">例如， `ComponentsSample/Pages/Index.razor`位于命名空间`ComponentsSample.Pages`中。</span><span class="sxs-lookup"><span data-stu-id="445a1-412">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="445a1-413">组件遵循C#名称绑定规则。</span><span class="sxs-lookup"><span data-stu-id="445a1-413">Components follow C# name binding rules.</span></span> <span data-ttu-id="445a1-414">对于*Index*，同一文件夹、*页面*和父文件夹*ComponentsSample*中的所有组件都在作用域内。</span><span class="sxs-lookup"><span data-stu-id="445a1-414">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="445a1-415">在不同的命名空间中定义的组件可以使用 Razor 的[ \@using](xref:mvc/views/razor#using)指令进入作用域。</span><span class="sxs-lookup"><span data-stu-id="445a1-415">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="445a1-416">`NavMenu.razor`如果文件夹`ComponentsSample/Shared/`中存在另一个组件，则可在中`Index.razor`将组件与以下`@using`语句一起使用：</span><span class="sxs-lookup"><span data-stu-id="445a1-416">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="445a1-417">还可以使用其完全限定名称来引用组件，这无需[ \@使用](xref:mvc/views/razor#using)指令：</span><span class="sxs-lookup"><span data-stu-id="445a1-417">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="445a1-418">不`global::`支持此限制。</span><span class="sxs-lookup"><span data-stu-id="445a1-418">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="445a1-419">不支持导入`using`带有别名的语句的`@using Foo = Bar`组件（例如）。</span><span class="sxs-lookup"><span data-stu-id="445a1-419">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="445a1-420">不支持部分限定的名称。</span><span class="sxs-lookup"><span data-stu-id="445a1-420">Partially qualified names aren't supported.</span></span> <span data-ttu-id="445a1-421">例如， `<Shared.NavMenu></Shared.NavMenu>`不支持`@using ComponentsSample`添加和`NavMenu.razor`引用。</span><span class="sxs-lookup"><span data-stu-id="445a1-421">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="445a1-422">条件 HTML 元素特性</span><span class="sxs-lookup"><span data-stu-id="445a1-422">Conditional HTML element attributes</span></span>

<span data-ttu-id="445a1-423">HTML 元素特性根据 .NET 值有条件地呈现。</span><span class="sxs-lookup"><span data-stu-id="445a1-423">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="445a1-424">如果值为`false`或`null`，则不呈现特性。</span><span class="sxs-lookup"><span data-stu-id="445a1-424">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="445a1-425">如果该值为`true`，则以最小化的特性呈现特性。</span><span class="sxs-lookup"><span data-stu-id="445a1-425">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="445a1-426">在下面的示例中`IsCompleted` ， `checked`确定是否在元素的标记中呈现：</span><span class="sxs-lookup"><span data-stu-id="445a1-426">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="445a1-427">如果`IsCompleted` 为`true`，则复选框将呈现为：</span><span class="sxs-lookup"><span data-stu-id="445a1-427">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="445a1-428">如果`IsCompleted` 为`false`，则复选框将呈现为：</span><span class="sxs-lookup"><span data-stu-id="445a1-428">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="445a1-429">有关详细信息，请参阅 <xref:mvc/views/razor>。</span><span class="sxs-lookup"><span data-stu-id="445a1-429">For more information, see <xref:mvc/views/razor>.</span></span>

## <a name="raw-html"></a><span data-ttu-id="445a1-430">原始 HTML</span><span class="sxs-lookup"><span data-stu-id="445a1-430">Raw HTML</span></span>

<span data-ttu-id="445a1-431">通常使用 DOM 文本节点呈现字符串，这意味着将忽略它们可能包含的任何标记，并将其视为文本文本。</span><span class="sxs-lookup"><span data-stu-id="445a1-431">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="445a1-432">若要呈现原始 html，请在`MarkupString`值中换行 html 内容。</span><span class="sxs-lookup"><span data-stu-id="445a1-432">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="445a1-433">该值将分析为 HTML 或 SVG，并插入 DOM 中。</span><span class="sxs-lookup"><span data-stu-id="445a1-433">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="445a1-434">呈现从任何不受信任的源构造的原始 HTML 会**带来安全风险**，应避免！</span><span class="sxs-lookup"><span data-stu-id="445a1-434">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="445a1-435">下面的示例演示如何使用`MarkupString`类型向组件的呈现输出添加静态 HTML 内容块：</span><span class="sxs-lookup"><span data-stu-id="445a1-435">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="445a1-436">模板化组件</span><span class="sxs-lookup"><span data-stu-id="445a1-436">Templated components</span></span>

<span data-ttu-id="445a1-437">模板化组件是接受一个或多个 UI 模板作为参数的组件，可将其用作组件呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="445a1-437">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="445a1-438">模板化组件允许你创作比常规组件更易于使用的更高级别的组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-438">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="445a1-439">几个示例包括：</span><span class="sxs-lookup"><span data-stu-id="445a1-439">A couple of examples include:</span></span>

* <span data-ttu-id="445a1-440">允许用户为表的标头、行和脚注指定模板的表组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-440">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="445a1-441">允许用户在列表中指定用于呈现项的模板的列表组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-441">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="445a1-442">模板参数</span><span class="sxs-lookup"><span data-stu-id="445a1-442">Template parameters</span></span>

<span data-ttu-id="445a1-443">通过指定一个或多个类型`RenderFragment`为或`RenderFragment<T>`的组件参数来定义模板化组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-443">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="445a1-444">呈现片段表示要呈现的 UI 段。</span><span class="sxs-lookup"><span data-stu-id="445a1-444">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="445a1-445">`RenderFragment<T>`采用可在调用呈现片段时指定的类型参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-445">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="445a1-446">`TableTemplate`组件</span><span class="sxs-lookup"><span data-stu-id="445a1-446">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="445a1-447">使用模板化组件时，可以使用与参数名称匹配的子元素（`TableHeader` `RowTemplate`在以下示例中）指定模板参数：</span><span class="sxs-lookup"><span data-stu-id="445a1-447">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="@pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a><span data-ttu-id="445a1-448">模板上下文参数</span><span class="sxs-lookup"><span data-stu-id="445a1-448">Template context parameters</span></span>

<span data-ttu-id="445a1-449">作为元素传递的`RenderFragment<T>`类型的组件参数具有一个名为`context`的隐式参数（如前面`@context.PetId`的代码示例所示），但你可以使用子上`Context`的属性更改参数名称element.</span><span class="sxs-lookup"><span data-stu-id="445a1-449">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="445a1-450">在下面的示例中， `RowTemplate`元素的`Context`属性指定`pet`参数：</span><span class="sxs-lookup"><span data-stu-id="445a1-450">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="@pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="445a1-451">或者，您可以在 component `Context`元素上指定属性。</span><span class="sxs-lookup"><span data-stu-id="445a1-451">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="445a1-452">指定`Context`的特性应用于所有指定的模板参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-452">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="445a1-453">如果要为隐式子内容指定内容参数名称（不包含任何换行子元素），这会很有用。</span><span class="sxs-lookup"><span data-stu-id="445a1-453">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="445a1-454">在下面的示例中， `Context`属性出现`TableTemplate`在元素上，并应用于所有模板参数：</span><span class="sxs-lookup"><span data-stu-id="445a1-454">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="@pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a><span data-ttu-id="445a1-455">泛型类型化组件</span><span class="sxs-lookup"><span data-stu-id="445a1-455">Generic-typed components</span></span>

<span data-ttu-id="445a1-456">模板化组件通常是通用类型。</span><span class="sxs-lookup"><span data-stu-id="445a1-456">Templated components are often generically typed.</span></span> <span data-ttu-id="445a1-457">例如，泛型`ListViewTemplate`组件可用于呈现`IEnumerable<T>`值。</span><span class="sxs-lookup"><span data-stu-id="445a1-457">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="445a1-458">若要定义一般组件，请使用`@typeparam`指令指定类型参数：</span><span class="sxs-lookup"><span data-stu-id="445a1-458">To define a generic component, use the `@typeparam` directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="445a1-459">当使用泛型类型的组件时，将在可能的情况下推断类型参数：</span><span class="sxs-lookup"><span data-stu-id="445a1-459">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="445a1-460">否则，必须使用与类型参数的名称匹配的属性显式指定 type 参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-460">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="445a1-461">在下面的示例中`TItem="Pet"` ，指定类型：</span><span class="sxs-lookup"><span data-stu-id="445a1-461">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="445a1-462">级联值和参数</span><span class="sxs-lookup"><span data-stu-id="445a1-462">Cascading values and parameters</span></span>

<span data-ttu-id="445a1-463">在某些情况下，使用[组件参数](#component-parameters)将数据从祖先组件流式传输到附属组件是不方便的，尤其是在有多个组件层时。</span><span class="sxs-lookup"><span data-stu-id="445a1-463">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="445a1-464">级联值和参数通过提供一种方便的方法，使上级组件为其所有子代组件提供值。</span><span class="sxs-lookup"><span data-stu-id="445a1-464">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="445a1-465">级联值和参数还提供了一种方法来协调组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-465">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="445a1-466">主题示例</span><span class="sxs-lookup"><span data-stu-id="445a1-466">Theme example</span></span>

<span data-ttu-id="445a1-467">在下面的示例应用程序示例中， `ThemeInfo`类指定要向下传递组件层次结构的主题信息，以便应用程序中给定部分内的所有按钮共享相同样式。</span><span class="sxs-lookup"><span data-stu-id="445a1-467">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="445a1-468">*UIThemeClasses/ThemeInfo*：</span><span class="sxs-lookup"><span data-stu-id="445a1-468">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="445a1-469">祖先组件可以使用级联值组件提供级联值。</span><span class="sxs-lookup"><span data-stu-id="445a1-469">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="445a1-470">`CascadingValue`组件包装组件层次结构的子树，并向该子树内的所有组件提供单个值。</span><span class="sxs-lookup"><span data-stu-id="445a1-470">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="445a1-471">例如，示例应用程序将应用程序的一个`ThemeInfo`布局中的主题信息（）指定为构成`@Body`属性布局正文的所有组件的级联参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-471">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="445a1-472">`ButtonClass`为指定了布局组件`btn-success`中的值。</span><span class="sxs-lookup"><span data-stu-id="445a1-472">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="445a1-473">任何子代组件都可以通过`ThemeInfo`级联对象使用此属性。</span><span class="sxs-lookup"><span data-stu-id="445a1-473">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="445a1-474">`CascadingValuesParametersLayout`组件</span><span class="sxs-lookup"><span data-stu-id="445a1-474">`CascadingValuesParametersLayout` component:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="445a1-475">为了使用级联值，组件使用`[CascadingParameter]`属性声明级联参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-475">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="445a1-476">级联值按类型绑定到级联参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-476">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="445a1-477">在示例应用中，该`CascadingValuesParametersTheme`组件将`ThemeInfo`级联值绑定到级联参数。</span><span class="sxs-lookup"><span data-stu-id="445a1-477">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="445a1-478">参数用于设置由组件显示的一个按钮的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="445a1-478">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="445a1-479">`CascadingValuesParametersTheme`组件</span><span class="sxs-lookup"><span data-stu-id="445a1-479">`CascadingValuesParametersTheme` component:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="445a1-480">若要将同一类型的多个值层叠到相同的子树中`Name` ，请为`CascadingValue`每个组件及其`CascadingParameter`相应的提供一个唯一字符串。</span><span class="sxs-lookup"><span data-stu-id="445a1-480">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="445a1-481">在下面的示例中， `CascadingValue`两个组件按名称`MyCascadingType`层叠了不同的实例：</span><span class="sxs-lookup"><span data-stu-id="445a1-481">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="445a1-482">在子代组件中，级联参数按名称从上级组件中相应的级联值接收值：</span><span class="sxs-lookup"><span data-stu-id="445a1-482">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="445a1-483">TabSet 示例</span><span class="sxs-lookup"><span data-stu-id="445a1-483">TabSet example</span></span>

<span data-ttu-id="445a1-484">级联参数还允许组件跨组件层次结构进行协作。</span><span class="sxs-lookup"><span data-stu-id="445a1-484">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="445a1-485">例如，请看下面的示例应用中的*TabSet*示例。</span><span class="sxs-lookup"><span data-stu-id="445a1-485">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="445a1-486">该示例应用程序具有`ITab`选项卡实现的接口：</span><span class="sxs-lookup"><span data-stu-id="445a1-486">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="445a1-487">组件使用包含若干`TabSet` `Tab`组件的组件： `CascadingValuesParametersTabSet`</span><span class="sxs-lookup"><span data-stu-id="445a1-487">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="445a1-488">子`Tab`组件不会显式作为参数传递`TabSet`给。</span><span class="sxs-lookup"><span data-stu-id="445a1-488">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="445a1-489">子`Tab`组件是的子内容`TabSet`的一部分。</span><span class="sxs-lookup"><span data-stu-id="445a1-489">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="445a1-490">但是， `TabSet`仍然需要了解每个`Tab`组件，以便能够呈现标头和活动的选项卡。若要启用此协调而不需要额外的`TabSet`代码，组件*可将自身作为级联值提供*，然后由子代`Tab`组件选取。</span><span class="sxs-lookup"><span data-stu-id="445a1-490">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="445a1-491">`TabSet`组件</span><span class="sxs-lookup"><span data-stu-id="445a1-491">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="445a1-492">子代`Tab`组件将包含`TabSet`的作为`Tab`级联参数捕获，因此组件会将其自身添加`TabSet`到中，并协调选项卡处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="445a1-492">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="445a1-493">`Tab`组件</span><span class="sxs-lookup"><span data-stu-id="445a1-493">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="445a1-494">Razor 模板</span><span class="sxs-lookup"><span data-stu-id="445a1-494">Razor templates</span></span>

<span data-ttu-id="445a1-495">可以使用 Razor 模板语法来定义呈现片段。</span><span class="sxs-lookup"><span data-stu-id="445a1-495">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="445a1-496">Razor 模板是一种定义 UI 代码段并采用以下格式的一种方法：</span><span class="sxs-lookup"><span data-stu-id="445a1-496">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="445a1-497">下面的示例演示如何在组件`RenderFragment`中`RenderFragment<T>`直接指定和值和呈现模板。</span><span class="sxs-lookup"><span data-stu-id="445a1-497">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="445a1-498">呈现片段还可以作为参数传递给[模板化组件](#templated-components)。</span><span class="sxs-lookup"><span data-stu-id="445a1-498">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="445a1-499">前面的代码的呈现输出：</span><span class="sxs-lookup"><span data-stu-id="445a1-499">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="445a1-500">手动 RenderTreeBuilder 逻辑</span><span class="sxs-lookup"><span data-stu-id="445a1-500">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="445a1-501">`Microsoft.AspNetCore.Components.RenderTree`提供操作组件和元素的方法，包括在代码中C#手动生成组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-501">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="445a1-502">`RenderTreeBuilder`使用创建组件是一种高级方案。</span><span class="sxs-lookup"><span data-stu-id="445a1-502">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="445a1-503">格式不正确的组件（例如，未闭合的标记标记）可能会导致未定义的行为。</span><span class="sxs-lookup"><span data-stu-id="445a1-503">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="445a1-504">请考虑以下`PetDetails`组件，该组件可以手动内置到另一个组件中：</span><span class="sxs-lookup"><span data-stu-id="445a1-504">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="445a1-505">在下面的示例中， `CreateComponent`方法中的循环生成三个`PetDetails`组件。</span><span class="sxs-lookup"><span data-stu-id="445a1-505">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="445a1-506">当调用`RenderTreeBuilder`方法来创建组件（`OpenComponent`和`AddAttribute`）时，序列号是源代码行号。</span><span class="sxs-lookup"><span data-stu-id="445a1-506">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="445a1-507">Blazor 差异算法依赖于与不同代码行对应的序列号，而不是不同的调用调用。</span><span class="sxs-lookup"><span data-stu-id="445a1-507">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="445a1-508">使用`RenderTreeBuilder`方法创建组件时，将序列号的参数硬编码。</span><span class="sxs-lookup"><span data-stu-id="445a1-508">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="445a1-509">**使用计算或计数器生成序列号会导致性能不佳。**</span><span class="sxs-lookup"><span data-stu-id="445a1-509">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="445a1-510">有关详细信息，请参阅 "[序列号与代码行号和非执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)" 部分。</span><span class="sxs-lookup"><span data-stu-id="445a1-510">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="445a1-511">`BuiltContent`组件</span><span class="sxs-lookup"><span data-stu-id="445a1-511">`BuiltContent` component:</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="445a1-512">序列号与代码行号相关，而不是与执行顺序相关</span><span class="sxs-lookup"><span data-stu-id="445a1-512">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="445a1-513">始终`.razor`编译 Blazor 文件。</span><span class="sxs-lookup"><span data-stu-id="445a1-513">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="445a1-514">这可能是一个很好的`.razor`优点，因为编译步骤可用于注入在运行时提高应用性能的信息。</span><span class="sxs-lookup"><span data-stu-id="445a1-514">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="445a1-515">这些改进涉及到*序列号*的主要示例。</span><span class="sxs-lookup"><span data-stu-id="445a1-515">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="445a1-516">序列号指示运行时输出来自代码的不同和有序行。</span><span class="sxs-lookup"><span data-stu-id="445a1-516">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="445a1-517">运行时使用此信息以线性时间生成有效的树差异，其速度远快于一般树差异算法通常可以实现的速度。</span><span class="sxs-lookup"><span data-stu-id="445a1-517">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="445a1-518">请考虑以下简单`.razor`文件：</span><span class="sxs-lookup"><span data-stu-id="445a1-518">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="445a1-519">前面的代码编译为类似于下面的内容：</span><span class="sxs-lookup"><span data-stu-id="445a1-519">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="445a1-520">当第一次执行代码时， `someFlag` `true`生成器将接收：</span><span class="sxs-lookup"><span data-stu-id="445a1-520">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="445a1-521">序列</span><span class="sxs-lookup"><span data-stu-id="445a1-521">Sequence</span></span> | <span data-ttu-id="445a1-522">类型</span><span class="sxs-lookup"><span data-stu-id="445a1-522">Type</span></span>      | <span data-ttu-id="445a1-523">Data</span><span class="sxs-lookup"><span data-stu-id="445a1-523">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="445a1-524">0</span><span class="sxs-lookup"><span data-stu-id="445a1-524">0</span></span>        | <span data-ttu-id="445a1-525">Text 节点</span><span class="sxs-lookup"><span data-stu-id="445a1-525">Text node</span></span> | <span data-ttu-id="445a1-526">第一个</span><span class="sxs-lookup"><span data-stu-id="445a1-526">First</span></span>  |
| <span data-ttu-id="445a1-527">1</span><span class="sxs-lookup"><span data-stu-id="445a1-527">1</span></span>        | <span data-ttu-id="445a1-528">Text 节点</span><span class="sxs-lookup"><span data-stu-id="445a1-528">Text node</span></span> | <span data-ttu-id="445a1-529">第二个</span><span class="sxs-lookup"><span data-stu-id="445a1-529">Second</span></span> |

<span data-ttu-id="445a1-530">假设它`someFlag`变为`false`，并再次呈现标记。</span><span class="sxs-lookup"><span data-stu-id="445a1-530">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="445a1-531">这次，生成器将接收：</span><span class="sxs-lookup"><span data-stu-id="445a1-531">This time, the builder receives:</span></span>

| <span data-ttu-id="445a1-532">序列</span><span class="sxs-lookup"><span data-stu-id="445a1-532">Sequence</span></span> | <span data-ttu-id="445a1-533">类型</span><span class="sxs-lookup"><span data-stu-id="445a1-533">Type</span></span>       | <span data-ttu-id="445a1-534">Data</span><span class="sxs-lookup"><span data-stu-id="445a1-534">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="445a1-535">1</span><span class="sxs-lookup"><span data-stu-id="445a1-535">1</span></span>        | <span data-ttu-id="445a1-536">Text 节点</span><span class="sxs-lookup"><span data-stu-id="445a1-536">Text node</span></span>  | <span data-ttu-id="445a1-537">第二个</span><span class="sxs-lookup"><span data-stu-id="445a1-537">Second</span></span> |

<span data-ttu-id="445a1-538">当运行时执行差异时，它会发现序列`0`中的项已被删除，因此它生成以下简单的*编辑脚本*：</span><span class="sxs-lookup"><span data-stu-id="445a1-538">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="445a1-539">删除第一个文本节点。</span><span class="sxs-lookup"><span data-stu-id="445a1-539">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="445a1-540">如果以编程方式生成序列号，会出现什么错误</span><span class="sxs-lookup"><span data-stu-id="445a1-540">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="445a1-541">假设你编写了以下呈现树生成器逻辑：</span><span class="sxs-lookup"><span data-stu-id="445a1-541">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="445a1-542">现在，第一个输出是：</span><span class="sxs-lookup"><span data-stu-id="445a1-542">Now, the first output is:</span></span>

| <span data-ttu-id="445a1-543">序列</span><span class="sxs-lookup"><span data-stu-id="445a1-543">Sequence</span></span> | <span data-ttu-id="445a1-544">类型</span><span class="sxs-lookup"><span data-stu-id="445a1-544">Type</span></span>      | <span data-ttu-id="445a1-545">Data</span><span class="sxs-lookup"><span data-stu-id="445a1-545">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="445a1-546">0</span><span class="sxs-lookup"><span data-stu-id="445a1-546">0</span></span>        | <span data-ttu-id="445a1-547">Text 节点</span><span class="sxs-lookup"><span data-stu-id="445a1-547">Text node</span></span> | <span data-ttu-id="445a1-548">第一个</span><span class="sxs-lookup"><span data-stu-id="445a1-548">First</span></span>  |
| <span data-ttu-id="445a1-549">1</span><span class="sxs-lookup"><span data-stu-id="445a1-549">1</span></span>        | <span data-ttu-id="445a1-550">Text 节点</span><span class="sxs-lookup"><span data-stu-id="445a1-550">Text node</span></span> | <span data-ttu-id="445a1-551">第二个</span><span class="sxs-lookup"><span data-stu-id="445a1-551">Second</span></span> |

<span data-ttu-id="445a1-552">此结果与以前的情况相同，因此不存在负面问题。</span><span class="sxs-lookup"><span data-stu-id="445a1-552">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="445a1-553">`someFlag``false`在第二次呈现时，输出为：</span><span class="sxs-lookup"><span data-stu-id="445a1-553">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="445a1-554">序列</span><span class="sxs-lookup"><span data-stu-id="445a1-554">Sequence</span></span> | <span data-ttu-id="445a1-555">类型</span><span class="sxs-lookup"><span data-stu-id="445a1-555">Type</span></span>      | <span data-ttu-id="445a1-556">Data</span><span class="sxs-lookup"><span data-stu-id="445a1-556">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="445a1-557">0</span><span class="sxs-lookup"><span data-stu-id="445a1-557">0</span></span>        | <span data-ttu-id="445a1-558">Text 节点</span><span class="sxs-lookup"><span data-stu-id="445a1-558">Text node</span></span> | <span data-ttu-id="445a1-559">第二个</span><span class="sxs-lookup"><span data-stu-id="445a1-559">Second</span></span> |

<span data-ttu-id="445a1-560">这次，比较算法会发现*两个*更改已发生，并且算法将生成以下编辑脚本：</span><span class="sxs-lookup"><span data-stu-id="445a1-560">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="445a1-561">将第一个文本节点的值更改为`Second`。</span><span class="sxs-lookup"><span data-stu-id="445a1-561">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="445a1-562">删除第二个文本节点。</span><span class="sxs-lookup"><span data-stu-id="445a1-562">Remove the second text node.</span></span>

<span data-ttu-id="445a1-563">生成序列号丢失了有关`if/else`分支和循环在原始代码中的位置的所有有用信息。</span><span class="sxs-lookup"><span data-stu-id="445a1-563">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="445a1-564">这会导致**两次**比较，就像以前一样。</span><span class="sxs-lookup"><span data-stu-id="445a1-564">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="445a1-565">这是一个简单的示例。</span><span class="sxs-lookup"><span data-stu-id="445a1-565">This is a trivial example.</span></span> <span data-ttu-id="445a1-566">在具有复杂、深度嵌套结构的更真实的情况下，特别是在循环中，性能开销更严重。</span><span class="sxs-lookup"><span data-stu-id="445a1-566">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="445a1-567">比较算法不必立即确定哪些循环块或分支已插入或删除，而是必须将其深入地递归到呈现树，并且通常会生成更长的编辑脚本，因为它 misinformed 了新的和新的结构彼此关联。</span><span class="sxs-lookup"><span data-stu-id="445a1-567">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="445a1-568">指导和结论</span><span class="sxs-lookup"><span data-stu-id="445a1-568">Guidance and conclusions</span></span>

* <span data-ttu-id="445a1-569">如果动态生成了序列号，则应用程序性能会受到影响。</span><span class="sxs-lookup"><span data-stu-id="445a1-569">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="445a1-570">框架无法在运行时自动创建自己的序列号，因为所需信息不存在，除非它在编译时捕获。</span><span class="sxs-lookup"><span data-stu-id="445a1-570">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="445a1-571">不要编写长时间的手动实现`RenderTreeBuilder`逻辑。</span><span class="sxs-lookup"><span data-stu-id="445a1-571">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="445a1-572">首选`.razor`文件，并允许编译器处理序列号。</span><span class="sxs-lookup"><span data-stu-id="445a1-572">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span>
* <span data-ttu-id="445a1-573">如果对序列号进行硬编码，则 diff 算法只要求序列号的值增加。</span><span class="sxs-lookup"><span data-stu-id="445a1-573">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="445a1-574">初始值和间隙无关。</span><span class="sxs-lookup"><span data-stu-id="445a1-574">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="445a1-575">一个合法选项是将代码行号用作序列号，或从零开始，并按一个或数百个（或任何首选间隔）递增。</span><span class="sxs-lookup"><span data-stu-id="445a1-575">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="445a1-576">Blazor 使用序列号，而其他树比较的 UI 框架不使用序列号。</span><span class="sxs-lookup"><span data-stu-id="445a1-576">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="445a1-577">使用序列号时，比较速度要快得多，并且 Blazor 具有用于为开发人员创作`.razor`文件自动处理序列号的编译步骤。</span><span class="sxs-lookup"><span data-stu-id="445a1-577">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="445a1-578">本地化</span><span class="sxs-lookup"><span data-stu-id="445a1-578">Localization</span></span>

<span data-ttu-id="445a1-579">Blazor 服务器端应用使用[本地化中间件](xref:fundamentals/localization#localization-middleware)进行本地化。</span><span class="sxs-lookup"><span data-stu-id="445a1-579">Blazor server-side apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="445a1-580">中间件为从应用程序请求资源的用户选择相应的区域性。</span><span class="sxs-lookup"><span data-stu-id="445a1-580">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="445a1-581">可以使用以下方法之一设置区域性：</span><span class="sxs-lookup"><span data-stu-id="445a1-581">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="445a1-582">Cookie</span><span class="sxs-lookup"><span data-stu-id="445a1-582">Cookies</span></span>](#cookies)
* [<span data-ttu-id="445a1-583">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="445a1-583">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="445a1-584">有关更多信息和示例，请参见<xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="445a1-584">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="445a1-585">Cookie</span><span class="sxs-lookup"><span data-stu-id="445a1-585">Cookies</span></span>

<span data-ttu-id="445a1-586">本地化区域性 cookie 可以保存用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="445a1-586">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="445a1-587">Cookie 是通过`OnGet`应用程序主机页（*Pages/host. .cs*）的方法创建的。</span><span class="sxs-lookup"><span data-stu-id="445a1-587">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="445a1-588">本地化中间件会在后续请求上读取 cookie，以设置用户的区域性。</span><span class="sxs-lookup"><span data-stu-id="445a1-588">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="445a1-589">使用 cookie 可确保 WebSocket 连接可以正确地传播区域性。</span><span class="sxs-lookup"><span data-stu-id="445a1-589">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="445a1-590">如果本地化方案基于 URL 路径或查询字符串，则该方案可能无法与 Websocket 一起使用，因此无法持久保存区域性。</span><span class="sxs-lookup"><span data-stu-id="445a1-590">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="445a1-591">因此，建议使用本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="445a1-591">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="445a1-592">如果在本地化 cookie 中保留了区域性，则可使用任何方法来分配区域性。</span><span class="sxs-lookup"><span data-stu-id="445a1-592">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="445a1-593">如果应用已建立服务器端 ASP.NET Core 的本地化方案，请继续使用应用的现有本地化基础结构，并在应用方案中设置本地化区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="445a1-593">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="445a1-594">下面的示例演示如何在可由本地化中间件读取的 cookie 中设置当前区域性。</span><span class="sxs-lookup"><span data-stu-id="445a1-594">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="445a1-595">在 Blazor 服务器端应用中创建包含以下内容的*页面/主机 .cs*文件：</span><span class="sxs-lookup"><span data-stu-id="445a1-595">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor server-side app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="445a1-596">在应用程序中处理本地化：</span><span class="sxs-lookup"><span data-stu-id="445a1-596">Localization is handled in the app:</span></span>

1. <span data-ttu-id="445a1-597">浏览器将初始 HTTP 请求发送到应用程序。</span><span class="sxs-lookup"><span data-stu-id="445a1-597">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="445a1-598">区域性由本地化中间件分配。</span><span class="sxs-lookup"><span data-stu-id="445a1-598">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="445a1-599">_Host `OnGet`中的方法将区域性作为响应的一部分保留在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="445a1-599">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="445a1-600">浏览器将打开 WebSocket 连接以创建交互式 Blazor 服务器端会话。</span><span class="sxs-lookup"><span data-stu-id="445a1-600">The browser opens a WebSocket connection to create an interactive Blazor server-side session.</span></span>
1. <span data-ttu-id="445a1-601">本地化中间件读取 cookie 并分配区域性。</span><span class="sxs-lookup"><span data-stu-id="445a1-601">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="445a1-602">Blazor 服务器端会话以正确的区域性开头。</span><span class="sxs-lookup"><span data-stu-id="445a1-602">The Blazor server-side session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="445a1-603">提供用于选择区域性的 UI</span><span class="sxs-lookup"><span data-stu-id="445a1-603">Provide UI to choose the culture</span></span>

<span data-ttu-id="445a1-604">为了提供允许用户选择区域性的 UI，建议使用*基于重定向的方法*。</span><span class="sxs-lookup"><span data-stu-id="445a1-604">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="445a1-605">此过程类似于用户尝试访问安全资源&mdash;时在 web 应用中发生的情况，用户将重定向到登录页，然后重定向回原始资源。</span><span class="sxs-lookup"><span data-stu-id="445a1-605">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="445a1-606">应用程序通过重定向到控制器来持久保存用户的所选区域性。</span><span class="sxs-lookup"><span data-stu-id="445a1-606">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="445a1-607">控制器将用户选定的区域性设置为 cookie，并将用户重定向回原始 URI。</span><span class="sxs-lookup"><span data-stu-id="445a1-607">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="445a1-608">在服务器上创建一个 HTTP 终结点，以在 cookie 中设置用户的所选区域性，并执行重定向回原始 URI：</span><span class="sxs-lookup"><span data-stu-id="445a1-608">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="445a1-609">`LocalRedirect`使用操作结果可防止开放重定向攻击。</span><span class="sxs-lookup"><span data-stu-id="445a1-609">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="445a1-610">有关详细信息，请参阅 <xref:security/preventing-open-redirects> 。</span><span class="sxs-lookup"><span data-stu-id="445a1-610">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="445a1-611">以下组件显示了一个示例，说明如何在用户选择区域性时执行初始重定向：</span><span class="sxs-lookup"><span data-stu-id="445a1-611">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(UIChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="445a1-612">在 Blazor 应用中使用 .NET 本地化方案</span><span class="sxs-lookup"><span data-stu-id="445a1-612">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="445a1-613">在 Blazor 应用程序中，可以使用以下 .NET 本地化和全球化方案：</span><span class="sxs-lookup"><span data-stu-id="445a1-613">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="445a1-614">.网络资源系统</span><span class="sxs-lookup"><span data-stu-id="445a1-614">.NET's resources system</span></span>
* <span data-ttu-id="445a1-615">区域性特定的数字和日期格式设置</span><span class="sxs-lookup"><span data-stu-id="445a1-615">Culture-specific number and date formatting</span></span>

<span data-ttu-id="445a1-616">Blazor 的`@bind`功能基于用户的当前区域性执行全球化。</span><span class="sxs-lookup"><span data-stu-id="445a1-616">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="445a1-617">有关详细信息，请参阅[数据绑定](#data-binding)部分。</span><span class="sxs-lookup"><span data-stu-id="445a1-617">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="445a1-618">目前支持有限的一组 ASP.NET Core 本地化方案：</span><span class="sxs-lookup"><span data-stu-id="445a1-618">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="445a1-619">`IStringLocalizer<>`在 Blazor 应用中*受支持*。</span><span class="sxs-lookup"><span data-stu-id="445a1-619">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="445a1-620">`IHtmlLocalizer<>`、 `IViewLocalizer<>`和数据批注本地化 ASP.NET Core MVC 方案，在 Blazor 应用中**不受支持**。</span><span class="sxs-lookup"><span data-stu-id="445a1-620">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="445a1-621">有关详细信息，请参阅 <xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="445a1-621">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="445a1-622">可缩放的向量图形（SVG）图像</span><span class="sxs-lookup"><span data-stu-id="445a1-622">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="445a1-623">由于 Blazor 呈现 HTML，因此支持浏览器支持的图像（包括可缩放的向量图形（svg）图像（*svg*）） `<img>`是通过标记支持的：</span><span class="sxs-lookup"><span data-stu-id="445a1-623">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="445a1-624">同样，样式表文件（ *.css*）的 CSS 规则支持 SVG 图像：</span><span class="sxs-lookup"><span data-stu-id="445a1-624">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="445a1-625">但是，在所有情况下均不支持内联 SVG 标记。</span><span class="sxs-lookup"><span data-stu-id="445a1-625">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="445a1-626">如果将`<svg>`标记直接放入组件文件（*razor*），则支持基本图像呈现，但尚不支持很多高级方案。</span><span class="sxs-lookup"><span data-stu-id="445a1-626">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="445a1-627">例如， `<use>`标记当前未遵循，并且`@bind`不能与一些 SVG 标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="445a1-627">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="445a1-628">我们预计会在将来的版本中解决这些限制。</span><span class="sxs-lookup"><span data-stu-id="445a1-628">We expect to address these limitations in a future release.</span></span>