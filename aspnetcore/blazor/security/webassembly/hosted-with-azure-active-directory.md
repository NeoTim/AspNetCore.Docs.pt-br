---
title: Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com Azure Active Directory
author: guardrex
description: Saiba como proteger um aplicativo ASP.NET Core Blazor WebAssembly hospedado com Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/08/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: e6f514793a2efde120f70ac58f4ad4be7516ada7
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900826"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Proteger um Blazor WebAssembly aplicativo ASP.NET Core hospedado com Azure Active Directory

Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

Este artigo descreve como criar um [ Blazor WebAssembly aplicativo hospedado](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação.

## <a name="register-apps-in-aad-and-create-solution"></a>Registrar aplicativos no AAD e criar a solução

### <a name="create-a-tenant"></a>Criar um locatário

Siga as orientações em [início rápido: configurar um locatário](/azure/active-directory/develop/quickstart-create-new-tenant) para criar um locatário no AAD.

### <a name="register-a-server-api-app"></a>Registrar um aplicativo de API do servidor

Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo de API do servidor* e, em seguida, faça o seguinte:

1. Em **Azure Active Directory**  >  **registros de aplicativo**, selecione **novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, ** Blazor Server AAD**).
1. Escolha um **tipo de conta com suporte**. Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.
1. O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.
1. Desmarque a caixa de seleção **permissões**  >  **conceder consentimento de administrador às permissões OpenID e offline_access** .
1. Selecione **Registrar**.

Registre as seguintes informações:

* *Aplicativo de API do servidor* ID do aplicativo (cliente) (por exemplo, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )
* ID do diretório (locatário) (por exemplo, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )
* Domínio principal/Publicador/locatário do AAD (por exemplo, `contoso.onmicrosoft.com` ): o domínio está disponível como o **domínio do Publicador** na folha de **identidade visual** do portal do Azure para o aplicativo registrado.

Em **permissões de API**, remova a permissão **Microsoft Graph**  >  **User. Read** , pois o aplicativo não requer acesso de entrada ou perfil de usuário.

No **expor uma API**:

1. Selecione **Adicionar um escopo**.
1. Selecione **Salvar e continuar**.
1. Forneça um **nome de escopo** (por exemplo, `API.Access` ).
1. Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API` ).
1. Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.` ).
1. Confirme se o **estado** está definido como **habilitado**.
1. Selecione **Adicionar escopo**.

Registre as seguintes informações:

* URI da ID do aplicativo (por exemplo,, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ou o valor personalizado que você fornece)
* Nome do escopo (por exemplo, `API.Access` )

### <a name="register-a-client-app"></a>Registrar um aplicativo cliente

Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *`Client`* aplicativo e, em seguida, faça o seguinte:

::: moniker range=">= aspnetcore-5.0"

1. Em **Azure Active Directory** > **registros de aplicativo**, selecione **novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, ** Blazor cliente AAD**).
1. Escolha um **tipo de conta com suporte**. Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.
1. Defina a lista suspensa **URI de redirecionamento** para o **aplicativo de página única (Spa)** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` . A porta padrão para um aplicativo em execução no Kestrel é 5001. Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo. Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas *`Server`* Propriedades do aplicativo no painel de **depuração** . Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento. Um comentário é exibido na seção [criar o aplicativo](#create-the-app) para lembrar IIS Express usuários para atualizar o URI de redirecionamento.
1. Desmarque a caixa de seleção **permissões** > **conceder consentimento de administrador às permissões OpenID e offline_access** .
1. Selecione **Registrar**.

Registre a *`Client`* ID do aplicativo (cliente) de aplicativos (por exemplo, `4369008b-21fa-427c-abaa-9b53bf58e538` ).

Em configurações de plataforma de **autenticação** > **Platform configurations** > **, um aplicativo de página única (Spa)**:

1. Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.
1. Para **concessão implícita**, verifique se as caixas de seleção para **tokens de acesso** e **tokens de ID** **não** estão selecionadas.
1. Os padrões restantes para o aplicativo são aceitáveis para essa experiência.
1. Selecione o botão **Salvar**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Em **Azure Active Directory** > **registros de aplicativo**, selecione **novo registro**.
1. Forneça um **nome** para o aplicativo (por exemplo, ** Blazor cliente AAD**).
1. Escolha um **tipo de conta com suporte**. Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.
1. Deixe a lista suspensa **URI de redirecionamento** definida como **Web** e forneça o seguinte URI de redirecionamento: `https://localhost:{PORT}/authentication/login-callback` . A porta padrão para um aplicativo em execução no Kestrel é 5001. Se o aplicativo for executado em uma porta Kestrel diferente, use a porta do aplicativo. Por IIS Express, a porta gerada aleatoriamente para o aplicativo pode ser encontrada nas *`Server`* Propriedades do aplicativo no painel de **depuração** . Como o aplicativo não existe neste ponto e a porta de IIS Express não é conhecida, retorne a essa etapa depois que o aplicativo for criado e atualize o URI de redirecionamento. Um comentário é exibido na seção [criar o aplicativo](#create-the-app) para lembrar IIS Express usuários para atualizar o URI de redirecionamento.
1. Desmarque a caixa de seleção **permissões** > **conceder consentimento de administrador às permissões OpenID e offline_access** .
1. Selecione **Registrar**.

Registre a *`Client`* ID do aplicativo (cliente) de aplicativos (por exemplo, `4369008b-21fa-427c-abaa-9b53bf58e538` ).

Em **Authentication** > **configurações da plataforma** de autenticação > **Web**:

1. Confirme se o **URI de redirecionamento** do `https://localhost:{PORT}/authentication/login-callback` está presente.
1. Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.
1. Os padrões restantes para o aplicativo são aceitáveis para essa experiência.
1. Selecione o botão **Salvar**.

::: moniker-end

Em **permissões de API**:

1. Confirme se o aplicativo tem a permissão **Microsoft Graph**  >  **User. Read** .
1. Selecione **Adicionar uma permissão** seguida por **minhas APIs**.
1. Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, ** Blazor Server AAD**).
1. Abra a lista de **APIs** .
1. Habilite o acesso à API (por exemplo, `API.Access` ).
1. Escolha **Adicionar permissões**.
1. Selecione o botão **conceder consentimento de administrador para {nome do locatário}** . Selecione **Sim** para confirmar.

### <a name="create-the-app"></a>Criar o aplicativo

Em uma pasta vazia, substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| Espaço reservado                  | Nome do portal do Azure                                     | Exemplo                                      |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                               |
| `{CLIENT APP CLIENT ID}`     | ID do aplicativo (cliente) para o *`Client`* aplicativo        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`            | Nome do escopo                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}` | ID do aplicativo (cliente) para o *aplicativo de API do servidor*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`    | URI da ID de Aplicativo                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | Domínio primário/Publicador/locatário                       | `contoso.onmicrosoft.com`                    |
| `{TENANT ID}`                | ID do diretório (locatário)                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`       |

A localização de saída especificada com a opção `-o|--output` criará uma pasta de projeto se ela não existir e se tornará parte do nome do aplicativo.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Uma alteração de configuração pode ser necessária ao usar um locatário do Azure com um domínio do Publicador não verificado, que é descrito na seção [configurações do aplicativo](#app-settings) .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Uma alteração de configuração pode ser necessária ao usar um locatário do Azure com um domínio do Publicador não verificado, que é descrito na seção [escopos de token de acesso](#access-token-scopes) .

::: moniker-end

> [!NOTE]
> No portal do Azure, o *`Client`* **URI de redirecionamento** de configuração de plataforma do aplicativo é configurado para a porta 5001 para aplicativos executados no servidor Kestrel com as configurações padrão.
>
> Se o *`Client`* aplicativo for executado em uma porta IIS Express aleatória, a porta do aplicativo poderá ser encontrada nas propriedades *do aplicativo de API do servidor* no painel de **depuração** .
>
> Se a porta não foi configurada anteriormente com a *`Client`* porta conhecida do aplicativo, retorne ao *`Client`* registro do aplicativo na portal do Azure e atualize o URI de redirecionamento com a porta correta.

## <a name="server-app-configuration"></a>*`Server`* configuração do aplicativo

*Esta seção pertence ao aplicativo da solução **`Server`** .*

### <a name="authentication-package"></a>Pacote de autenticação

::: moniker range=">= aspnetcore-5.0"

O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web com a Identity plataforma Microsoft é fornecido pelos seguintes pacotes:

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em NuGet.org.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) pacote:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).

::: moniker-end

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

::: moniker range=">= aspnetcore-5.0"

O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão. O <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> método configura os serviços para proteger a API Web com a Identity plataforma Microsoft v 2.0. Esse método espera uma `AzureAd` seção na configuração do aplicativo com as configurações necessárias para inicializar as opções de autenticação.

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

O `AddAuthentication` método configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão. O <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> método configura os parâmetros específicos no manipulador de portador JWT necessários para validar tokens emitidos pelo Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> e <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Verifique se:

* O aplicativo tenta analisar e validar tokens em solicitações de entrada.
* Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a>Usuário. Identity . Nomes

Por padrão, a *`Server`* API do aplicativo é preenchida `User.Identity.Name` com o valor do `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` tipo de declaração (por exemplo, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).

Para configurar o aplicativo para receber o valor do `name` tipo de declaração, configure o <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> do <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> no `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Configurações do aplicativo

::: moniker range=">= aspnetcore-5.0"

O `appsettings.json` arquivo contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

Exemplo:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

O `appsettings.json` arquivo contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

Exemplo:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a>Controlador WeatherForecast

O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo aplicado ao controlador. É **importante** entender que:

* O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo nesse controlador de API é a única coisa que protege essa API contra o acesso não autorizado.
* O [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo usado no Blazor WebAssembly aplicativo serve apenas como uma dica para o aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>*`Client`* configuração do aplicativo

*Esta seção pertence ao aplicativo da solução **`Client`** .*

### <a name="authentication-package"></a>Pacote de autenticação

Quando um aplicativo é criado para usar contas corporativas ou de estudante ( `SingleOrg` ), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ). O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.

Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Para o espaço reservado `{VERSION}` , a versão estável mais recente do pacote que corresponde à versão de estrutura compartilhada do aplicativo pode ser encontrada no **histórico de versão** do pacote em [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).

O [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote adiciona transitivamente o [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pacote ao aplicativo.

### <a name="authentication-service-support"></a>Suporte ao serviço de autenticação

O suporte para <xref:System.Net.Http.HttpClient> instâncias é adicionado que inclui tokens de acesso ao fazer solicitações ao projeto do servidor.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample.ServerAPI` ).

O suporte para autenticação de usuários é registrado no contêiner de serviço com o <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método de extensão fornecido pelo [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pacote. Esse método configura os serviços necessários para que o aplicativo interaja com o Identity provedor (IP).

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

O <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> método aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo. Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.

A configuração é fornecida pelo `wwwroot/appsettings.json` arquivo:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Exemplo:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>Escopos de token de acesso

Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:

* Incluído por padrão na solicitação de entrada.
* Usado para provisionar um token de acesso imediatamente após a autenticação.

Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory. Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Especifique escopos adicionais com `AdditionalScopesToConsent` :

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

Para obter mais informações, consulte as seguintes seções do artigo *cenários adicionais* :

* [Solicitar tokens de acesso adicionais](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anexar tokens a solicitações de saída](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a>Modo de logon

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a>Arquivo de importações

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Componente do aplicativo

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Componente de autenticação

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Executar o aplicativo

Execute o aplicativo no projeto do servidor. Ao usar o Visual Studio, seja:

* Defina a lista suspensa **projetos de inicialização** na barra de ferramentas para o aplicativo de *API do servidor* e selecione o botão **executar** .
* Selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/security/webassembly/additional-scenarios>
* [Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Documentação da plataforma de identidade da Microsoft](/azure/active-directory/develop/)
