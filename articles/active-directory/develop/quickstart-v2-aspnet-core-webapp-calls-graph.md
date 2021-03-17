---
title: 'Rychlý Start: ASP.NET Core webovou aplikaci, která se přihlásí uživatelům a voláním Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak aplikace využívá Microsoft. identity. Web k implementaci přihlášení Microsoftu do ASP.NET Core webové aplikace pomocí OpenID Connect a volání Microsoft Graph
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/10/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 0a5d44f0cc42b80fa344e06f31275e87d265800e
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103834"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>Rychlý Start: ASP.NET Core webovou aplikaci, která podepisuje uživatele a volá Microsoft Graph jejich jménem

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která demonstruje, jak se může webová aplikace ASP.NET Core přihlašovat uživatelům z jakékoli organizace Azure Active Directory (Azure AD) a volá Microsoft Graph.  

Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Požadavky
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) nebo [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-quickstart-app"></a>Registrace a stažení aplikace pro rychlý Start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Expresní] [Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejít k prostředí rychlý Start pro <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetCoreWebAppQuickstartPage/sourceType/docs" target="_blank">Azure Portal registrace aplikací</a>
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Zadejte **název** vaší aplikace, například `AspNetCoreWebAppCallsGraph-Quickstart` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
> 1. Zadejte **identifikátor URI přesměrování** `https://localhost:44321/signin-oidc` .
> 1. Vyberte **Zaregistrovat**.
> 1. V části **Spravovat** vyberte **ověřování**.
> 1. Zadejte **adresu URL pro odhlášení front-Channel** `https://localhost:44321/signout-oidc` .
> 1. Vyberte **Uložit**.
> 1. V části **Spravovat** vyberte **certifikáty & tajných klíčů**  >  **nový tajný klíč klienta**.
> 1. Zadejte **Popis**, například `clientsecret1` .
> 1. Pro vypršení platnosti tajného kódu vyberte **1 rok** .
> 1. Vyberte **Přidat** a hned zaznamenejte **hodnotu** tajného klíče pro použití v pozdějším kroku. Hodnota tajného klíče se *nikdy nezobrazuje* a je irretrievable jakýmkoli jiným způsobem. Zaznamenejte si ho na bezpečném místě, stejně jako jakékoli heslo.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby ukázka kódu v tomto rychlém startu fungovala, přidejte do registrace aplikace **identifikátor URI přesměrování** `https://localhost:44321/signin-oidc` a **adresu URL pro odhlášení front-Channel** `https://localhost:44321/signout-oidc` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-aspnet-core-project"></a>Krok 2: stažení ASP.NET Coreho projektu

> [!div renderon="docs"]
> [Stažení řešení ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Spusťte projekt.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

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
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - Nahraďte `Enter_the_Application_Id_here` **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali v Azure Portal. **ID aplikace (klienta)** můžete najít na stránce **Přehled** aplikace.
>    - Nahraďte `common` některou z následujících možností:
>       - Pokud vaše aplikace podporuje **jenom účty v tomto organizačním adresáři**, nahraďte tuto hodnotu **ID adresáře (TENANT)** (identifikátor GUID) nebo **názvem tenanta** (například `contoso.onmicrosoft.com` ). **ID adresáře (tenant)** najdete na stránce **Přehled** aplikace.
>       - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu za `organizations`.
>       - Pokud vaše aplikace podporuje **všechny účet Microsoft uživatele**, ponechte tuto hodnotu jako `common`
>    - Nahraďte `Enter_the_Client_Secret_Here` **tajným klíčem klienta** , který jste vytvořili a nahráli v předchozím kroku.
> 
> V tomto rychlém startu Neměňte žádné jiné hodnoty v *appsettings.js* souboru.
>
> #### <a name="step-4-build-and-run-the-application"></a>Krok 4: Sestavení a spuštění aplikace
>
> Sestavte a spusťte aplikaci v aplikaci Visual Studio tak, že vyberete nabídku **ladění** > **Spustit ladění** nebo stisknete klávesu `F5` .
>
> Zobrazí se výzva k zadání přihlašovacích údajů a potom se zobrazí výzva k vyjádření souhlasu s oprávněními, které vaše aplikace vyžaduje. Na příkazovém řádku pro vyjádření souhlasu vyberte **přijmout** .
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="Dialog pro vyjádření souhlasu, který zobrazuje oprávnění, která aplikace požaduje od >ho uživatele":::
>
> Po přijetí požadovaných oprávnění aplikace zobrazí, že jste se úspěšně přihlásili pomocí přihlašovacích údajů Azure Active Directory a v části výsledek rozhraní API stránky se zobrazí vaše e-mailová adresa. To bylo extrahováno pomocí Microsoft Graph.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="Webový prohlížeč zobrazující běžící webovou aplikaci a přihlášeného uživatele":::

## <a name="about-the-code"></a>O kódu

V této části najdete přehled kódu potřebného k přihlašování uživatelů a volání rozhraní API Microsoft Graph jménem. Tento přehled může být užitečný pro pochopení, jak kód funguje, hlavní argumenty a také, pokud chcete přidat přihlášení do existující ASP.NET Core aplikace a volání Microsoft Graph. Používá [Microsoft. identity. Web](microsoft-identity-web.md), což je Obálka kolem [MSAL.NET](msal-overview.md).

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Spouštěcí třída

Middleware *Microsoft. AspNetCore. Authentication* používá `Startup` třídu, která se spustí při inicializaci hostujícího procesu:

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

`AddAuthentication()`Metoda nakonfiguruje službu tak, aby přidala ověřování pomocí souborů cookie, které se používá v prohlížečových scénářích a nastavení výzvy pro OpenID připojení.

Řádek, který obsahuje `.AddMicrosoftIdentityWebApp` , přidá do vaší aplikace ověřování Microsoft Identity Platform. K tomu poskytuje [Microsoft. identity. Web](microsoft-identity-web.md). Pak se nakonfiguruje tak, aby se přihlásila pomocí platformy Microsoft identity na základě informací v `AzureAD` části *appsettings.jsv* konfiguračním souboru:

| *appsettings.jsna* klíč | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **ID aplikace (klienta)** aplikace zaregistrované v Azure Portal.                                                                                       |
| `Instance`             | Koncový bod služby tokenu zabezpečení (STS) pro ověření uživatele. Tato hodnota se obvykle `https://login.microsoftonline.com/` označuje jako veřejný cloud Azure. |
| `TenantId`             | Název vašeho tenanta nebo jeho ID klienta (identifikátor GUID) nebo *běžné* pro přihlašování uživatelů pomocí pracovních nebo školních účtů nebo osobních účtů společnosti Microsoft.                             |

`EnableTokenAcquisitionToCallDownstreamApi`Metoda umožňuje vaší aplikaci získat token pro volání chráněných webových rozhraní API. `AddMicrosoftGraph` umožňuje vašim řadičům nebo stránkám Razor těžit přímo `GraphServiceClient` (pomocí injektáže závislosti) a `AddInMemoryTokenCaches` metody umožňují vaší aplikaci těžit z mezipaměti tokenů.

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

Můžete chránit kontrolér nebo jeho metody použitím `[Authorize]` atributu na třídu kontroleru nebo jednu nebo více jeho metod. Tento `[Authorize]` atribut omezuje přístup tím, že povoluje pouze ověřené uživatele. Pokud uživatel ještě není ověřený, může se spustit ověřovací výzva pro přístup k řadiči. V tomto rychlém startu se obory čtou z konfiguračního souboru:

```CSharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
 ```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Úložiště GitHub obsahující ukázku kódu ASP.NET Core, na kterou se odkazuje v tomto rychlém startu, obsahuje pokyny a další ukázky kódu, které vám ukážou, jak:

- Přidání ověřování do nové ASP.NET Core webové aplikace
- Volání Microsoft Graph, dalších rozhraní API Microsoftu nebo vašich vlastních webových rozhraní API
- Přidat autorizaci
- Přihlaste se k uživatelům v národních cloudech nebo pomocí sociálních identit

> [!div class="nextstepaction"]
> [ASP.NET Core výukových kurzů pro webové aplikace na GitHubu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
