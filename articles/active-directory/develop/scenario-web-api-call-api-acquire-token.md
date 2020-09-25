---
title: Získání tokenu pro webové rozhraní API, které volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet webové rozhraní API, které volá webová rozhraní API, která vyžadují získání tokenu pro aplikaci.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ab0b74ffbcd8167613c6a8470e2f9102566edc60
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257227"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webové rozhraní API, které volá webová rozhraní API: Získá token pro aplikaci.

Po vytvoření objektu klientské aplikace jej použijte k získání tokenu, který můžete použít k volání webového rozhraní API.

## <a name="code-in-the-controller"></a>Kód v kontroleru

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. identity. Web* přidává rozšiřující metody, které poskytují praktické služby pro volání Microsoft Graph nebo webového rozhraní API pro příjem dat. Tyto metody jsou podrobně vysvětleny ve [webovém rozhraní API, které volá webová rozhraní API: volání rozhraní API](scenario-web-api-call-api-call-api.md). Pomocí těchto pomocných metod není nutné ručně získat token.

Pokud ale chcete token získat ručně, následující kód ukazuje příklad použití *Microsoft. identity. Web* k tomu v kontroleru rozhraní API. Volá rozhraní API pro příjem dat s názvem *ToDoList*.
Chcete-li získat token pro volání rozhraní API pro příjem dat, vložíte `ITokenAcquisition` službu pomocí injektáže závislosti do konstruktoru kontroleru (nebo vašeho konstruktoru stránky, pokud používáte Blazor), a použijete ji v akcích kontroleru, získáte token pro uživatele ( `GetAccessTokenForUserAsync` ) nebo pro samotnou aplikaci ( `GetAccessTokenForAppAsync` ) v případě scénáře démona.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Podrobnosti o této `callTodoListService` metodě najdete v tématu  [webové rozhraní API, které volá webová rozhraní API: volání rozhraní API](scenario-web-api-call-api-call-api.md).

# <a name="java"></a>[Java](#tab/java)
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

# <a name="python"></a>[Python](#tab/python)

Webové rozhraní API v Pythonu vyžaduje k ověření, že je nosný token přijatý od klienta, použití middleware. Webové rozhraní API pak může získat přístupový token pro rozhraní API pro příjem dat pomocí knihovny Pythonu MSAL voláním [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metody. Ukázka demonstrující tento tok pomocí MSAL Pythonu ještě není k dispozici.

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové rozhraní API, které volá webová rozhraní API: volá rozhraní API.](scenario-web-api-call-api-call-api.md)
