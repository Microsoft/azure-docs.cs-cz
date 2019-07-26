---
title: Microsoft Identity Platform ASP.NET Core Web App Starter | Azure
description: Naučte se implementovat přihlášení Microsoftu do ASP.NET Core webové aplikace pomocí služby OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4723b224d61b2ccc2b563150befa5ea2d33453ad
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335610"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Rychlý start: Přidání přihlašování pomocí Microsoftu do ASP.NET Core webové aplikace

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

V tomto rychlém startu se dozvíte, jak může webová aplikace ASP.NET Core přihlašovat osobní účty (hotmail.com, outlook.com, ostatní) a pracovní a školní účty z jakékoli instance Azure Active Directory (Azure AD).

![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * Express [Možnost 1: Zaregistrujte a automaticky nakonfigurujte svoji aplikaci a Stáhněte si ukázku kódu.](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Zásah [Možnost 2: Registrace a ruční konfigurace vaší aplikace a ukázky kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Zaregistrujte a automaticky nakonfigurujte svoji aplikaci a Stáhněte si ukázku kódu.
>
> 1. Přejít na [Registrace aplikací Azure Portal](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace vaší aplikace a ukázky kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte svoji aplikaci.
> K registraci aplikace a ručnímu přidání registračních informací aplikace do řešení použijte následující postup:
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>    - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AspNetCore-Quickstart`.
>    - V seznamu **identifikátor URI**pro `https://localhost:44321/`přesměrování přidejte a vyberte **Registrovat**.
> 1. Vyberte nabídku **ověřování** a přidejte následující informace:
>    - V rozevíracích identifikátorech `https://localhost:44321/signin-oidc`URI pro **přesměrování**přidejte a vyberte **Uložit**.
>    - V části **Upřesnit nastavení** nastavte **adresu URL** pro odhlášení `https://localhost:44321/signout-oidc`na.
>    - V části **implicitní udělení**ověřte **tokeny ID**.
>    - Vyberte **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby ukázka kódu pro tento rychlý Start fungovala, je třeba přidat adresy URL `https://localhost:44321/` odpovědi jako a `https://localhost:44321/signin-oidc`, `https://localhost:44321/signout-oidc`přidat adresu URL pro odhlášení a vyžádat tokeny ID, které budou vydány koncovým bodem autorizace.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2: Stažení projektu ASP.NET Core

- [Stažení řešení pro Visual Studio 2019](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: Konfigurace projektu sady Visual Studio

1. Extrahujte soubor zip do místní složky v kořenové složce, například **C:\Azure-Samples**
1. Pokud používáte Visual Studio 2019, otevřete řešení v aplikaci Visual Studio (volitelné).
1. Upravte soubor **appSettings. JSON** . Vyhledejte `ClientId` a aktualizujte `ClientId` hodnotu s hodnotou **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Tento rychlý Start podporuje Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> Kde:
> - `Enter_the_Application_Id_here`– je **ID aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali v Azure Portal. **ID aplikace (klienta)** můžete najít na stránce **Přehled** aplikace.
> - `Enter_the_Tenant_Info_Here`– je jedna z následujících možností:
>   - Pokud vaše aplikace podporuje **jenom účty v tomto organizačním adresáři**, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).
>   - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu za `organizations`.
>   - Pokud vaše aplikace podporuje režim **Všichni uživatelé účtu Microsoft**, nahraďte tuto hodnotu za `common`.
>
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)** , **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.

## <a name="more-information"></a>Další informace

V této části najdete přehled kódu potřebného k přihlášení uživatelů. Tento přehled může být užitečný pro pochopení, jak kód funguje, hlavní argumenty a také, pokud chcete přidat přihlášení do existující aplikace ASP.NET Core.

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

Metoda `AddAuthentication` nakonfiguruje službu tak, aby přidala ověřování na základě souborů cookie, které se používá v prohlížečových scénářích a nastavení výzvy pro OpenID Connect. 

Řádek, který `.AddAzureAd` obsahuje, přidá do vaší aplikace ověřování Microsoft Identity Platform. Pak se nakonfiguruje tak, aby se přihlásilo pomocí koncového bodu Microsoft Identity Platform.

> |Kde  |  |
> |---------|---------|
> | ClientId  | ID aplikace (klienta) z aplikace zaregistrované v Azure Portal. |
> | Autorita | Koncový bod služby STS pro uživatele, který se má ověřit Obvykle je <https://login.microsoftonline.com/{tenant}/v2.0> to pro veřejný cloud, kde {tenant} je název vašeho tenanta nebo ID tenanta nebo *společný* odkaz na společný koncový bod (používaný pro víceklientské aplikace). |
> | TokenValidationParameters | Seznam parametrů pro ověřování tokenů; V tomto případě `ValidateIssuer` je `false` nastaveno, aby označoval, že může přijímat přihlášení z libovolného osobního nebo pracovního nebo školního účtu. |


> [!NOTE]
> Nastavení `ValidateIssuer = false` je zjednodušení pro tento rychlý Start. Ve skutečných aplikacích potřebujete k ověření vystavitele.
> V ukázkách si můžete uvědomit, jak to udělat.

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrana řadiče nebo akcí řadiče

Pomocí `[Authorize]` atributu můžete chránit metody kontroleru nebo kontroleru. Tento atribut omezuje přístup k řadiči nebo metodám tím, že povoluje pouze ověřené uživatele, což znamená, že je možné spustit ověřovací výzvu pro přístup k řadiči, pokud se uživatel neověřuje.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další postup

Další informace najdete v tomto kurzu ASP.NET Core v úložišti GitHub, kde najdete další informace, včetně pokynů k přidání ověřování do značky nové ASP.NET Core webové aplikace, způsobu volání Microsoft Graph a dalších rozhraní API Microsoftu, jak volat vlastní rozhraní API, jak přidat autorizace, jak přihlašovat uživatele v národních cloudech nebo pomocí sociálních identit a dalších:

> [!div class="nextstepaction"]
> [Kurz ASP.NET Core webové aplikace](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)