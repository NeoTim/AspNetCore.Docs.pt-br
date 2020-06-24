---
title: Webassembly ASP.NET Core seguro Blazor
author: guardrex
description: Saiba como proteger Blazor aplicativos WebAssemlby como spas (aplicativos de página única).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/index
ms.openlocfilehash: 877b2bb4b055cca25d64258383cdb39d812e2d6a
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243233"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a>Webassembly ASP.NET Core seguro Blazor

Por [Javier Calvarro Nelson](https://github.com/javiercn)

BlazorOs aplicativos Webassembly são protegidos da mesma maneira que os aplicativos de página única (SPAs). Há várias abordagens para autenticar usuários no SPAs, mas a abordagem mais comum e abrangente é usar uma implementação baseada no [protocolo OAuth 2,0](https://oauth.net/), como o [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteca de autenticação

BlazorO Webassembly dá suporte à autenticação e à autorização de aplicativos usando o OIDC por meio da [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) biblioteca. A biblioteca fornece um conjunto de primitivos para autenticação direta contra back-ends ASP.NET Core. A biblioteca integra ASP.NET Core Identity com suporte à autorização de API criado na parte superior do [ Identity servidor](https://identityserver.io/). A biblioteca pode se autenticar em qualquer provedor de terceiros Identity (IP) que ofereça suporte a OIDC, que são chamadas de provedores de OpenID (op).

O suporte de autenticação no Blazor Webassembly é criado sobre a `oidc-client.js` biblioteca, que é usada para lidar com os detalhes do protocolo de autenticação subjacente.

Existem outras opções para autenticar SPAs, como o uso de cookies SameSite. No entanto, o design de engenharia do Blazor Webassembly é liquidado em OAuth e OIDC como a melhor opção para autenticação em Blazor aplicativos Webassembly. [A autenticação baseada em token](xref:security/anti-request-forgery#token-based-authentication) baseada em [tokens Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) foi escolhida pela [autenticação baseada em cookie](xref:security/anti-request-forgery#cookie-based-authentication) para motivos funcionais e de segurança:

* O uso de um protocolo baseado em token oferece uma área de superfície de ataque menor, pois os tokens não são enviados em todas as solicitações.
* Os pontos de extremidade do servidor não exigem proteção contra [falsificação de solicitação entre sites (CSRF)](xref:security/anti-request-forgery) porque os tokens são enviados explicitamente. Isso permite que você hospede Blazor aplicativos Webassembly juntamente com aplicativos MVC ou Razor páginas.
* Tokens têm permissões mais estreitas do que cookies. Por exemplo, os tokens não podem ser usados para gerenciar a conta de usuário ou alterar a senha de um usuário, a menos que essa funcionalidade seja explicitamente implementada.
* Os tokens têm um tempo de vida curto, uma hora por padrão, o que limita a janela de ataque. Os tokens também podem ser revogados a qualquer momento.
* As JWTs independentes oferecem garantias ao cliente e ao servidor sobre o processo de autenticação. Por exemplo, um cliente tem os meios para detectar e validar que os tokens recebidos são legítimos e foram emitidos como parte de um determinado processo de autenticação. Se um terceiro tentar alternar um token no meio do processo de autenticação, o cliente poderá detectar o token comutado e evitar usá-lo.
* Tokens com OAuth e OIDC não dependem do agente do usuário se comportarem corretamente para garantir que o aplicativo seja seguro.
* Protocolos baseados em token, como OAuth e OIDC, permitem autenticar e autorizar aplicativos hospedados e autônomos com o mesmo conjunto de características de segurança.

## <a name="authentication-process-with-oidc"></a>Processo de autenticação com OIDC

A [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) biblioteca oferece vários primitivos para implementar a autenticação e a autorização usando o OIDC. Em termos gerais, a autenticação funciona da seguinte maneira:

* Quando um usuário anônimo seleciona o botão de logon ou solicita uma página com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo aplicado, o usuário é redirecionado para a página de logon do aplicativo ( `/authentication/login` ).
* Na página de logon, a biblioteca de autenticação se prepara para um redirecionamento para o ponto de extremidade de autorização. O ponto de extremidade de autorização está fora do Blazor aplicativo Webassembly e pode ser hospedado em uma origem separada. O ponto de extremidade é responsável por determinar se o usuário está autenticado e para emitir um ou mais tokens em resposta. A biblioteca de autenticação fornece um retorno de chamada de logon para receber a resposta de autenticação.
  * Se o usuário não estiver autenticado, o usuário será redirecionado para o sistema de autenticação subjacente, que geralmente é ASP.NET Core Identity .
  * Se o usuário já foi autenticado, o ponto de extremidade de autorização gera os tokens apropriados e redireciona o navegador de volta para o ponto de extremidade de retorno de chamada de logon ( `/authentication/login-callback` ).
* Quando o Blazor aplicativo Webassembly carrega o ponto de extremidade de retorno de chamada de logon ( `/authentication/login-callback` ), a resposta de autenticação é processada.
  * Se o processo de autenticação for concluído com êxito, o usuário será autenticado e, opcionalmente, enviado de volta para a URL protegida original solicitada pelo usuário.
  * Se o processo de autenticação falhar por algum motivo, o usuário será enviado à página de falha de logon ( `/authentication/login-failed` ) e um erro será exibido.

## <a name="authorization"></a>Autorização

Em Blazor aplicativos Webassembly, as verificações de autorização podem ser ignoradas porque todo o código do lado do cliente pode ser modificado por usuários. Isso também ocorre com todas as tecnologias de aplicativo do lado do cliente, incluindo estruturas de SPA do JavaScript ou aplicativos nativos em qualquer sistema operacional.

**Sempre execute as verificações de autorização no servidor em qualquer ponto de extremidade da API acessada pelo aplicativo do lado do cliente.**

## <a name="refresh-tokens"></a>Tokens de atualização

Tokens de atualização não podem ser protegidos no lado do cliente em Blazor aplicativos Webassembly. Portanto, os tokens de atualização não devem ser enviados para o aplicativo para uso direto.

Os tokens de atualização podem ser mantidos e usados pelo aplicativo do lado do servidor em uma Blazor solução Webassembly hospedada para acessar APIs de terceiros. Para obter mais informações, consulte <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.

## <a name="implementation-guidance"></a>Diretrizes de implementação

Os artigos nesta *visão geral* fornecem informações sobre como autenticar usuários em Blazor aplicativos Webassembly em relação a provedores específicos.

BlazorAplicativos de Webassembly autônomos:

* [Diretrizes gerais para provedores de OIDC e a biblioteca de autenticação Webassembly](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Contas da Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [AAD (Azure Active Directory)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

BlazorAplicativos Webassembly hospedados:

* [AAD (Azure Active Directory)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [IdentityServidor](xref:blazor/security/webassembly/hosted-with-identity-server)

Para obter mais diretrizes sobre a configuração, consulte <xref:blazor/security/webassembly/additional-scenarios> .