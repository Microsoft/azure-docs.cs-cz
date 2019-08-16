---
title: Začínáme s webovým serverem Azure AD v 2.0 ASP.NET | Microsoft Docs
description: Implementace přihlášení Microsoftu v řešení ASP.NET s použitím tradiční aplikace založené na webovém prohlížeči pomocí standardu OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: fce6ab2c5068d1628860356a9df0dd33c73948b3
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511895"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Přidání přihlašování do webové aplikace ASP.NET pomocí Microsoftu

Tato příručka ukazuje, jak implementovat přihlášení do Microsoftu pomocí řešení ASP.NET MVC s tradiční aplikací založenou na webovém prohlížeči pomocí OpenID Connect.

Na konci tohoto průvodce bude aplikace moci přijímat přihlašování osobních účtů, například outlook.com, live.com a dalších. Tyto účty také zahrnují pracovní a školní účty z libovolné společnosti nebo organizace, která je integrovaná s Azure Active Directory.

> Tato příručka vyžaduje Visual Studio 2019.  Nemáte ji?  [Stáhnout Visual Studio 2019 zdarma](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak ukázková aplikace vygenerovaná touto příručkou funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Ukázková aplikace, kterou vytvoříte, je založená na scénáři, ve kterém používáte prohlížeč pro přístup k webu ASP.NET, který požaduje uživatele k ověření prostřednictvím přihlašovacího tlačítka. V tomto scénáři se většina aktivit vykreslení webové stránky odehrává na straně serveru.

## <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovny:

|Knihovna|Popis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware, který aplikaci umožňuje použít OpenIdConnect pro ověřování|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware, který aplikaci umožňuje udržovat relaci uživatele pomocí souborů cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Aplikacím založeným na specifikaci OWIN umožňuje spouštět se ve službě IIS pomocí kanálu požadavků ASP.NET|

## <a name="set-up-your-project"></a>Nastavení projektu

V této části se dozvíte, jak nainstalovat a nakonfigurovat kanál ověřování prostřednictvím middlewaru OWIN v projektu ASP.NET pomocí OpenID Connect.

> Chcete raději stáhnout tuto ukázkovou aplikaci Visual Studio? [Stáhněte si projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) a před spuštěním napřed proveďte [konfigurační krok](#register-your-application) , abyste mohli nakonfigurovat ukázku kódu.

### <a name="create-your-aspnet-project"></a>Vytvoření projektu ASP.NET

1. In Visual Studio: `File` > `New` > `Project`
2. V *části C#Visual \Web*vyberte `ASP.NET Web Application (.NET Framework)`.
3. Pojmenujte aplikaci a klikněte na *OK* .
4. Vyberte `Empty` a zaškrtněte políčko pro přidání `MVC` odkazů.

## <a name="add-authentication-components"></a>Přidat komponenty ověřování

1. In Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Přidejte *balíčky NuGet middlewaru OWIN* tak, že do okna konzoly Správce balíčků zadáte toto:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>O těchto knihovnách
> Knihovny uvedené výše umožňují jednotné přihlašování pomocí OpenID Connect přes ověřování na základě souborů cookie. Po dokončení ověřování a odeslání tokenu, který reprezentuje uživatele, do aplikace, vytvoří middleware OWIN soubor cookie relace. Prohlížeč pak tento soubor cookie použije u dalších požadavků, takže uživatel nebude muset heslo znovu zadávat a nebude nutné provádět žádné další ověření.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurace ověřovacího kanálu

Následující postup slouží k vytvoření třídy OWIN middleware pro konfiguraci ověřování OpenID Connect. Tato třída se spustí automaticky při spuštění procesu IIS.

> [!TIP]
> Pokud projekt nemá soubor `Startup.cs` v kořenové složce:
> 1. Pravým tlačítkem myši klikněte na kořenovou složku projektu: >`Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Pojmenujte ji `Startup.cs`
>
>> Zkontrolujte, že vybraná třída je počáteční třídou OWIN a nikoli standardní třídou C#. Ověříte si to tak, že nad oborem názvů bude uvedeno `[assembly: OwinStartup(typeof({NameSpace}.Startup))]`.

1. Přidejte odkazy *Owin* a *Microsoft. IdentityModel* na `Startup.cs`:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Nahraďte třídu po spuštění následujícím kódem:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> Nastavení `ValidateIssuer = false` je zjednodušení pro tento rychlý Start. Ve skutečných aplikacích, které potřebujete k ověření vystavitele, se podívejte na ukázky, které vám pochopí, jak to udělat.

<!--start-collapse-->
> ### <a name="more-information"></a>Další informace
> Parametry, které zadáte v *OpenIDConnectAuthenticationOptions*, slouží jako souřadnice pro komunikaci aplikace s Azure AD. Vzhledem k tomu, že middleware OpenID Connect používá soubory cookie na pozadí, je také nutné nastavit ověřování souborů cookie, jak je uvedeno výše v následujícím kódu. Hodnota *ValidateIssuer* dává middlewaru OpenIDConnect pokyn, aby neomezoval přístup na jednu konkrétní organizaci.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Přidání kontroleru pro zpracování žádostí o přihlášení a odhlášení

Tento krok ukazuje, jak vytvořit nový kontroler pro vystavování metod přihlášení a odhlášení.

1.  Klikněte pravým `Controllers` tlačítkem na složku a vyberte`Add` > `Controller`
2.  Vyberte `MVC (.NET version) Controller – Empty`.
3.  Klikněte na *Přidat* .
4.  Pojmenujte ji `HomeController` a klikněte na *Přidat* .
5.  Přidat odkazy *Owin* do třídy:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Pomocí následujících dvou metod můžete zpracovat přihlášení a odhlásit se k řadiči tím, že inicializujete ověřovací výzvu prostřednictvím kódu:
    
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

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Vytvoření domovské stránky aplikace pro přihlášení uživatelů prostřednictvím přihlašovacího tlačítka

V sadě Visual Studio vytvořte nové zobrazení, do kterého přidáte tlačítko pro přihlášení a ve kterém se po ověření zobrazí informace o uživateli:

1.  Klikněte pravým `Views\Home` tlačítkem na složku a vyberte`Add View`
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
> Tato stránka přidá do formátu SVG tlačítko pro přihlášení s černým pozadím:<br/>![Přihlásit se účtem Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Další tlačítka pro přihlášení najdete v pokynech pro(https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "značky") [této stránky].
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Přidání kontroleru pro zobrazení deklarací identity uživatele
Tento kontroler demonstruje použití atributu `[Authorize]` k ochraně kontroleru. Tento atribut omezuje přístup ke kontroleru tím, že povolí pouze ověřené uživatele. Následující kód používá atribut k zobrazení deklarací identity uživatele, které byly načteny jako součást přihlášení.

1.  Pravým tlačítkem `Controllers` myši klikněte na složku:`Add` > `Controller`
2.  Vyberte `MVC {version} Controller – Empty`.
3.  Klikněte na *Přidat* .
4.  Pojmenujte ji `ClaimsController`
5.  Nahraďte kód vaší třídy kontroleru kódem níže – přidá `[Authorize]` atribut do třídy:

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
> Protože je použit atribut `[Authorize]`, všechny metody tohoto kontroleru lze spustit pouze v případě, že je uživatel ověřen. Pokud uživatel není ověřený a pokusí se získat přístup k řadiči, OWIN spustí výzvu k ověření a vynutí ověření uživatele. Výše uvedený kód vyhledá v seznamu deklarací identity pro konkrétní atributy uživatele zahrnuté v tokenu ID uživatele. Tyto atributy zahrnují celé jméno uživatele a jeho uživatelské jméno, ale také subjekt globálního identifikátoru uživatele. Obsahuje také *ID tenanta*, které představuje ID organizace uživatele. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Vytvoření zobrazení pro zobrazení deklarací identity uživatele

V sadě Visual Studio vytvořte nové zobrazení, ve kterém se budou zobrazovat deklarace identity uživatele na webové stránce:

1.  Pravým tlačítkem `Views\Claims` myši klikněte na složku a postupujte takto:`Add View`
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

## <a name="register-your-application"></a>Zaregistrujte svoji aplikaci.

K registraci aplikace a přidání informací o registraci vaší aplikace do vašeho řešení máte dvě možnosti:

### <a name="option-1-express-mode"></a>Možnost 1: Expresní režim

Aplikaci můžete rychle zaregistrovat pomocí následujícího postupu:

1. Přejít na nové podokno [Azure Portal-registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) .
1. Zadejte název vaší aplikace a klikněte na **Zaregistrovat**.
1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.

### <a name="option-2-advanced-mode"></a>Možnost 2: Rozšířený režim

Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:

1. Přejít na Visual Studio a:
   1. v Průzkumník řešení vyberte projekt a podívejte se na okno Vlastnosti (Pokud nevidíte okno Vlastnosti, stiskněte F4).
   1. Změňte povolený protokol `True`SSL na.
   1. Klikněte pravým tlačítkem myši na projekt v aplikaci Visual Studio, zvolte možnost **vlastnosti**a kartu **Web** . V oddílu *servery* změňte *adresu URL projektu* tak, aby byla adresa URL protokolu SSL.
   1. Zkopírujte adresu URL protokolu SSL. Tato adresa URL se přidá do seznamu adres URL pro přesměrování v seznamu adres URL pro přesměrování v dalším kroku:<br/><br/>![Vlastnosti projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
1. Vyberte **Nová registrace**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
   1. V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `ASPNET-Tutorial`.
   1. Do pole **Adresa URL odpovědi**přidejte adresu URL protokolu SSL, kterou jste zkopírovali ze `https://localhost:44368/`sady Visual Studio v kroku 1 (pro instanci), a klikněte na **zaregistrovat**.
1. Vyberte nabídku **Ověřování**, v části **Implicitní udělení oprávnění** nastavte **Tokeny ID** a pak vyberte **Uložit**.
1. Do složky `web.config` umístěné v kořenové složce v části `configuration\appSettings`přidejte následující:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Nahraďte `ClientId` ID aplikace, kterou jste právě zaregistrovali.
1. Nahraďte `redirectUri` adresou URL vašeho projektu SSL.

## <a name="test-your-code"></a>Testování kódu

Chcete-li otestovat aplikaci v aplikaci Visual Studio, stiskněte klávesu **F5** ke spuštění projektu. V prohlížeči se otevře umístění http://<span></span>localhost: {port} a zobrazí se tlačítko **Přihlásit se účtem Microsoft** . Kliknutím na tlačítko spusťte proces přihlášení.

Až budete připraveni spustit test, použijte účet Microsoft Azure Active Directory (Azure AD) (pracovní nebo školní účet) nebo osobní účet Microsoft (<span>Live).</span> com nebo <span>Outlook.</span> com) pro přihlášení.

![Přihlásit se účtem Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Přihlaste se ke svému účet Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Zobrazit výsledky aplikace

Po přihlášení se uživatel přesměruje na domovskou stránku vašeho webu. Domovská stránka je adresa URL HTTPS, která je zadaná v informacích o registraci vaší aplikace na portálu pro registraci aplikací Microsoftu. Domovská stránka obsahuje uvítací zprávu *"Hello \<User >,"* odkaz pro odhlášení a odkaz k zobrazení deklarací identity uživatele. Odkaz na deklarace identity uživatele přejde na kontroler *deklarací identity* , který jste vytvořili dříve.

### <a name="browse-to-see-the-users-claims"></a>Procházet a zobrazit deklarace identity uživatele

Chcete-li zobrazit deklarace identity uživatele, vyberte odkaz pro přechod k zobrazení kontroleru, které je k dispozici pouze pro ověřené uživatele.

#### <a name="view-the-claims-results"></a>Zobrazení výsledků deklarací identity

Po procházení zobrazení řadiče by se měla zobrazit tabulka, která obsahuje základní vlastnosti uživatele:

|Vlastnost |Value |Popis |
|---|---|---|
|**Název** |Celé jméno uživatele | Křestní jméno a příjmení uživatele.
|**Uživatelské jméno** |uživatelský<span>@domain.com</span> | Uživatelské jméno, které se používá k identifikaci uživatele.
|**Subject** |Subject |Řetězec, který jedinečně identifikuje uživatele napříč webem.|
|**ID tenanta** |Guid | **Identifikátor GUID** , který jedinečně představuje organizaci Azure AD pro uživatele.|

Kromě toho by se měla zobrazit tabulka všech deklarací identity, které jsou v žádosti o ověření. Další informace najdete v [seznamu deklarací identity, které jsou v tokenu Azure AD ID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Otestujte přístup k metodě, která má atribut autorizace (volitelné).

K otestování přístupu jako anonymní uživatel k řadiči chráněnému pomocí `Authorize` atributu použijte následující postup:

1. Vyberte odkaz pro odhlášení uživatele a dokončení procesu odhlášení.
2. V prohlížeči zadejte http://<span></span>localhost: {port}/deklarace identity pro přístup k řadiči, který je chráněný `Authorize` atributem.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Po přístupu k chráněnému kontroleru se očekávaly výsledky.

Zobrazí se výzva k ověření, že se má použít zobrazení chráněného řadiče.

## <a name="advanced-options"></a>Pokročilá nastavení

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Ochrana celého webu
Chcete-li chránit celý web, přidejte `AuthorizeAttribute` do souboru **Global. asax** atribut `GlobalFilters` do filtru v `Application_Start` metodě:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Omezení toho, kdo se může přihlásit k aplikaci

Ve výchozím nastavení, když sestavíte aplikaci vytvořenou v této příručce, bude aplikace přijímat přihlašování osobních účtů (včetně outlook.com, live.com a dalších) a také pracovních a školních účtů z jakékoli společnosti nebo organizace, která je integrovaná s Azure Active Directory. Toto je doporučená možnost pro aplikace SaaS.

K dispozici je více možností pro omezení přístupu uživatelů k aplikaci.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Možnost 1: Omezení přihlášení k aplikaci na uživatele pouze z instance Active Directory jedné organizace (jeden tenant)

Tato možnost je běžným scénářem pro obchodní *aplikace*: Pokud chcete, aby aplikace přijímala přihlášení jenom z účtů, které patří do konkrétní instance Azure Active Directory (včetně *účtů Guest* této instance), udělejte toto:

1. V souboru **Web. config** změňte hodnotu `Tenant` parametru z `Common` na `contoso.onmicrosoft.com`název tenanta organizace, například.
2. Ve [třídě Owin Startup](#configure-the-authentication-pipeline)nastavte `ValidateIssuer` argument na `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Možnost 2: Omezení přístupu k aplikaci uživatelům v konkrétním seznamu organizací

Přístup pro přihlášení můžete omezit jenom na uživatelské účty v organizaci Azure AD, které jsou v seznamu povolených organizací:
1. Ve [třídě Owin Startup](#configure-the-authentication-pipeline)nastavte `ValidateIssuer` argument na `true`.
2. Nastavte hodnotu `ValidIssuers` parametru na seznam povolených organizací.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Možnost 3: Použití vlastní metody k ověření vystavitelů

Můžete implementovat vlastní metodu pro ověření vystavitelů pomocí parametru **IssuerValidator** . Další informace o tom, jak tento parametr použít, naleznete v tématu [Třída TokenValidationParameters](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Další postup

Přečtěte si o webových aplikacích, které volají webová rozhraní API:.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Postup vytvoření aplikace použité v tomto rychlém startu

> [!div class="nextstepaction"]
> [Webové aplikace, které volají webová rozhraní API]( https://aka.ms/msal-net-authorization-code)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)