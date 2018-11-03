---
title: Heslo pro ověřování, registrace, obnovení v Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak vytvořit webovou aplikaci, která má přihlášení-registrace/přihlášení, úpravy profilu a resetování hesla pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1db805efe7eaec77fcafeb169b3d99098b57f582
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978974"
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Vytvoření webové aplikace ASP.NET pomocí Azure Active Directory B2C profil registrace, přihlašování, úpravy a resetování hesla

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Přidání funkce identity Azure AD B2C do webové aplikace
> * Registrace webové aplikace v adresáři Azure AD B2C
> * Vytvořte uživatele přihlášení-registrace/přihlášení, úpravy profilu a zásady pro resetování hesla pro vaši webovou aplikaci

## <a name="prerequisites"></a>Požadavky

- Tenanta B2C musíte se připojit k účtu Azure. Můžete vytvořit si bezplatný účet Azure [tady](https://azure.microsoft.com/).
- Potřebujete [sady Microsoft Visual Studio](https://www.visualstudio.com/) nebo podobný program k zobrazení a úprava vzorového kódu.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

Než budete moct použít Azure AD B2C, musíte vytvořit tenanta. Tenant je kontejner pro všechny uživatele, aplikace, skupiny a další. Pokud je nemáte, vytvořte tenanta B2C předtím, než budete pokračovat v této příručce.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Budete muset připojit ke svému předplatnému Azure tenanta Azure AD B2C. Po výběru **vytvořit**, vyberte **Tenanta odkaz stávající službou Azure AD B2C s mým předplatným Azure** možnost a pak v **Tenanta Azure AD B2C** rozevírací seznam, vyberte Klient, který chcete přidružit.

## <a name="create-and-register-an-application"></a>Vytvoření a registrace aplikace

Dále je třeba vytvořit a zaregistrovat aplikaci v tenantovi Azure AD B2C. To poskytuje informace, které Azure AD B2C potřebuje k bezpečné komunikaci s vaší aplikací. 

Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**. Měli byste nyní použít klienta, který jste předtím vytvořili.

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Jakmile budete hotovi, budete mít rozhraní API a nativní aplikace v aplikaci nastavení.

## <a name="create-policies-on-your-b2c-tenant"></a>Vytvoření zásad ve svém tenantovi B2C

V Azure AD B2C je každé uživatelské rozhraní definováno [zásadou](active-directory-b2c-reference-policies.md). Tato ukázka kódu obsahuje tři činnosti identity: **registrace a přihlášení**, **profilu úpravy**, a **resetování hesla**.  Pro každý typ rozhraní musíte vytvořit zásadu, jak je popsáno v [článku o zásadách](active-directory-b2c-reference-policies.md). U každé zásady nezapomeňte vybrat atribut název zobrazení nebo deklarací identity a poznamenejte název zásady pro pozdější použití.

### <a name="add-your-identity-providers"></a>Přidat svoje zprostředkovatele identit

Nastavení, vyberte **zprostředkovatelé Identity** a možnost registrace uživatelského jména nebo e-mailová registrace.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Vytvoření zásad registrace a přihlášení

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Vytvoření zásady upravování profilu

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Vytvoření zásady resetování hesla

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

Po vytvoření zásad jste připraveni k sestavení aplikace.

## <a name="download-the-sample-code"></a>Stáhněte si ukázkový kód

Kód k tomuto kurzu se udržuje na [GitHubu](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Ukázku můžete klonovat spuštěním:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Po stažení ukázkového kódu otevřete soubor Visual Studio .sln, abyste mohli začít. Soubor řešení obsahuje dva projekty: `TaskWebApp` a `TaskService`. `TaskWebApp` je webová aplikace MVC, kterou uživatel komunikuje. `TaskService` je back-endové webové rozhraní API aplikace, které ukládá seznam úkolů každého uživatele. Tento článek bude probírat pouze aplikaci `TaskWebApp`. Chcete-li další informace o vytváření `TaskService` pomocí služby Azure AD B2C, najdete v článku [Náš kurz webových rozhraní api .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>Aktualizace kódu pro použití vašeho tenanta a zásady

Naše ukázka je nakonfigurovaná k použití zásad a ID klienta naše ukázkového tenanta. Aby se připojila k vašeho vlastního tenanta, budete muset otevřít `web.config` v `TaskWebApp` projektu a nahraďte následující hodnoty:

* `ida:Tenant` názvem vašeho tenanta
* `ida:ClientId` identifikátorem webového aplikace
* `ida:ClientSecret` tajným klíčem webové aplikace
* `ida:SignUpSignInPolicyId` názvem zásady registrace/přihlášení
* `ida:EditProfilePolicyId` názvem zásady pro úpravu profilu
* `ida:ResetPasswordPolicyId` názvem zásady pro resetování hesla

## <a name="launch-the-app"></a>Spuštění aplikace
Ze sady Visual Studio, spusťte aplikaci. Přejděte na kartu seznam úkolů a Všimněte si, že je adresa URl: https://*YourTenantName*.b2clogin.com/*YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName* & client_id =*YourclientID*...

Zaregistrujte se pro aplikaci s použitím e-mailové adresy nebo uživatelského jména. Odhlaste, pak znovu přihlásit a upravte profil nebo resetování hesla. Odhlaste se a přihlaste se jako jiný uživatel. 

## <a name="add-social-idps"></a>Přidání sociálních sítí zprostředkovatelů identity

V současné době aplikace podporuje pouze uživatele, registrace a přihlášení pomocí **místní účty**; účty uložené v adresáři B2C, které používají uživatelské jméno a heslo. Pomocí Azure AD B2C, můžete přidat podporu pro ostatní **zprostředkovatelé identity** (IDP) bez změny vašich kód.

Chcete-li přidat zprostředkovatelů sociálních sítí do vaší aplikace, začněte podle podrobných pokynů v těchto článcích. Pro každého zprostředkovatele identity, které chcete podporovat budete muset zaregistrovat aplikaci v daném systému a získat ID klienta.

* [Nastavení sítě Facebook jako identity](active-directory-b2c-setup-fb-app.md)
* [Nastavení Google jako identity](active-directory-b2c-setup-goog-app.md)
* [Nastavení Amazon jako identity](active-directory-b2c-setup-amzn-app.md)
* [Nastavení Linkedinu jako identity](active-directory-b2c-setup-li-app.md)

Po přidání zprostředkovatele identity do vašeho tenanta B2C, upravit, všechny tři zásady zahrnout nové zprostředkovatelů identity, jak je popsáno v [článku o zásadách](active-directory-b2c-reference-policies.md). Po uložení zásady znovu spusťte aplikaci.  Měli byste vidět nové zprostředkovatelů identity, které jsou přidány jako přihlášení a prostředí možností registrace v jednotlivých vaši identitu.

Můžete experimentovat s vašimi zásadami a můžete sledovat účinek na ukázkovou aplikaci. Přidat nebo odebrat zprostředkovatelů identity, manipulovat s deklaracemi identity aplikace nebo změnit atributy registrace. Experiment, dokud se nezobrazí, jak spojovat zásady žádosti o ověření a OWIN.

## <a name="sample-code-walkthrough"></a>Ukázkový názorný postup kódu
Následující části vysvětlují konfiguraci ukázkový kód aplikace. Můžete ji používat jako vodítko při vývoji budoucí aplikace.

### <a name="add-authentication-support"></a>Přidání podpory ověřování

Teď můžete nakonfigurovat aplikace pro použití Azure AD B2C. Vaše aplikace komunikovala s Azure AD B2C odesláním žádosti o ověření OpenID Connect. Požadavky určují uživatelské prostředí, které aplikace chce provést tak, že určíte zásady. Společnosti Microsoft OWIN knihovny slouží k odesílání těchto požadavků, spuštění zásady, spravovat uživatelské relace a další věci.

#### <a name="install-owin"></a>Instalace OWIN

Pokud chcete začít, přidáte do projektu balíčky NuGet middleware OWIN pomocí konzole Správce balíčků Visual Studio.

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

Otevřete soubor `App_Start\Startup.Auth.cs` a implementujte metodu `ConfigureAuth(...)`. Parametry, které poskytnete v `OpenIdConnectAuthenticationOptions` sloužit jako souřadnice pro vaši aplikaci ke komunikaci s Azure AD B2C. Pokud nezadáte určité parametry, použije se výchozí hodnota. Například jsme nezadávejte `ResponseType` ve vzorku, tak na výchozí hodnotu `code id_token` se použije v každý odchozí požadavek na Azure AD B2C.

Také je nutné nastavit ověřování souborů cookie. Middleware OpenID Connect používá soubory cookie k Udržovat uživatelské relace, mimo jiné.

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

                // Specify the scope by appending all of the scopes requested into one string (separated by a blank space)
                Scope = $"openid profile offline_access {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

V `OpenIdConnectAuthenticationOptions` výše definujeme sadu funkcí zpětného volání pro upozornění, které jsou přijaty middlewarem OpenID Connect. Těchto projevů jsou definovány pomocí `OpenIdConnectAuthenticationNotifications` objektu a uložena do `Notifications` proměnné. V naší ukázce budeme definovat tři různé zpětná volání v závislosti na události.

### <a name="using-different-policies"></a>Použití různých zásad

`RedirectToIdentityProvider` Upozornění se aktivuje vždy, když se požadavek na Azure AD B2C. Ve funkci zpětného volání `OnRedirectToIdentityProvider`, zkontrolujeme v odchozích volání, pokud chcete použít jiné zásady. Aby bylo možné provést resetování hesla nebo upravte profil, budete muset použít odpovídající zásady, jako je místo výchozí zásady "Registrace / přihlášení" zásady pro resetování hesla.

V naší ukázce když uživatel chce resetovat heslo nebo upravte profil, přidáme na zásadu, kterou jsme dávají přednost používání do kontextu OWIN. To jde udělat následujícím způsobem:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

A můžete implementovat funkci zpětného volání `OnRedirectToIdentityProvider` následujícím způsobem:

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

### <a name="handling-authorization-codes"></a>Zpracování autorizační kódy

`AuthorizationCodeReceived` Upozornění se aktivuje při přijetí autorizační kód. Middleware OpenID Connect nepodporuje pro přístupových tokenů výměnou kódy. Můžete ručně vyměnit kód pro daný token ve funkci zpětného volání. Další informace, podívejte se prosím na [dokumentaci](active-directory-b2c-devquickstarts-web-api-dotnet.md) , který vysvětluje, jak.

### <a name="handling-errors"></a>Zpracování chyb

`AuthenticationFailed` Upozornění se aktivuje, pokud se ověření nezdaří. Ve své metodě zpětného volání může zpracovávat chyby podle potřeby. Měli byste ale přidat kontrolu kód chyby: `AADB2C90118`. Při zpracování zásady "Registrace nebo přihlašování" uživatel má možnost vybrat **zapomněli jste heslo?** odkaz. V takovém případě Azure AD B2C pošle vaší aplikace tento kód chyby označující, že vaše aplikace by měl vytvořit žádost o místo toho použít zásady resetování hesla.

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

Vaše aplikace je teď správně nakonfigurované pro komunikaci s Azure AD B2C pomocí ověřovacího protokolu OpenID Connect. Podrobnosti ohledně vytváření ověřovacích zpráv, ověřování tokenů z Azure AD B2C a udržování uživatelských relací slouží ke správě OWIN. Už jen zbývá k zahájení toku jednotlivých uživatelů.

Když uživatel vybere **Sign up/přihlášení**, **upravit profil**, nebo **resetovat heslo** ve webové aplikaci, je vyvolána přidružené akce v `Controllers\AccountController.cs`:

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

Také vám pomůže OWIN neodhlásí uživatele z aplikace. V `Controllers\AccountController.cs` máme k dispozici:

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

Kromě explicitně vyvolání zásady, můžete použít `[Authorize]` značku ve vašich kontrolerech, zásady se spustí, pokud uživatel není přihlášený. Otevřít `Controllers\HomeController.cs` a přidejte `[Authorize]` značka, které deklarace identity kontroleru.  OWIN Vybere poslední zásady nakonfigurované při `[Authorize]` stiskněte značku.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Zobrazení informací o uživateli

Při ověřování uživatelů pomocí OpenID Connect, Azure AD B2C vrátí ID token pro aplikaci, která obsahuje **deklarace identity**. Jedná se o kontrolní výrazy informace o uživateli. Deklarace identity můžete použít k přizpůsobení aplikace.

Otevřete soubor `Controllers\HomeController.cs`. Dostanete deklarace identity uživatelů ve vašich kontrolerech prostřednictvím `ClaimsPrincipal.Current` zaregistrovaný objekt zabezpečení.

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

Všechny deklarace identity, které aplikace přijímá stejným způsobem můžete přistupovat.  Seznam všech deklarací identity aplikace obdrží je k dispozici na **deklarace identity** stránky.
