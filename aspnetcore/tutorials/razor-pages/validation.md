---
title: 将验证添加到 ASP.NET Core Razor 页面
author: rick-anderson
description: 了解如何将验证添加到 ASP.NET Core 中的 Razor 页面。
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 38e1fff9c7a212af992951dbf57e124cae69d36f
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874988"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>将验证添加到 ASP.NET Core Razor 页面

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本部分中向 `Movie` 模型添加了验证逻辑。 每当用户创建或编辑电影时，都会强制执行验证规则。

## <a name="validation"></a>验证

软件开发的一个关键原则被称为 [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself)（即“不要自我重复”）。 Razor 页面鼓励进行仅指定一次功能的开发，且功能在整个应用中反映。 DRY 可以帮助：

* 减少应用中的代码量。
* 使代码更加不易出错，且更易于测试和维护。

Razor 页面和 Entity Framework 提供的验证支持是 DRY 原则的极佳示例。 验证规则在模型类中的某处以声明方式指定，且在应用的所有位置强制执行。

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

### <a name="validation-error-ui-in-razor-pages"></a>Razor 页面中的“验证错误”UI

运行应用并导航到“页面/电影”。

选择“新建”链接。 使用无效值填写表单。 当 jQuery 客户端验证检测到错误时，会显示一条错误消息。

![带有多个 jQuery 客户端验证错误的电影视图表单](validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

请注意表单如何自动呈现每个包含无效值的字段中的验证错误消息。 客户端（使用 JavaScript 和 jQuery）和服务器端（若用户禁用 JavaScript）都必定会遇到这些错误。

一项重要优势是，无需在“创建”或“编辑”页面中更改代码。 在模型应用 DataAnnotations 后，即已启用验证 UI。 本教程中自动创建的 Razor 页面自动选取了验证规则（使用 `Movie` 模型类的属性上的验证特性）。 使用“编辑”页面测试验证后，即已应用相同验证。

存在客户端验证错误时，不会将表单数据发布到服务器。 请通过以下一种或多种方法验证是否未发布表单数据：

* 在 `OnPostAsync` 方法中放置一个断点。 提交表单（选择“创建”或“保存”）。 从未命中断点。
* 使用 [Fiddler 工具](http://www.telerik.com/fiddler)。
* 使用浏览器开发人员工具监视网络流量。

### <a name="server-side-validation"></a>服务器端验证

在浏览器中禁用 JavaScript 后，提交出错表单将发布到服务器。

（可选）测试服务器端验证：

* 在浏览器中禁用 JavaScript。 可以使用浏览器的开发人员工具执行此操作。 如果无法在浏览器中禁用 JavaScript，请尝试其他浏览器。
* 在“创建”或“编辑”页面的 `OnPostAsync` 方法中设置断点。
* 提交带有验证错误的表单。
* 验证模型状态是否无效：

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

以下代码显示了之前在本教程中设定其基架的“Create.cshtml”的一部分。 它用于在“创建”和“编辑”页面中显示初始表单并在发生错误后重新显示表单。

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[输入标记帮助程序](xref:mvc/views/working-with-forms)使用 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 特性并在客户端生成 jQuery 验证所需的 HTML 特性。 [验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)用于显示验证错误。 有关详细信息，请参阅[验证](xref:mvc/models/validation)。

“创建”和“编辑”页面中没有验证规则。 仅可在 `Movie` 类中指定验证规则和错误字符串。 这些验证规则将自动应用于编辑 `Movie` 模型的 Razor 页面。

需要更改验证逻辑时，也只能在该模型中更改。 将始终在整个应用程序中应用验证（在一处定义验证逻辑）。 单处验证有助于保持代码干净，且更易于维护和更新。

## <a name="using-datatype-attributes"></a>使用 DataType 特性

检查 `Movie` 类。 除了一组内置的验证特性，`System.ComponentModel.DataAnnotations` 命名空间还提供格式特性。 `DataType` 特性应用于 `ReleaseDate` 和 `Price` 属性。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType` 特性仅提供相关提示来帮助视图引擎设置数据格式（并提供特性，例如向 URL 提供 `<a>` 和向电子邮件提供 `<a href="mailto:EmailAddress.com">`）。 使用 `RegularExpression` 特性验证数据的格式。 `DataType` 属性用于指定比数据库内部类型更具体的数据类型。 `DataType` 特性不是验证特性。 示例应用程序中仅显示日期，不显示时间。

`DataType` 枚举提供了多种数据类型，例如日期、时间、电话号码、货币、电子邮件地址等。 应用程序还可通过 `DataType` 特性自动提供类型特定的功能。 例如，可为 `DataType.EmailAddress` 创建 `mailto:` 链接。 可在支持 HTML5 的浏览器中为 `DataType.Date` 提供日期选择器。 `DataType` 特性发出 HTML 5 `data-`（读作 data dash）特性供 HTML 5 浏览器使用。 `DataType` 特性不提供任何验证。

`DataType.Date` 不指定显示日期的格式。 默认情况下，数据字段根据基于服务器的 `CultureInfo` 的默认格式进行显示。

要使 Entity Framework Core 能将 `Price` 正确地映射到数据库中的货币，则必须使用 `[Column(TypeName = "decimal(18, 2)")]` 数据注释。 有关详细信息，请参阅[数据类型](/ef/core/modeling/relational/data-types)。

`DisplayFormat` 特性用于显式指定日期格式：

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` 设置用于指定在显示值进行编辑时需应用格式。 可能不希望某些字段具有此行为。 例如，在货币值中，可能不希望编辑 UI 中存在货币符号。

可单独使用 `DisplayFormat` 特性，但通常建议使用 `DataType` 特性。 `DataType` 特性传达数据的语义而不是传达如何在屏幕上呈现数据，并提供 DisplayFormat 不具备的以下优势：

* 浏览器可启用 HTML5 功能（例如显示日历控件、区域设置适用的货币符号、电子邮件链接等）
* 默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。
* 借助 `DataType` 特性，ASP.NET Core 框架可选择适当的字段模板来呈现数据。 单独使用时，`DisplayFormat` 特性将使用字符串模板。

注意：jQuery 验证不适用于 `Range` 属性和 `DateTime`。 例如，以下代码将始终显示客户端验证错误，即便日期在指定的范围内：

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

通常，在模型中编译固定日期是不恰当的，因此不推荐使用 `Range` 特性和 `DateTime`。

以下代码显示组合在一行上的特性：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Razor Pages 和 EF Core 入门](xref:data/ef-rp/intro)显示了 Razor Pages 的高级 EF Core 操作。

### <a name="publish-to-azure"></a>发布到 Azure

若要了解如何部署到 Azure，请参阅[教程：使用 SQL 数据库在 Azure 中生成 ASP.NET 应用](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase)。 此说明适用于 ASP.NET 应用，而不适用于 ASP.NET Core 应用，但步骤均相同。

感谢读完这篇 Razor 页面简介。 [Razor Pages 和 EF Core 入门](xref:data/ef-rp/intro)是本教程的优选后续教程。

## <a name="additional-resources"></a>其他资源

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [本教程的 YouTube 版本](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [上一篇：添加新字段](xref:tutorials/razor-pages/new-field)
