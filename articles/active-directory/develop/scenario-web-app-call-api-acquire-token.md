---
title: Webová aplikace, která volá webová rozhraní API (získá token pro aplikaci) – Microsoft Identity Platform
description: Naučte se, jak vytvořit webovou aplikaci, která volá webová rozhraní API (získání tokenu pro aplikaci).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860623"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webová aplikace, která volá webová rozhraní API – získá token pro aplikaci.

Teď, když jste vytvořili objekt klientské aplikace, ho použijete k získání tokenu pro volání webového rozhraní API. V ASP.NET nebo ASP.NET Core volání webového rozhraní API se pak provede v kontroleru. O:

- Získání tokenu pro webové rozhraní API pomocí mezipaměti tokenů Chcete-li získat tento token, `AcquireTokenSilent`zavoláte.
- Volání chráněného rozhraní API pomocí přístupového tokenu.

## <a name="aspnet-core"></a>ASP.NET Core

Metody kontroleru jsou chráněné `[Authorize]` atributem, který vynucuje ověřování uživatelů pro používání webové aplikace. Zde je kód, který volá Microsoft Graph.

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Tady je zjednodušený kód akce HomeController, která získá token pro volání Microsoft Graph.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

Pro lepší pochopení kódu potřebného pro tento scénář si přečtěte téma fáze 2 ([2-1 – volání webové aplikace Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) v kurzu [MS-identity-aspnetcore-WebApp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Existuje mnoho dalších složitosti, například:

- Implementace mezipaměti tokenů pro webovou aplikaci (kurz nabízí několik implementací)
- Odebrání účtu z mezipaměti, když se uživatel odhlásí
- Volání několika rozhraní API, včetně přírůstkového souhlasu

## <a name="aspnet"></a>ASP.NET

Věci jsou podobné v ASP.NET:

- Akce kontroleru chráněná atributem [autorizovat] extrahuje ID tenanta a ID `ClaimsPrincipal` uživatele člena kontroleru. (ASP.NET používá `HttpContext.User`.)
- Odtud vytvoří MSAL.NET `IConfidentialClientApplication`.
- Nakonec volá `AcquireTokenSilent` metodu důvěrné klientské aplikace.

Kód je podobný kódu, který je zobrazený pro ASP.NET Core.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-web-app-call-api-call-api.md)
