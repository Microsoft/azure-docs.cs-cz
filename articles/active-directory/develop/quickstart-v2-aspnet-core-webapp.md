---
title: Azure AD v2.0 rychlý úvod k aplikacím web ASP.NET Core | Dokumentace Microsoftu
description: Zjistěte, jak implementovat přihlašování společnosti Microsoft o webové aplikace ASP.NET Core pomocí OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: ba67acec778a48c084897095aa457e5637240a57
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227419"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Přidání přihlašování s Microsoftem do webové aplikace ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Tento rychlý Start obsahuje ukázku kódu, který ukazuje, jak můžete podepsat webové aplikace ASP.NET Core v osobním (hotmail.com, live.com, ostatní) a pracovní a školní účty z libovolné instance služby Azure Active Directory.

![Jak ukázková aplikace vygenerované v tomto rychlém startu funguje](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Zaregistrujte si vaši aplikaci a stáhněte si aplikaci rychlý start
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Registrace a konfigurace ukázky aplikace a kód
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace
> 
> 1. Přejděte [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app).
> 1. Zadejte název pro vaši aplikaci, ujistěte se, že možnost **instalační program s asistencí** není zaškrtnuté políčko a klikněte na tlačítko **vytvořit**.
> 1. Klikněte na tlačítko `Add Platform`a pak vyberte `Web`.
> 1. Ujistěte se, že **povolit implicitní tok** je *zaškrtnutí*.
> 1. V **adresy URL pro přesměrování**, zadejte `https://localhost:3110/`.
> 1. Posuňte se dolů dolní části stránky a klikněte na tlačítko **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure portal
> Pro ukázkový kód pro tento rychlý start pro práci, budete muset přidat adresu URL odpovědi jako `http://localhost:3110/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provedení této změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurovali](media/quickstart-v2-aspnet-core-webapp/green-check.png) vaše aplikace je nakonfigurovaná pomocí tohoto atributu

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2: Stáhněte si svůj projekt ASP.NET Core

- [Stáhněte si projekt ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurace projektu

1. Extrahujte soubor zip do místní složky (například **C:\Azure-Samples**)
1. Pokud používáte Visual Studio 2017, otevřete projekt v sadě Visual Studio (volitelné)
1. Upravit **appsettings.json** a nahraďte hodnotu pro `ClientId` s Id aplikace z aplikace, které jste právě zaregistrovali:

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. Pokud je vaše aplikace *jednoho tenanta aplikaci* (cílení na účty v aktuálním adresáři) v vaše **appsettings.json** souboru, najít hodnotu pro `TenantId` a nahraďte `common`s vaší **Id Tenanta** nebo **název Tenanta** (například contoso.microsoft.com). Název tenanta v můžete získat **stránka s přehledem**.

## <a name="more-information"></a>Další informace

Tato část poskytuje přehled o kód potřebný k přihlášení uživatelů. To může být užitečné k pochopení, jak kód funguje, hlavní argumenty i pokud budete chtít přidání přihlašování do stávající aplikace ASP.NET Core.

### <a name="startup-class"></a>Třída při spuštění

*Microsoft.AspNetCore.Authentication* middleware použije při spuštění třídy, která se spustí v případě hostitelského procesu inicializuje:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddAzureAd(options => Configuration.Bind("AzureAd", options))
    .AddCookie();

    services.AddMvc();
}
```

Metoda `AddAuthentication` konfiguruje službu architektura přidat na základě souboru Cookie ověřování – použít pro scénáře prohlížeče, jakož i nastavit před obrovskou výzvou – OpenIdConnect. 

Na řádek obsahující `.AddAzureAd` přidává ověřování Azure Active Directory pro vaši aplikaci.

Kromě, soubor **AzureAdAuthenticationBuilderExtensions.cs** přidá metody rozšíření do kanálu ověřování Azure AD. Tato metoda rozšíření nakonfigurovat atributy, které jsou potřebné pro ověřování Azure AD. `Configure` Metoda `IConfigureNamedOptions` rozhraní obsahuje následující:

```csharp
public void Configure(string name, OpenIdConnectOptions options)
{
    options.ClientId = _azureOptions.ClientId;
    options.Authority = $"{_azureOptions.Instance}common/v2.0";   // V2 specific
    options.UseTokenLifetime = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters.ValidateIssuer = false;     // accept several tenants (here simplified)
}
```
> |Kde  |  |
> |---------|---------|
> |ID klienta     |Id aplikace z aplikace zaregistrované na webu Azure Portal|
> |Autorita | Koncový bod služby tokenů zabezpečení fo uživatele k ověření. Obvykle https://login.microsoftonline.com/{tenant}/v2.0 pro veřejný cloud, kde je název tenanta, Id tenanta {klient} nebo *běžné* odkazu na společný koncový bod (používá se pro aplikace s více tenanty)|
> |UseTokenLifetime |Označuje, že ověřovacího souboru cookie by měla odpovídat hodnotě tokenu ověřování|
> |RequireHttpsMetadata     |Vyžadujte protokol HTTPS pro adresu metadat nebo autority. Doporučuje se tuto hodnotu na změnit `True`|
> |Parametry tokenvalidationparameters.     | Seznam parametrů pro ověřování tokenů. V takovém případě `ValidateIssuer` je nastavena na `false` k označení, aby mohl přijímat přihlášení z jakékoli osobní, nebo pracovní nebo školní účty|

### <a name="initiate-an-authentication-challenge"></a>Zahájit výzvu ověřování

Můžete přinutit uživatele k přihlášení pomocí žádosti výzvu ověřování v řadiči, stejně jako v **AccountController.cs**:

```csharp
public IActionResult SignIn()
{
    var redirectUrl = Url.Action(nameof(HomeController.Index), "Home");
    return Challenge(
        new AuthenticationProperties { RedirectUri = redirectUrl },
        OpenIdConnectDefaults.AuthenticationScheme);
}
```

> [!TIP]
> Požaduje výzvu ověřování pomocí metody popsané výše je volitelné a commonsly použít, pokud chcete zobrazit dostupné pro ověřených i neověřených uživatelů. Řadiče můžete chránit pomocí metody popsané v další části.

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrana kontroler nebo metodu kontroleru

Můžete chránit kontroler nebo pomocí řadiče methodss `[Authorize]` atribut. Tento atribut omezuje přístup k řadiči nebo metody povolením jenom ověření uživatelé – to znamená, že lze spustit výzvu ověřování pro přístup k řadiči, pokud uživatel není ověřen.

## <a name="next-steps"></a>Další kroky

Projděte si úložiště GitHub pro tento rychlý start ASP.NET Core pro další údaje, včetně informací o tom, jak přidat ověřování do zcela nové aplikace ASP.NET Core Web:

> [!div class="nextstepaction"]
> [Vzorový kód aplikace Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]