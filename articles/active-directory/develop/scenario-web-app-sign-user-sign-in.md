---
title: Zápis webové aplikace, která podepisuje uživatele – Microsoft Identity Platform | Azure
description: Informace o tom, jak vytvořit webovou aplikaci, která přihlašuje uživatele (přihlášení)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8d7d5737a8332416a225154709ab7d66e447764
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961977"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Webová aplikace, která přihlašuje uživatele: přihlášení a odhlášení

Naučte se, jak přidat přihlášení do kódu pro vaši webovou aplikaci, která se přihlásí uživatelům. Pak se dozvíte, jak je nechat odhlásit.

## <a name="sign-in"></a>Přihlášení

Přihlášení se skládá ze dvou částí:

- Tlačítko pro přihlášení na stránce HTML
- Akce přihlášení v kódu na pozadí v řadiči

### <a name="sign-in-button"></a>Tlačítko pro přihlášení

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core se v `Views\Shared\_LoginPartial.cshtml`zveřejňuje tlačítko pro přihlášení. Zobrazuje se pouze v případě, že není k dispozici žádný ověřený účet. To znamená, že se zobrazí, když uživatel ještě není přihlášený nebo se odhlásil.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET MVC je k dispozici tlačítko pro odhlášení v `Views\Shared\_LoginPartial.cshtml`. Zobrazuje se pouze v případě, že je k dispozici ověřený účet. To znamená, že se zobrazí, když se uživatel dřív přihlásil.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

V našem rychlém startu Java se v souboru [Main/Resources/Templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) nachází tlačítko pro přihlášení.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="pythontabpython"></a>[Python](#tab/python)

V rychlém startu Pythonu není k dispozici žádné tlačítko pro přihlášení. Kód na pozadí automaticky vyzve uživatele k přihlášení, když se dostane do kořenového adresáře webové aplikace. Viz [App. py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>akce `SignIn` kontroleru

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Výběrem tlačítka pro **přihlášení** ve webové aplikaci v ASP.NET spustí akci `SignIn` na řadiči `AccountController`. V předchozích verzích šablon ASP.NET byl řadič `Account` vložený do webové aplikace. To už neplatí, protože kontroler je teď součástí ASP.NET Core Frameworku.

Kód pro `AccountController` je k dispozici z úložiště ASP.NET Core v [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Řízení účtu vyžádá uživatele tím, že přesměruje na koncový bod Microsoft Identity Platform. Podrobnosti najdete v části Metoda [přihlášení](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) poskytovaná jako součást ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET se odhlášení aktivuje z metody `SignOut()` na řadiči (např [. AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Tato metoda není součástí ASP.NET architektury (na rozdíl od toho, co se stane v ASP.NET Core). Po navržení identifikátoru URI přesměrování pošle OpenID výzvu k přihlášení.

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

V jazyce Java se odhlášení zpracovává voláním přímo koncového bodu Microsoft Identity Platform `logout` a zadáním `post_logout_redirect_uri` hodnoty. Podrobnosti najdete v tématu [AuthPageController. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Na rozdíl od jiných platforem se MSAL Python dbá na to, aby se uživatel přihlásil ze stránky pro přihlášení. Viz [App. py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

Metoda `_build_msal_app()` je definována v [App. py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) následujícím způsobem:

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Až se uživatel přihlásí do vaší aplikace, budete ho chtít povolit, aby se odhlásili.

## <a name="sign-out"></a>Odhlášení

Odhlášení z webové aplikace zahrnuje více než odebrání informací o přihlášeném účtu ze stavu webové aplikace.
Webová aplikace musí také přesměrovat uživatele na Microsoft Identity Platform `logout` koncový bod pro odhlášení. 

Když webová aplikace přesměruje uživatele na `logout` koncový bod, tento koncový bod vymaže relaci uživatele z prohlížeče. Pokud vaše aplikace nepřešla do koncového bodu `logout`, uživatel se znovu ověří do vaší aplikace bez zadání přihlašovacích údajů. Důvodem je, že budou mít platnou relaci jednotného přihlašování s koncovým bodem Microsoft Identity Platform.

Další informace najdete v části [odeslání žádosti o přihlášení](v2-protocols-oidc.md#send-a-sign-out-request) na [platformě Microsoft Identity Platform a v dokumentaci k protokolu OpenID Connect](v2-protocols-oidc.md) .

### <a name="application-registration"></a>Registrace aplikací

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Během registrace aplikace zaregistrujete identifikátor URI po odhlášení. V našem kurzu jste v poli **Adresa URL pro odhlášení** v části **Upřesnit nastavení** na stránce **ověřování** zaregistrovali `https://localhost:44321/signout-oidc`. Podrobnosti najdete v tématu [Registrace aplikace WebApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Během registrace aplikace zaregistrujete identifikátor URI po odhlášení. V našem kurzu jste v poli **Adresa URL pro odhlášení** v části **Upřesnit nastavení** na stránce **ověřování** zaregistrovali `https://localhost:44308/Account/EndSession`. Podrobnosti najdete v tématu [Registrace aplikace WebApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="javatabjava"></a>[Java](#tab/java)

Během registrace aplikace zaregistrujete identifikátor URI po odhlášení. V našem kurzu jste v poli **Adresa URL pro odhlášení** v části **Upřesnit nastavení** na stránce **ověřování** zaregistrovali `http://localhost:8080/msal4jsample/sign_out`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Během registrace aplikace nemusíte registrovat další adresu URL pro odhlášení. Aplikace se bude volat zpátky na svou hlavní adresu URL.

---

### <a name="sign-out-button"></a>Tlačítko pro odhlášení

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core se v `Views\Shared\_LoginPartial.cshtml`zveřejňuje tlačítko pro odhlášení. Zobrazuje se pouze v případě, že je k dispozici ověřený účet. To znamená, že se zobrazí, když se uživatel dřív přihlásil.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET MVC je k dispozici tlačítko pro odhlášení v `Views\Shared\_LoginPartial.cshtml`. Zobrazuje se pouze v případě, že je k dispozici ověřený účet. To znamená, že se zobrazí, když se uživatel dřív přihlásil.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

V našem rychlém startu Java se v souboru main/resources/templates/auth_page.html nachází tlačítko pro odhlášení.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

V rychlém startu Pythonu se tlačítko pro odhlášení nachází v souboru [Templates/index. html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) .

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>akce `SignOut` kontroleru

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Výběrem tlačítka pro **odhlášení** ve webové aplikaci v ASP.NET spustí akci `SignOut` na řadiči `AccountController`. V předchozích verzích šablon ASP.NET Core byl kontroler `Account` vložen do webové aplikace. To už neplatí, protože kontroler je teď součástí ASP.NET Core Frameworku.

Kód pro `AccountController` je k dispozici z úložiště jádra ASP.NET v [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Řízení účtu:

- Nastaví OpenID identifikátor URI pro přesměrování pro `/Account/SignedOut` tak, že se kontroler zavolá zpátky, když Azure AD dokončí odhlášení.
- Volá `Signout()`, která umožňuje middlewaru OpenID Connect kontaktovat koncový bod Microsoft Identity Platform `logout`. Koncový bod pak:

  - Vymaže soubor cookie relace z prohlížeče.
  - Volá zpět adresu URL pro odhlášení. Ve výchozím nastavení zobrazí adresa URL pro odhlášení stránku zobrazení se znaménkem [. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml). Tato stránka je také k dispozici jako součást ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET se odhlášení aktivuje z metody `SignOut()` na řadiči (např [. AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Tato metoda není součástí ASP.NET architektury, a to v rozporu s tím, co se stane v ASP.NET Core. Její

- Pošle výzvu k odhlášení OpenID.
- Vymaže mezipaměť.
- Přesměruje na stránku, kterou chce.

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

V jazyce Java se odhlášení zpracovává voláním přímo koncového bodu Microsoft Identity Platform `logout` a zadáním `post_logout_redirect_uri` hodnoty. Podrobnosti najdete v tématu [AuthPageController. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Kód, který podepisuje uživatele, je v [App. py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Zachytávání volání do koncového bodu `logout`

Identifikátor URI po odhlášení umožňuje aplikacím, aby se účastnili globálního odhlašování.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Middleware ASP.NET Core OpenID Connect umožňuje vaší aplikaci zachytit volání služby Microsoft Identity Platform `logout` koncový bod tím, že poskytne událost OpenID Connect s názvem `OnRedirectToIdentityProviderForSignOut`. Příklad toho, jak se přihlásit k odběru této události (Pokud chcete vymazat mezipaměť tokenu), najdete v tématu [Microsoft. identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

```CSharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET delegujete na middleware, abyste mohli spustit odhlášení a vymazat soubor cookie relace:

```CSharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

V rychlém startu Java se k identifikátoru URI přesměrování po odhlášení zobrazí jenom stránka index. html.

# <a name="pythontabpython"></a>[Python](#tab/python)

V rychlém startu Pythonu se k identifikátoru URI přesměrování po odhlášení zobrazí jenom stránka index. html.

---

## <a name="protocol"></a>Protocol (Protokol)

Pokud chcete získat další informace o odhlášení, přečtěte si dokumentaci k protokolu, která je k dispozici v [otevřeném ID připojit](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přesunout do produkčního prostředí](scenario-web-app-sign-user-production.md)
