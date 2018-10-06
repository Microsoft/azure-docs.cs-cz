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
ms.openlocfilehash: 4ab3d0b74e8305d67af862020197c69b15221086
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830221"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Přidání přihlašování s Microsoftem do webové aplikace ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Tento rychlý Start obsahuje ukázku kódu, který ukazuje, jak můžete podepsat webové aplikace ASP.NET Core v osobním (hotmail.com, live.com, ostatní) a pracovní a školní účty z libovolné instance služby Azure Active Directory.

![Jak funguje ukázková aplikace vygenerovaná v tomto rychlém startu](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Registrace aplikace a stažení aplikace pro rychlý start
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Zaregistrujte si a nakonfigurujte svoji aplikaci a vzorový kód
> #### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte si aplikaci
> 
> 1. Přejděte na portál [Microsoft Application Registration Portal](https://apps.dev.microsoft.com/portal/register-app).
> 1. Zadejte název svojí aplikace, ujistěte se, že políčko **Guided Setup** (Instalace s asistencí) není zaškrtnuté, a klikněte na **Create** (Vytvořit).
> 1. Klikněte na `Add Platform`, pak vyberte `Web`.
> 1. Zajistěte, aby bylo políčko **Allow Implicit Flow** (Povolit implicitní tok) *zaškrtnuté*.
> 1. V části **Redirect URLs** (Adresy URL pro přesměrování) zadejte `http://localhost:3110/`.
> 1. Přejděte do dolní části stránky a klikněte na **Save** (Uložit).

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Nakonfigurujte si aplikaci na portálu Azure Portal
> Aby mohl vzorový kód pro rychlý start fungovat, musíte přidat adresu URL odpovědi ve formě `http://localhost:3110/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurováno](media/quickstart-v2-aspnet-core-webapp/green-check.png) Vaše aplikace už má tento atribut nakonfigurovaný.

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2: Stáhněte si svůj projekt ASP.NET Core

- [Stáhněte si projekt ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurace projektu

1. Extrahujte soubor zip do místní složky blíž ke kořenové složce – například **C:\Azure-Samples**
1. Pokud používáte Visual Studio 2017, otevřete projekt v sadě Visual Studio (volitelné)
1. Upravit **appsettings.json** a nahraďte hodnotu pro `ClientId` s Id aplikace z aplikace, které jste právě zaregistrovali:

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. Pokud je vaše aplikace *jednoho tenanta aplikaci* (cílení na účty v aktuálním adresáři) v vaše **appsettings.json** souboru, najít hodnotu pro `TenantId` a nahraďte `common`s vaší **Id Tenanta** nebo **název Tenanta** (například contoso.microsoft.com). Název tenanta zjistíte na **stránce s přehledem**.

## <a name="more-information"></a>Další informace

Tato část obsahuje základní informace o kódu, který je nutný pro přihlášení uživatelů. To může být užitečné k pochopení, jak kód funguje, hlavní argumenty i pokud budete chtít přidání přihlašování do stávající aplikace ASP.NET Core.

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
> |ClientId     |Id aplikace z aplikace zaregistrované na webu Azure Portal|
> |Autorita | Koncový bod služby tokenů zabezpečení fo uživatele k ověření. obvykle https://login.microsoftonline.com/{tenant}/v2.0 pro veřejný cloud, kde hodnota {tenant} představuje název tenanta, ID tenanta nebo hodnotu *common* odkazující na společný koncový bod (používaný u multitenantních aplikací)|
> |UseTokenLifetime |Označuje, že ověřovacího souboru cookie by měla odpovídat hodnotě tokenu ověřování|
> |RequireHttpsMetadata     |Vyžadujte protokol HTTPS pro adresu metadat nebo autority. Doporučuje se tuto hodnotu na změnit `True`|
> |Parametry tokenvalidationparameters.     | Seznam parametrů pro ověřování tokenů; V takovém případě `ValidateIssuer` je nastavena na `false` k označení, aby mohl přijímat přihlášení z jakékoli osobní, nebo pracovní nebo školní účty|

### <a name="initiate-an-authentication-challenge"></a>Iniciace výzvy ověřování

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

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrana řadiče nebo akcí řadiče

Můžete chránit kontroler nebo pomocí řadiče methodss `[Authorize]` atribut. Tento atribut omezuje přístup k řadiči nebo metody povolením jenom ověření uživatelé – to znamená, že lze spustit výzvu ověřování pro přístup k řadiči, pokud uživatel není ověřen.

## <a name="next-steps"></a>Další kroky

Projděte si úložiště GitHub pro tento rychlý start ASP.NET Core pro další údaje, včetně informací o tom, jak přidat ověřování do zcela nové aplikace ASP.NET Core Web:

> [!div class="nextstepaction"]
> [Vzorový kód aplikace Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]