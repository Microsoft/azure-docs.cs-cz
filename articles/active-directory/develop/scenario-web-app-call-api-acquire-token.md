---
title: Získání tokenu ve webové aplikaci, která volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak získat token pro webovou aplikaci, která volá webová rozhraní API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4fe3744f3f8cb39a7493ce788ee9badc1b31b75e
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396174"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webová aplikace, která volá webová rozhraní API: Získá token pro aplikaci.

Sestavili jste objekt klientské aplikace. Teď ho použijete k získání tokenu pro volání webového rozhraní API. V ASP.NET nebo ASP.NET Core se volání webového rozhraní API provádí v kontroleru:

- Získání tokenu pro webové rozhraní API pomocí mezipaměti tokenů Chcete-li získat tento token, zavoláte `AcquireTokenSilent` metodu MSAL (nebo ekvivalent v Microsoft. identity. Web).
- Zavolejte chráněné rozhraní API a předejte mu přístupový token jako parametr.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. identity. Web* přidává rozšiřující metody, které poskytují praktické služby pro volání Microsoft Graph nebo webového rozhraní API pro příjem dat. Tyto metody jsou podrobně vysvětleny ve [webové aplikaci, která volá webová rozhraní API: volání rozhraní API](scenario-web-app-call-api-call-api.md). Pomocí těchto pomocných metod není nutné ručně získat token.

Pokud ale chcete token získat ručně, následující kód ukazuje příklad použití *Microsoft. identity. Web* k tomu, aby byl v rámci domovského kontroleru. Volá Microsoft Graph pomocí REST API (místo sady Microsoft Graph SDK). Chcete-li získat token pro volání rozhraní API pro příjem dat, vložíte `ITokenAcquisition` službu pomocí injektáže závislosti do konstruktoru kontroleru (nebo vašeho konstruktoru stránky, pokud používáte Blazor), a použijete ji v akcích kontroleru, získáte token pro uživatele ( `GetAccessTokenForUserAsync` ) nebo pro vlastní aplikaci ( `GetAccessTokenForAppAsync` ) ve scénáři démon.

Metody kontroleru jsou chráněné `[Authorize]` atributem, který zajišťuje, aby webová aplikace mohla používat jenom ověření uživatelé.

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

`ITokenAcquisition`Služba je vložená pomocí ASP.NET pomocí injektáže závislosti.

Zde je zjednodušený kód pro akci `HomeController` , která získá token pro volání Microsoft Graph:

```csharp
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Pro lepší pochopení kódu potřebného pro tento scénář si přečtěte téma fáze 2 ([2-1 – volání webové aplikace Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) v kurzu [MS-identity-aspnetcore-WebApp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

`AuthorizeForScopes`Atribut na vrcholu akce kontroleru (nebo stránka Razor Pokud používáte šablonu Razor) poskytuje Microsoft. identity. Web. Zajistí, že se uživateli v případě potřeby vyzve k zadání souhlasu a přírůstkově.

Existují i jiné složité variace, jako například:

- Volání několika rozhraní API.
- Probíhá zpracování přírůstkového souhlasu a podmíněného přístupu.

Tyto pokročilé kroky jsou uvedené v části 3 kurzu [3-WebApp-multi-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Kód pro ASP.NET je podobný kódu, který je zobrazený pro ASP.NET Core:

- Akce kontroleru chráněná atributem [autorizovat] extrahuje ID tenanta a ID uživatele `ClaimsPrincipal` člena kontroleru. (ASP.NET používá `HttpContext.User` .)
- Odtud vytvoří `IConfidentialClientApplication` objekt MSAL.NET.
- Nakonec volá `AcquireTokenSilent` metodu důvěrné klientské aplikace.
- Pokud se vyžaduje interakce, Webová aplikace musí uživatele vyzvat (znovu přihlásit) a požádat o další deklarace identity.

Následující fragment kódu se extrahuje z [HomeController. cs # L157-L192](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Controllers/HomeController.cs#L157-L192) v ukázce kódu [MS-identity-ASPNET-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) ASP.NET MVC:

```C#
public async Task<ActionResult> ReadMail()
{
    IConfidentialClientApplication app = MsalAppBuilder.BuildConfidentialClientApplication();
    AuthenticationResult result = null;
    var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());
    string[] scopes = { "Mail.Read" };

    try
    {
        // try to get token silently
        result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync().ConfigureAwait(false);
    }
    catch (MsalUiRequiredException)
    {
        ViewBag.Relogin = "true";
        return View();
    }

    // More code here
    return View();
}
```

Podrobnosti najdete v kódu pro [BuildConfidentialClientApplication ()](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs) a [GetMsalAccountId](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Utils/ClaimPrincipalExtension.cs#L38) v ukázce kódu.


# <a name="java"></a>[Java](#tab/java)

V ukázce Java je kód, který volá rozhraní API, v metodě getUsersFromGraph v [AuthPageController. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

Metoda se pokusí o volání `getAuthResultBySilentFlow` . Pokud uživatel musí souhlasit s více rozsahy, kód zpracuje `MsalInteractionRequiredException` objekt a požádá uživatele o výzvu.

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

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-web-app-call-api-call-api.md)
