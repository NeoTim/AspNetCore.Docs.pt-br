---
title: Esquemas de política no ASP.NET Core
author: rick-anderson
description: Os esquemas de política de autenticação facilitam a tarefa de ter um único esquema de autenticação lógica
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 60ac9914ef811a705c61ab3b2bec61643acc6ec0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634976"
---
# <a name="policy-schemes-in-aspnet-core"></a>Esquemas de política no ASP.NET Core

Os esquemas de diretiva de autenticação facilitam a utilização de um único esquema de autenticação lógica, o que pode usar várias abordagens. Por exemplo, um esquema de política pode usar a autenticação do Google para desafios e a cookie autenticação para todo o resto. Os esquemas de política de autenticação o fazem:

* É fácil encaminhar qualquer ação de autenticação para outro esquema.
* Encaminhe dinamicamente com base na solicitação.

Todos os esquemas de autenticação que usam derivado <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> e [o \<TOptions> AuthenticationHandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)associado:

* São esquemas de política automaticamente no ASP.NET Core 2,1 e posterior.
* Pode ser habilitado por meio da configuração das opções do esquema.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra um esquema de nível superior que combina esquemas de nível inferior. A autenticação do Google é usada para desafios e a cookie autenticação é usada para tudo o mais:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

O exemplo a seguir habilita a seleção dinâmica de esquemas de acordo com a solicitação. Ou seja, como misturar a cookie autenticação de s e de API:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
