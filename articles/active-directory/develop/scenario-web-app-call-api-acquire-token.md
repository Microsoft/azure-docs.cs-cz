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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f30194592989b74aca96a5a483e9128cd3a86eb5
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274468"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webová aplikace, která volá webová rozhraní API – získá token pro aplikaci.

Teď, když jste vytvořili objekt klientské aplikace, ho použijete k získání tokenu pro volání webového rozhraní API. V ASP.NET nebo ASP.NET Core volání webového rozhraní API se pak provede v kontroleru. O:

- Získání tokenu pro webové rozhraní API pomocí mezipaměti tokenů Chcete-li získat tento token, zavoláte `AcquireTokenSilent`.
- Volání chráněného rozhraní API pomocí přístupového tokenu.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Metody kontroleru jsou chráněné pomocí atributu `[Authorize]`, který vynucuje ověřování uživatelů pro používání webové aplikace. Zde je kód, který volá Microsoft Graph.

```CSharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

Služba `ITokenAcquisition` je vložená pomocí ASP.NET prostřednictvím injektáže závislosti.


Tady je zjednodušený kód akce HomeController, která získá token pro volání Microsoft Graph.

```CSharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // use the access token to call a protected web API
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Pro lepší pochopení kódu potřebného pro tento scénář si přečtěte téma fáze 2 ([2-1 – volání webové aplikace Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) v kurzu [MS-identity-aspnetcore-WebApp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Existuje mnoho dalších složitosti, například:

- Volání několika rozhraní API,
- Probíhá zpracování přírůstkového souhlasu a podmíněného přístupu.

Tyto rozšířené kroky jsou zpracovávány v kapitole 3 kurzu [3 – WebApp-multi-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Věci jsou podobné v ASP.NET:

- Akce kontroleru chráněná atributem [autorizovat] extrahuje ID tenanta a ID uživatele `ClaimsPrincipal` člena kontroleru. (ASP.NET používá `HttpContext.User`.)
- Odtud vytvoří MSAL.NET `IConfidentialClientApplication`.
- Nakonec zavolá metodu `AcquireTokenSilent` důvěrné klientské aplikace.

Kód je podobný kódu, který je zobrazený pro ASP.NET Core.

# <a name="javatabjava"></a>[Java](#tab/java)

V ukázce Java kód, který volá rozhraní API, je v metodě getUsersFromGraph [AuthPageController. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Pokusí se zavolat `getAuthResultBySilentFlow`. Pokud uživatel musí souhlasit s více rozsahy, kód zpracovává `MsalInteractionRequiredException` pro výzvu uživateli.

```java
@RequestMapping("/msal4jsample/graph/users")
    public ModelAndView getUsersFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
            throws Throwable {

        IAuthenticationResult result;
        ModelAndView mav;
        try {
            result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
        } catch (ExecutionException e) {
            if (e.getCause() instanceof MsalInteractionRequiredException) {

                // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
                // so user can consent to new scopes
                String state = UUID.randomUUID().toString();
                String nonce = UUID.randomUUID().toString();

                SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

                String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                        httpRequest.getParameter("claims"),
                        "User.ReadBasic.all",
                        authHelper.getRedirectUriGraphUsers(),
                        state,
                        nonce);

                return new ModelAndView("redirect:" + authorizationCodeUrl);
            } else {

                mav = new ModelAndView("error");
                mav.addObject("error", e);
                return mav;
            }
        }
    // Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

V ukázce Pythonu je kód, který volá Microsoft Graph, v [App. py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Pokusí se získat token z mezipaměti tokenu a potom po nastavení autorizační hlavičky volá rozhraní API eb. V takovém případě se znovu podepíše uživatel.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-web-app-call-api-call-api.md)
