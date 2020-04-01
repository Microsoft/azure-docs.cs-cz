---
title: Přidání přihlášení se společností Microsoft k ASP.NET základních webových aplikací – platforma identit Microsoftu | Azure
description: Zjistěte, jak implementovat přihlášení k Microsoftu v ASP.NET Core Web App pomocí OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e60d3f0221bce836f49e87a96c762e68cf8a60ad
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473680"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Úvodní příručka: Přidání přihlášení s Microsoftem do webové aplikace ASP.NET Core
V tomto rychlém startu se pomocí ukázky kódu dozvíte, jak ASP.NET webová aplikace Core může přihlašovat osobní účty (hotmail.com, outlook.com, ostatní) a pracovní a školní účty z libovolné instance Služby Azure Active Directory (Azure AD). (Viz [jak ukázka funguje](#how-the-sample-works) pro ilustraci.)
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Expresní] [Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejděte na [portál Azure – registrace aplikací](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Chcete-li zaregistrovat přihlášku a ručně přidat registrační údaje aplikace do vašeho řešení, postupujte takto:
>
> 1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na platformu identit Microsoftpro vývojáře [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
> 1. Vyberte **možnost Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>    - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AspNetCore-Quickstart`.
>    - V **identifikátoru URI přesměrování**přidejte `https://localhost:44321/`a vyberte **registrovat**.
> 1. Vyberte nabídku **Ověřování** a přidejte následující informace:
>    - V **přesměrovat uris**, přidat `https://localhost:44321/signin-oidc`a vybrat **uložit**.
>    - V části **Upřesnit nastavení** nastavte adresu `https://localhost:44321/signout-oidc`URL **odhlášení** na adresu .
>    - V části **Implicitní udělení** zaškrtněte políčko **Tokeny ID**.
>    - Vyberte **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure Portal
> Aby ukázka kódu pro tento rychlý start fungovala, je `https://localhost:44321/` `https://localhost:44321/signin-oidc`třeba přidat adresy URL `https://localhost:44321/signout-oidc`odpovědí jako a , přidat adresu URL odhlášení jako a požádat o id tokeny, které mají být vydány koncovým bodem autorizace.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2: Stáhněte si svůj projekt ASP.NET Core

> [!div renderon="docs"]
> [Stažení řešení Visual Studia 2019](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Spusťte projekt pomocí Visual Studia 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stáhnout ukázku kódu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: Aplikace je nakonfigurovaná a připravená ke spuštění
> Nakonfigurovali jsme váš projekt s hodnotami vlastností vaší aplikace a je připravený ke spuštění. 
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Krok 3: Spuštění projektu sady Visual Studio
> 1. Extrahovat soubor ZIP do místní složky v kořenové složce – například **C:\Azure-Samples**
> 1. Otevření řešení v sadě Visual Studio 
> 1. Upravte soubor **appsettings.json.** Vyhledejte `ClientId` a aktualizujte hodnotu `ClientId` **id aplikace (klienta)** aplikace, kterou jste zaregistrovali. 
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here"
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```



> [!div renderon="docs"]
> Kde:
> - `Enter_the_Application_Id_here`- je **ID aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali na webu Azure Portal. **ID aplikace (klienta)** najdete na stránce **Přehled** aplikace.
> - `Enter_the_Tenant_Info_Here`- je jednou z následujících možností:
>   - Pokud vaše aplikace podporuje **účty pouze v tomto organizačním adresáři**, nahraďte tuto hodnotu **ID klienta** nebo **názvem klienta** (například contoso.microsoft.com)
>   - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu za `organizations`.
>   - Pokud vaše aplikace podporuje režim **Všichni uživatelé účtu Microsoft**, nahraďte tuto hodnotu za `common`.
>
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.

## <a name="more-information"></a>Další informace

Tato část poskytuje přehled kódu potřebného k přihlášení uživatelů. Tento přehled může být užitečné pochopit, jak funguje kód, hlavní argumenty a také pokud chcete přidat přihlášení k existující ASP.NET základní aplikace.

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Spouštěcí třída

*Middleware Microsoft.AspNetCore.Authentication* používá třídu Startup, která je spuštěna při inicializaci procesu hostování:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Microsoft identity platform

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

Metoda `AddAuthentication` konfiguruje službu pro přidání ověřování založeného na souborech cookie, které se používá ve scénářích prohlížeče a k nastavení výzvy na OpenID Connect. 

Řádek obsahující `.AddAzureAd` přidá microsoft identity platformy ověřování do aplikace. Je pak nakonfigurován pro přihlášení pomocí koncového bodu platformy identity Microsoftu.

> |Kde  |  |
> |---------|---------|
> | ClientId  | ID aplikace (klienta) z aplikace registrované na webu Azure Portal. |
> | Autorita | Koncový bod STS pro uživatele k ověření. Obvykle se <https://login.microsoftonline.com/{tenant}/v2.0> jedná o veřejný cloud, kde {tenant} je název vašeho klienta nebo ID klienta nebo *běžné* pro odkaz na společný koncový bod (používá se pro víceklientské aplikace) |
> | Parametry ověření tokenu | Seznam parametrů pro ověřování tokenů; V tomto `ValidateIssuer` případě je `false` nastavena tak, aby označovala, že může přijímat přihlášení z jakýchkoli osobních nebo pracovních nebo školních účtů. |


> [!NOTE]
> Nastavení `ValidateIssuer = false` je zjednodušení pro tento rychlý start. V reálných aplikacích je třeba ověřit vystavittele.
> Podívejte se na ukázky, abyste pochopili, jak to udělat.

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrana řadiče nebo akcí řadiče

Pomocí atributu můžete chránit metody `[Authorize]` řadiče nebo řadiče. Tento atribut omezuje přístup k řadiči nebo metodám tím, že umožňuje pouze ověřeným uživatelům, což znamená, že výzvu ověřování lze spustit pro přístup k řadiči, pokud uživatel není ověřen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Podívejte se na úložiště GitHub pro tento kurz ASP.NET Core pro další informace, včetně pokynů, jak přidat ověřování do zbrusu nové webové aplikace ASP.NET, jak volat Microsoft Graph a další rozhraní API Microsoft, jak volat vlastní rozhraní API, jak přidat autorizaci, jak přihlásit uživatele v národních cloudech nebo se sociálními identitami a dalšími :

> [!div class="nextstepaction"]
> [kurz ASP.NET Základní webová aplikace](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

Pomozte nám vylepšit platformu identit Microsoftu. Řekněte nám, co si myslíte, že dokončení krátké hod-průzkum u dvou otázek.

> [!div class="nextstepaction"]
> [Průzkum platformy identity Microsoftu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
