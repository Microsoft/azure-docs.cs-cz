---
title: Resetování hesla pro zápis, ověřování, ASP.NET Azure Active Directory B2C
description: Jak vytvořit webovou aplikaci, která má registrace-množství nebo přihlášení, úprava profilu a resetování hesla pomocí Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/17/2017
ms.author: davidmu
ms.openlocfilehash: 052d81f8170273bb0e40572322349550e7219363
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Vytvoření webové aplikace ASP.NET s Azure Active Directory B2C profil registrace, přihlášení, úpravy a resetování hesla

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Přidání funkce Azure AD B2C identity do webové aplikace
> * Registrace vaší webové aplikace ve vašem adresáři Azure AD B2C
> * Vytvoření uživatele registrace-množství nebo přihlášení, upravit profil a zásady resetování hesel pro vaši webovou aplikaci

## <a name="prerequisites"></a>Požadavky

- Svého klienta B2C je nutné připojit k účtu Azure. Můžete vytvořit bezplatný účet Azure [zde](https://azure.microsoft.com/).
- Je třeba [Microsoft Visual Studio](https://www.visualstudio.com/) nebo podobné program zobrazovat a upravovat ukázkový kód.

## <a name="create-an-azure-ad-b2c-directory"></a>Vytvoření adresáře Azure AD B2C

Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta. Adresář je kontejner pro všechny uživatele, aplikace, skupiny a další. Pokud nemáte již, vytvořte adresář B2C, než budete pokračovat v této příručce.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Potřebujete připojit k předplatnému Azure klienta B2C. Po výběru **vytvořit**, vyberte **odkaz existující Azure AD B2C klienta pro Moje předplatné** možnost a potom v **klienta Azure AD B2C** rozevírací nabídku, vyberte klienta, které chcete přidružit.

## <a name="create-and-register-an-application"></a>Vytvoření a registrace aplikace

Dále musíte vytvořit a registrovat aplikace ve svém adresáři B2C. To poskytuje informace, které Azure AD B2C potřebné k bezpečné komunikaci s vaší aplikací. 

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Až skončíte, bude mít rozhraní API a nativní aplikace v nastavení aplikace.

## <a name="create-policies-on-your-b2c-tenant"></a>Vytvořit zásady na svého klienta B2C

V Azure AD B2C je každé uživatelské rozhraní definováno [zásadou](active-directory-b2c-reference-policies.md). Tato ukázka kódu obsahuje tři činnosti identity: **registrace a přihlášení**, **profilu upravit**, a **resetování hesla**.  Pro každý typ rozhraní musíte vytvořit zásadu, jak je popsáno v [článku o zásadách](active-directory-b2c-reference-policies.md). U každé zásady nezapomeňte vybrat atribut název zobrazení nebo deklarace identity a poznamenejte název vaší zásady pro pozdější použití.

### <a name="add-your-identity-providers"></a>Přidat vaši zprostředkovatelé identity

Nastavení, vyberte **zprostředkovatelů Identity** a zvolte registrace uživatelského jména nebo e-mailu registrace.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Vytvoření zásad registrace a přihlášení

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Vytvoření zásady upravování profilu

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Vytvoření zásady resetování hesla

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

Po vytvoření zásad jste připraveni k sestavení aplikace.

## <a name="download-the-sample-code"></a>Stažení ukázkového kódu

Kód k tomuto kurzu se udržuje na [GitHubu](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Ukázku můžete klonovat spuštěním:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Po stažení ukázkového kódu otevřete soubor Visual Studio .sln, abyste mohli začít. Soubor řešení obsahuje dva projekty: `TaskWebApp` a `TaskService`. `TaskWebApp` je webová aplikace MVC, která uživatel komunikuje. `TaskService` je back-endové webové rozhraní API aplikace, které ukládá seznam úkolů každého uživatele. Tento článek bude probírat pouze aplikaci `TaskWebApp`. Další informace o sestavení `TaskService` pomocí Azure AD B2C, najdete v tématu [naše kurz webové rozhraní api .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>Aktualizujte kód používat klienta a zásady

Naše ukázka je nakonfigurovaná k použití zásad a ID klienta naše ukázkového tenanta. Aby se připojila k vlastního klienta, budete muset otevřít `web.config` v `TaskWebApp` projektu a nahraďte následující hodnoty:

* `ida:Tenant` názvem vašeho tenanta
* `ida:ClientId` identifikátorem webového aplikace
* `ida:ClientSecret` tajným klíčem webové aplikace
* `ida:SignUpSignInPolicyId` názvem zásady registrace/přihlášení
* `ida:EditProfilePolicyId` názvem zásady pro úpravu profilu
* `ida:ResetPasswordPolicyId` názvem zásady pro resetování hesla

## <a name="launch-the-app"></a>Spusťte aplikaci
Z Visual Studia, spusťte aplikaci. Přejděte na kartu seznam úkolů a poznamenejte si, že je adresa URl: https://login.microsoftonline.com/ *YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName*& client_id =*YourclientID*.....

Zaregistrujte se pro aplikace pomocí e-mailové adresy nebo uživatelské jméno. Odhlásit se, potom znovu přihlásit a upravte profil nebo resetování hesla. Odhlásit se a přihlaste se jako jiný uživatel. 

## <a name="add-social-idps"></a>Přidat sociálních IDPs

Aplikace v současné době podporuje pouze uživatel registrace a přihlášení pomocí **místní účty**; uložené v adresáři B2C účty, které používají uživatelské jméno a heslo. Pomocí Azure AD B2C můžete přidat podporu pro ostatní **zprostředkovatelů identity** (IDPs) beze změny některé z vašeho kódu.

Sociální IDPs přidat do vaší aplikace, začněte tím, že následující podrobné pokyny v těchto článcích. Pro každý deklarací identity, které chcete podporovat je třeba zaregistrovat aplikaci v daném systému a získat ID klienta.

* [Nastavení sítě Facebook jako IDP](active-directory-b2c-setup-fb-app.md)
* [Nastavit Google jako IDP](active-directory-b2c-setup-goog-app.md)
* [Nastavit Amazon jako IDP](active-directory-b2c-setup-amzn-app.md)
* [Nastavit LinkedIn jako IDP](active-directory-b2c-setup-li-app.md)

Po přidání zprostředkovatelů identity do vašeho adresáře B2C, upravit, každý z vaší tři zásady, které zahrnují nové IDPs, jak je popsáno v [článku o zásadách](active-directory-b2c-reference-policies.md). Po uložení zásad, znovu spusťte aplikaci.  Měli byste vidět nové IDPs přidat jako přihlášení a registrace možnosti v každé z vaší identity činnost.

Můžete experimentovat s vašimi zásadami a sledovat účinek na ukázkové aplikace. Přidat nebo odebrat IDPs, pracovat s deklarace identity aplikace nebo změnit atributy registrace. Experiment, dokud se nezobrazí, jak zásady, žádosti o ověření a OWIN tie společně.

## <a name="sample-code-walkthrough"></a>Ukázka kódu návod
Následující části vysvětlují, jak jsou nakonfigurované ukázkového kódu aplikace. Může to použijte jako vodítko při vývoji vaší budoucí aplikace.

### <a name="add-authentication-support"></a>Přidat podporu ověřování

Teď můžete konfigurovat aplikaci používají Azure AD B2C. Aplikace komunikuje se službou Azure AD B2C odesláním OpenID Connect žádosti o ověření. Požadavky určují uživatelské prostředí, které vaše aplikace chce provést zadáním zásady. Knihovna OWIN společnosti Microsoft můžete odesílat tyto požadavky, provést zásady a spravovat uživatelské relace a další.

#### <a name="install-owin"></a>Instalace OWIN

Pokud chcete začít, přidejte do projektu balíčky NuGet middleware OWIN pomocí konzoly Správce balíčků Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

#### <a name="add-an-owin-startup-class"></a>Přidání třídy pro spuštění OWIN

Přidejte třídu pro spuštění OWIN s názvem `Startup.cs` do rozhraní API.  Klikněte pravým tlačítkem myši na projekt, vyberte **Přidat** a **Nová položka**, a poté vyhledejte OWIN. Middleware OWIN při spuštění vaší aplikace vyvolá metodu `Configuration(…)`.

V naší ukázce jsme změnili deklaraci třídy na `public partial class Startup` a implementovali druhou část třídy v `App_Start\Startup.Auth.cs`. Uvnitř metody `Configuration` jsme přidali volání metody `ConfigureAuth`, která je definovaná v `Startup.Auth.cs`. Po úpravách vypadá `Startup.cs` následovně:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

#### <a name="configure-the-authentication-middleware"></a>Nakonfigurujte middleware ověřování.

Otevřete soubor `App_Start\Startup.Auth.cs` a implementovat `ConfigureAuth(...)` metoda. Parametry, zadejte v `OpenIdConnectAuthenticationOptions` sloužit jako souřadnice pro vaši aplikaci ke komunikaci s Azure AD B2C. Pokud některé parametry nezadáte, použije se výchozí hodnota. Například můžeme nezadávejte `ResponseType` v ukázce proto výchozí hodnota `code id_token` se použije v každém odchozí požadavku na Azure AD B2C.

Musíte taky nastavit ověřování souborů cookie. Middleware OpenID Connect používá soubory cookie k udržování uživatelské relace, mimo jiné.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"openid profile offline_access {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

V `OpenIdConnectAuthenticationOptions` vyšší, jsme definovali sadu funkce zpětného volání pro konkrétní oznámení, které jsou přijaty middlewarem OpenID Connect. Tyto chování jsou definovány pomocí `OpenIdConnectAuthenticationNotifications` objektu a uložena do `Notifications` proměnné. V našem ukázce jsme definovali tři různé zpětná volání v závislosti na události.

### <a name="using-different-policies"></a>Pomocí různých zásad

`RedirectToIdentityProvider` Oznámení se aktivuje vždy, když požadavku na službu Azure AD B2C. Ve funkci zpětného volání `OnRedirectToIdentityProvider`, jsme změnami odchozí volání, pokud chcete použít jinou zásadu. Aby bylo možné provést resetování hesla nebo upravte profil, budete muset použít odpovídající zásada, jako je heslo resetovat zásady místo výchozí zásady "Registrace nebo přihlášení".

Ve výběru když uživatel chce resetovat heslo nebo upravte profil, přidáme na zásadu, kterou jsme dávají přednost používání do kontextu OWIN. To můžete udělat následujícím způsobem:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

A můžete implementovat funkce zpětného volání `OnRedirectToIdentityProvider` provedením následujících akcí:

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

### <a name="handling-authorization-codes"></a>Zpracování autorizačních kódů

`AuthorizationCodeReceived` Oznámení se aktivuje při přijetí autorizační kód. Middleware OpenID Connect nepodporuje výměnou kódy pro tokeny přístupu. Kód pro token ve funkci zpětného volání, můžete je ručně vyměnit. Další informace, podívejte se prosím na [dokumentace](active-directory-b2c-devquickstarts-web-api-dotnet.md) to vysvětluje, jak.

### <a name="handling-errors"></a>Zpracování chyb

`AuthenticationFailed` Oznámení se aktivuje, když se ověřování nezdaří. V jeho metoda zpětného volání může zpracovávat chyby podle potřeby. Měli byste ale přidat kontrolu pro kód chyby `AADB2C90118`. Během provádění zásady "Registrace nebo přihlášení", uživatel má možnost vybrat **zapomněli jste heslo?** odkaz. V takovém případě Azure AD B2C odešle aplikace této chyby kódu oznamující, že vaše aplikace by měl provádět žádost místo toho použít zásady resetování hesel.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

### <a name="send-authentication-requests-to-azure-ad"></a>Odeslání žádosti o ověření do služby Azure AD

Aplikace je nyní správně nakonfigurováno pro komunikaci s Azure AD B2C pomocí ověřovacího protokolu OpenID Connect. OWIN spravuje podrobnosti věnujte zpráv ověřování, ověřování tokenů z Azure AD B2C a údržbě uživatelské relace. Zbývá zahájíte toku každého uživatele.

Když uživatel vybere **až přihlášení nebo přihlášení**, **upravit profil**, nebo **resetovat heslo** ve webové aplikaci, přidružené akce je volána v `Controllers\AccountController.cs`:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

OWIN můžete také použít k odhlášení uživatele z aplikace. V `Controllers\AccountController.cs` máme:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

Kromě explicitně vyvolání zásady, můžete použít `[Authorize]` značky v řadičích, které provádí zásadu, pokud uživatel není přihlášený. Otevřete `Controllers\HomeController.cs` a přidejte `[Authorize]` značky k řadiči deklarací identity.  Vybere OWIN poslední zásady nakonfigurované při zpracování `[Authorize]` je dosáhl značky.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Zobrazit informace o uživateli

Při ověřování uživatele pomocí OpenID Connect, Azure AD B2C vrátí ID token na aplikaci, která obsahuje **deklarace identity**. Tyto jsou tvrzení o uživateli. Deklarace identity můžete použít k přizpůsobení funkcí aplikace.

Otevřete soubor `Controllers\HomeController.cs`. Dostanete deklarace identity uživatelů v řadičích prostřednictvím `ClaimsPrincipal.Current` zaregistrovaný objekt zabezpečení.

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Všechny deklarace identity, které aplikace přijímá stejným způsobem můžete přistupovat.  Seznam všech deklarací identity aplikace obdrží je k dispozici pro vás na **deklarace identity** stránky.
