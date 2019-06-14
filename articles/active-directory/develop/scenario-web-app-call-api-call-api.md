---
title: Webové aplikace, že volání webových rozhraní API (volání webového rozhraní API) - platforma identit Microsoft
description: Zjistěte, jak sestavit webovou aplikaci, která volá webové rozhraní API (volání webového rozhraní API)
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
ms.openlocfilehash: dd44dda06b2f6fc48538f2fb74c0bf8e04d0362b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074632"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>Webovou aplikaci, která volá webové rozhraní API – volání webového rozhraní API

Teď, když máte token, můžete volat chráněné webové rozhraní API.

## <a name="aspnet-core"></a>ASP.NET Core

Tady je zjednodušené kód akce `HomeController`. Tento kód získá tokenu pro volání Microsoft Graphu. Tento kód se úspěšně přidala ukazující, jak volání Microsoft Graphu jako rozhraní REST API.

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

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> Stejný princip můžete použít k volání jakékoli webové rozhraní API.
>
> Většinu služeb Azure webové rozhraní API poskytnout sadu SDK, která zjednodušuje volání. To platí také z Microsoft Graphu. Kde najít kurz ilustrující tyto aspekty se získáte informace v následujícím článku.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejít do produkčního prostředí](scenario-web-app-call-api-production.md)
