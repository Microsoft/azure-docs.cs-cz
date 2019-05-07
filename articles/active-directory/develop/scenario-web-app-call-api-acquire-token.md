---
title: Webovou aplikaci, která volá webové rozhraní API (získat token pro aplikace) – platforma identit Microsoft
description: Zjistěte, jak sestavit webovou aplikaci, která volá webové rozhraní API (získání tokenu pro aplikaci)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074797"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webovou aplikaci, která volá webové rozhraní API – získání tokenu pro aplikaci

Nyní jste je vytvořili objekt klientské aplikace, použijete ho k získání tokenu, který pak použijete k volání webového rozhraní API. V technologii ASP.NET nebo ASP.NET Core volání webového rozhraní API je pak provedeno v kontroleru. Jedná se o:

- Získání tokenu pro webové rozhraní API s využitím mezipaměť tokenu. V takovém případě můžete volání `AcquireTokenSilent`
- Volání rozhraní API chráněné pomocí přístupového tokenu

## <a name="aspnet-core"></a>Jádro ASP.NET

Jsou chráněny metody kontroleru `[Authorize]` atribut, který vynutí uživatelé byli ověření i pro použití webové aplikace. Tady je kód, který volání Microsoft Graphu

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Tady je zjednodušené kód akce HomeController, která získá tokenu pro volání Microsoft Graphu.

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

Pokud chcete pochopit podrobnosti celkem kódu vyžadovaného pro tento scénář, přečtěte si téma fáze 2 [2-1-Web App volání Microsoft Graphu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) kroku [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) kurz

Existuje mnoho dalších složitostí, jako:

- implementace mezipaměť tokenu pro webovou aplikaci (v kurzu k dispozici několik implementací)
- Odebráním účtu z mezipaměti, když uživatel příznaky out
- Volání několik rozhraní API, včetně přírůstkové souhlas s

## <a name="aspnet"></a>ASP.NET

Co jsou podobné v technologii ASP.NET:

- Akce kontroleru chráněn atribut [Authorize] se extrahovat ID a uživatelského ID tenanta `ClaimsPrincipal` člen kontroleru (technologie ASP.NET používá `HttpContext.User`)
- odtud sestavuje MSAL.NET `IConfidentialClientApplication`
- Volání IT `AcquireTokenSilent` metoda důvěrné klientské aplikace 

kód je podobný kódu pro ASP.NET Core

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-web-app-call-api-call-api.md)
