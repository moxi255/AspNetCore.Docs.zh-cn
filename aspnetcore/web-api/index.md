---
title: 使用 ASP.NET Core 创建 Web API
author: scottaddie
description: 了解在 ASP.NET Core 中创建 Web API 的基础知识。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 05/07/2019
uid: web-api/index
ms.openlocfilehash: 593fd33babc81cddfc4db2150a37e5ec3bc1a0be
ms.sourcegitcommit: a3926eae3f687013027a2828830c12a89add701f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2019
ms.locfileid: "65450833"
---
# <a name="create-web-apis-with-aspnet-core"></a>使用 ASP.NET Core 创建 Web API

作者：[Scott Addie](https://github.com/scottaddie) 和 [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core 支持使用 C# 创建 RESTful 服务，也称为 Web API。 若要处理请求，Web API 使用控制器。 Web API 中的*控制器*是派生自 `ControllerBase` 的类。 本文介绍了如何使用控制器处理 API 请求。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)。 （[下载方法](xref:index#how-to-download-a-sample)）。

## <a name="controllerbase-class"></a>ControllerBase 类

Web API 有一个或多个派生自 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 的控制器类。 例如，Web API 项目模板创建一个 Values 控制器：

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=3)]

不要通过从 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类派生来创建 Web API 控制器。 `Controller` 派生自 `ControllerBase`，并添加对视图的支持，因此它用于处理 Web 页面，而不是 Web API 请求。  此规则有一个例外：如果打算为视图和 API 使用相同的控制器，则从 `Controller` 派生控制器。

`ControllerBase` 类提供了很多用于处理 HTTP 请求的属性和方法。 例如，`ControllerBase.CreatedAtAction` 返回 201 状态代码：

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=8-9)]

 下面是 `ControllerBase` 提供的方法的更多示例。

|方法  |说明  |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| 返回 400 状态代码。|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> |返回 404 状态代码。|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|返回文件。|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|调用[模型绑定](xref:mvc/models/model-binding)。|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|调用[模型验证](xref:mvc/models/validation)。|

有关可用方法和属性的列表，请参阅 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>。

## <a name="attributes"></a>特性

<xref:Microsoft.AspNetCore.Mvc> 命名空间提供可用于配置 Web API 控制器的行为和操作方法的属性。 以下示例使用这些属性来指定接受的 HTTP 方法和返回的状态代码：

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

以下是可用属性的更多示例。

|特性|说明|
|---------|-----|
|[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |指定控制器或操作的 URL 模式。|
|[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |指定要包含的前缀和属性，以进行模型绑定。|
|[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |标识支持 HTTP GET 方法的操作。|
|[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|指定某个操作接受的数据类型。|
|[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|指定某个操作返回的数据类型。|

有关包含可用属性的列表，请参阅 <xref:Microsoft.AspNetCore.Mvc> 命名空间。

## <a name="apicontroller-attribute"></a>ApiController 属性

[[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性可应用于控制器类，以启用 API 特定的行为：

* [属性路由要求](#attribute-routing-requirement)
* [自动 HTTP 400 响应](#automatic-http-400-responses)
* [绑定源参数推理](#binding-source-parameter-inference)
* [Multipart/form-data 请求推理](#multipartform-data-request-inference)
* [错误状态代码的问题详细信息](#problem-details-for-error-status-codes)

这些功能需要[兼容性版本](<xref:mvc/compatibility-version>)为 2.1 或更高版本。

### <a name="apicontroller-on-specific-controllers"></a>特定控制器上的 ApiController

`[ApiController]` 属性可应用于特定控制器，如项目模板中的以下示例所示：

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=2)]

### <a name="apicontroller-on-multiple-controllers"></a>多个控制器上的 ApiController

在多个控制器上使用该属性的一种方法是创建通过 `[ApiController]` 属性批注的自定义基控制器类。 下面这个示例显示了自定义基类和从其派生的控制器：

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

### <a name="apicontroller-on-an-assembly"></a>程序集上的 ApiController

如果将[兼容性版本](<xref:mvc/compatibility-version>)设置为 2.2 或更高版本，则 `[ApiController]` 属性可应用于程序集。 以这种方式进行注释，会将 web API 行为应用到程序集中的所有控制器。 无法针对单个控制器执行选择退出操作。 将程序集级别的属性应用于以下示例所示的 `Startup` 类：

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

## <a name="attribute-routing-requirement"></a>特性路由要求

`ApiController` 属性使属性路由成为要求。 例如:

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=1)]

不能通过 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> 定义的[传统路由](xref:mvc/controllers/routing#conventional-routing)或通过 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> 访问操作。

## <a name="automatic-http-400-responses"></a>自动 HTTP 400 响应

`ApiController` 属性使模型验证错误自动触发 HTTP 400 响应。 因此，操作方法中不需要以下代码：

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

### <a name="default-badrequest-response"></a>默认 BadRequest 响应 

使用 2.2 或更高版本的兼容性版本时，HTTP 400 响应的默认响应类型为 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>。 `ValidationProblemDetails` 类型符合 [RFC 7807 规范](https://tools.ietf.org/html/rfc7807)。

若要将默认响应更改为 <xref:Microsoft.AspNetCore.Mvc.SerializableError>，请在 `Startup.ConfigureServices` 中将 `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` 属性设置为 `true`，如以下示例所示：

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,9)]

### <a name="customize-badrequest-response"></a>自定义 BadRequest 响应

若要自定义验证错误引发的响应，请使用 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>。 将以下突出显示的代码添加到 `services.AddMvc().SetCompatibilityVersion` 后面：

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=3-20)]

### <a name="log-automatic-400-responses"></a>记录自动 400 响应

请参阅[如何对模型验证错误记录自动 400 响应 (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157)。

### <a name="disable-automatic-400"></a>禁用自动 400

若要禁用自动 400 行为，请将 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> 属性设置为 `true`。 将 `Startup.ConfigureServices` 中以下突出显示的代码添加到 `services.AddMvc().SetCompatibilityVersion` 后面：

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

## <a name="binding-source-parameter-inference"></a>绑定源参数推理

绑定源特性定义可找到操作参数值的位置。 存在以下绑定源特性：

|特性|绑定源 |
|---------|---------|
|[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | 请求正文 |
|[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | 请求正文中的表单数据 |
|[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | 请求标头 |
|[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | 请求查询字符串参数 |
|[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | 当前请求中的路由数据 |
|[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | 作为操作参数插入的请求服务 |

> [!WARNING]
> 当值可能包含 `%2f`（即 `/`）时，请勿使用 `[FromRoute]`。 `%2f` 不会转换为 `/`（非转移形式）。 如果值可能包含 `%2f`，则使用 `[FromQuery]`。

如果没有 `[ApiController]` 属性或诸如 `[FromQuery]` 的绑定源属性，ASP.NET Core 运行时会尝试使用复杂对象模型绑定器。 复杂对象模型绑定器按已定义顺序从值提供程序拉取数据。

在下面的示例中，`[FromQuery]` 特性指示 `discontinuedOnly` 参数值在请求 URL 的查询字符串中提供：

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

`[ApiController]` 属性将推理规则应用于操作参数的默认数据源。 借助这些规则，无需通过将属性应用于操作参数来手动识别绑定源。 绑定源推理规则的行为如下：

* `[FromBody]` 针对复杂类型参数进行推断。 `[FromBody]` 不适用于具有特殊含义的任何复杂的内置类型，如 <xref:Microsoft.AspNetCore.Http.IFormCollection> 和 <xref:System.Threading.CancellationToken>。 绑定源推理代码将忽略这些特殊类型。 
* `[FromForm]` 针对 <xref:Microsoft.AspNetCore.Http.IFormFile> 和 <xref:Microsoft.AspNetCore.Http.IFormFileCollection> 类型的操作参数进行推断。 该特性不针对任何简单类型或用户定义类型进行推断。
* `[FromRoute]` 针对与路由模板中的参数相匹配的任何操作参数名称进行推断。 当多个路由与一个操作参数匹配时，任何路由值都视为 `[FromRoute]`。
* `[FromQuery]` 针对任何其他操作参数进行推断。

### <a name="frombody-inference-notes"></a>FromBody 推理说明

对于简单类型（例如 `string` 或 `int`），推断不出 `[FromBody]`。 因此，如果需要该功能，对于简单类型，应使用 `[FromBody]` 属性。

当操作拥有多个从请求正文中绑定的参数时，将会引发异常。 例如，以下所有操作方法签名都会导致异常：

* `[FromBody]` 对两者进行推断，因为它们是复杂类型。

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* `[FromBody]` 对一个进行归属，对另一个进行推断，因为它是复杂类型。

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* `[FromBody]` 对两者进行归属。

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

> [!NOTE]
> 在 ASP.NET Core 2.1 中，集合类型参数（如列表和数组）被不正确地推断为 `[FromQuery]`。 若要从请求正文中绑定参数，应对这些参数使用 `[FromBody]` 属性。 此行为在 ASP.NET Core 2.2 或更高版本中得到了更正，其中集合类型参数默认被推断为从正文中绑定。

### <a name="disable-inference-rules"></a>禁用推理规则

若要禁用绑定源推理，请将 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> 设置为 `true`。 在 `Startup.ConfigureServices` 中的 `services.AddMvc().SetCompatibilityVersion` 后添加下列代码：

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

## <a name="multipartform-data-request-inference"></a>Multipart/form-data 请求推理

使用 [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) 属性批注操作参数时，`[ApiController]` 属性应用推理规则：将推断 `multipart/form-data` 请求内容类型。

若要禁用默认行为，请在 `Startup.ConfigureServices` 中将 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> 设置为 `true`，如以下示例所示：

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

## <a name="problem-details-for-error-status-codes"></a>错误状态代码的问题详细信息

当兼容性版本为 2.2 或更高版本时，MVC 会将错误结果（状态代码为 400 或更高的结果）转换为状态代码为 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 的结果。 `ProblemDetails` 类型基于 [RFC 7807 规范](https://tools.ietf.org/html/rfc7807)，用于提供 HTTP 响应中计算机可读的错误详细信息。

考虑在控制器操作中使用以下代码：

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

`NotFound` 的 HTTP 响应具有 404 状态代码和 `ProblemDetails` 正文。 例如:

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a>自定义 ProblemDetails 响应

使用 `ClientErrorMapping` 属性配置 `ProblemDetails` 响应的内容。 例如，以下代码会更新 404 响应的 `type` 属性：

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

### <a name="disable-problemdetails-response"></a>禁用 ProblemDetails 响应

当 `SuppressMapClientErrors` 属性设置为 `true` 时，会禁用 `ProblemDetails` 的自动创建。 在 `Startup.ConfigureServices` 中添加下列代码：

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

## <a name="additional-resources"></a>其他资源 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
