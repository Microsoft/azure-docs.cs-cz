---
title: Získání tokenu pro webové rozhraní API, které volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vytvořit webové rozhraní API, které volá webová rozhraní API, která vyžadují získání tokenu pro aplikaci.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 79f8eb9e804502a7c0e61c18e4998fa05db10278
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885136"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webové rozhraní API, které volá webová rozhraní API: Získání tokenu pro aplikaci

Po vytvoření objektu klientské aplikace jej použijte k získání tokenu, který můžete použít k volání webového rozhraní API.

## <a name="code-in-the-controller"></a>Kód v regulátoru

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Tady je příklad kódu, který se nazývá v akcích řadičů rozhraní API. Volá downstream API s názvem *todolista*.

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

`BuildConfidentialClient()`je podobný scénáři ve [webovém rozhraní API, které volá webová rozhraní API: Konfigurace aplikace](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()`instance `IConfidentialClientApplication` s mezipamětí, která obsahuje informace pouze pro jeden účet. Účet je poskytován `GetAccountIdentifier` metodou.

Metoda `GetAccountIdentifier` používá deklarace identity, které jsou spojeny s identitou uživatele, pro kterého webové rozhraní API obdrželo webový token JSON (JWT):

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

# <a name="java"></a>[Java](#tab/java)
Tady je příklad kódu, který se nazývá v akcích řadičů rozhraní API. Volá rozhraní API pro příjem dat - Microsoft Graph.

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

# <a name="python"></a>[Python](#tab/python)

Webové rozhraní API Pythonu bude muset použít nějaký middleware k ověření nosné tokenu přijatého od klienta. Webové rozhraní API pak můžete získat přístupový token pro příjem rozhraní [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) API pomocí knihovny MSAL Python voláním metody. Ukázka demonstrující tento tok s MSAL Python ještě není k dispozici.

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové rozhraní API, které volá webová rozhraní API: Volání rozhraní API](scenario-web-api-call-api-call-api.md)
