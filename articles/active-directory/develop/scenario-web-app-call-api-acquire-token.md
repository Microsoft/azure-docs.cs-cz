---
title: Získání tokenu ve webové aplikaci, která volá webová rozhraní API – Microsoft Identity Platform | Azure
description: Zjistěte, jak získat token pro webovou aplikaci, která volá webová rozhraní API.
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: abf7d800eda376c21dfdd672032ddb65e27355be
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759070"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webová aplikace, která volá webová rozhraní API: Získá token pro aplikaci.

Sestavili jste objekt klientské aplikace. Teď ho použijete k získání tokenu pro volání webového rozhraní API. V ASP.NET nebo ASP.NET Core se volání webového rozhraní API provádí v kontroleru:

- Získání tokenu pro webové rozhraní API pomocí mezipaměti tokenů Chcete-li získat tento token, zavoláte metodu `AcquireTokenSilent`.
- Zavolejte chráněné rozhraní API a předejte mu přístupový token jako parametr.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Metody kontroleru jsou chráněné atributem `[Authorize]`, který vynutí, aby se uživatelé k používání webové aplikace ověřili. Zde je kód, který volá Microsoft Graph:

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

Služba `ITokenAcquisition` je vložená pomocí injektáže ASP.NET pomocí injektáže závislosti.

Zde je zjednodušený kód pro akci `HomeController`, která získá token pro volání Microsoft Graph:

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

Pro lepší pochopení kódu potřebného pro tento scénář si přečtěte téma fáze 2 ([2-1 – volání webové aplikace Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) v kurzu [MS-identity-aspnetcore-WebApp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Existují i jiné složité variace, jako například:

- Volání několika rozhraní API.
- Probíhá zpracování přírůstkového souhlasu a podmíněného přístupu.

Tyto pokročilé kroky jsou uvedené v části 3 kurzu [3-WebApp-multi-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Kód pro ASP.NET je podobný kódu, který je zobrazený pro ASP.NET Core:

- Akce kontroleru chráněná atributem [autorizovat] extrahuje ID tenanta a ID uživatele `ClaimsPrincipal`ho člena kontroleru. (ASP.NET používá `HttpContext.User`.)
- Odtud vytvoří objekt MSAL.NET `IConfidentialClientApplication`.
- Nakonec volá metodu `AcquireTokenSilent` v důvěrné klientské aplikaci.

# <a name="javatabjava"></a>[Java](#tab/java)

V ukázce Java je kód, který volá rozhraní API, v metodě getUsersFromGraph v [AuthPageController. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Metoda se pokusí volat `getAuthResultBySilentFlow`. Pokud uživatel musí souhlasit s více rozsahy, kód zpracuje objekt `MsalInteractionRequiredException` a požádá uživatele.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

V ukázce Pythonu je kód, který volá Microsoft Graph, v [App. py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

Kód se pokusí získat token z mezipaměti tokenu. Po nastavení autorizační hlavičky pak volá webové rozhraní API. Pokud nemůže token získat, uživatel ho znovu přihlásí.

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
