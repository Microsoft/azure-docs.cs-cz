---
title: Získání tokenu ve webové aplikaci, která volá webová rozhraní API – platforma identit Microsoftu | Azure
description: Zjistěte, jak získat token pro webovou aplikaci, která volá webová api
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1069b4288f8253ccb9a7774b3144d10d85dcdd36
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537096"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webová aplikace, která volá webová api: Získání tokenu pro aplikaci

Vytvořili jste objekt klientské aplikace. Nyní ji použijete k získání tokenu pro volání webového rozhraní API. V ASP.NET nebo ASP.NET Core se volání webového rozhraní API provádí v kontroleru:

- Získejte token pro webové rozhraní API pomocí mezipaměti tokenů. Chcete-li získat tento `AcquireTokenSilent` token, volání metody.
- Volání chráněné rozhraní API, předávání přístupový token k němu jako parametr.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Metody kontroleru jsou `[Authorize]` chráněny atributem, který nutí uživatele, kteří jsou ověřováni, aby používali webovou aplikaci. Zde je kód, který volá Microsoft Graph:

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

Služba `ITokenAcquisition` je vložena ASP.NET pomocí vkládání závislostí.

Zde je zjednodušený kód pro `HomeController`akci , který získá token pro volání Microsoft Graph:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Chcete-li lépe porozumět kódu potřebnému pro tento scénář, podívejte se na krok fáze 2 ([2-1-Web app Calls Microsoft Graph)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) [v kurzu kurzu ms-identity-aspnetcore-webapp-tutorial.](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Existují i jiné složité varianty, jako například:

- Volání několika api.
- Zpracování přírůstkového souhlasu a podmíněného přístupu.

Tyto pokročilé kroky jsou popsány v kapitole 3 kurz [3-WebApp-multi-API.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kód pro ASP.NET je podobný kódu zobrazenému pro ASP.NET Core:

- Akce kontroleru, chráněná atributem [Authorize], extrahuje ID klienta a ID uživatele `ClaimsPrincipal` člena řadiče. (ASP.NET `HttpContext.User`použití .)
- Odtud vytvoří MSAL.NET `IConfidentialClientApplication` objekt.
- Nakonec volá metodu `AcquireTokenSilent` důvěrné klientské aplikace.

# <a name="java"></a>[Java](#tab/java)

V ukázce Java je kód, který volá rozhraní API, v metodě getUsersFromGraph v [authPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Metoda se pokusí `getAuthResultBySilentFlow`volat . Pokud uživatel potřebuje souhlas s více obory, `MsalInteractionRequiredException` kód zpracuje objekt napadnout uživatele.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

V ukázce Pythonu je kód, který volá Microsoft Graph v [app.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Kód se pokusí získat token z mezipaměti tokenů. Potom po nastavení hlavičky autorizace volá webové rozhraní API. Pokud nemůže získat token, znovu přihlásí uživatele.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-web-app-call-api-call-api.md)
