---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 98bb86be1e1d0dccb5a76b91489e664ee4a30765
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943585"
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Přidat řadič pro zpracování požadavků na přihlášení a odhlášení

Tento krok ukazuje, jak vytvořit nový řadič vystavit metody přihlášení a odhlášení.

1.  Klikněte pravým tlačítkem `Controllers` složky a vyberte `Add` > `Controller`
2.  Vyberte `MVC (.NET version) Controller – Empty`.
3.  Klikněte na tlačítko *přidat*
4.  Pojmenujte ji `HomeController` a klikněte na tlačítko *přidat*
5.  Přidat *OWIN* odkazy na třídu:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Přidejte tyto dvě metody níže zpracování přihlášení a odhlášení řadiče pomocí inicializace výzvu ověřování prostřednictvím kódu:
    
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

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Vytvoření aplikace domovskou stránku pro přihlášení uživatele prostřednictvím přihlášení tlačítko

V sadě Visual Studio vytvořte nové zobrazení přidat tlačítko přihlásit a zobrazit informace o uživateli po ověření:

1.  Klikněte pravým tlačítkem `Views\Home` složky a vyberte `Add View`
2.  Pojmenujte ji `Index`.
3.  Přidejte následující kód HTML, která obsahuje tlačítko přihlášení, do souboru:

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
> Tato stránka přidá tlačítko přihlášení ve formátu SVG s černým pozadí:<br/>![Přihlaste se pomocí Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Tlačítka Další přihlášení, přejděte [tuto stránku](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Branding pokyny").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Přidat řadič zobrazíte deklarací identity uživatele
Tento řadič ukazuje použití `[Authorize]` atribut k ochraně řadiči. Tento atribut omezuje přístup k řadiči tím, že se pouze ověřené uživatele. Následující kód využívá atribut zobrazíte deklarace identity uživatelů, které byly získány v rámci přihlášení.

1.  Klikněte pravým tlačítkem `Controllers` složky: `Add` > `Controller`
2.  Vyberte `MVC {version} Controller – Empty`.
3.  Klikněte na tlačítko *přidat*
4.  Název `ClaimsController`
5.  Nahraďte kód vaší třídy kontroleru pomocí kódu níže – tím se přidá `[Authorize]` atribut třídy:

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
> Protože se používá `[Authorize]` atribut, všechny metody tohoto řadiče lze spustit pouze pokud je uživatel ověřený. Pokud uživatel není ověřen a pokusí o přístup k kontroleru, bude OWIN zahájit výzvu ověřování a nutí uživatele k ověření. Výše uvedený kód zjistí seznam deklarací identity pro konkrétního uživatele atributy součástí token Id uživatele. Tyto atributy zahrnují úplné uživatelské jméno a uživatelské jméno, jakož i subjektu identifikátor globální uživatele. Obsahuje taky *ID klienta*, která reprezentuje ID pro uživatele organizaci. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Vytvoření zobrazení deklaracích identity uživatele

V sadě Visual Studio vytvořte nové zobrazení zobrazíte deklaracích identity uživatele na webové stránce:

1.  Klikněte pravým tlačítkem `Views\Claims` složky a: `Add View`
2.  Pojmenujte ji `Index`.
3.  Do souboru přidejte následující kód HTML:

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
