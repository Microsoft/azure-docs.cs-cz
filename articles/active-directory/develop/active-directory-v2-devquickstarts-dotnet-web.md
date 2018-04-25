---
title: "Služba Azure AD v2.0 .NET webové přihlášení aplikace Začínáme | Microsoft Docs"
description: "Postup vytvoření webové aplikace MVC .NET, která podepisuje uživatele přihlašují pomocí Account Microsoft i osobní a pracovní nebo školní účty."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: c8b97ac6-0a06-4367-81b6-7d1d98152b14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a23b3b1084cf6776cee8583891ae3d879183d072
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
---
# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Přidání přihlašování do webové aplikace .NET MVC
S koncovým bodem v2.0 můžete rychle přidat ověřování do vaší webové aplikace s podporou pro oba osobní účty Microsoft a pracovní nebo školní účty.  V aplikacích ASP.NET web můžete to provést pomocí middlewaru OWIN společnosti Microsoft, zahrnutá v rozhraní .NET Framework 4.5.

> [!NOTE]
> Ne všechny scénáře Azure Active Directory a funkce jsou podporovány koncového bodu v2.0.  Pokud chcete zjistit, pokud byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>
>

 Zde jsme budete vytvářet webové aplikace, která používá OWIN uživatele přihlásit, zobrazí některé informace o uživateli a přihlášení uživatele mimo aplikaci.

## <a name="download"></a>Ke stažení
Kód k tomuto kurzu je udržovaný [na GitHubu](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Chcete-li sledovat, můžete [stáhnout kostru aplikace jako ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) nebo tuto kostru klonovat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

Dokončená aplikace je k dispozici na konci tohoto kurzu také.

## <a name="register-an-app"></a>Registrace aplikace
Vytvoření nové aplikace v [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo postupujte podle těchto [podrobné kroky](active-directory-v2-app-registration.md).  Zkontrolujte, že:

* Zkopírování **Id aplikace** přiřazené vaší aplikaci, budete ho potřebovat brzy k dispozici.
* Přidat **webové** platformu pro vaši aplikaci.
* Zadejte správný **identifikátor URI pro přesměrování**. Určuje identifikátor uri přesměrování do služby Azure AD, kde se mají směrovat ověřování odpovědí – výchozí hodnota pro tento kurz je `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Instalace a konfigurace ověřování OWIN.
Zde nakonfigurujeme middleware OWIN pro použití ověřovacího protokolu OpenID Connect.  OWIN se použije k vydávání požadavků na přihlášení a odhlášení, spravovat relace uživatele a získat informace o uživateli, mimo jiné.

1. Chcete-li začít, otevřete `web.config` v kořenovém adresáři projektu soubor a zadejte hodnoty konfigurace vaší aplikace v `<appSettings>` oddílu.

  * `ida:ClientId` Je **Id aplikace** přiřazené vaší aplikaci v portálu pro registraci.
  * `ida:RedirectUri` Je **identifikátor Uri pro přesměrování** jste zadali v portálu.

2. Balíčky NuGet middleware OWIN v dalším kroku přidejte do projektu pomocí konzoly Správce balíčků.

        ```
        PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
        PM> Install-Package Microsoft.Owin.Security.Cookies
        PM> Install-Package Microsoft.Owin.Host.SystemWeb
        ```  

3. Přidání "Třídy pro spuštění OWIN" do projektu názvem `Startup.cs` správné, klikněte na projekt--> **přidat** --> **nová položka** --> vyhledejte "OWIN".  Middleware OWIN při spuštění vaší aplikace vyvolá metodu `Configuration(...)`.
4. Změňte deklaraci třídy k `public partial class Startup` -již implementovali jsme součástí této třídy pro vás v jiném souboru.  V `Configuration(...)` metoda, zkontrolujte zavolá ConfigureAuth(...) nastavení ověřování pro webovou aplikaci  

        ```csharp
        [assembly: OwinStartup(typeof(Startup))]
        
        namespace TodoList_WebApp
        {
            public partial class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    ConfigureAuth(app);
                }
            }
        }
        ```

5. Otevřete soubor `App_Start\Startup.Auth.cs` a implementovat `ConfigureAuth(...)` metoda.  Parametry, zadejte v `OpenIdConnectAuthenticationOptions` bude sloužit jako souřadnice pro vaši aplikaci komunikovat s Azure AD.  Také budete muset nastavit ověřování souborů Cookie – používá soubory cookie pod pozadí, middleware OpenID Connect.

        ```csharp
        public void ConfigureAuth(IAppBuilder app)
                     {
                             app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);
        
                             app.UseCookieAuthentication(new CookieAuthenticationOptions());
        
                             app.UseOpenIdConnectAuthentication(
                                     new OpenIdConnectAuthenticationOptions
                                     {
                                             // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                                             // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                                             // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.
        
                                             ClientId = clientId,
                                             Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
                                             RedirectUri = redirectUri,
                                             Scope = "openid email profile",
                                             ResponseType = "id_token",
                                             PostLogoutRedirectUri = redirectUri,
                                             TokenValidationParameters = new TokenValidationParameters
                                             {
                                                     ValidateIssuer = false,
                                             },
                                             Notifications = new OpenIdConnectAuthenticationNotifications
                                             {
                                                     AuthenticationFailed = OnAuthenticationFailed,
                                             }
                                     });
                     }
        ```

## <a name="send-authentication-requests"></a>Odeslání žádosti o ověření
Aplikace je nyní správně nakonfigurováno pro komunikaci s koncovým bodem v2.0 pomocí ověřovacího protokolu OpenID Connect.  OWIN má postaráno všechny ugly podrobnosti věnujte zpráv ověřování, ověřování tokenů z Azure AD a udržování uživatelské relace.  Už jen zbývá umožnit uživatelům způsob, jak přihlášení a odhlášení.

- Můžete použít autorizovat značky v řadičích tak, aby vyžadovala přihlášení tohoto uživatele před přístupem k určité stránky.  Otevřete `Controllers\HomeController.cs`a přidejte `[Authorize]` značka, které je o kontroleru.
        
        ```csharp
        [Authorize]
        public ActionResult About()
        {
          ...
        ```

- Můžete taky OWIN přímo vydání žádosti o ověření z vašeho kódu.  Otevřete `Controllers\AccountController.cs`.  V SignIn() a SignOut() akce vydejte OpenID Connect výzvy a odhlášení požadavků, v uvedeném pořadí.

        ```csharp
        public void SignIn()
        {
            // Send an OpenID Connect sign-in request.
            if (!Request.IsAuthenticated)
            {
                HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
            }
        }
        
        // BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
        public void SignOut()
        {
            // Send an OpenID Connect sign-out request.
            HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
            Response.Redirect("/");
        }
        ```

- Nyní otevřete `Views\Shared\_LoginPartial.cshtml`.  Toto je, kde můžete zobrazit uživatele vaší aplikace přihlášení a odhlášení odkazy a vytiskněte uživatelské jméno v zobrazení.

        ```HTML
        @if (Request.IsAuthenticated)
        {
            <text>
                <ul class="nav navbar-nav navbar-right">
                    <li class="navbar-text">
        
                        @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@
        
                        Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
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

## <a name="display-user-information"></a>Zobrazit informace o uživateli
Při ověřování uživatelů s OpenID Connect, vrátí koncový bod v2.0 požadavku id_token na aplikaci, která obsahuje deklarace identity nebo tvrzení o uživateli.  Tyto deklarace identity můžete použít k přizpůsobení funkcí aplikace:

- Otevřete soubor `Controllers\HomeController.cs`.  Dostanete deklaracích identity uživatele v řadičích prostřednictvím `ClaimsPrincipal.Current` zaregistrovaný objekt zabezpečení.

        ```csharp
        [Authorize]
        public ActionResult About()
        {
            ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;
        
            // The object ID claim will only be emitted for work or school accounts at this time.
            Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
            ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;
        
            // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
            ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;
        
            // The subject or nameidentifier claim can be used to uniquely identify the user
            ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        
            return View();
        }
        ```

## <a name="run"></a>Spusťte
Nakonec sestavte a spusťte aplikaci!   Přihlaste se pomocí osobního Account Microsoft nebo pracovní nebo školní účet a Všimněte si, jak se odrazí identitu uživatelů v horním navigačním panelu.  Nyní máte webovou aplikaci zabezpečené pomocí standardních oborových protokolech, které může ověřit uživatele s svoje osobní, tak i pracovní nebo školní účty.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) [je k dispozici jako ZIP zde](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip), nebo ji můžete klonovat z Githubu:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Další postup
Nyní se můžete přesunout na pokročilejší témata.  Můžete se pokusit:

[Zabezpečení webového rozhraní API se koncový bod v2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Další zdroje projděte si:

* [Příručka vývojáře v2.0 >>](active-directory-appmodel-v2-overview.md)
* [Značka StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Získejte bezpečnostní aktualizace našich produktů
Doporučujeme vám získávat oznámení o bezpečnostních incidentech tak, že navštívíte [tuto stránku](https://technet.microsoft.com/security/dd252948) a přihlásíte se k odběru služby Security Advisory Alerts.
