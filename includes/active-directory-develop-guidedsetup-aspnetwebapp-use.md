---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 167fccd8e0546bc8f5ac1b24489cae68cc14191f
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843036"
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Přidání kontroleru pro zpracování požadavků na přihlášení a odhlášení

Tento krok ukazuje, jak vytvořit nový kontroler zveřejňovat metody přihlašování a odhlašování.

1.  Klikněte pravým tlačítkem myši `Controllers` a pak zvolte položku `Add` > `Controller`
2.  Vyberte `MVC (.NET version) Controller – Empty`.
3.  Klikněte na tlačítko *přidat*
4.  Pojmenujte ji `HomeController` a klikněte na tlačítko *přidat*
5.  Přidat *OWIN* odkazy na třídu:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Přidejte tyto dvě metody níže pro zpracování přihlašování a odhlašování k řadiči pomocí inicializace výzvu ověřování prostřednictvím kódu:
    
    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Vytvoření aplikace domovskou stránku pro přihlášení prostřednictvím tlačítka přihlašování uživatelů

V sadě Visual Studio vytvořte nové zobrazení, do kterého přidáte tlačítko pro přihlášení a ve kterém se po ověření zobrazí informace o uživateli:

1.  Klikněte pravým tlačítkem myši `Views\Home` a pak zvolte položku `Add View`
2.  Pojmenujte ji `Index`.
3.  Do souboru přidejte následující kód HTMP, který obsahuje tlačítko pro přihlášení:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Další informace
> Tato stránka přidá tlačítko přihlášení ve formátu SVG s černým pozadím:<br/>![Přihlásit se účtem Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Pro tlačítka Další přihlásit, přejděte prosím [tuto stránku](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "pokyny pro Branding").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Přidání kontroleru pro zobrazení deklarací identity uživatele
Tento kontroler demonstruje použití atributu `[Authorize]` k ochraně kontroleru. Tento atribut omezuje přístup ke kontroleru tím, že povolí pouze ověřené uživatele. Následující kód používá atribut pro zobrazení deklarací identity uživatelů, které byly načteny v rámci přihlášení.

1.  Klikněte pravým tlačítkem myši `Controllers` složky: `Add` > `Controller`
2.  Vyberte `MVC {version} Controller – Empty`.
3.  Klikněte na tlačítko *přidat*
4.  Pojmenujte ji `ClaimsController`
5.  Nahraďte kód třídy kontroleru kód uvedený níže – tím se přidá `[Authorize]` atribut pro třídu:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Další informace
> Protože je použit atribut `[Authorize]`, všechny metody tohoto kontroleru lze spustit pouze v případě, že je uživatel ověřen. Pokud uživatel není ověřen a pokusí o přístup k řadiči, bude OWIN zahájit výzvu ověřování a nutí uživatele k ověření. Výše uvedený kód vyhledá v seznamu deklarací pro atributy konkrétního uživatele zahrnuté v tokenu Id uživatele. Tyto atributy zahrnují celé jméno uživatele a jeho uživatelské jméno, ale také subjekt globálního identifikátoru uživatele. Obsahuje také *ID tenanta*, které představuje ID organizace uživatele. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Vytvoření zobrazení pro zobrazení deklarací identity uživatele

V sadě Visual Studio vytvořte nové zobrazení, ve kterém se budou zobrazovat deklarace identity uživatele na webové stránce:

1.  Klikněte pravým tlačítkem myši `Views\Claims` složky a: `Add View`
2.  Pojmenujte ji `Index`.
3.  Přidejte do souboru následující kód HTML:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```
