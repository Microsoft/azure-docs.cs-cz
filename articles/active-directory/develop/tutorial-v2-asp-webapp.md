---
title: 'Kurz: Vytvoření webové aplikace v ASP.NET, která používá Microsoft Identity Platform pro ověřování | Azure'
titleSuffix: Microsoft identity platform
description: V tomto kurzu vytvoříte webovou aplikaci v ASP.NET, která pro povolení přihlášení uživatele používá Microsoft Identity Platform a middleware OWIN.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 9ff43202bdace577024413c9cc177de2997a0ad5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627937"
---
# <a name="tutorial-add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Kurz: přidání přihlášení do Microsoftu do webové aplikace v ASP.NET

Tato příručka ukazuje, jak implementovat přihlášení do Microsoftu prostřednictvím řešení ASP.NET MVC pomocí tradiční aplikace založené na webovém prohlížeči a připojení OpenID.

Po dokončení této příručky bude aplikace moci přijímat přihlášení osobních účtů z podobných outlook.com a live.com. Pracovní a školní účty z libovolné společnosti nebo organizace, která je integrovaná s platformou Microsoft Identity Platform, se budou moct přihlašovat do vaší aplikace.

V tomto kurzu:

> [!div class="checklist"]
> * Vytvoření projektu *webové aplikace v ASP.NET* v aplikaci Visual Studio
> * Přidejte součásti middlewaru pro Open Web Interface for .NET (OWIN).
> * Přidat kód pro podporu přihlášení a odhlášení uživatele
> * Registrace aplikace v Azure Portal
> * Otestování aplikace

## <a name="prerequisites"></a>Předpoklady

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) s nainstalovanou úlohou **vývoj pro ASP.NET a web**

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak ukázková aplikace vygenerovaná touto příručkou funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Ukázková aplikace, kterou vytvoříte, je založená na scénáři, ve kterém používáte prohlížeč k přístupu k webu ASP.NET, který vyzývá uživatele k ověření prostřednictvím přihlašovacího tlačítka. V tomto scénáři se většina aktivit vykreslení webové stránky odehrává na straně serveru.

## <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovny:

|Knihovna|Popis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware, který aplikaci umožňuje použít OpenIdConnect pro ověřování|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware, který umožňuje aplikaci udržovat relaci uživatelů pomocí souborů cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware, který umožňuje, aby se aplikace založené na OWIN spouštěly na Internetová informační služba (IIS) pomocí kanálu žádosti ASP.NET|

## <a name="set-up-your-project"></a>Nastavení projektu

Tato část popisuje, jak nainstalovat a nakonfigurovat kanál ověřování prostřednictvím middlewaru OWIN v projektu ASP.NET pomocí OpenID Connect.

> Chcete raději stáhnout tuto ukázkovou aplikaci Visual Studio? [Stáhněte si projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) a přejděte k [registraci aplikace](#register-your-application) , abyste před spuštěním nakonfigurovali ukázku kódu.

### <a name="create-your-aspnet-project"></a>Vytvoření projektu ASP.NET

1. V aplikaci Visual Studio: Přejít na **soubor**  >  **Nový**  >  **projekt**.
2. V části **Visual C#\Web** vyberte **Webová aplikace ASP.NET (.NET Framework)**.
3. Pojmenujte aplikaci a vyberte **OK**.
4. Vyberte **prázdné**a potom zaškrtněte políčko pro přidání odkazů **MVC** .

## <a name="add-authentication-components"></a>Přidat komponenty ověřování

1. V aplikaci Visual Studio: přejít do **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.
2. Přidejte *balíčky NuGet middlewaru OWIN* tak, že do okna konzoly Správce balíčků zadáte toto:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

### <a name="about-these-libraries"></a>O těchto knihovnách
Tyto knihovny umožňují jednotné přihlašování (SSO) pomocí OpenID připojení prostřednictvím ověřování založeného na souborech cookie. Po dokončení ověřování a odeslání tokenu, který reprezentuje uživatele, do aplikace, vytvoří middleware OWIN soubor cookie relace. Prohlížeč pak tento soubor cookie použije u dalších požadavků, aby ho uživatel nemusel znovu zadávat a nemuseli by provádět žádné další ověření.

## <a name="configure-the-authentication-pipeline"></a>Konfigurace ověřovacího kanálu

Následující kroky slouží k vytvoření třídy OWIN middleware pro konfiguraci ověřování OpenID Connect. Tato třída se spustí automaticky při spuštění procesu IIS.

> [!TIP]
> Pokud projekt nemá soubor `Startup.cs` v kořenové složce:
> 1. Klikněte pravým tlačítkem na kořenovou složku projektu a pak vyberte **Přidat**  >  **novou položku**  >  **Owin po spuštění třídy**.<br/>
> 2. Pojmenujte ho **Startup.cs**.
>
>> Ujistěte se, že vybraná třída je třída OWIN Startup a ne standardní třída jazyka C#. Potvrďte to tak, že ověříte, že jste viděli [assembly: OwinStartup (typeof ({NameSpace}. Po spuštění))] nad oborem názvů.

1. Přidejte odkazy *Owin* a *Microsoft. IdentityModel* na Startup.cs:

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

2. Nahraďte počáteční třídu následujícím kódem:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Microsoft identity platform endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
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
> Nastavení `ValidateIssuer = false` je zjednodušení pro tento rychlý Start. Ve skutečných aplikacích je nutné ověřit vystavitele.
> V ukázkách se dozvíte, jak to udělat.

### <a name="more-information"></a>Další informace

Parametry, které zadáte v *OpenIDConnectAuthenticationOptions* , slouží jako souřadnice pro aplikaci ke komunikaci s platformou Microsoft identity. Vzhledem k tomu, že middleware OpenID Connect používá soubory cookie na pozadí, musíte také nastavit ověřování souborů cookie, jak ukazuje předchozí kód. Hodnota *ValidateIssuer* oznamuje OpenIdConnect, že neomezuje přístup k jedné konkrétní organizaci.

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Přidání kontroleru pro zpracování žádostí o přihlášení a odhlášení

Pokud chcete vytvořit nový kontroler pro vystavování metod přihlášení a odhlášení, postupujte podle těchto kroků:

1.  Klikněte pravým tlačítkem na složku **řadiče** a vyberte **Přidat**  >  **kontroler**.
2.  Vyberte **Kontroler MVC (verze .NET) – prázdný**.
3.  Vyberte **Přidat**.
4.  Pojmenujte ji **HomeController** a pak vyberte **Přidat**.
5.  Přidat odkazy OWIN do třídy:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Přidejte následující dvě metody pro zpracování přihlášení a odhlášení k řadiči pomocí výzvy k ověření:

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

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Vytvoření domovské stránky aplikace pro přihlášení uživatele

V aplikaci Visual Studio vytvořte nové zobrazení, abyste přidali tlačítko pro přihlášení a zobrazili informace o uživateli po ověření:

1.  Pravým tlačítkem myši klikněte na složku **Views\Home** a vyberte **Přidat zobrazení**.
2.  Pojmenujte nový **index**zobrazení.
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

### <a name="more-information"></a>Další informace
 Tato stránka přidá tlačítko pro přihlášení ve formátu SVG s černým pozadím:<br/>![Přihlásit se účtem Microsoft – tlačítko](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Další tlačítka pro přihlášení najdete v [pokynech pro značky](./howto-add-branding-in-azure-ad-apps.md "Pokyny pro branding").

## <a name="add-a-controller-to-display-users-claims"></a>Přidání kontroleru pro zobrazení deklarací identity uživatele
Tento kontroler demonstruje použití atributu `[Authorize]` k ochraně kontroleru. Tento atribut omezuje přístup k řadiči tím, že povoluje pouze ověřené uživatele. Následující kód využívá atribut k zobrazení deklarací identity uživatele, které byly načteny jako součást přihlášení:

1.  Klikněte pravým tlačítkem na složku **Controllers** a pak vyberte **Přidat**  >  **kontroler**.
2.  Vyberte **Kontroler MVC {version} – prázdný**.
3.  Vyberte **Přidat**.
4.  Pojmenujte ho **ClaimsController**.
5.  Nahraďte kód třídy kontroleru následujícím kódem. Tím se `[Authorize]` do třídy přidá atribut:

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

            //You get the user's first and last name below:
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

### <a name="more-information"></a>Další informace
Z důvodu použití `[Authorize]` atributu lze všechny metody tohoto kontroleru spustit pouze v případě, že je uživatel ověřený. Pokud uživatel není ověřený a pokusí se získat přístup k řadiči, OWIN spustí výzvu ověřování a vynutí uživatele k ověření. Předchozí kód vyhledá seznam deklarací pro konkrétní atributy uživatele zahrnuté v tokenu ID uživatele. Mezi tyto atributy patří celé jméno a uživatelské jméno uživatele a také subjekt globálního identifikátoru uživatele. Obsahuje také *ID tenanta*, které představuje ID pro organizaci uživatele.

## <a name="create-a-view-to-display-the-users-claims"></a>Vytvoření zobrazení pro zobrazení deklarací identity uživatele

V sadě Visual Studio vytvořte nové zobrazení, ve kterém se budou zobrazovat deklarace identity uživatele na webové stránce:

1.  Klikněte pravým tlačítkem na složku **Views\Claims** a pak vyberte **Přidat zobrazení**.
2.  Pojmenujte nový **index**zobrazení.
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

## <a name="register-your-application"></a>Registrace aplikace

K registraci aplikace a přidání informací o registraci vaší aplikace do vašeho řešení máte dvě možnosti:

### <a name="option-1-express-mode"></a>Možnost 1: Expresní režim

K rychlé registraci aplikace použijte následující postup:

1. Přejít na nové podokno  [Azure Portal-registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) .
1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jediným kliknutím.

### <a name="option-2-advanced-mode"></a>Možnost 2: rozšířený režim

Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:

1. Otevřete Visual Studio a potom:
   1. v Průzkumník řešení vyberte projekt a zobrazte okno Vlastnosti (Pokud nevidíte okno Vlastnosti stiskněte F4).
   1. Změňte povolený protokol SSL na `True` .
   1. Klikněte pravým tlačítkem myši na projekt v aplikaci Visual Studio, vyberte možnost **vlastnosti**a pak vyberte kartu **Web** . V části **servery** změňte nastavení **adresy URL projektu** na **adresu URL SSL**.
   1. Zkopírujte adresu URL protokolu SSL. Tuto adresu URL přidáte do seznamu adres URL pro přesměrování v seznamu adres URL pro přesměrování v dalším kroku.<br/><br/>![Vlastnosti projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte svůj účet v pravém horním rohu a nastavte svou relaci portálu na klienta služby Azure AD, kterého chcete.
1. Přejít na stránku Microsoft Identity Platform for Developers [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Vyberte **Nová registrace**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
   1. V části **název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, jako je například **ASPNET-tutorial**.
   1. Přidejte adresu URL protokolu SSL, kterou jste zkopírovali ze sady Visual Studio v kroku 1 (například `https://localhost:44368/` ) v **adrese URL odpovědi**, a vyberte možnost **Registrovat**.
1. Vyberte nabídku **ověřování** , v části **implicitní udělení**vyberte **tokeny ID** a pak vyberte **Uložit**.
1. V souboru web.config přidejte následující příkaz, který se nachází v kořenové složce v `configuration\appSettings` části:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Nahraďte `ClientId` ID aplikace, kterou jste právě zaregistrovali.
1. Nahraďte `redirectUri` adresou URL vašeho projektu SSL.

## <a name="test-your-code"></a>Testování kódu

Chcete-li otestovat aplikaci v aplikaci Visual Studio, stiskněte klávesu F5 ke spuštění projektu. Prohlížeč se otevře v umístění http:// <span></span> localhost: {port} a zobrazí se tlačítko **Přihlásit se účtem Microsoft** . Kliknutím na tlačítko spusťte proces přihlášení.

Až budete připraveni spustit test, použijte účet Azure AD (pracovní nebo školní účet) nebo osobní účet Microsoft (<span>Live).</span> com nebo <span>Outlook.</span> com) pro přihlášení.

![Tlačítko Přihlásit se účtem Microsoft zobrazené na přihlašovací stránce prohlížeče v prohlížeči](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Přihlaste se ke svému účet Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Oprávnění a souhlas v koncovém bodu Microsoft Identity Platform

Aplikace, které se integrují se sadou Microsoft Identity Platform, se řídí autorizačním modelem, který poskytuje uživatelům a správcům kontrolu nad tím, jak budou data dostupná. Po ověření uživatele s Microsoft Identity platformou pro přístup k této aplikaci se zobrazí výzva k vyjádření souhlasu s oprávněními požadovanými aplikací ("zobrazit váš základní profil" a "zachovat přístup k datům, ke kterým jste udělili přístup."). Po přijetí těchto oprávnění bude uživatel pokračovat do výsledků aplikace. Uživatel se ale může místo toho zobrazit na stránce **vyžadovat souhlas správce** , pokud nastane jedna z následujících možností:

- Vývojář aplikace přidá jakákoli další oprávnění, která vyžadují **souhlas správce**.
- Nebo je tenant nakonfigurovaný (v **podnikových aplikacích – > uživatelských nastavení**), kde uživatelé nemůžou udělit souhlas s aplikacemi, které přistupují k firemním datům jejich jménem.

Další informace najdete [v tématu oprávnění a souhlas v koncovém bodě Microsoft Identity Platform](./v2-permissions-and-consent.md).

### <a name="view-application-results"></a>Zobrazit výsledky aplikace

Po přihlášení se uživatel přesměruje na domovskou stránku vašeho webu. Domovská stránka je adresa URL protokolu HTTPS zadaná v informacích o registraci aplikace na portálu pro registraci aplikací společnosti Microsoft. Stránka domů obsahuje uvítací zprávu *"Hello \<user> "* , odkaz pro odhlášení a odkaz k zobrazení deklarací identity uživatele. Odkaz na deklarace identity uživatele se připojí k řadiči deklarací identity, který jste vytvořili dříve.

### <a name="view-the-users-claims"></a>Zobrazit deklarace identity uživatele

Chcete-li zobrazit deklarace identity uživatele, vyberte odkaz pro přechod k zobrazení kontroleru, které je k dispozici pouze pro ověřené uživatele.

#### <a name="view-the-claims-results"></a>Zobrazení výsledků deklarací identity

Po procházení zobrazení řadiče by se měla zobrazit tabulka, která obsahuje základní vlastnosti uživatele:

|Vlastnost |Hodnota |Popis |
|---|---|---|
|**Název** |Celé jméno uživatele | Křestní jméno a příjmení uživatele
|**Uživatelské jméno** |uživatelský<span>@domain.com</span> | Uživatelské jméno, které se používá k identifikaci uživatele|
|**Předmět** |Předmět |Řetězec, který jedinečně identifikuje uživatele napříč webem|
|**ID tenanta** |Identifikátor GUID | **Identifikátor GUID** , který jednoznačně představuje organizaci Azure AD pro uživatele|

Kromě toho by se měla zobrazit tabulka všech deklarací identity, které jsou v žádosti o ověření. Další informace najdete v [seznamu deklarací identity, které jsou v tokenu ID](./id-tokens.md).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Otestujte přístup k metodě, která má atribut autorizace (volitelné).

Pokud chcete otestovat přístup jako anonymní uživatel k řadiči, který je chráněný `Authorize` atributem, postupujte takto:

1. Vyberte odkaz pro odhlášení uživatele a dokončete proces odhlašování.
2. V prohlížeči zadejte http:// <span></span> localhost: {port}/deklarace identity pro přístup k řadiči, který je chráněný `Authorize` atributem.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Po přístupu k chráněnému kontroleru se očekávaly výsledky.

Zobrazí se výzva k ověření, že se má použít zobrazení chráněného řadiče.

## <a name="advanced-options"></a>Upřesnit možnosti

### <a name="protect-your-entire-website"></a>Ochrana celého webu

Chcete-li chránit celý web, přidejte do souboru **Global. asax** atribut do `AuthorizeAttribute` `GlobalFilters` filtru v `Application_Start` metodě:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```

### <a name="restrict-who-can-sign-in-to-your-application"></a>Omezení toho, kdo se může přihlásit k aplikaci

Ve výchozím nastavení, když sestavíte aplikaci vytvořenou touto příručkou, bude vaše aplikace přijímat přihlašování osobních účtů (včetně outlook.com, live.com a dalších) a také pracovních a školních účtů z jakékoli společnosti nebo organizace, která je integrovaná s platformou Microsoft identity. Toto je doporučená možnost pro aplikace SaaS.

K dispozici je více možností pro omezení přístupu uživatelů k aplikaci.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Možnost 1: Omezte přístup uživatelů jenom na jednu instanci Active Directory organizace, abyste se mohli přihlásit k vaší aplikaci (jeden tenant).

Tato možnost se často používá pro obchodní *aplikace*: Pokud chcete, aby vaše aplikace přijímala přihlášení jenom z účtů, které patří do konkrétní instance služby Azure AD (včetně *účtů Guest* této instance), postupujte podle následujících kroků:

1. V souboru web.config změňte hodnotu `Tenant` parametru z `Common` na název tenanta organizace, například `contoso.onmicrosoft.com` .
2. Ve [třídě Owin Startup](#configure-the-authentication-pipeline)nastavte `ValidateIssuer` argument na `true` .

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Možnost 2: omezení přístupu uživatelům v konkrétním seznamu organizací

Přístup pro přihlášení můžete omezit jenom na uživatelské účty v organizaci Azure AD, které jsou v seznamu povolených organizací:
1. Ve [třídě Owin Startup](#configure-the-authentication-pipeline)nastavte `ValidateIssuer` argument na `true` .
2. Nastavte hodnotu `ValidIssuers` parametru na seznam povolených organizací.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Možnost 3: použití vlastní metody pro ověření vystavitelů

Můžete implementovat vlastní metodu pro ověření vystavitelů pomocí parametru **IssuerValidator** . Další informace o tom, jak použít tento parametr, naleznete v tématu Třída [TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters) .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Seznamte se s voláním chráněných webových rozhraní API z webových aplikací s využitím platformy Microsoft Identity Platform:

> [!div class="nextstepaction"]
> [Webové aplikace, které volají webová rozhraní API](scenario-web-app-sign-user-overview.md)
