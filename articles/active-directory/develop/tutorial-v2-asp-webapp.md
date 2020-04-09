---
title: Přidání přihlášení k platformě Microsoft identit ASP.NET webové aplikaci
titleSuffix: Microsoft identity platform
description: Implementace přihlášení Microsoftu k řešení ASP.NET pomocí tradiční aplikace založené na webovém prohlížeči a standardu OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 017cc886b3d47a97a29f45979cb49215de31ae0a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880971"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Přidání přihlášení k Microsoftu do ASP.NET webové aplikace

Tato příručka ukazuje, jak implementovat přihlášení k Microsoftprostřednictvím ASP.NET řešení MVC pomocí tradiční webové aplikace založené na prohlížeči a OpenID Connect.

Po dokončení této příručky bude vaše aplikace moci přijímat přihlášení k osobním účtům od outlook.com a live.com. Kromě toho se budou moci k vaší aplikaci přihlásit pracovní a školní účty od jakékoli společnosti nebo organizace integrované s platformou identit Microsoftu.

> Tato příručka vyžaduje Microsoft Visual Studio 2019.  Nemáte ji?  [Stáhněte si Visual Studio 2019 zdarma](https://www.visualstudio.com/downloads/).

>[!NOTE]
> Pokud s platformou microsoftu začínáte, doporučujeme začít s přihlášením k [ASP.NET webové aplikaci platformou Microsoft identit](quickstart-v2-aspnet-webapp.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak ukázková aplikace vygenerovaná touto příručkou funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Ukázková aplikace, kterou vytvoříte, je založena na scénáři, kdy pomocí prohlížeče získáte přístup k webu ASP.NET, který vyzve uživatele k ověření pomocí přihlašovacího tlačítka. V tomto scénáři se většina aktivit vykreslení webové stránky odehrává na straně serveru.

## <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovny:

|Knihovna|Popis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware, který aplikaci umožňuje použít OpenIdConnect pro ověřování|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware, který umožňuje aplikaci udržovat relaci uživatele pomocí souborů cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware, který umožňuje spuštění aplikací založených na OWIN v Internetové informační službě (IIS) pomocí kanálu ASP.NET požadavků|

## <a name="set-up-your-project"></a>Nastavení projektu

Tato část popisuje, jak nainstalovat a nakonfigurovat kanál ověřování prostřednictvím middlewaru OWIN v projektu ASP.NET pomocí OpenID Connect.

> Chcete si místo toho stáhnout projekt sady Visual Studio této ukázky? [Stáhněte si projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) a přeskočte na [Zaregistrovat aplikaci](#register-your-application) a nakonfigurujte ukázku kódu před spuštěním.

### <a name="create-your-aspnet-project"></a>Vytvoření ASP.NET projektu

1. V sadě Visual Studio: Přejděte na **soubor** > **nový** > **projekt**.
2. V části **Visual C#\Web** vyberte **Webová aplikace ASP.NET (.NET Framework)**.
3. Pojmenujte aplikaci a vyberte **OK**.
4. Vyberte **Vyprázdnit**a zaškrtněte políčko pro přidání odkazů **MVC.**

## <a name="add-authentication-components"></a>Přidání ověřovacích součástí

1. V sadě Visual Studio: Přejděte na **nástrojové nástroje** > **Nuget Package Manager** > **Package Manager Console**.
2. Přidejte *balíčky NuGet middlewaru OWIN* tak, že do okna konzoly Správce balíčků zadáte toto:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>O těchto knihovnách
> Tyto knihovny umožňují jednotné přihlašování (SSO) pomocí OpenID Connect prostřednictvím ověřování založeného na souborech cookie. Po dokončení ověřování a odeslání tokenu, který reprezentuje uživatele, do aplikace, vytvoří middleware OWIN soubor cookie relace. Prohlížeč pak používá tento soubor cookie na následné požadavky, takže uživatel nemusí znovu zadávat heslo a není nutné žádné další ověření.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurace kanálu ověřování

Následující kroky slouží k vytvoření třídy Middleware Startup OWIN pro konfiguraci ověřování OpenID Connect. Tato třída se spustí automaticky při spuštění procesu iis.

> [!TIP]
> Pokud projekt nemá soubor `Startup.cs` v kořenové složce:
> 1. Klepněte pravým tlačítkem myši na kořenovou složku projektu a potom vyberte **přidat** > **novou položku** > **OWIN Startup class**.<br/>
> 2. **Pojmenujte**jej Startup.cs .
>
>> Ujistěte se, že vybraná třída je třída Spuštění OWIN a není standardní třídy C#. Potvrďte to ověřením, že se zobrazí [assembly: OwinStartup(typeof({NameSpace}. Startup))] nad oborem názvů.

1. Přidejte odkazy *OWIN* a *Microsoft.IdentityModel* na Startup.cs:

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
> Nastavení `ValidateIssuer = false` je zjednodušení pro tento rychlý start. V reálných aplikacích je nutné ověřit vystavittele.
> Podívejte se na ukázky, jak to udělat.

<!--start-collapse-->
> ### <a name="more-information"></a>Další informace
> Parametry, které zadáte v *OpenIDConnectAuthenticationOptions* slouží jako souřadnice pro aplikaci komunikovat s platformou identity Microsoft. Vzhledem k tomu, že middleware OpenID Connect používá soubory cookie na pozadí, musíte také nastavit ověřování souborů cookie podle předchozího kódu. Hodnota *ValidateIssuer* říká OpenIdConnect neomezovat přístup k jedné konkrétní organizaci.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Přidání kontroléru pro zpracování žádostí o přihlášení a odhlášení

Chcete-li vytvořit nový řadič pro vystavení metod přihlášení a odhlašování, postupujte takto:

1.  Klepněte pravým tlačítkem myši na složku **Řadiče** a vyberte **přidat** > **řadič**.
2.  Vyberte **Kontroler MVC (verze .NET) – prázdný**.
3.  Vyberte **Přidat**.
4.  Pojmenujte jej **HomeController** a pak vyberte **Přidat**.
5.  Přidejte odkazy OWIN do třídy:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Přidejte následující dvě metody pro zpracování přihlášení a odhlášení do řadiče spuštěním výzvy ověřování:

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

V sadě Visual Studio vytvořte nové zobrazení pro přidání přihlašovacího tlačítka a zobrazení informací o uživateli po ověření:

1.  Pravým tlačítkem myši klikněte na složku **Views\Home** a vyberte **Přidat zobrazení**.
2.  Pojmenujte nový **index**zobrazení .
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
>  Tato stránka přidá tlačítko pro přihlášení ve formátu SVG s černým pozadím:<br/>![Přihlášení u Microsoftu](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Další přihlašovací tlačítka najdete v [pokynech pro branding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Pokyny pro branding").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Přidání ovladače pro zobrazení deklarací identity uživatele
Tento kontroler demonstruje použití atributu `[Authorize]` k ochraně kontroleru. Tento atribut omezuje přístup k řadiči tím, že umožňuje pouze ověřené uživatele. Následující kód využívá atribut k zobrazení deklarací uživatelů, které byly načteny jako součást přihlášení:

1.  Klepněte pravým tlačítkem myši na složku **Řadiče** a potom vyberte **přidat** > **řadič**.
2.  Vyberte **Kontroler MVC {version} – prázdný**.
3.  Vyberte **Přidat**.
4.  Pojmenujte ho **ClaimsController**.
5.  Nahraďte kód třídy řadiče následujícím kódem. Tím přidáte `[Authorize]` atribut do třídy:

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
> Z důvodu použití `[Authorize]` atributu všechny metody tohoto řadiče lze provést pouze v případě, že uživatel je ověřen. Pokud uživatel není ověřen a pokusí se o přístup k řadiči, OWIN zahájí výzvu ověřování a vynutí uživatele k ověření. Předchozí kód se zabývá seznamem deklarací deklarací pro konkrétní atributy uživatele zahrnuté v tokenu ID uživatele. Tyto atributy zahrnují celé jméno uživatele a jeho uživatelské jméno, ale také subjekt globálního identifikátoru uživatele. Obsahuje také *ID tenanta*, které představuje ID organizace uživatele. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Vytvoření zobrazení pro zobrazení deklarací identity uživatele

V sadě Visual Studio vytvořte nové zobrazení, ve kterém se budou zobrazovat deklarace identity uživatele na webové stránce:

1.  Klepněte pravým tlačítkem myši na složku **Zobrazení\Deklarace identity** a potom vyberte **příkaz Přidat zobrazení**.
2.  Pojmenujte nový **index**zobrazení .
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

## <a name="register-your-application"></a>Registrace vaší aplikace

Chcete-li zaregistrovat přihlášku a přidat do svého řešení registrační údaje, máte dvě možnosti:

### <a name="option-1-express-mode"></a>Možnost 1: Expresní režim

Chcete-li aplikaci rychle zaregistrovat, postupujte takto:

1. Přejděte na nové [podokno Registrace aplikací azure.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)
1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci jediným kliknutím.

### <a name="option-2-advanced-mode"></a>Možnost 2: Rozšířený režim

Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:

1. Otevřete Visual Studio a pak:
   1. v Průzkumníku řešení vyberte projekt a zobrazte okno Vlastnosti (pokud okno Vlastnosti nevidíte, stiskněte klávesu F4).
   1. Změnit ssl `True`povoleno na .
   1. Klikněte pravým tlačítkem myši na projekt v sadě Visual Studio, vyberte **Vlastnosti**a vyberte **webovou** kartu. V části **Servery** změňte nastavení **adresy URL projektu** na adresu URL **ssl**.
   1. Zkopírujte adresu URL SSL. Tuto adresu URL přidáte do seznamu adres URL přesměrování v seznamu adres URL přesměrování v dalším kroku v seznamu adres URL přesměrování.<br/><br/>![Vlastnosti projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo pomocí osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více než jednomu tenantovi, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na klienta Azure AD, který chcete.
1. Přejděte na stránku Microsoft identity platformy pro vývojáře [Registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Vyberte **možnost Nová registrace**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
   1. V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například **ASPNET-Tutorial**.
   1. Přidejte adresu URL SSL, kterou jste zkopírovali z Visual Studia, `https://localhost:44368/`v kroku 1 (například) v adrese Adresa URL pro **odpověď**a vyberte **Registrovat**.
1. Vyberte nabídku **Ověřování,** v části **Implicitní udělení**vyberte **Tokeny ID** a pak vyberte **Uložit**.
1. Do souboru web.config, který se nachází v `configuration\appSettings` kořenové složce v části, přidejte následující:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Nahraďte `ClientId` id aplikace, které jste právě zaregistrovali.
1. Nahraďte `redirectUri` ji adresou URL SSL projektu.

## <a name="test-your-code"></a>Testování kódu

Chcete-li aplikaci otestovat v sadě Visual Studio, spusťte projekt stisknutím klávesy F5. Prohlížeč se otevře<span></span>do umístění http:// localhost:{port} a zobrazí se tlačítko **Přihlásit se pomocí microsoftu.** Vyberte tlačítko pro spuštění procesu přihlášení.

Až budete připraveni spustit test, použijte účet Azure AD (pracovní nebo školní účet) nebo osobní účet Microsoft<span>(live.When</span> you're ready to run your test, use a Azure AD account (work or school account) or a personal Microsoft account (live. com nebo <span>výhled.</span> com) pro přihlášení.

![Přihlášení u Microsoftu](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Přihlášení k účtu Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Oprávnění a souhlas v koncovém bodě platformy identit Microsoftu
>  Aplikace, které se integrují s platformou identit microsoftu, se řídí modelem autorizace, který uživatelům a správcům umožňuje kontrolu nad přístupem k datům. Poté, co se uživatel ověří pomocí platformy identit společnosti Microsoft pro přístup k této aplikaci, bude vyzván k souhlasu s oprávněními požadovanými aplikací ("Zobrazení základního profilu" a "Udržovat přístup k datům, ke kterým jste mu udělili přístup"). Po přijetí těchto oprávnění bude uživatel pokračovat ve výsledcích aplikace. Uživatel však může být místo toho vyzván i se stránkou **Need admin consent,** pokud dojde k některé z následujících:
>  > - Vývojář aplikace přidá další oprávnění, která vyžadují **souhlas správce**.
>  > - Nebo je klient nakonfigurován (v **nastavení > uživatele ) podniku Enterprise Applications**, kde uživatelé nemohou souhlasit s tím, aby aplikace přistupovaly k firemním datům jejich jménem.
>
> Další informace naleznete v části [Oprávnění a souhlas v koncovém bodě platformy identit microsoftu](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).
<!--end-collapse-->

#### <a name="view-application-results"></a>Zobrazit výsledky aplikace

Po přihlášení je uživatel přesměrován na domovskou stránku vašeho webu. Domovská stránka je adresa URL HTTPS, která je zadána v registračních informacích o vaší aplikaci na portálu registrace aplikací společnosti Microsoft. Domovská stránka obsahuje uvítací zprávu *"Dobrý den \<uživatele>",* odkaz na odhlášení a odkaz pro zobrazení deklarací identity uživatele. Odkaz pro deklarace identity uživatele se připojuje k řadiči Deklarace identity, který jste vytvořili dříve.

### <a name="view-the-users-claims"></a>Zobrazit deklarace identity uživatele

Chcete-li zobrazit deklarace identity uživatele, vyberte odkaz pro procházení zobrazení řadiče, které je k dispozici pouze pro ověřené uživatele.

#### <a name="view-the-claims-results"></a>Zobrazit výsledky deklarací identity

Po procházení zobrazení řadiče byste měli vidět tabulku, která obsahuje základní vlastnosti pro uživatele:

|Vlastnost |Hodnota |Popis |
|---|---|---|
|**Název** |Celé jméno uživatele | Jméno a příjmení uživatele
|**Username** |Uživatele<span>@domain.com</span> | Uživatelské jméno, které se používá k identifikaci uživatele|
|**Subjekt** |Subjekt |Řetězec, který jednoznačně identifikuje uživatele na webu|
|**ID tenanta** |Identifikátor GUID | **Identifikátor GUID,** který jednoznačně představuje organizaci Azure AD uživatele|

Kromě toho byste měli vidět tabulku všech deklarací, které jsou v žádosti o ověření. Další informace naleznete v [seznamu deklarací, které jsou v tokenu ID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Otestovat přístup k metodě, která má atribut Authorize (volitelné)

Chcete-li otestovat přístup jako anonymní uživatel k `Authorize` řadiči, který je chráněn atributem, postupujte takto:

1. Vyberte odkaz, který chcete odhlásit uživatele, a dokončete proces odhlašování.
2. V prohlížeči zadejte http://<span></span>localhost:{port}/claims pro přístup k `Authorize` ovladači, který je chráněn atributem.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Očekávané výsledky po přístupu k chráněnému řadiči

Budete vyzváni k ověření k použití zobrazení chráněného řadiče.

## <a name="advanced-options"></a>Upřesnit možnosti

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Ochrana celého webu
Chcete-li chránit celý web, přidejte v souboru **Global.asax** `AuthorizeAttribute` atribut do `GlobalFilters` filtru v metodě: `Application_Start`

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Omezení, kdo se může přihlásit k vaší aplikaci

Ve výchozím nastavení při vytváření aplikace vytvořené touto příručkou bude aplikace přijímat přihlášení k osobním účtům (včetně outlook.com, live.com a dalších) a také pracovních a školních účtů od jakékoli společnosti nebo organizace integrované s platformou identit microsoftu. Toto je doporučená možnost pro aplikace SaaS.

Chcete-li omezit přístup k přihlášení uživatele pro vaši aplikaci, je k dispozici více možností.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Možnost 1: Omezte uživatele pouze z instance služby Active Directory jedné organizace, aby se přihlásili k vaší aplikaci (jednoklientský)

Tato možnost se často používá pro *obchodní aplikace*: Pokud chcete, aby vaše aplikace přijímat přihlášení pouze z účtů, které patří do konkrétní instance Azure AD (včetně *účtů guest* této instance), postupujte takto:

1. V souboru web.config změňte `Tenant` hodnotu `Common` parametru z na název `contoso.onmicrosoft.com`klienta organizace, například .
2. Ve [třídě OWIN Startup](#configure-the-authentication-pipeline) `ValidateIssuer` nastavte `true`argument na .

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Možnost 2: Omezení přístupu k uživatelům v určitém seznamu organizací

Přístup k přihlášení můžete omezit pouze na ty uživatelské účty, které jsou v organizaci Azure AD, která je na seznamu povolených organizací:
1. Ve [třídě OWIN Startup](#configure-the-authentication-pipeline) `ValidateIssuer` nastavte `true`argument na .
2. Nastavte hodnotu `ValidIssuers` parametru na seznam povolených organizací.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Možnost 3: Ověření vystavitelů pomocí vlastní metody

Můžete implementovat vlastní metodu k ověření vystavitetele pomocí **Parametr IssuerValidator.** Další informace o použití tohoto parametru naleznete v [tématu TokenValidationParameters class](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak můžou webové aplikace volat webová api.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Naučte se, jak vytvořit aplikaci použitou v této příručce quickstart

Další informace o webových aplikacích volajících webová rozhraní API s platformou microsoftových identit:

> [!div class="nextstepaction"]
> [Webové aplikace volající webová api](scenario-web-app-sign-user-overview.md)

Přečtěte si, jak vytvářet webové aplikace s voláním Microsoft Graphu:

> [!div class="nextstepaction"]
> [Kurz ASP.NET microsoft graphu](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomozte nám vylepšit platformu identit Microsoftu. Řekněte nám, co si myslíte, že dokončení maze:

> [!div class="nextstepaction"]
> [Průzkum platformy identity Microsoftu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
