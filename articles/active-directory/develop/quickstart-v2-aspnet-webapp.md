---
title: 'Rychlý Start: Přidání přihlašování pomocí Microsoftu do webové aplikace ASP.NET | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak implementovat přihlašování Microsoftu ve webové aplikaci v ASP.NET pomocí OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: dbddf35b0aa1494ef719803fa84cafae04f3ec50
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178580"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Rychlý Start: přidání přihlášení k platformě Microsoft Identity Platform do webové aplikace v ASP.NET

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která ukazuje, jak se webová aplikace ASP.NET může přihlašovat uživatelům z jakékoli organizace služby Azure Active Directory (Azure AD). 

Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.
> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Požadavky
>
> * Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2 +](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Expresní] [Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejít k prostředí rychlý Start pro <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">Azure Portal registrace aplikací <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
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
> 1. Zadejte **název** vaší aplikace, například `ASPNET-Quickstart` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
> 1. Přidejte `https://localhost:44368/` do **identifikátoru URI přesměrování** a vyberte **Registrovat**.
> 1. V části **Spravovat** vyberte **ověřování**.
> 1. V dílčí části **implicitní udělení grantu** vyberte možnost **tokeny ID**.
> 1. Vyberte **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Nakonfigurujte si aplikaci na portálu Azure Portal
> Aby mohl vzorový kód pro rychlý start fungovat, musíte přidat adresu URL odpovědi ve formě `https://localhost:44368/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s tímto atributem

#### <a name="step-2-download-your-project"></a>Krok 2: Stáhněte si projekt

> [!div renderon="docs"]
> [Stažení řešení pro Visual Studio 2019](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Spusťte projekt pomocí sady Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Stažení ukázky kódu](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
> Nakonfigurovali jsme projekt s hodnotami vlastností vaší aplikace.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Krok 3: spuštění projektu sady Visual Studio

1. Extrahujte soubor ZIP do místní složky bližší ke kořenové složce, třeba **C:\Azure-Samples**.
1. Otevřete řešení v sadě Visual Studio (AppModelv2-WebApp-OpenIDConnect-DotNet.sln).
1. V závislosti na verzi sady Visual Studio možná budete muset kliknout pravým tlačítkem na projekt `AppModelv2-WebApp-OpenIDConnect-DotNet` a **obnovit balíčky NuGet** .
1. Otevřete konzolu Správce balíčků (zobrazení – > jiné konzoly Správce balíčků > systému Windows) a spusťte příkaz. `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`

> [!div renderon="docs"]
> 5. Upravte soubor **Web.config** a nahraďte parametry `ClientId` a `Tenant` následujícími hodnotami:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    ```
>    Kde:
> - `Enter_the_Application_Id_here` je ID aplikace, kterou jste zaregistrovali.
> - `Enter_the_Tenant_Info_Here` je jedna z následujících možností:
>   - Pokud vaše aplikace podporuje **pouze moji organizaci**, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.onmicrosoft.com).
>   - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu za `organizations`.
>   - Pokud vaše aplikace podporuje režim **Všichni uživatelé účtu Microsoft**, nahraďte tuto hodnotu za `common`.
>
> > [!TIP]
> > - Hodnoty *ID aplikace*, *ID adresáře (tenanta)* a *Podporované typy účtu* najdete na stránce **Přehled**.
> > - Zajistěte, aby hodnota `redirectUri` v **Web.config** odpovídala **identifikátoru URI přesměrování** definovanému pro registraci aplikace ve službě Azure AD (Pokud ne, přejděte do nabídky **ověřování** pro registraci aplikace a aktualizujte **identifikátor URI přesměrování** tak, aby odpovídal).

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Další informace

Tato část obsahuje základní informace o kódu, který je nutný pro přihlášení uživatelů. Tento přehled může být užitečný pro pochopení, jak kód funguje, hlavní argumenty a také, pokud chcete přidat přihlášení do existující aplikace ASP.NET.

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>Balíčky NuGet middlewaru OWIN

Můžete nastavit ověřovací kanál využívající ověřování na základě souborů cookie tak, že použijete OpenID Connect v ASP.NET společně s balíčky middlewaru OWIN. Uvedené balíčky je možné nainstalovat spuštěním následujících příkazů v **konzole Správce balíčků** v sadě Visual Studio:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>Třída OWIN Startup

Middleware OWIN používá *spouštěcí třídu* , která se spouští při inicializaci hostitelského procesu. V tomto rychlém startu se nachází soubor *Startup.cs* , který se nachází v kořenové složce. Následující kód obsahuje parametr použitý v rámci tohoto rychlého startu:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the ClientId, authority, RedirectUri as obtained from web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the id_token - which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.IdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Kde  | Description |
> |---------|---------|
> | `ClientId`     | ID aplikace, kterou jste zaregistrovali na portálu Azure Portal |
> | `Authority`    | Koncový bod služby tokenů zabezpečení pro uživatele k ověření, obvykle `https://login.microsoftonline.com/{tenant}/v2.0` pro veřejný cloud, kde hodnota {tenant} představuje název tenanta, ID tenanta nebo hodnotu *common* odkazující na společný koncový bod (používaný u multitenantních aplikací) |
> | `RedirectUri`  | Adresa URL, kam se po ověření s koncovým bodem platformy Microsoft Identity odesílají uživatelé |
> | `PostLogoutRedirectUri`     | Adresa URL, na kterou jsou uživatelé přesměrováni po odhlášení |
> | `Scope`     | Seznam požadovaných oborů oddělených mezerami |
> | `ResponseType`     | Žádost, aby odpověď ověřování obsahovala token ID |
> | `TokenValidationParameters`     | Seznam parametrů pro ověřování tokenů; v tomto případě je parametr `ValidateIssuer` nastavený na hodnotu `false`, což znamená, že může přijímat přihlášení všech typů osobních, pracovních nebo školních účtů |
> | `Notifications`     | Seznam delegátů, které jde spustit u různých zpráv *OpenIdConnect* |


> [!NOTE]
> Nastavení `ValidateIssuer = false` je zjednodušení pro tento rychlý Start. Ve skutečných aplikacích potřebujete k ověření vystavitele.
> V ukázkách si můžete uvědomit, jak to udělat.

### <a name="initiate-an-authentication-challenge"></a>Iniciace výzvy ověřování

Uživatele můžete k přihlášení přinutit tím, že si v řadiči vyžádáte výzvu ověřování:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Vyžádání výzvy ověřování výše uvedeným způsobem je volitelné a obvykle se používá v případě, že chcete zajistit přístup k zobrazení ověřeným i neověřeným uživatelům. Řadiče můžete zároveň chránit způsobem popsaným v další části.

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrana řadiče nebo akcí řadiče

Řadič a jeho akce můžete chránit pomocí atributu `[Authorize]`. Tento atribut omezuje přístup k řadiči nebo akcím tak, že ho umožňuje pouze ověřeným uživatelům. To znamená, že výzva ověřování se iniciuje automaticky, pokud o přístup k některé z akcí nebo řadiči s nastaveným atributem `[Authorize]` pokusí *neověřený* uživatel.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Vyzkoušejte kurz pro ASP.NET, který nabízí vyčerpávající podrobný návod k vytváření aplikací a nových funkcí, včetně detailního vysvětlení těchto pokynů pro rychlý start.

> [!div class="nextstepaction"]
> [Přidání přihlašování do webové aplikace v ASP.NET](tutorial-v2-asp-webapp.md)
