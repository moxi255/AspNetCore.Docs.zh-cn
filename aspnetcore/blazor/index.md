---
title: ASP.NET Core 中的 Blazor 简介
author: guardrex
description: 了解 ASP.NET Core Blazor，用户可以借助它在 ASP.NET Core 应用中使用 .NET 生成交互式客户端 Web UI。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 07/01/2019
uid: blazor/index
ms.openlocfilehash: dbfadf9481cf16279e2a491bc04f51058e1d8a9c
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500429"
---
# <a name="introduction-to-blazor"></a>Blazor 简介

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

*欢迎使用 Blazor！*

Blazor 是一个用于使用 .NET 生成交互式客户端 Web UI 的框架：

* 使用 C# 代替 JavaScript 来创建丰富的交互式 UI。
* 共享使用 .NET 编写的服务器端和客户端应用逻辑。
* 将 UI 呈现为 HTML 和 CSS，以支持众多浏览器，其中包括移动浏览器。

使用 .NET 进行客户端 Web 开发可提供以下优势：

* 使用 C# 代替 JavaScript 来编写代码。
* 利用现有的 .NET 库生态系统。
* 在服务器和客户端之间共享应用逻辑。
* 受益于 .NET 的性能、可靠性和安全性。
* 始终高效支持 Windows、Linux 和 macOS 上的 Visual Studio。
* 以一组稳定、功能丰富且易用的通用语言、框架和工具为基础来进行生成。

## <a name="components"></a>组件数

Blazor 应用基于组件。 Blazor 中的组件是指 UI 元素，例如，页面、对话框或数据输入窗体。

组件是内置到 .NET 程序集的 .NET 类，用来：

* 定义灵活的 UI 呈现逻辑。
* 处理用户事件。
* 可以嵌套和重用。
* 可以作为 [Razor 类库](xref:razor-pages/ui-class)或 [NuGet 包](/nuget/what-is-nuget)共享和分发。

组件类通常以 [Razor](xref:mvc/views/razor) 标记页（文件扩展名为 .razor）的形式编写。 Blazor 中的组件有时被称为 Razor 组件。 Razor 是用于将 HTML 标记与专为提高开发人员工作效率而设计的 C# 代码结合在一起的语法。 借助 Razor，可以使用 [IntelliSense](/visualstudio/ide/using-intellisense) 支持在同一文件中的 HTML 标记和 C# 之间切换。 Razor Pages 和 MVC 也使用 Razor。 与围绕请求/响应模型生成的 Razor Pages 和 MVC 不同，组件专门用于处理客户端 UI 逻辑和构成。

以下 Razor 标记演示组件 (Dialog.razor)，该组件可以嵌套在另一个组件中：

```cshtml
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="@OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    private string Title { get; set; }

    [Parameter]
    private RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

对话框的正文内容 (`ChildContent`) 和标题 (`Title`) 由在其 UI 中使用此组件的组件提供。 `OnYes` 是由按钮的 `onclick` 事件触发的 C# 方法。

Blazor 使用 UI 构成的自然 HTML 标记。 HTML 元素指定组件，并且标记的特性将值传递给组件的属性。

在以下示例中，`Index` 组件使用 `Dialog` 组件。 `ChildContent` 和 `Title` 由 `<Dialog>` 元素的属性和内容设置。

Index.razor：

```cshtml
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

在浏览器中访问父级 (Index.razor) 时，将呈现该对话框：

![浏览器中呈现的对话框组件](index/_static/dialog.png)

如果在应用中使用此组件，[Visual Studio](/visualstudio/ide/using-intellisense) 和 [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) 中的 IntelliSense 可加快使用语法和参数补全的开发。

组件呈现为浏览器文档对象模型 (DOM) 的内存中表现形式，称为“呈现树”，用于以灵活高效的方式更新 UI。

## <a name="blazor-client-side"></a>Blazor 客户端

Blazor 客户端是一个单页应用框架，用于使用 .NET 生成交互式客户端 Web 应用。 Blazor 客户端使用开放的 Web 标准（没有插件或代码转换），并且适用于所有新式 Web 浏览器（包括移动浏览器）。

通过 [WebAssembly](http://webassembly.org)（缩写为 wasm），可在 Web 浏览器内运行 .NET 代码。 WebAssembly 是针对快速下载和最大执行速度优化的压缩字节码格式。 WebAssembly 是开放的 Web 标准，支持用于无插件的 Web 浏览器。

WebAssembly 代码可通过 JavaScript（称为 JavaScript 互操作性或 JavaScript 互操作）访问浏览器的完整功能。 通过浏览器中的 WebAssembly 执行的 .NET 代码在浏览器的 JavaScript 沙盒中运行，沙盒提供的保护可防御客户端计算机上的恶意操作。

![Blazor 客户端使用 WebAssembly 在浏览器中运行 .NET 代码。](index/_static/blazor-client-side.png)

生成 Blazor 客户端应用并在浏览器中运行时：

* C# 代码文件和 Razor 文件将被编译为 .NET 程序集。
* 该程序集和 .NET 运行时将被下载到浏览器。
* Blazor 客户端启动 .NET 运行时并配置运行时，为应用加载程序集。 Blazor 客户端运行时使用 JavaScript 互操作处理 DOM 操作和浏览器 API 调用。

已发布应用的大小（其有效负载大小）是应用可用性的关键性能因素。 大型应用需要相对较长的时间才能下载到浏览器，这会损害用户体验。 Blazor 客户端优化有效负载大小，以缩短下载时间：

* 在[中间语言 (IL) 链接器](xref:host-and-deploy/blazor/configure-linker)发布应用时，会从应用删除未使用的代码。
* 压缩 HTTP 响应。
* .NET 运行时和程序集缓存在浏览器中。

## <a name="blazor-server-side"></a>Razor 服务器端

Razor 将组件呈现逻辑从 UI 更新的应用方式中分离出来。 Razor 服务器端在 ASP.NET Core 应用中添加了对在服务器上托管 Razor 组件的支持。 可通过 [SignalR](xref:signalr/introduction) 连接处理 UI 更新。

运行时处理从浏览器向服务器发送 UI 事件，并在运行组件后，将服务器发送的 UI 更新重新应用到浏览器。

被 Razor 服务器端用于与浏览器进行通信的组件还可用于处理 JavaScript 互操作调用。

![Razor 服务器端在服务器上运行 .NET 代码，并通过 SignalR 连接与客户端上的文档对象模型进行交互](index/_static/blazor-server-side.png)

## <a name="javascript-interop"></a>JavaScript 互操作

对于需要第三方 JavaScript 库和访问浏览器 API 的应用，组件与 JavaScript 进行互操作。 组件能够使用 JavaScript 能够使用的任何库或 API。 C# 代码可以调用到 JavaScript 代码，而 JavaScript 代码可以调用到 C# 代码。 有关详细信息，请参阅 <xref:blazor/javascript-interop>。

## <a name="code-sharing-and-net-standard"></a>代码共享和 .NET Standard

Blazor 实现 [.NET Standard 2.0](/dotnet/standard/net-standard)。 .NET Standard 是正式的 .NET API 规范，常见于 .NET 实现中。 .NET Standard 类库可以在不同 .NET 平台之间共享，例如 Blazor、.NET Framework、.NET Core、Xamarin、Mono 和 Unity。

不适用于 Web 浏览器内部的 API（例如，访问文件系统、打开套接字和线程处理）将引发 <xref:System.PlatformNotSupportedException>。

## <a name="additional-resources"></a>其他资源

* [WebAssembly](http://webassembly.org/)
* <xref:blazor/hosting-models>
* [C# 指南](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
