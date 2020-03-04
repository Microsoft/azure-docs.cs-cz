---
title: Přidání přihlášení do Microsoftu pro ASP.NET Core Web Apps – Microsoft Identity Platform | Azure
description: Naučte se implementovat přihlášení Microsoftu do ASP.NET Core webové aplikace pomocí služby OpenID Connect.
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
ms.openlocfilehash: 72c363c34a3e7e01cb32917dd87237e4bbfc9490
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249152"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Rychlý Start: Přidání přihlašování s Microsoftem do webové aplikace ASP.NET Core
V tomto rychlém startu se naučíte, jak může webová aplikace ASP.NET Core přihlašovat osobní účty (hotmail.com, outlook.com, ostatní) a pracovní a školní účty z jakékoli instance Azure Active Directory (Azure AD). (Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.)
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Expresní] [Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejít na [Registrace aplikací Azure Portal](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte si aplikaci
> K registraci aplikace a ručnímu přidání registračních informací aplikace do řešení použijte následující postup:
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>    - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AspNetCore-Quickstart`.
>    - V **identifikátoru URI přesměrování**přidejte `https://localhost:44321/`a vyberte **Registrovat**.
> 1. Vyberte nabídku **ověřování** a přidejte následující informace:
>    - V **rozevíracích** **identifikátorech URI pro přesměrování**přidejte `https://localhost:44321/signin-oidc`a vyberte Uložit.
>    - V části **Upřesnit nastavení** nastavte **adresu URL pro odhlášení** na `https://localhost:44321/signout-oidc`.
>    - V části **implicitní udělení**ověřte **tokeny ID**.
>    - Vyberte **Save** (Uložit).

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby ukázka kódu pro tento rychlý Start fungovala, je třeba přidat adresy URL odpovědi jako `https://localhost:44321/` a `https://localhost:44321/signin-oidc`, přidat odhlašovací adresu URL jako `https://localhost:44321/signout-oidc`a vyžádat tokeny ID, které budou vydány koncovým bodem autorizace.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2: stažení projektu ASP.NET Core

> [!div renderon="docs"]
> [Stažení řešení pro Visual Studio 2019](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Spusťte projekt pomocí sady Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stažení ukázky kódu]()

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
> Nakonfigurovali jsme projekt s hodnotami vlastností vaší aplikace a je připraven ke spuštění. 
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here
> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Krok 3: spuštění projektu sady Visual Studio
> 1. Extrahujte soubor zip do místní složky v kořenové složce, například **C:\Azure-Samples**
> 1. Otevřete řešení v aplikaci Visual Studio 
> 1. Upravte soubor **appSettings. JSON** . Vyhledejte `ClientId` a aktualizujte hodnotu `ClientId` s hodnotou **ID aplikace (klienta)** , kterou jste zaregistrovali. 
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here"
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```



> [!div renderon="docs"]
> Kde:
> - `Enter_the_Application_Id_here` – je **ID aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali v Azure Portal. **ID aplikace (klienta)** můžete najít na stránce **Přehled** aplikace.
> - `Enter_the_Tenant_Info_Here` – jedná se o jednu z následujících možností:
>   - Pokud vaše aplikace podporuje **jenom účty v tomto organizačním adresáři**, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).
>   - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu za `organizations`.
>   - Pokud vaše aplikace podporuje režim **Všichni uživatelé účtu Microsoft**, nahraďte tuto hodnotu za `common`.
>
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)** , **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.

## <a name="more-information"></a>Další informace

V této části najdete přehled kódu potřebného k přihlášení uživatelů. Tento přehled může být užitečný pro pochopení, jak kód funguje, hlavní argumenty a také, pokud chcete přidat přihlášení do existující aplikace ASP.NET Core.

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Spouštěcí třída

Middleware *Microsoft. AspNetCore. Authentication* používá spouštěcí třídu, která se spustí při inicializaci hostitelského procesu:

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

Metoda `AddAuthentication` nakonfiguruje službu tak, aby přidala ověřování pomocí souborů cookie, které se používá v prohlížečových scénářích a nastavení výzvy pro OpenID připojení. 

Řádek obsahující `.AddAzureAd` přidá do vaší aplikace ověřování platformy Microsoft Identity Platform. Pak se nakonfiguruje tak, aby se přihlásilo pomocí koncového bodu Microsoft Identity Platform.

> |Kde  |  |
> |---------|---------|
> | ClientId  | ID aplikace (klienta) z aplikace zaregistrované v Azure Portal. |
> | Dohled | Koncový bod služby STS pro uživatele, který se má ověřit Obvykle je to <https://login.microsoftonline.com/{tenant}/v2.0> pro veřejný cloud, kde {tenant} je název vašeho tenanta nebo ID tenanta nebo *společný* odkaz na společný koncový bod (používaný pro víceklientské aplikace). |
> | TokenValidationParameters | Seznam parametrů pro ověřování tokenů; V takovém případě je `ValidateIssuer` nastavená na `false`, která označuje, že může přijímat přihlášení z libovolného osobního nebo pracovního nebo školního účtu. |


> [!NOTE]
> Nastavení `ValidateIssuer = false` je zjednodušení pro tento rychlý Start. Ve skutečných aplikacích potřebujete k ověření vystavitele.
> V ukázkách si můžete uvědomit, jak to udělat.

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrana řadiče nebo akcí řadiče

Pomocí atributu `[Authorize]` můžete chránit metody kontroleru nebo kontroleru. Tento atribut omezuje přístup k řadiči nebo metodám tím, že povoluje pouze ověřené uživatele, což znamená, že je možné spustit ověřovací výzvu pro přístup k řadiči, pokud se uživatel neověřuje.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace najdete v tomto kurzu ASP.NET Core v úložišti GitHub, kde najdete další informace, včetně pokynů k přidání ověřování do značky nové ASP.NET Core webové aplikace, způsobu volání Microsoft Graph a dalších rozhraní API Microsoftu, jak volat vlastní rozhraní API, jak přidat autorizace, jak přihlašovat uživatele v národních cloudech nebo pomocí sociálních identit a dalších:

> [!div class="nextstepaction"]
> [Kurz ASP.NET Core webové aplikace](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
