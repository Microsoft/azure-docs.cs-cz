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
ms.openlocfilehash: e7296b04e3e912e96ac8c2ed77b44288324c262f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578698"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Rychlý Start: Přidání přihlašování s Microsoftem do webové aplikace ASP.NET Core

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která demonstruje, jak se může webová aplikace ASP.NET Core přihlašovat uživatelům z jakékoli organizace Azure Active Directory (Azure AD).  

> [!div renderon="docs"]
> Následující diagram ukazuje, jak ukázková aplikace funguje:
>
> ![Diagram interakce mezi webovým prohlížečem, webovou aplikací a platformou Microsoft identity v ukázkové aplikaci.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Předpoklady
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) nebo [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>Registrace a stažení aplikace
> Máte dvě možnosti, jak začít sestavovat aplikaci: Automatická nebo ruční konfigurace.
>
> ### <a name="automatic-configuration"></a>Automatická konfigurace
> Pokud chcete svou aplikaci automaticky nakonfigurovat a potom stáhnout ukázku kódu, postupujte podle následujících kroků:
>
> 1. Přejít na <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">stránku Azure Portal pro registraci aplikace</a>.
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci jediným kliknutím.
>
> ### <a name="manual-configuration"></a>Ruční konfigurace
> Pokud chcete ručně nakonfigurovat ukázku aplikace a kódu, použijte následující postupy.
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> 1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Máte-li přístup k více klientům, použijte filtr **adresář a odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Jako **název** zadejte název vaší aplikace. Například zadejte **AspNetCore-rychlý Start**. Uživatelé vaší aplikace uvidí tento název a později ho můžete změnit.
> 1. Jako **identifikátor URI přesměrování** zadejte **https://localhost:44321/signin-oidc** .
> 1. Vyberte **Zaregistrovat**.
> 1. V části **Spravovat** vyberte **ověřování**.
> 1. Jako **adresu URL pro odhlášení front-Channel** zadejte **https://localhost:44321/signout-oidc** .
> 1. V části **implicitní udělení a hybridní toky** vyberte **tokeny ID**.
> 1. Vyberte **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Ukázka kódu v tomto rychlém startu funguje:
> - Pro **identifikátor URI přesměrování** zadejte **https://localhost:44321/** a **https://localhost:44321/signin-oidc** .
> - Jako **adresu URL pro odhlášení front-Channel** zadejte **https://localhost:44321/signout-oidc** . 
>
> Koncový bod autorizace bude vydávat tokeny žádostí o ID.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-aspnet-core-project"></a>Krok 2: stažení ASP.NET Coreho projektu

> [!div renderon="docs"]
> [Stažení řešení ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Spusťte projekt.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
> Nakonfigurovali jsme projekt s hodnotami vlastností vaší aplikace a je připravený ke spuštění.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Krok 3: konfigurace projektu ASP.NET Core
> 1. Extrahujte archiv zip do místní složky poblíž kořene jednotky. Například extrahujte do *C:\Azure-Samples*.
> 
>    K tomu, aby se předešlo chybám způsobeným omezeními délky cest ve Windows, doporučujeme extrakci archivu do adresáře v blízkosti vaší jednotky.
> 1. Otevřete řešení v aplikaci Visual Studio 2019.
> 1. Otevřete *appsettings.jsv* souboru a upravte následující kód:
>
>    ```json
>    "Domain": "Enter the domain of your tenant, e.g. contoso.onmicrosoft.com",
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Nahraďte `Enter_the_Application_Id_here` ID aplikace (klienta) aplikace, kterou jste zaregistrovali v Azure Portal. Hodnotu **ID aplikace (klienta)** můžete najít na stránce **Přehled** aplikace.
>    - Nahraďte `common` některou z následujících možností:
>       - Pokud vaše aplikace podporuje **jenom účty v tomto organizačním adresáři**, nahraďte tuto hodnotu ID adresáře (tenant) (identifikátor GUID) nebo názvem tenanta (například `contoso.onmicrosoft.com` ). Hodnotu **ID adresáře (tenant)** najdete na stránce **Přehled** aplikace.
>       - Pokud vaše aplikace podporuje **účty v jakémkoli organizačním adresáři**, nahraďte tuto hodnotu hodnotou `organizations` .
>       - Pokud vaše aplikace podporuje **všechny účet Microsoft uživatele**, ponechte tuto hodnotu jako `common` .
>
> V tomto rychlém startu Neměňte žádné jiné hodnoty v *appsettings.js* souboru.
>
> #### <a name="step-4-build-and-run-the-application"></a>Krok 4: Sestavení a spuštění aplikace
>
> Sestavte a spusťte aplikaci v aplikaci Visual Studio tak, že vyberete nabídku **ladění** > **Spustit ladění** nebo stisknete klávesu F5.
>
> Zobrazí se výzva k zadání přihlašovacích údajů a potom se zobrazí výzva k vyjádření souhlasu s oprávněními, která vaše aplikace vyžaduje. Na příkazovém řádku pro vyjádření souhlasu vyberte **přijmout** .
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Snímek obrazovky s dialogovým oknem souhlasu se zobrazením oprávnění, která aplikace požaduje od uživatele.":::
>
> Po vyjádření souhlasu s požadovanými oprávněními aplikace zobrazí, že jste se úspěšně přihlásili pomocí přihlašovacích údajů Azure Active Directory.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Snímek obrazovky webového prohlížeče, který zobrazuje běžící webovou aplikaci a přihlášeného uživatele.":::

## <a name="more-information"></a>Další informace

V této části najdete přehled kódu potřebného k přihlášení uživatelů. Tento přehled může být užitečný pro pochopení, jak kód funguje, co jsou hlavní argumenty a jak přidat přihlášení do existující ASP.NET Core aplikace.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Jak ukázka funguje
>
> ![Diagram interakce mezi webovým prohlížečem, webovou aplikací a platformou Microsoft identity v ukázkové aplikaci.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Spouštěcí třída

Middleware *Microsoft. AspNetCore. Authentication* používá `Startup` třídu, která se spouští při spuštění hostitelského procesu:

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

`AddAuthentication()`Metoda nakonfiguruje službu tak, aby přidala ověřování založené na souborech cookie. Toto ověřování se používá ve scénářích prohlížeče a k nastavení výzvy pro OpenID připojení.

Řádek, který obsahuje, `.AddMicrosoftIdentityWebApp` přidá do vaší aplikace ověřování platformy Microsoft Identity Platform. Aplikace je pak nakonfigurována pro přihlašování uživatelů na základě následujících informací v `AzureAD` části *appsettings.jsv* konfiguračním souboru:

| *appsettings.jsna* klíč | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | ID aplikace (klienta) aplikace zaregistrované v Azure Portal.                                                                                       |
| `Instance`             | Koncový bod služby tokenu zabezpečení (STS) pro ověření uživatele. Tato hodnota se obvykle `https://login.microsoftonline.com/` označuje jako veřejný cloud Azure. |
| `TenantId`             | Název vašeho tenanta nebo ID klienta (identifikátor GUID) nebo `common` pro přihlášení uživatelů pomocí pracovních nebo školních účtů nebo osobních účtů společnosti Microsoft.                             |

`Configure()`Metoda obsahuje dvě důležité metody `app.UseAuthentication()` a `app.UseAuthorization()` , které umožňují jejich pojmenované funkce. V metodě je také `Configure()` nutné zaregistrovat webové trasy Microsoft identity pomocí alespoň jednoho volání `endpoints.MapControllerRoute()` nebo volání `endpoints.MapControllers()` :

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

### <a name="attribute-for-protecting-a-controller-or-methods"></a>Atribut pro ochranu kontroleru nebo metod

Pomocí atributu můžete chránit metody kontroleru nebo kontroleru `[Authorize]` . Tento atribut omezuje přístup k řadiči nebo metodám tím, že povoluje pouze ověřené uživatele. Výzva k ověření se pak může spustit pro přístup k řadiči, pokud se uživatel neověřuje.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Úložiště GitHub, které obsahuje tento kurz ASP.NET Core, obsahuje pokyny a další ukázky kódu, které vám ukážou, jak:

- Přidejte ověřování do nové ASP.NET Core webové aplikace.
- Zavolejte Microsoft Graph, jiná rozhraní API Microsoftu nebo vaše vlastní webová rozhraní API.
- Přidejte autorizaci.
- Přihlaste se k uživatelům v národních cloudech nebo pomocí sociálních identit.

> [!div class="nextstepaction"]
> [ASP.NET Core výukových kurzů pro webové aplikace na GitHubu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
