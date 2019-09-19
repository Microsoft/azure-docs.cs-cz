---
title: Webová aplikace, která přihlašuje uživatele (přihlásit se) – Microsoft Identity Platform
description: Informace o tom, jak vytvořit webovou aplikaci, která přihlašuje uživatele (přihlásit se)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086458"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Webová aplikace, která přihlašuje uživatele – přihlásit se

Naučte se, jak přidat přihlášení k kódu pro vaši webovou aplikaci, která přihlašuje uživatele.

## <a name="sign-in"></a>Přihlášení

Kód, který jsme prozkoumali v [konfiguraci kódu předchozí aplikace](scenario-web-app-sign-user-app-configuration.md) v článku, je vše, co potřebujete k implementaci přihlášení.
Jakmile se uživatel přihlásí do vaší aplikace, pravděpodobně budete chtít povolit, aby se odhlásili. ASP.NET Core zpracovává pro vás registraci.

## <a name="what-sign-out-involves"></a>Co zahrnuje i odhlášení

Odhlášení z webové aplikace je o více než odebrání informací o přihlášeném účtu ze stavu webové aplikace.
Webová aplikace musí také přesměrovat uživatele na koncový bod platformy `logout` Microsoft identity, aby se odhlásily. Když vaše webová aplikace přesměruje uživatele na `logout` koncový bod, tento koncový bod vymaže relaci uživatele z prohlížeče. Pokud vaše aplikace nepřešla do `logout` koncového bodu, uživatel se znovu ověří do vaší aplikace bez zadání přihlašovacích údajů, protože by měl platnou relaci jednotného přihlašování s koncovým bodem Microsoft Identity Platform.

Další informace najdete v části [odeslání žádosti o přihlášení](v2-protocols-oidc.md#send-a-sign-out-request) na [platformě Microsoft Identity Platform a](v2-protocols-oidc.md) v Koncepční dokumentaci k protokolu OpenID Connect.

### <a name="application-registration"></a>Registrace aplikace

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Během registrace aplikace jste zaregistrovali **identifikátor URI po odhlášení**. V našem `https://localhost:44321/signout-oidc` kurzu jste se zaregistrovali v poli **Adresa URL pro odhlášení** v části **Upřesnit nastavení** na stránce **ověřování** . Podrobnosti najdete v tématu [Registrace aplikace WebApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp) .

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Během registrace aplikace jste zaregistrovali **identifikátor URI po odhlášení**. V našem `https://localhost:44308/Account/EndSession` kurzu jste se zaregistrovali v poli **Adresa URL pro odhlášení** v části **Upřesnit nastavení** na stránce **ověřování** . Podrobnosti najdete v tématu [Registrace aplikace WebApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet) .

# <a name="javatabjava"></a>[Java](#tab/java)

Během registrace aplikace zaregistrujete **identifikátor URI odhlašovacího příspěvku**. V našem `http://localhost:8080/msal4jsample/` kurzu jste se zaregistrovali v poli **Adresa URL pro odhlášení** v části **Upřesnit nastavení** na stránce **ověřování** .

# <a name="pythontabpython"></a>[Python](#tab/python)

Při registraci aplikace nemusíte registrovat adresu URL pro odhlášení. Ukázka neimplementuje globální odhlašování

---

### <a name="sign-out-button"></a>Tlačítko pro odhlášení

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET Core se tlačítko odhlášení zveřejňuje v `Views\Shared\_LoginPartial.cshtml` a zobrazí se, jenom když je k dispozici ověřený účet (tj. když se uživatel dřív přihlásil).

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

V ASP.NET MVC se tlačítko odhlášení zveřejňuje v `Views\Shared\_LoginPartial.cshtml` a zobrazí se, jenom když je k dispozici ověřený účet (tj. když se uživatel dřív přihlásil).

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

V rychlém startu Pythonu se v souboru [Templates/Display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20) nachází tlačítko odhlášení.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`akce kontroleru

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

V ASP.NET se po `SignOut` stisknutí **tlačítka pro odhlášení ve** webové aplikaci aktivuje akce na `AccountController` řadiči. V předchozích verzích základních šablon `Account` ASP.NET byl kontroler vložen do webové aplikace, ale už se nejedná o případ, kdy je teď součástí samotného ASP.NET Core Frameworku.

Kód pro `AccountController` je k dispozici v úložišti ASP.NET Core na adrese z [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Řízení účtu:

- Nastaví identifikátor URI pro přesměrování OpenID `/Account/SignedOut` na tak, aby se řadič zavolal zpátky, když Azure AD provedl odhlášení.
- Volání `Signout()`, která umožňují middlewaru OpenIdConnect kontaktovat koncový bod Microsoft identity `logout` Platform, který:

  - Vymaže soubor cookie relace z prohlížeče a
  - Nakonec zavolá zpět **odhlašovací adresu URL**, která ve výchozím nastavení zobrazuje [odhlášenou](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) stránku zobrazení odhlásilo. html také jako součást ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET se odhlášení aktivuje z `SignOut()` metody na řadiči (například instance AccountController). Tato metoda není součástí ASP.NET architektury (na rozdíl od toho, co se stane v ASP.NET Core). Nepoužívá `async`a je to proto:

- pošle výzvu k odhlášení OpenId.
- Vymaže mezipaměť.
- přesměrování na stránku, kterou chce

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

V jazyce Java se odhlášení zpracovává voláním koncového bodu pro odhlášení platformy Microsoft Identity Platform a zadáním post_logout_redirect_uri.

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

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Zachytávání volání `logout` koncového bodu

Identifikátor URI po odhlášení umožňuje aplikacím, aby se účastnili globálního odhlašování.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Middleware ASP.NET Core OpenIdConnect umožňuje vaší aplikaci zachytit volání koncového bodu Microsoft Identity Platform `logout` tím, že poskytuje událost OpenIdConnect s názvem. `OnRedirectToIdentityProviderForSignOut` Příklad toho, jak se přihlásit k odběru této události (pro vymazání mezipaměti tokenu), najdete v tématu [Microsoft. identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)

```CSharp
               // Handling the global sign-out
                options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
                {
                    // Forget about the signed-in user
                };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

V ASP.NET delegujete na middleware, aby bylo možné spustit odhlášení, a vymazat soubor cookie relace:

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

V našem rychlém startu v jazyce Java se k identifikátoru URI přesměrování po odhlášení zobrazí jenom stránka index. html. 

# <a name="pythontabpython"></a>[Python](#tab/python)

V rychlém startu pro Python zobrazuje identifikátor URI přesměrování po odhlášení jenom stránku index. html.

---

## <a name="protocol"></a>Protocol

Pokud chcete získat další informace o odhlášení, přečtěte si dokumentaci k protokolu, která je k dispozici v [otevřeném ID připojit](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přesunout do produkčního prostředí](scenario-web-app-sign-user-production.md)
