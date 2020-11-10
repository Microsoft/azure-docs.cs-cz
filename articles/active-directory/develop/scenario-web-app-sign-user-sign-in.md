---
title: Zápis webové aplikace s přihlašováním/vycházejícími uživateli – Microsoft Identity Platform | Azure
description: Naučte se, jak vytvořit webovou aplikaci, která se přihlásí uživatelům.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: e7397f6d02d71a6344953b8210b0349b9ee26360
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443547"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Webová aplikace, která přihlašuje uživatele: přihlášení a odhlášení

Naučte se, jak přidat přihlášení do kódu pro vaši webovou aplikaci, která se přihlásí uživatelům. Pak se dozvíte, jak je nechat odhlásit.

## <a name="sign-in"></a>Přihlášení

Přihlášení se skládá ze dvou částí:

- Tlačítko pro přihlášení na stránce HTML
- Akce přihlášení v kódu na pozadí v řadiči

### <a name="sign-in-button"></a>Tlačítko pro přihlášení

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core je pro aplikace Microsoft Identity Platform k dispozici tlačítko **Přihlásit** `Views\Shared\_LoginPartial.cshtml` (pro aplikaci MVC) nebo `Pages\Shared\_LoginPartial.cshtm` (pro aplikaci Razor). Zobrazuje se pouze v případě, že uživatel není ověřený. To znamená, že se zobrazí, když uživatel ještě není přihlášený nebo se odhlásil. V opačném případě se zobrazí tlačítko **Odhlásit** , pokud je uživatel už přihlášený. Všimněte si, že je řadič účtu definovaný v balíčku NuGet **Microsoft. identity. Web. UI** v oblasti s názvem **MicrosoftIdentity** .

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET MVC je tlačítko odhlášení vystaveno v `Views\Shared\_LoginPartial.cshtml` . Zobrazuje se pouze v případě, že je k dispozici ověřený účet. To znamená, že se zobrazí, když se uživatel dřív přihlásil.

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

V našem rychlém startu Java se přihlašovací tlačítko nachází v souboru [Main/Resources/Templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) .

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

V rychlém startu Pythonu není k dispozici žádné tlačítko pro přihlášení. Kód na pozadí automaticky vyzve uživatele k přihlášení, když se dostane do kořenového adresáře webové aplikace. Viz [App. py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` akce kontroleru

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET výběr tlačítka pro **přihlášení** ve webové aplikaci aktivuje `SignIn` akci na `AccountController` řadiči. V předchozích verzích základních šablon ASP.NET `Account` byl kontroler vložen do webové aplikace. To už neplatí, protože kontroler je teď součástí balíčku NuGet **Microsoft. identity. Web. UI** . Podrobnosti najdete v tématu [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

Tento kontroler také zpracovává aplikace Azure AD B2C.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET se odhlášení aktivuje z `SignOut()` metody na řadiči (např [. AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Tato metoda není součástí ASP.NET architektury (na rozdíl od toho, co se stane v ASP.NET Core). Po navržení identifikátoru URI přesměrování pošle OpenID výzvu k přihlášení.

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

V jazyce Java se odhlášení zpracovává voláním `logout` koncového bodu Microsoft Identity Platform přímo a zadáním `post_logout_redirect_uri` hodnoty. Podrobnosti najdete v tématu [AuthPageController. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

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

`_build_msal_app()`Metoda je definována v [App. py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) následujícím způsobem:

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
Webová aplikace musí také přesměrovat uživatele na koncový bod platformy Microsoft identity, `logout` aby se odhlásily.

Když vaše webová aplikace přesměruje uživatele na `logout` koncový bod, tento koncový bod vymaže relaci uživatele z prohlížeče. Pokud vaše aplikace nepřešla do `logout` koncového bodu, uživatel se znovu ověří do vaší aplikace bez zadání přihlašovacích údajů. Důvodem je, že budou mít platnou relaci jednotného přihlašování s koncovým bodem Microsoft Identity Platform.

Další informace najdete v části [odeslání žádosti o přihlášení](v2-protocols-oidc.md#send-a-sign-out-request) na [platformě Microsoft Identity Platform a v dokumentaci k protokolu OpenID Connect](v2-protocols-oidc.md) .

### <a name="application-registration"></a>Registrace aplikací

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Během registrace aplikace zaregistrujete identifikátor URI po odhlášení. V našem kurzu jste si zaregistrovali `https://localhost:44321/signout-oidc` v poli **Adresa URL pro odhlášení** v části **Upřesnit nastavení** na stránce **ověřování** . Podrobnosti najdete v tématu [ Registrace aplikace WebApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Během registrace aplikace zaregistrujete identifikátor URI po odhlášení. V našem kurzu jste si zaregistrovali `https://localhost:44308/Account/EndSession` v poli **Adresa URL pro odhlášení** v části **Upřesnit nastavení** na stránce **ověřování** . Podrobnosti najdete v tématu [Registrace aplikace WebApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="java"></a>[Java](#tab/java)

Během registrace aplikace zaregistrujete identifikátor URI po odhlášení. V našem kurzu jste si zaregistrovali `http://localhost:8080/msal4jsample/sign_out` v poli **Adresa URL pro odhlášení** v části **Upřesnit nastavení** na stránce **ověřování** .

# <a name="python"></a>[Python](#tab/python)

Během registrace aplikace nemusíte registrovat další adresu URL pro odhlášení. Aplikace se bude volat zpátky na svou hlavní adresu URL.

---

### <a name="sign-out-button"></a>Tlačítko pro odhlášení

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Výběrem tlačítka **Odhlásit** ve webové aplikaci v ASP.NET spustí `SignOut` akci na `AccountController` řadiči (viz níže).

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET MVC je tlačítko odhlášení vystaveno v `Views\Shared\_LoginPartial.cshtml` . Zobrazuje se pouze v případě, že je k dispozici ověřený účet. To znamená, že se zobrazí, když se uživatel dřív přihlásil.

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

V našem rychlém startu Java se tlačítko pro odhlášení nachází v souboru Main/Resources/Templates/auth_page.html.

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

V rychlém startu Pythonu se tlačítko pro odhlášení nachází v souboru [Templates/index.html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) .

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` akce kontroleru

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

V předchozích verzích základních šablon ASP.NET `Account` byl kontroler vložen do webové aplikace. To už neplatí, protože kontroler je teď součástí balíčku NuGet **Microsoft. identity. Web. UI** . Podrobnosti najdete v tématu [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

- Nastaví identifikátor URI pro přesměrování OpenID na `/Account/SignedOut` tak, aby se řadič zavolal zpátky, když Azure AD dokončí odhlášení.
- Volání `Signout()` , která umožňují middlewaru OpenID Connect, se obrátit na koncový bod Microsoft Identity Platform `logout` . Koncový bod pak:

  - Vymaže soubor cookie relace z prohlížeče.
  - Volá zpět adresu URL pro odhlášení. Ve výchozím nastavení zobrazuje adresa URL pro odhlášení [SignedOut.cshtml.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Pages/Account/SignedOut.cshtml.cs)stránku zobrazení se znaménkem. Tato stránka je také k dispozici jako součást MIcrosoft. identity. Web.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET se odhlášení aktivuje z `SignOut()` metody na řadiči (např [. AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Tato metoda není součástí ASP.NET architektury, a to v rozporu s tím, co se stane v ASP.NET Core. Její

- Pošle výzvu k odhlášení OpenID.
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

V jazyce Java se odhlášení zpracovává voláním `logout` koncového bodu Microsoft Identity Platform přímo a zadáním `post_logout_redirect_uri` hodnoty. Podrobnosti najdete v tématu [AuthPageController. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

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

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Zachytávání volání `logout` koncového bodu

Identifikátor URI po odhlášení umožňuje aplikacím, aby se účastnili globálního odhlašování.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Middleware ASP.NET Core OpenID Connect umožňuje vaší aplikaci zachytit volání `logout` koncového bodu Microsoft Identity Platform poskytnutím události OpenID Connect s názvem `OnRedirectToIdentityProviderForSignOut` . Tím se automaticky zpracuje Microsoft. identity. Web (který vymaže účty v případě, kdy webová aplikace volá webová rozhraní API).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET delegujete na middleware, abyste mohli spustit odhlášení a vymazat soubor cookie relace:

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

V rychlém startu v Java se identifikátor URI přesměrování po odhlášení zobrazí jenom stránka index.html.

# <a name="python"></a>[Python](#tab/python)

V rychlém startu Pythonu se identifikátor URI přesměrování po odhlášení zobrazí jenom stránka index.html.

---

## <a name="protocol"></a>Protokol

Pokud chcete získat další informace o odhlášení, přečtěte si dokumentaci k protokolu, která je k dispozici v [otevřeném ID připojit](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [přejděte do produkčního](scenario-web-app-sign-user-production.md)prostředí.