---
title: Získání tokenu pro webové rozhraní API, které volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet webové rozhraní API, které volá webová rozhraní API, která vyžadují získání tokenu pro aplikaci.
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
ms.openlocfilehash: 2721837459af24f39bb15ee17d394345cbb37eb1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834106"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webové rozhraní API, které volá webová rozhraní API: Získá token pro aplikaci.

Po vytvoření objektu klientské aplikace jej použijte k získání tokenu, který můžete použít k volání webového rozhraní API.

## <a name="code-in-the-controller"></a>Kód v kontroleru

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Tady je příklad kódu, který se volá v akcích řadičů rozhraní API. Volá rozhraní API pro příjem dat s názvem *ToDoList*.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` je podobná scénáři ve [webovém rozhraní API, které volá webová rozhraní API: Konfigurace aplikace](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` vytvoří instanci `IConfidentialClientApplication` s mezipamětí, která obsahuje informace pouze pro jeden účet. Účet je poskytován metodou `GetAccountIdentifier`.

Metoda `GetAccountIdentifier` používá deklarace identity, které jsou přidružené k identitě uživatele, pro kterou webové rozhraní API přijalo JSON Web Token (JWT):

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

# <a name="javatabjava"></a>[Java](#tab/java)
Tady je příklad kódu, který se volá v akcích řadičů rozhraní API. Volá rozhraní API pro příjem dat Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Webové rozhraní API v Pythonu bude muset použít nějaký middleware k ověření, že je nosný token přijatý od klienta. Webové rozhraní API pak může získat přístupový token pro rozhraní API pro příjem dat pomocí MSAL knihovny Python voláním metody [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) . Ukázka demonstrující tento tok pomocí MSAL Python ještě není k dispozici.

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové rozhraní API, které volá webová rozhraní API: volá rozhraní API.](scenario-web-api-call-api-call-api.md)
