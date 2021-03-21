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
ms.openlocfilehash: 9f9758ec765ad34e5ef5d8b4d4e0a420a6701b6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756391"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webové rozhraní API, které volá webová rozhraní API: Získá token pro aplikaci.

Po vytvoření objektu klientské aplikace jej použijte k získání tokenu, který můžete použít k volání webového rozhraní API.

## <a name="code-in-the-controller"></a>Kód v kontroleru

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

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

### <a name="java"></a>[Java](#tab/java)

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

### <a name="python"></a>[Python](#tab/python)
 
Webové rozhraní API v Pythonu vyžaduje k ověření, že je nosný token přijatý od klienta, použití middleware. Webové rozhraní API pak může získat přístupový token pro rozhraní API pro příjem dat pomocí knihovny Pythonu MSAL voláním [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metody.
 
Zde je příklad kódu, který získá přístupový token pomocí `acquire_token_on_behalf_of` metody a rozhraní pro baňky. Volá rozhraní API pro příjem dat – koncový bod předplatných pro správu Azure.
 
```python
def get(self):
 
        _scopes = ["https://management.azure.com/user_impersonation"]
        _azure_management_subscriptions_uri = "https://management.azure.com/subscriptions?api-version=2020-01-01"
 
        current_access_token = request.headers.get("Authorization", None)
        
        #This example only uses the default memory token cache and should not be used for production
        msal_client = msal.ConfidentialClientApplication(
                client_id=os.environ.get("CLIENT_ID"),
                authority=os.environ.get("AUTHORITY"),
                client_credential=os.environ.get("CLIENT_SECRET"))
 
        #acquire token on behalf of the user that called this API
        arm_resource_access_token = msal_client.acquire_token_on_behalf_of(
            user_assertion=current_access_token.split(' ')[1],
            scopes=_scopes
        )
 
        headers = {'Authorization': arm_resource_access_token['token_type'] + ' ' + arm_resource_access_token['access_token']}
 
        subscriptions_list = req.get(_azure_management_subscriptions_uri), headers=headers).json()
 
        return jsonify(subscriptions_list)
```

## <a name="advanced-accessing-the-signed-in-users-token-cache-from-background-apps-apis-and-services"></a>Upřesnit Přístup k mezipaměti tokenů přihlášeného uživatele z aplikací na pozadí, rozhraní API a služeb

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, zavolejte na [rozhraní API](scenario-web-api-call-api-call-api.md).
