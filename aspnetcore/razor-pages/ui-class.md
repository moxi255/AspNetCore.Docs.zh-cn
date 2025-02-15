---
title: ASP.NET Core 的类库中的可重用 Razor UI
author: Rick-Anderson
description: 说明如何创建可重复使用 Razor UI 在 ASP.NET Core 中的类库中使用分部视图。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 06/28/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: d59f643a23b48ccbddf498ef534ee8432b010f40
ms.sourcegitcommit: 6d9cf728465cdb0de1037633a8b7df9a8989cccb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67463258"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>创建使用 ASP.NET Core 中 Razor 类库项目的可重用 UI

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

Razor 视图、 页面、 控制器、 页面模型[Razor 组件](xref:blazor/class-libraries)，[查看组件](xref:mvc/views/view-components)，并且可以到 Razor 类库 (RCL) 生成数据模型。 RCL 可以打包并重复使用。 应用程序可以包括 RCL，并重写其中包含的视图和页面。 如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先  。

此功能要求 [!INCLUDE[](~/includes/2.1-SDK.md)]

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="create-a-class-library-containing-razor-ui"></a>创建一个包含 Razor UI 的类库

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”    。
* 选择“ASP.NET Core Web 应用程序”  。
* 命名库（例如，“RazorClassLib”）>“确定”  。 为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。
* 验证是否已选择 ASP.NET Core 2.1 或更高版本  。
* 选择“Razor 类库” > “确定”   。

RCL 具有以下项目文件：

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

从命令行中，运行 `dotnet new razorclasslib`。 例如：

```console
dotnet new razorclasslib -o RazorUIClassLib
```

有关详细信息，请查看 [dotnet new](/dotnet/core/tools/dotnet-new)。 为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。

---

将 Razor 文件添加到 RCL。

ASP.NET Core 模板假定 RCL 内容位于*领域*文件夹。 请参阅[RCL 页面布局](#afs)若要创建中的内容公开 RCL`~/Pages`而非`~/Areas/Pages`。

## <a name="referencing-rcl-content"></a>引用 RCL 内容

可以通过以下方式引用 RCL：

* NuGet 包。 请参阅[创建 NuGet 包](/nuget/create-packages/creating-a-package)、[dotnet 添加包](/dotnet/core/tools/dotnet-add-package)和[创建和发布 NuGet 包](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。
* {ProjectName}.csproj  。 请查看 [dotnet-add 引用](/dotnet/core/tools/dotnet-add-reference)。

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>演练：创建 RCL 项目并使用从 Razor 页面项目

可以下载并测试[完整项目](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)，无需创建项目。 示例下载包含附加代码和链接，以方便测试项目。 可以在[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/6098)中留下反馈，评论下载示例和分步说明的对比。

### <a name="test-the-download-app"></a>测试下载应用

如果尚未下载已完成的应用，并更愿意创建演练项目，请跳转至[下一节](#create-an-rcl)。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

在 Visual Studio 中打开 .sln 文件  。 运行应用。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

在 cli 目录中的命令提示符下生成 RCL 和 Web 应用  。

```console
dotnet build
```

移动至 WebApp1 目录，并运行应用  ：

```console
dotnet run
```

---

按[“测试 Test WebApp1”](#test)中的说明进行操作

## <a name="create-an-rcl"></a>创建 RCL

在本部分中，创建 RCL。 将 Razor 文件添加到 RCL。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

创建 RCL 项目：

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”    。
* 选择“ASP.NET Core Web 应用程序”  。
* 命名应用程序**RazorUIClassLib** > **确定**。
* 验证是否已选择 ASP.NET Core 2.1 或更高版本  。
* 选择“Razor 类库” > “确定”   。
* 添加一个名为 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 的 Razor 分部视图文件  。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

从命令行运行以下命令：

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

前面的命令：

* 创建`RazorUIClassLib`RCL。
* 创建 Razor _Message 页面，并将其添加至 RCL。 `-np` 参数创建不含 `PageModel` 的页面。
* 创建[_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view)文件，并将其添加到 RCL。

*_ViewStart.cshtml*时需要使用 （其中添加下一节中） 的 Razor 页面项目的布局文件。

---

### <a name="add-razor-files-and-folders-to-the-project"></a>将 Razor 文件和文件夹添加到项目

* 使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 中的标记  ：

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* 使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml 中的标记  ：

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

使用分步视图 (`<partial name="_Message" />`) 需要 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`。 可以添加一个 _ViewImports.cshtml 文件，无需包含 `@addTagHelper` 指令  。 例如：

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

有关详细信息 *_ViewImports.cshtml*，请参阅[导入共享指令](xref:mvc/views/layout#importing-shared-directives)

* 生成类库以验证是否不存在编译器错误：

```console
dotnet build RazorUIClassLib
```

生成输出内容包含 RazorUIClassLib.dll 和 RazorUIClassLib.Views.dll   。 RazorUIClassLib.Views.dll 包含已编译的 Razor 内容  。

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>从 Razor 页面项目使用 Razor UI 库

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

创建 Razor 页面 Web 应用：

* 在解决方案资源管理器中，右键单击解决方案 >“添加” >  “新建项目”    。
* 选择“ASP.NET Core Web 应用程序”  。
* 将应用命名为 WebApp1  。
* 验证是否已选择 ASP.NET Core 2.1 或更高版本  。
* 选择“Web 应用程序” > “确定”   。

* 在解决方案资源管理器中，右键单击“WebApp1”，然后选择“设为启动项目”    。
* 在解决方案资源管理器中，右键单击“WebApp1”，然后选择“生成依赖项” > “项目依赖项”     。
* 将 RazorUIClassLib 勾选为 WebApp1 的依赖项   。
* 在解决方案资源管理器中，右键单击“WebApp1”，选择“添加” > “引用”     。
* 在“引用管理器”对话框中勾选“RazorUIClassLib” > “确定”    。

运行应用。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

创建 Razor 页面 web 应用和解决方案文件包含 Razor 页面应用和 RCL:

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

生成并运行 Web 应用：

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a>测试 WebApp1

验证正在使用中的 Razor UI 类库：

* 浏览到 `/MyFeature/Page1`。

## <a name="override-views-partial-views-and-pages"></a>重写视图、分部视图和页面

如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先  。 例如，添加*WebApp1/Areas/MyFeature/Pages/Page1.cshtml*到 WebApp1，并在 WebApp1 Page1 将优先于在 RCL Page1。

在示例下载中，将 WebApp1/Areas/MyFeature2 重命名为 WebApp1/Areas/MyFeature 来测试优先级   。

将 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 分部视图复制到 WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml   。 更新标记以指示新的位置。 生成并运行应用，验证使用部分的应用版本。

<a name="afs"></a>

### <a name="rcl-pages-layout"></a>RCL 页面布局

为引用 RCL 内容就好像它是 web 应用的一部分*页面*文件夹中，创建具有以下文件结构 RCL 项目：

* *RazorUIClassLib/页*
* *RazorUIClassLib/页/Shared*

假设*RazorUIClassLib/页/共享*包含两个部分的文件： *_Header.cshtml*并 *_Footer.cshtml*。 `<partial>`标记无法添加到 *_Layout.cshtml*文件：

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker range=">= aspnetcore-3.0"

## <a name="create-an-rcl-with-static-assets"></a>创建具有静态资产 RCL

RCL 可能需要的 RCL 正在使用的应用，可以引用的配套静态资产。 ASP.NET Core，可以创建包含适用于正在使用的应用的静态资产的 RCLs。

要包括的 RCL 一部分附带的资产，创建*wwwroot*类库中的文件夹和该文件夹中包含任何所需的文件。

打包 RCL 时, 所有伴生中的资产*wwwroot*文件夹自动包含在包中，并可供引用包的应用。

### <a name="consume-content-from-a-referenced-rcl"></a>使用提供的内容引用 RCL

中包含的文件*wwwroot* RCL 文件夹，会面临在前缀下正在使用的应用`_content/{LIBRARY NAME}/`。 `{LIBRARY NAME}` 库项目名称转换为小写使用句点 (`.`) 中删除。 例如，一个名为的库*Razor.Class.Lib*构成的路径为静态内容`_content/razorclasslib/`。

正在使用的应用引用静态资产库提供`<script>`， `<style>`， `<img>`，和其他 HTML 标记。 正在使用的应用必须具有[静态文件支持](xref:fundamentals/static-files)启用。

### <a name="multi-project-development-flow"></a>多项目开发流

正在使用的应用的运行时：

* RCL 保留在它们的原始文件夹中的资产。 资产不会移到正在使用的应用。
* RCL 内的任何更改*wwwroot*文件夹将反映 RCL 重新生成后，正在使用的应用，无需重新生成正在使用的应用。

RCL 生成时，会生成一个清单描述静态 web 资产位置。 正在使用的应用读取在运行时要使用从引用的项目和包资产的清单。 新的资产添加到 RCL，则必须重新生成 RCL 正在使用的应用可以访问新的资产之前更新其清单。

### <a name="publish"></a>发布

发布应用程序时，将从所有的被引用项目和包的配套资产复制到*wwwroot*文件夹下的已发布应用`_content/{LIBRARY NAME}/`。

::: moniker-end
