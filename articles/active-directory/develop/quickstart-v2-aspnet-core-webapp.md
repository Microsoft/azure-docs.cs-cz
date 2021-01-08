---
title: 'Rychlý Start: přidání přihlášení Microsoftu do webové aplikace ASP.NET Core | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak aplikace implementuje přihlášení Microsoftu na ASP.NET Core webové aplikaci pomocí nástroje OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 34af3a7b6c3dd9420c07b6b29ff114b8139fea39
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011964"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Rychlý Start: Přidání přihlašování s Microsoftem do webové aplikace ASP.NET Core

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která demonstruje, jak se může webová aplikace ASP.NET Core přihlašovat uživatelům z jakékoli organizace Azure Active Directory (Azure AD).  

Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Požadavky
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) nebo [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/download)
>
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
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se <a href="https://portal.azure.com/" target="_blank">k <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>.
> 1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Zadejte **název** vaší aplikace, například `AspNetCore-Quickstart` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
> 1. Zadejte **identifikátor URI přesměrování** . `https://localhost:44321/`
> 1. Vyberte **Zaregistrovat**.
> 1. V části **Spravovat** vyberte **ověřování**.
> 1. V části **identifikátory URI pro přesměrování** vyberte **Přidat identifikátor URI** a potom zadejte `https://localhost:44321/signin-oidc`
> 1. Zadejte **adresu URL pro odhlášení** . `https://localhost:44321/signout-oidc`
> 1. V části **Implicitní udělení** vyberte **Tokeny ID**.
> 1. Vyberte **Uložit**.

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
> [Stažení řešení ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Spusťte projekt.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
> Nakonfigurovali jsme projekt s hodnotami vlastností vaší aplikace a je připraven ke spuštění.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Krok 3: konfigurace projektu ASP.NET Core
> 1. Extrahujte archiv zip do místní složky poblíž kořene jednotky. Například do *C:\Azure-Samples*.
> 1. Otevřete řešení v aplikaci Visual Studio 2019.
> 1. Otevřete *appsettings.jsv* souboru a upravte následující:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Nahraďte `Enter_the_Application_Id_here` **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali v Azure Portal. **ID aplikace (klienta)** můžete najít na stránce **Přehled** aplikace.
>    - Nahraďte `common` některou z následujících možností:
>       - Pokud vaše aplikace podporuje **jenom účty v tomto organizačním adresáři**, nahraďte tuto hodnotu **ID adresáře (TENANT)** (identifikátor GUID) nebo **názvem tenanta** (například `contoso.onmicrosoft.com` ). **ID adresáře (tenant)** najdete na stránce **Přehled** aplikace.
>       - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu za `organizations`.
>       - Pokud vaše aplikace podporuje **všechny účet Microsoft uživatele**, ponechte tuto hodnotu jako `common`
>
> V tomto rychlém startu Neměňte žádné jiné hodnoty v *appsettings.js* souboru.
>
> #### <a name="step-4-build-and-run-the-application"></a>Krok 4: Sestavení a spuštění aplikace
>
> Sestavte a spusťte aplikaci v aplikaci Visual Studio tak, že vyberete nabídku **ladění** > **Spustit ladění** nebo stisknete klávesu `F5` .
>
> Zobrazí se výzva k zadání přihlašovacích údajů a potom se zobrazí výzva k vyjádření souhlasu s oprávněními, které vaše aplikace vyžaduje. Na příkazovém řádku pro vyjádření souhlasu vyberte **přijmout** .
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Dialog pro vyjádření souhlasu, který zobrazuje oprávnění, která aplikace požaduje od >ho uživatele":::
>
> Po souhlasu s požadovanými oprávněními aplikace zobrazí, že jste se úspěšně přihlásili pomocí přihlašovacích údajů Azure Active Directory.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Webový prohlížeč zobrazující běžící webovou aplikaci a přihlášeného uživatele":::

## <a name="more-information"></a>Další informace

V této části najdete přehled kódu potřebného k přihlášení uživatelů. Tento přehled může být užitečný pro pochopení, jak kód funguje, hlavní argumenty a také, pokud chcete přidat přihlášení do existující aplikace ASP.NET Core.

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Spouštěcí třída

Middleware *Microsoft. AspNetCore. Authentication* používá `Startup` třídu, která se spustí při inicializaci hostujícího procesu:

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

`AddAuthentication()`Metoda nakonfiguruje službu tak, aby přidala ověřování pomocí souborů cookie, které se používá v prohlížečových scénářích a nastavení výzvy pro OpenID připojení.

Řádek, který obsahuje, `.AddMicrosoftIdentityWebApp` přidá do vaší aplikace ověřování platformy Microsoft Identity Platform. Pak se nakonfiguruje tak, aby se přihlásilo pomocí koncového bodu Microsoft Identity Platform na základě informací v `AzureAD` části *appsettings.jsv* konfiguračním souboru:

| *appsettings.jsna* klíč | Popis                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **ID aplikace (klienta)** aplikace zaregistrované v Azure Portal.                                                                                       |
| `Instance`             | Koncový bod služby tokenu zabezpečení (STS) pro ověření uživatele. Tato hodnota se obvykle `https://login.microsoftonline.com/` označuje jako veřejný cloud Azure. |
| `TenantId`             | Název vašeho tenanta nebo jeho ID klienta (identifikátor GUID) nebo *běžné* pro přihlašování uživatelů pomocí pracovních nebo školních účtů nebo osobních účtů společnosti Microsoft.                             |

`Configure()`Metoda obsahuje dvě důležité metody `app.UseAuthentication()` a `app.UseAuthorization()` , které umožňují jejich pojmenované funkce. V metodě je také `Configure()` nutné zaregistrovat trasy webu Microsoft Identity web s alespoň jedním voláním `endpoints.MapControllerRoute()` nebo voláním `endpoints.MapControllers()` .

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrana řadiče nebo akcí řadiče

Pomocí atributu můžete chránit metody kontroleru nebo kontroleru `[Authorize]` . Tento atribut omezuje přístup k řadiči nebo metodám tím, že povoluje pouze ověřené uživatele, což znamená, že je možné spustit ověřovací výzvu pro přístup k řadiči, pokud se uživatel neověřuje.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Úložiště GitHub, které obsahuje tento kurz ASP.NET Core, obsahuje pokyny a další ukázky kódu, které vám ukážou, jak:

- Přidání ověřování do nové ASP.NET Core webové aplikace
- Volání Microsoft Graph, dalších rozhraní API Microsoftu nebo vašich vlastních webových rozhraní API
- Přidat autorizaci
- Přihlaste se k uživatelům v národních cloudech nebo pomocí sociálních identit

> [!div class="nextstepaction"]
> [ASP.NET Core výukových kurzů pro webové aplikace na GitHubu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
