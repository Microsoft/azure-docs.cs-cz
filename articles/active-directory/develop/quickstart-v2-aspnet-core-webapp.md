---
title: Přidání přihlášení do Microsoftu pro ASP.NET Core Web Apps – Microsoft Identity Platform | Azure
description: Naučte se implementovat přihlášení Microsoftu do ASP.NET Core webové aplikace pomocí služby OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 1bc8a9c06b564282af15d6a6aa53b6fc696857b2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165766"
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
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> K registraci aplikace a ručnímu přidání registračních informací aplikace do řešení použijte následující postup:
>
> 1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>    - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AspNetCore-Quickstart`.
>    - V seznamu **identifikátor URI pro přesměrování**přidejte `https://localhost:44321/` a vyberte **Registrovat**.
> 1. Vyberte nabídku **ověřování** a přidejte následující informace:
>    - V rozevíracích **identifikátorech URI pro přesměrování**přidejte `https://localhost:44321/signin-oidc` a vyberte **Uložit**.
>    - V části **Upřesnit nastavení** nastavte **adresu URL pro odhlášení** na `https://localhost:44321/signout-oidc` .
>    - V části **Implicitní udělení** zaškrtněte políčko **Tokeny ID**.
>    - Vyberte **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby ukázka kódu pro tento rychlý Start fungovala, je třeba přidat adresy URL odpovědi jako `https://localhost:44321/` a `https://localhost:44321/signin-oidc` , přidat adresu URL pro odhlášení `https://localhost:44321/signout-oidc` a vyžádat tokeny ID, které budou vydány koncovým bodem autorizace.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2: stažení projektu ASP.NET Core

> [!div renderon="docs"]
> [Stažení řešení ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Spusťte projekt.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
> Nakonfigurovali jsme projekt s hodnotami vlastností vaší aplikace a je připraven ke spuštění.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-run-your-aspnet-core-project"></a>Krok 3: spuštění projektu ASP.NET Core
> 1. Extrahujte soubor zip do místní složky v kořenové složce, například **C:\Azure-Samples**
> 1. Otevřete řešení v integrovaném vývojovém prostředí
> 1. Upravte **appsettings.jsv** souboru. Vyhledejte `ClientId` a aktualizujte hodnotu `ClientId` s hodnotou **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali.
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here"
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```



> [!div renderon="docs"]
> Kde:
> - `Enter_the_Application_Id_here`– je **ID aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali v Azure Portal. **ID aplikace (klienta)** můžete najít na stránce **Přehled** aplikace.
> - `Enter_the_Tenant_Info_Here`– je jedna z následujících možností:
>   - Pokud vaše aplikace podporuje **jenom účty v tomto organizačním adresáři**, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).
>   - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu za `organizations`.
>   - Pokud vaše aplikace podporuje režim **Všichni uživatelé účtu Microsoft**, nahraďte tuto hodnotu za `common`.
>
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.

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

Metoda `AddAuthentication` nakonfiguruje službu tak, aby přidala ověřování na základě souborů cookie, které se používá v prohlížečových scénářích a nastavení výzvy pro OpenID Connect.

Řádek, který obsahuje `.AddAzureAd` , přidá do vaší aplikace ověřování Microsoft Identity Platform. Pak se nakonfiguruje tak, aby se přihlásilo pomocí koncového bodu Microsoft Identity Platform.

> |Kde | Description |
> |---------|---------|
> | ClientId  | ID aplikace (klienta) z aplikace zaregistrované v Azure Portal. |
> | Autorita | Koncový bod služby STS pro uživatele, který se má ověřit Obvykle je to <https://login.microsoftonline.com/{tenant}/v2.0> pro veřejný cloud, kde {tenant} je název vašeho tenanta nebo ID tenanta nebo *společný* odkaz na společný koncový bod (používaný pro víceklientské aplikace). |
> | TokenValidationParameters | Seznam parametrů pro ověřování tokenů; V tomto případě `ValidateIssuer` je nastaveno, aby `false` označoval, že může přijímat přihlášení z libovolného osobního nebo pracovního nebo školního účtu. |


> [!NOTE]
> Nastavení `ValidateIssuer = false` je zjednodušení pro tento rychlý Start. Ve skutečných aplikacích potřebujete k ověření vystavitele.
> V ukázkách si můžete uvědomit, jak to udělat.
>
> Všimněte si také `Configure` metody, která obsahuje dvě důležité metody: `app.UseCookiePolicy()` a`app.UseAuthentication()`

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more core
    app.UseCookiePolicy();
    app.UseAuthentication();
    // more core
}
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrana řadiče nebo akcí řadiče

Pomocí atributu můžete chránit metody kontroleru nebo kontroleru `[Authorize]` . Tento atribut omezuje přístup k řadiči nebo metodám tím, že povoluje pouze ověřené uživatele, což znamená, že je možné spustit ověřovací výzvu pro přístup k řadiči, pokud se uživatel neověřuje.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace najdete v tomto kurzu v úložišti GitHub pro tento ASP.NET Core, včetně pokynů, jak přidat ověřování do značky nové ASP.NET Core webové aplikace, jak volat Microsoft Graph a dalších rozhraní Microsoft API, jak volat vlastní rozhraní API, jak přidat autorizaci, jak přihlašovat uživatele v národních cloudech nebo pomocí sociálních identit a dalších. :

> [!div class="nextstepaction"]
> [Kurz ASP.NET Core webové aplikace](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
