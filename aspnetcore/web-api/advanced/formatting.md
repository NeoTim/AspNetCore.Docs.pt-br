---
title: Formatar dados de resposta na API Web ASP.NET Core
author: ardalis
description: Saiba como formatar dados de resposta na API Web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/formatting
ms.openlocfilehash: b89be93fc33d1eba5c2ad9508adf93fa54014ff8
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606791"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formatar dados de resposta na API Web ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC tem suporte para formatar dados de resposta. Os dados de resposta podem ser formatados usando formatos específicos ou em resposta ao formato solicitado do cliente.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Resultados de ação específica de formato

Alguns tipos de resultado de ação são específicos a um formato específico, como <xref:Microsoft.AspNetCore.Mvc.JsonResult> e <xref:Microsoft.AspNetCore.Mvc.ContentResult>. As ações podem retornar resultados formatados em um formato específico, independentemente das preferências do cliente. Por exemplo, retornando `JsonResult` retorna dados formatados para JSON. Retornar `ContentResult` ou uma cadeia de caracteres retorna dados de cadeia de caracteres formatados com texto sem formatação.

Uma ação não é necessária para retornar qualquer tipo específico. ASP.NET Core dá suporte a qualquer valor de retorno de objeto.  Os resultados de ações que retornam objetos que não são <xref:Microsoft.AspNetCore.Mvc.IActionResult> tipos são serializados usando a <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementação apropriada. Para obter mais informações, consulte <xref:web-api/action-return-types>.

O método auxiliar interno <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> retorna dados formatados em JSON: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

O download de exemplo retorna a lista de autores. Usando as ferramentas de desenvolvedor de navegador F12 ou o [postmaster](https://www.getpostman.com/tools) com o código anterior:

* O cabeçalho de resposta que contém o **tipo de conteúdo:** `application/json; charset=utf-8` é exibido.
* Os cabeçalhos de solicitação são exibidos. Por exemplo, o `Accept` cabeçalho. O `Accept` cabeçalho é ignorado pelo código anterior.

Para retornar dados formatados como texto sem formatação, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> e o auxiliar <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A>:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

No código anterior, o `Content-Type` retornado é `text/plain` . O retorno de uma cadeia de caracteres é entregue `Content-Type` de `text/plain` :

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Para ações com vários tipos de retorno, retorne `IActionResult` . Por exemplo, retornando códigos de status HTTP diferentes com base no resultado das operações executadas.

## <a name="content-negotiation"></a>Negociação de conteúdo

A negociação de conteúdo ocorre quando o cliente especifica um [cabeçalho Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). O formato padrão usado pelo ASP.NET Core é [JSON](https://json.org/). A negociação de conteúdo é:

* Implementado pelo <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .
* Incorporados aos resultados da ação específica do código de status retornados dos métodos auxiliares. Os métodos auxiliares de resultados de ação se baseiam em `ObjectResult` .

Quando um tipo de modelo é retornado, o tipo de retorno é `ObjectResult` .

O seguinte método de ação usa os métodos auxiliares `Ok` e `NotFound`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Por padrão, o ASP.NET Core dá suporte aos `application/json` `text/json` tipos de mídia, e `text/plain` . Ferramentas como o [Fiddler](https://www.telerik.com/fiddler) ou o [postmaster](https://www.getpostman.com/tools) podem definir o `Accept` cabeçalho da solicitação para especificar o formato de retorno. Quando o `Accept` cabeçalho contém um tipo ao qual o servidor dá suporte, esse tipo é retornado. A próxima seção mostra como adicionar outros formatadores.

As ações do controlador podem retornar POCOs (objetos Plain antigos do CLR). Quando um POCO é retornado, o tempo de execução cria automaticamente um `ObjectResult` que encapsula o objeto. O cliente obtém o objeto serializado formatado. Se o objeto que está sendo retornado for `null` , uma `204 No Content` resposta será retornada.

Retornando um tipo de objeto:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

No código anterior, uma solicitação para um alias de autor válido retorna uma `200 OK` resposta com os dados do autor. Uma solicitação para um alias inválido retorna uma `204 No Content` resposta.

### <a name="the-accept-header"></a>O cabeçalho Accept

A *negociação* de conteúdo ocorre quando um `Accept` cabeçalho é exibido na solicitação. Quando uma solicitação contém um cabeçalho Accept, ASP.NET Core:

* Enumera os tipos de mídia no cabeçalho Accept na ordem de preferência.
* Tenta localizar um formatador que pode produzir uma resposta em um dos formatos especificados.

Se nenhum formatador for encontrado que possa atender à solicitação do cliente, ASP.NET Core:

* Retorna `406 Not Acceptable` se <xref:Microsoft.AspNetCore.Mvc.MvcOptions> foi definido ou-
* Tenta encontrar o primeiro formatador que pode produzir uma resposta.

Se nenhum formatador estiver configurado para o formato solicitado, o primeiro formatador que pode formatar o objeto será usado. Se nenhum `Accept` cabeçalho aparecer na solicitação:

* O primeiro formatador que pode manipular o objeto é usado para serializar a resposta.
* Não há nenhuma negociação ocorrendo. O servidor está determinando o formato a ser retornado.

Se o cabeçalho Accept contiver `*/*` , o cabeçalho será ignorado, a menos que `RespectBrowserAcceptHeader` seja definido como true em <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

### <a name="browsers-and-content-negotiation"></a>Navegadores e negociação de conteúdo

Diferentemente dos clientes de API típicos, os navegadores da Web fornecem `Accept` cabeçalhos. O navegador da Web especifica vários formatos, incluindo curingas. Por padrão, quando a estrutura detecta que a solicitação é proveniente de um navegador:

* O `Accept` cabeçalho é ignorado.
* O conteúdo é retornado em JSON, a menos que configurado de outra forma.

Isso fornece uma experiência mais consistente entre navegadores ao consumir APIs.

Para configurar um aplicativo para honrar os cabeçalhos de aceitação do navegador, defina <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> como `true` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Configurar formatadores

Os aplicativos que precisam dar suporte a formatos adicionais podem adicionar os pacotes NuGet apropriados e configurar o suporte. Há formatadores separados para entrada e saída. Os formatadores de entrada são usados pela [Associação de modelo](xref:mvc/models/model-binding). Os formatadores de saída são usados para formatar respostas. Para obter informações sobre como criar um formatador personalizado, consulte [formatadores personalizados](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Adicionar suporte ao formato XML

Os formatadores XML implementados usando o <xref:System.Xml.Serialization.XmlSerializer> são configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

O código anterior serializa os resultados usando `XmlSerializer` .

Ao usar o código anterior, os métodos do controlador retornam o formato apropriado com base no cabeçalho da solicitação `Accept` .

### <a name="configure-systemtextjson-based-formatters"></a>Configurar formatadores com base em System.Text.Json

Os recursos do formatadores com base em `System.Text.Json` podem ser configurados, usando `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

As opções de serialização de saída, em uma base por ação, podem ser configuradas usando `JsonResult` . Por exemplo:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Adicionar suporte ao formato JSON com base em Newtonsoft.Json

Antes do ASP.NET Core 3,0, os formatadores JSON usados por padrão implementaram usando o `Newtonsoft.Json` pacote. No ASP.NET Core 3.0 ou posterior, os formatadores JSON padrão baseiam-se no `System.Text.Json`. O suporte para `Newtonsoft.Json` formatadores e recursos baseados está disponível ao instalar o [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pacote NuGet e configurá-lo no `Startup.ConfigureServices` .

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

No código anterior, a chamada para `AddNewtonsoftJson` configura os seguintes recursos de API Web, MVC e Razor páginas a serem usados `Newtonsoft.Json` :

* Formatadores de entrada e saída que lêem e gravam JSON
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [JSON Patch](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [TempData](xref:fundamentals/app-state#tempdata)

Alguns recursos podem não funcionar bem com `System.Text.Json` formatadores baseados em e exigem uma referência aos `Newtonsoft.Json` formatadores baseados em. Continue usando os `Newtonsoft.Json` formatadores baseados em, se o aplicativo:

* Usa `Newtonsoft.Json` atributos. Por exemplo, `[JsonProperty]` ou `[JsonIgnore]`.
* Personaliza as configurações de serialização.
* O se baseia em recursos que o `Newtonsoft.Json` fornece.
* Configura `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Antes do ASP.NET Core 3.0, `JsonResult.SerializerSettings` aceita uma instância de `JsonSerializerSettings` que é específico para `Newtonsoft.Json`.
* Gera documentação [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).

Os recursos para os `Newtonsoft.Json` formatadores baseados em podem ser configurados usando `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

As opções de serialização de saída, em uma base por ação, podem ser configuradas usando `JsonResult` . Por exemplo:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Adicionar suporte ao formato XML

A formatação XML requer o [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) pacote NuGet.

Os formatadores XML implementados usando o <xref:System.Xml.Serialization.XmlSerializer> são configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

O código anterior serializa os resultados usando `XmlSerializer` .

Ao usar o código anterior, os métodos do controlador devem retornar o formato apropriado com base no `Accept` cabeçalho da solicitação.

::: moniker-end

### <a name="specify-a-format"></a>Especificar um formato

Para restringir os formatos de resposta, aplique o [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro. Assim como a maioria dos [filtros](xref:mvc/controllers/filters), `[Produces]` pode ser aplicado na ação, controlador ou escopo global:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

O [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro anterior:

* Força todas as ações dentro do controlador a retornar respostas formatadas em JSON.
* Se outros formatadores estiverem configurados e o cliente especificar um formato diferente, o JSON será retornado.

Para obter mais informações, consulte [filtros](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Formatadores de caso especiais

Alguns casos especiais são implementados com formatadores internos. Por padrão, os `string` tipos de retorno são formatados como *texto/simples* (*texto/HTML* , se solicitado por meio do `Accept` cabeçalho). Esse comportamento pode ser excluído com a remoção do <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> . Os formatadores são removidos do `ConfigureServices` método. Ações que têm um tipo de retorno de objeto de modelo retornam `204 No Content` ao retornar `null` . Esse comportamento pode ser excluído com a remoção do <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> . O código a seguir remove o `StringOutputFormatter` e o `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Sem o `StringOutputFormatter` , os formatos de formatadores JSON internos `string` retornam tipos de retorno. Se o formatador JSON interno for removido e um formatador XML estiver disponível, o formatador XML formatará os `string` tipos de retorno. Caso contrário, `string` retornarão tipos de retorno `406 Not Acceptable` .

Sem o `HttpNoContentOutputFormatter`, os objetos nulos são formatados com o formatador configurado. Por exemplo:

* O formatador JSON retorna uma resposta com um corpo de `null` .
* O formatador XML retorna um elemento XML vazio com o atributo `xsi:nil="true"` definido.

## <a name="response-format-url-mappings"></a>Mapeamentos de URL de formato de resposta

Os clientes podem solicitar um formato específico como parte da URL, por exemplo:

* Na cadeia de caracteres de consulta ou parte do caminho.
* Usando uma extensão de arquivo específica de formato, como. xml ou. JSON.

O mapeamento do caminho da solicitação deve ser especificado na rota que está sendo usada pela API. Por exemplo:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

A rota anterior permite que o formato solicitado seja especificado como uma extensão de arquivo opcional. O [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) atributo verifica a existência do valor de formato no `RouteData` e mapeia o formato de resposta para o formatador apropriado quando a resposta é criada.

|           Rota        |             Formatador              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    O formatador de saída padrão    |
| `/api/products/5.json` | O formatador JSON (se configurado) |
| `/api/products/5.xml`  | O formatador XML (se configurado)  |
