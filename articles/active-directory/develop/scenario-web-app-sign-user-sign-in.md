---
title: Napište webovou aplikaci, která přihlásí uživatelům – platformu microsoftidentity | Azure
description: Přečtěte si, jak vytvořit webovou aplikaci, která přizpůsobuje a odchozí uživatele.
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
ms.openlocfilehash: 317ca55adb9f680dc93343a185395abad08889da
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881311"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Webová aplikace, která se přihlašuje k uživatelům: Přihlášení a odhlášení

Přečtěte si, jak přidat přihlášení ke kódu webové aplikace, která se přihlašuje k uživatelům. Pak se naučte, jak je nechat se odhlásit.

## <a name="sign-in"></a>Přihlášení

Přihlášení se skládá ze dvou částí:

- Tlačítko přihlášení na stránce HTML
- Akce přihlášení v kódu na pozadí v kontroleru

### <a name="sign-in-button"></a>Tlačítko pro přihlášení

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core je tlačítko pro `Views\Shared\_LoginPartial.cshtml`přihlášení vystaveno v . Zobrazí se pouze v případě, že neexistuje žádný ověřený účet. To znamená, že se zobrazí, když se uživatel ještě nepřihlásil nebo se odhlásil.

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET MVC je tlačítko pro `Views\Shared\_LoginPartial.cshtml`odhlášení vystaveno v . Zobrazí se pouze v případě, že existuje ověřený účet. To znamená, že se zobrazí, když se uživatel dříve přihlásil.

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

# <a name="java"></a>[Java](#tab/java)

V našem rychlém startu v jazyce Java je přihlašovací tlačítko umístěno v [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) souboru.

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

# <a name="python"></a>[Python](#tab/python)

V pythonu rychlý start není žádné přihlašovací tlačítko. Kód na pozadí automaticky vyzve uživatele k přihlášení, když se dostane do kořenového adresáře webové aplikace. Viz [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`akce regulátoru

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET, když vyberete tlačítko **Přihlášení** ve webové `SignIn` aplikaci, spustíte akci na ovladači. `AccountController` V předchozích verzích základních šablon `Account` ASP.NET byl řadič vložen do webové aplikace. To už není případ, protože řadič je nyní součástí ASP.NET core framework.

Kód pro `AccountController` je k dispozici v úložišti ASP.NET Core v [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Ovládací prvek účtu výzvy uživatele přesměrováním na koncový bod platformy identit y Microsoft. Podrobnosti naleznete [v](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) přihlašovací metodě poskytované jako součást ASP.NET core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET se odhlášení `SignOut()` aktivuje z metody na řadiči (například [AccountController.cs#L16-L23).](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23) Tato metoda není součástí rámce ASP.NET (na rozdíl od toho, co se děje v ASP.NET Core). Odešle výzvu přihlášení OpenID po navržení identifikátoru URI přesměrování.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

V jazyce Java se odhlášení zpracovává voláním koncového bodu platformy `logout` identit y Microsoft přímo a poskytnutím `post_logout_redirect_uri` hodnoty. Podrobnosti naleznete [v tématu AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

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

# <a name="python"></a>[Python](#tab/python)

Na rozdíl od jiných platforem se MSAL Python stará o to, aby se uživatel přihlašuje z přihlašovací stránky. Viz [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

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

Metoda `_build_msal_app()` je definována v [app.py#L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) takto:

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

Až se uživatel přihlásí k vaší aplikaci, budete mu chtít povolit odhlášení.

## <a name="sign-out"></a>Odhlášení

Odhlášení z webové aplikace zahrnuje více než odebrání informací o přihlášeném účtu ze stavu webové aplikace.
Webová aplikace musí také přesměrovat uživatele na `logout` koncový bod platformy identit microsoftu, aby se odhlásit. 

Když vaše webová aplikace přesměruje uživatele do koncového `logout` bodu, tento koncový bod vymaže relaci uživatele z prohlížeče. Pokud vaše aplikace nepřejde `logout` do koncového bodu, uživatel se znovu ověří do vaší aplikace, aniž by znovu zadal své přihlašovací údaje. Důvodem je, že budou mít platnou relaci jednotného přihlášení s koncovým bodem platformy identit y Microsoft.

Další informace najdete v části [Odeslat žádost o odhlášení](v2-protocols-oidc.md#send-a-sign-out-request) na platformě microsoftu identita a v dokumentaci [k protokolu OpenID Connect.](v2-protocols-oidc.md)

### <a name="application-registration"></a>Registrace aplikací

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Během registrace aplikace zaregistrujete identifikátor URI po odhlášení. V našem kurzu `https://localhost:44321/signout-oidc` jste se zaregistrovali v poli **Adresa URL odhlášení** v části **Upřesnit nastavení** na stránce **Ověřování.** Podrobnosti viz [Registrace aplikace webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Během registrace aplikace zaregistrujete identifikátor URI po odhlášení. V našem kurzu `https://localhost:44308/Account/EndSession` jste se zaregistrovali v poli **Adresa URL odhlášení** v části **Upřesnit nastavení** na stránce **Ověřování.** Podrobnosti viz [Registrace aplikace webApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="java"></a>[Java](#tab/java)

Během registrace aplikace zaregistrujete identifikátor URI po odhlášení. V našem kurzu `http://localhost:8080/msal4jsample/sign_out` jste se zaregistrovali v poli **Adresa URL odhlášení** v části **Upřesnit nastavení** na stránce **Ověřování.**

# <a name="python"></a>[Python](#tab/python)

Během registrace aplikace není nutné registrovat další adresu URL odhlášení. Aplikace bude volána zpět na hlavní adresu URL.

---

### <a name="sign-out-button"></a>Tlačítko odhlášení

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET core je tlačítko pro `Views\Shared\_LoginPartial.cshtml`odhlášení vystaveno v . Zobrazí se pouze v případě, že existuje ověřený účet. To znamená, že se zobrazí, když se uživatel dříve přihlásil.

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET MVC je tlačítko pro `Views\Shared\_LoginPartial.cshtml`odhlášení vystaveno v . Zobrazí se pouze v případě, že existuje ověřený účet. To znamená, že se zobrazí, když se uživatel dříve přihlásil.

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

# <a name="java"></a>[Java](#tab/java)

V našem rychlém startu v jazyce Java je tlačítko pro odhlášení umístěno v souboru main/resources/templates/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

V rychlém startu Pythonu je tlačítko pro odhlášení umístěno v souboru [templates/index.html#L10.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

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

### <a name="signout-action-of-the-controller"></a>`SignOut`akce regulátoru

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET se výběrem tlačítka **odhlášení** ve webové `SignOut` aplikaci `AccountController` spustí akce na ovladači. V předchozích verzích šablon ASP.NET `Account` Core byl řadič vložen do webové aplikace. To už není případ, protože řadič je nyní součástí ASP.NET core framework.

Kód pro `AccountController` je k dispozici v ASP.NET základní úložiště v [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Kontrola účtu:

- Nastaví identifikátor URI openid přesměrování URI tak, aby `/Account/SignedOut` řadič je volána zpět po dokončení odhlášení Azure AD.
- Volání `Signout()`, který umožňuje OpenID Connect middleware kontaktovat `logout` koncový bod platformy identit y Microsoft. Koncový bod pak:

  - Vymaže soubor cookie relace z prohlížeče.
  - Zavolá zpět adresu URL odhlášení. Ve výchozím nastavení se na adrese URL odhlášení zobrazí odhlášená stránka zobrazení [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml). Tato stránka je také poskytována jako součást ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET se odhlášení `SignOut()` aktivuje z metody na řadiči (například [AccountController.cs#L25-L31).](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31) Tato metoda není součástí ASP.NET rámce, na rozdíl od toho, co se děje v ASP.NET Core. Je to:

- Odešle výzvu pro odhlášení OpenID.
- Vymaže mezipaměť.
- Přesměruje na stránku, kterou chce.

```csharp
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

# <a name="java"></a>[Java](#tab/java)

V jazyce Java se odhlášení zpracovává voláním koncového bodu platformy `logout` identit y Microsoft přímo a poskytnutím `post_logout_redirect_uri` hodnoty. Podrobnosti naleznete [v tématu AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

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

# <a name="python"></a>[Python](#tab/python)

Kód, který odhlásí uživatele je v [app.py#L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Zachycení volání ke `logout` koncovému bodu

Identifikátor URI po odhlášení umožňuje aplikacím účastnit se globálního odhlášení.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Middleware ASP.NET Core OpenID Connect umožňuje vaší aplikaci zachytit `logout` volání do koncového bodu platformy `OnRedirectToIdentityProviderForSignOut`identit microsoftu poskytnutím události OpenID Connect s názvem . Příklad, jak se přihlásit k odběru této události (chcete-li vymazat mezipaměť tokenů), naleznete v tématu [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET delegujete na middleware, abyste provedli odhlášení a vyčistili soubor cookie relace:

```csharp
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

# <a name="java"></a>[Java](#tab/java)

V rychlém startu javy se identifikátor URI přesměrování po odhlášení zobrazí pouze na stránce index.html.

# <a name="python"></a>[Python](#tab/python)

V pythonu rychlý start, post-odhlášení přesměrování URI pouze zobrazí stránku index.html.

---

## <a name="protocol"></a>Protocol (Protokol)

Pokud se chcete dozvědět více o odhlášení, přečtěte si dokumentaci protokolu, která je k dispozici na [open id connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přechod k produkčnímu prostředí](scenario-web-app-sign-user-production.md)
