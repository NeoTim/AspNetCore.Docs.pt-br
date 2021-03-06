---
title: Migrar de ASP.NET Web API para ASP.NET Core
author: ardalis
description: Saiba como migrar uma implementação de API da Web da API Web do ASP.NET 4. x para ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: e3e46f8050ba87c3108885341675c9d2a2cb7847
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635158"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Migrar de ASP.NET Web API para ASP.NET Core

De [Scott Addie](https://twitter.com/scott_addie) e [Steve Smith](https://ardalis.com/)

Uma API Web do ASP.NET 4. x é um serviço HTTP que atinge uma ampla variedade de clientes, incluindo navegadores e dispositivos móveis. ASP.NET Core combina os modelos de aplicativo de API Web e MVC do ASP.NET 4. x em um único modelo de programação conhecido como ASP.NET Core MVC. Este artigo demonstra as etapas necessárias para migrar da API Web do ASP.NET 4. x para ASP.NET Core MVC.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([como baixar](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Examine o projeto de API Web do ASP.NET 4. x

Este artigo usa o projeto *ProductsApp* criado em [introdução com ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). Nesse projeto, um projeto de API da Web básico do ASP.NET 4. x é configurado da seguinte maneira.

No *global.asax.cs*, é feita uma chamada para `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

A `WebApiConfig` classe é encontrada na pasta *App_Start* e tem um método estático `Register` :

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

A classe anterior:

* Configura o [Roteamento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), embora não esteja realmente sendo usado.
* Configura a tabela de roteamento.
O código de exemplo espera que as URLs correspondam ao formato `/api/{controller}/{id}` , `{id}` sendo opcional.

As seções a seguir demonstram a migração do projeto de API Web para ASP.NET Core MVC.

## <a name="create-the-destination-project"></a>Criar o projeto de destino

Crie uma nova solução em branco no Visual Studio e adicione o projeto de API Web ASP.NET 4. x para migrar:

1. No menu **Arquivo**, selecione **Novo** > **Projeto**.
1. Selecione o modelo de **solução em branco** e selecione **Avançar**.
1. Nomeie a solução *WebAPIMigration*. Selecione **Criar**.
1. Adicione o projeto *ProductsApp* existente à solução.

Adicione um novo projeto de API para migrar para o:

1. Adicione um novo projeto de **aplicativo Web ASP.NET Core** à solução.
1. Na caixa de diálogo **configurar seu novo projeto** , nomeie o projeto *ProductsCore*e selecione **criar**.
1. Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados. Selecione o modelo de projeto **API** e, em seguida, **Criar**.
1. Remova os arquivos de exemplo *WeatherForecast.cs* e *Controllers/WeatherForecastController. cs* do novo projeto *ProductsCore* .

A solução agora contém dois projetos. As seções a seguir explicam como migrar o conteúdo do projeto *ProductsApp* para o projeto *ProductsCore* .

## <a name="migrate-configuration"></a>Migrar configuração

ASP.NET Core não usa a pasta *App_Start* ou o arquivo *global. asax* . Além disso, o arquivo de *web.config* é adicionado no momento da publicação.

A classe `Startup`:

* Substitui *global. asax*.
* Manipula todas as tarefas de inicialização do aplicativo.

Para obter mais informações, consulte <xref:fundamentals/startup>.

## <a name="migrate-models-and-controllers"></a>Migrar modelos e controladores

O código a seguir mostra o `ProductsController` a ser atualizado para ASP.NET Core:

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

Atualize o `ProductsController` para ASP.NET Core:

1. Copie os *controladores/ProductsController. cs* e a pasta *modelos* do projeto original para o novo.
1. Altere o namespace raiz dos arquivos copiados para `ProductsCore` .
1. Atualize a `using ProductsApp.Models;` instrução para `using ProductsCore.Models;` .

Os seguintes componentes não existem no ASP.NET Core:

* Classe `ApiController`
* Namespace `System.Web.Http`
* Interface `IHttpActionResult`

Faça as seguintes alterações:

1. Alterar `ApiController` para <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Adicione `using Microsoft.AspNetCore.Mvc;` para resolver a `ControllerBase` referência.
1. Excluir `using System.Web.Http;`.
1. Altere o `GetProduct` tipo de retorno da ação de `IHttpActionResult` para `ActionResult<Product>` .
1. Simplifique a `GetProduct` instrução da ação `return` para o seguinte:

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Configurar o roteamento

O modelo de projeto de *API* ASP.NET Core inclui a configuração de roteamento de ponto de extremidade no código gerado.

O seguinte <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> e as <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> chamadas:

* Registre a correspondência de rota e a execução do ponto de extremidade no pipeline de [middleware](xref:fundamentals/middleware/index) .
* Substitua o arquivo de *App_Start/webapiconfig.cs* do projeto *ProductsApp* .

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

Configure o roteamento da seguinte maneira:

1. Marque a `ProductsController` classe com os seguintes atributos:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    O [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atributo anterior configura o padrão de roteamento de atributo do controlador. O [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo torna o atributo roteando um requisito para todas as ações neste controlador.

    O roteamento de atributos dá suporte a tokens, como `[controller]` e `[action]` . No tempo de execução, cada token é substituído pelo nome do controlador ou da ação, respectivamente, ao qual o atributo foi aplicado. Os tokens:
    * Reduza o número de cadeias de caracteres mágicos no projeto.
    * Certifique-se de que as rotas permaneçam sincronizadas com os controladores e ações correspondentes quando refatoração de renomeação automática forem aplicadas.
1. Habilite solicitações HTTP Get para as `ProductController` ações:
    * Aplique o [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo à `GetAllProducts` ação.
    * Aplique o `[HttpGet("{id}")]` atributo à `GetProduct` ação.

Execute o projeto migrado e navegue até `/api/products` . Uma lista completa de três produtos é exibida. Navegue até `/api/products/1`. O primeiro produto é exibido.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Examine o projeto de API Web do ASP.NET 4. x

Este artigo usa o projeto *ProductsApp* criado em [introdução com ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). Nesse projeto, um projeto de API da Web básico do ASP.NET 4. x é configurado da seguinte maneira.

No *global.asax.cs*, é feita uma chamada para `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

A `WebApiConfig` classe é encontrada na pasta *App_Start* e tem um método estático `Register` :

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

Essa classe configura o [Roteamento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), embora ele não esteja realmente sendo usado no projeto. Ele também configura a tabela de roteamento, que é usada pelo ASP.NET Web API. Nesse caso, a API Web ASP.NET 4. x espera que as URLs correspondam ao formato `/api/{controller}/{id}` , `{id}` sendo opcional.

As seções a seguir demonstram a migração do projeto de API Web para ASP.NET Core MVC.

## <a name="create-the-destination-project"></a>Criar o projeto de destino

Conclua as seguintes etapas no Visual Studio:

* Vá para **arquivo**  >  **novo**  >  **projeto**  >  **outros tipos de projeto**  >  **soluções do Visual Studio**. Selecione **solução em branco**e nomeie a solução *WebAPIMigration*. Clique no botão **OK**.
* Adicione o projeto *ProductsApp* existente à solução.
* Adicione um novo projeto de **aplicativo Web ASP.NET Core** à solução. Selecione a estrutura de destino **.NET Core** na lista suspensa e selecione o modelo projeto de **API** . Nomeie o projeto *ProductsCore*e clique no botão **OK** .

A solução agora contém dois projetos. As seções a seguir explicam como migrar o conteúdo do projeto *ProductsApp* para o projeto *ProductsCore* .

## <a name="migrate-configuration"></a>Migrar configuração

ASP.NET Core não usa:

* *App_Start* pasta ou o arquivo *global. asax*
* *web.config* arquivo é adicionado no momento da publicação.

A classe `Startup`:

* Substitui *global. asax*.
* Manipula todas as tarefas de inicialização do aplicativo.

Para obter mais informações, consulte <xref:fundamentals/startup>.

No ASP.NET Core MVC, o roteamento de atributo é incluído por padrão quando <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> é chamado em `Startup.Configure` . A chamada a seguir `UseMvc` substitui o arquivo de *App_Start/webapiconfig.cs* do projeto *ProductsApp* :

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a>Migrar modelos e controladores

O código a seguir mostra a `ProductsController` atualização para ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]

Atualize o `ProductsController` para ASP.NET Core:

1. Copie os *controladores/ProductsController. cs* do projeto original para o novo.
1. Copie a pasta *modelos* do projeto original para o novo.
1. Altere o namespace raiz dos arquivos copiados para `ProductsCore` .
1. Atualize a `using ProductsApp.Models;` instrução para `using ProductsCore.Models;` .

Os seguintes componentes não existem no ASP.NET Core:

* Classe `ApiController`
* Namespace `System.Web.Http`
* Interface `IHttpActionResult`

Faça as seguintes alterações:

1. Alterar `ApiController` para <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Adicione `using Microsoft.AspNetCore.Mvc;` para resolver a `ControllerBase` referência.
1. Excluir `using System.Web.Http;`.
1. Altere o `GetProduct` tipo de retorno da ação de `IHttpActionResult` para `ActionResult<Product>` .
1. Simplifique a `GetProduct` instrução da ação `return` para o seguinte:

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Configurar o roteamento

Configure o roteamento da seguinte maneira:

1. Marque a `ProductsController` classe com os seguintes atributos:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    O [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atributo anterior configura o padrão de roteamento de atributo do controlador. O [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo torna o atributo roteando um requisito para todas as ações neste controlador.

    O roteamento de atributos dá suporte a tokens, como `[controller]` e `[action]` . No tempo de execução, cada token é substituído pelo nome do controlador ou da ação, respectivamente, ao qual o atributo foi aplicado. Os tokens reduzem o número de cadeias de caracteres mágicos no projeto. Os tokens também garantem que as rotas permaneçam sincronizadas com os controladores e ações correspondentes quando refatoração de renomeação automática são aplicadas.
1. Defina o modo de compatibilidade do projeto para ASP.NET Core 2,2:

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    A alteração anterior:

    * É necessário para usar o `[ApiController]` atributo no nível do controlador.
    * Optamos pelos comportamentos potencialmente em potencial introduzidos no ASP.NET Core 2,2.
1. Habilite solicitações HTTP Get para as `ProductController` ações:
    * Aplique o [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo à `GetAllProducts` ação.
    * Aplique o `[HttpGet("{id}")]` atributo à `GetProduct` ação.

Execute o projeto migrado e navegue até `/api/products` . Uma lista completa de três produtos é exibida. Navegue até `/api/products/1`. O primeiro produto é exibido.

## <a name="compatibility-shim"></a>Correção de compatibilidade

A biblioteca [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) fornece um Shim de compatibilidade para mover projetos de API Web ASP.NET 4. x para ASP.NET Core. O Shim de compatibilidade estende ASP.NET Core para dar suporte a um número de convenções da API Web 2 do ASP.NET 4. x. O exemplo de porta anteriormente neste documento é básico o bastante para que o Shim de compatibilidade seja desnecessário. Para projetos maiores, o uso do Shim de compatibilidade pode ser útil para a ponte temporária da lacuna da API entre ASP.NET Core e a API Web 2 do ASP.NET 4. x.

O Shim da compatibilidade da API Web deve ser usado como uma medida temporária para dar suporte à migração de projetos de API Web grandes ASP.NET 4. x para ASP.NET Core. Ao longo do tempo, os projetos devem ser atualizados para usar padrões de ASP.NET Core em vez de depender do Shim de compatibilidade.

Os recursos de compatibilidade incluídos no `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluem:

* Adiciona um `ApiController` tipo para que os tipos base dos controladores não precisem ser atualizados.
* Habilita a associação de modelo de estilo de API Web. ASP.NET Core funções de associação de modelo MVC da mesma forma que o do ASP.NET 4. x MVC 5, por padrão. O Shim de compatibilidade altera a associação de modelo para ser mais semelhante às convenções de associação de modelo da API Web 2 do ASP.NET 4. x. Por exemplo, tipos complexos são associados automaticamente do corpo da solicitação.
* Estende a associação de modelo para que as ações do controlador possam ter parâmetros do tipo `HttpRequestMessage` .
* Adiciona os formatadores de mensagem, permitindo que as ações retornem os resultados do tipo `HttpResponseMessage` .
* Adiciona métodos de resposta adicionais que as ações da API Web 2 podem ter usado para atender às respostas:
  * `HttpResponseMessage` geradores
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Métodos de resultado da ação:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Adiciona uma instância do `IContentNegotiator` ao contêiner de injeção de dependência (di) do aplicativo e disponibiliza os tipos relacionados à negociação de conteúdo de [Microsoft. AspNet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/). Exemplos desses tipos incluem `DefaultContentNegotiator` e `MediaTypeFormatter` .

Para usar o Shim de compatibilidade:

1. Instale o pacote NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .
1. Registre os serviços da correção de compatibilidade com o contêiner de DI do aplicativo chamando `services.AddMvc().AddWebApiConventions()` em `Startup.ConfigureServices` .
1. Defina rotas específicas da API Web usando `MapWebApiRoute` no `IRouteBuilder` na chamada do aplicativo `IApplicationBuilder.UseMvc` .

## <a name="additional-resources"></a>Recursos adicionais

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
