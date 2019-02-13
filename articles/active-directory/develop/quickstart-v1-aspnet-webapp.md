---
title: Přidání přihlášení účtem Microsoft do webové aplikace ASP.NET | Microsoft Docs
description: Přečtěte si, jak přidat přihlášení účtem Microsoft v řešení ASP.NET s tradiční webovou aplikací pomocí standardu OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: andret
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcb235ab96ed8a85cbe44dd672dff004dd0a7310
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185312"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Rychlý start: Přidání přihlašování s Microsoftem do webové aplikace ASP.NET

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

V tomto rychlém startu se naučíte, jak implementovat přihlášení účtem Microsoft pomocí řešení ASP.NET MVC s tradiční webovou aplikací s využitím standardu OpenID Connect. Naučíte se povolit přihlášení z pracovního a školního účtu v aplikaci ASP.NET.

Na konci tohoto rychlého startu přijmete přihlášení pracovního a školního účtu z organizace, která má integrovanou službu Azure Active Directory (Azure AD).

> [!NOTE]
> Pokud kromě přihlášení z pracovního a školního účtu potřebujete povolit také přihlášení pro osobní účet, můžete použít [koncový bod v2.0](azure-ad-endpoint-comparison.md). Další informace najdete v [tomto kurzu ASP.NET pro koncový bod v2.0](tutorial-v2-asp-webapp.md) a v [tomto článku](active-directory-v2-limitations.md), který vysvětluje aktuální omezení koncového bodu v2.0.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, musíte splnit tyto předpoklady:

* Musíte mít nainstalovanou sadu Visual Studio 2015 Update 3 nebo Visual Studio 2017. Nemáte ji? [Stáhnout Visual Studio 2017 zdarma](https://www.visualstudio.com/downloads/)

## <a name="scenario-sign-in-users-from-work-and-school-accounts-in-your-aspnet-app"></a>Scénář: Přihlášení uživatele z pracovním a školním účtům v aplikaci ASP.NET

![Jak funguje tento průvodce](./media/quickstart-v1-aspnet-webapp/aspnet-intro.png)

V tomto scénáři prohlížeč přejde na web ASP.NET a požádá uživatele, aby se ověřil pomocí tlačítka pro přihlášení. V tomto scénáři se většina aktivit vykreslení webové stránky odehrává na straně serveru.

Rychlý start ukazuje, jak přihlásit uživatele ve webové aplikaci ASP.NET z prázdné šablony, a zahrnuje postupy, jako je přidání tlačítka pro přihlášení a jednotlivých kontrolerů a metod a vysvětluje koncepty skrývající se za těmito úlohami. Můžete také vytvořit projekt pro přihlášení uživatelů Azure AD (pracovní a školní účty) pomocí [webové šablony sady Visual Studio](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options), výběrem **účtů organizace** a poté jedné z cloudových možností – tato možnost používá podrobnější šablonu s dalšími kontrolery, metodami a zobrazeními.

## <a name="libraries"></a>Knihovny

Tento rychlý start používá následující balíčky:

| Knihovna | Popis |
|---|---|
| [Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) | Middleware, který aplikaci umožňuje použít OpenIdConnect pro ověřování |
| [Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies) |Middleware, který aplikaci umožňuje udržovat relaci uživatele pomocí souborů cookie |
| [Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb) | Aplikacím založeným na specifikaci OWIN umožňuje spouštět se ve službě IIS pomocí kanálu požadavků ASP.NET |
|  |  |

## <a name="step-1-set-up-your-project"></a>Krok 1: Nastavení projektu

Tento postup ukazuje, jak nainstalovat a nakonfigurovat kanál ověřování prostřednictvím middlewaru OWIN v projektu ASP.NET pomocí OpenID Connect.

Pokud si místo toho chcete stáhnout projekt sady Visual Studio této ukázky, postupujte takto:
1. [Stáhněte si projekt z GitHubu](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip).
1. Pokračujte krokem konfigurace před provedením konfigurace vzorový kód.

## <a name="step-2-create-your-aspnet-project"></a>Krok 2: Vytvoření projektu ASP.NET

1. V sadě Visual Studio přejděte na **Soubor > Nový > Projekt**.
2. V části **Visual C#\Web** vyberte **Webová aplikace ASP.NET (.NET Framework)**.
3. Pojmenujte aplikaci a vyberte **OK**.
4. Vyberte **Prázdné** a zaškrtnutím políčka přidejte reference **MVC**.

## <a name="step-3-add-authentication-components"></a>Krok 3: Přidání součástí ověřování

1. V sadě Visual Studio přejděte na **Nástroje > Správce balíčků NuGet > Konzola Správce balíčků**.
2. Přidejte **balíčky NuGet middlewaru OWIN** tak, že do okna konzoly Správce balíčků zadáte toto:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>Informace o těchto balíčcích
>Knihovny uvedené výše umožňují jednotné přihlašování pomocí OpenID Connect přes ověřování na základě souborů cookie. Po dokončení ověřování a odeslání tokenu, který reprezentuje uživatele, do aplikace, vytvoří middleware OWIN soubor cookie relace. Prohlížeč potom použijte tento soubor cookie v následných žádostech, aby se uživatel nemusel opakovaně ověřovat. Žádné další ověření není potřeba.
<!--end-collapse-->

## <a name="step-4-configure-the-authentication-pipeline"></a>Krok 4: Konfigurace ověřovacího kanálu

Podle následujícího postupu vytvořte *počáteční třídu* middlewaru OWIN a nakonfigurujte ověřování OpenID Connect. Tato třída se spouští automaticky.

> [!TIP]
> Pokud projekt nemá soubor `Startup.cs` v kořenové složce:<br/>
> 1. Pravým tlačítkem myši klikněte na kořenovou složku projektu: >    **Přidat > Nová položka... > Třída OWIN Startup**.<br/>
> 2. Pojmenujte ji `Startup.cs`<br/>
>
>> Zkontrolujte, že vybraná třída je počáteční třídou OWIN a nikoli standardní třídou C#. Ověříte si to tak, že nad oborem názvů bude uvedeno `[assembly: OwinStartup(typeof({NameSpace}.Startup))]`.

Postup vytvoření *počáteční třídy* middlewaru OWIN:

1. Přidejte obor názvů *OWIN* a *Microsoft.IdentityModel* do souboru `Startup.cs`:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Nahraďte počáteční třídu následujícím kódem:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Startup.cs?name=Startup "Startup.cs")]

<!--start-collapse-->
> [!NOTE]
> Parametry, které zadáte v *OpenIDConnectAuthenticationOptions*, slouží jako souřadnice pro komunikaci aplikace s Azure AD. Middleware OpenID Connect používá soubory cookie, a proto také musíte nastavit ověřování pomocí souborů cookie, jak ukazuje předchozí kód. Hodnota *ValidateIssuer* dává middlewaru OpenIDConnect pokyn, aby neomezoval přístup na jednu konkrétní organizaci.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="step-5-add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Krok 5: Přidání kontroleru pro zpracování požadavků na přihlášení a odhlášení

Vytvořte nový kontroler, který zpřístupní metody přihlášení a odhlášení.

1.  Pravým tlačítkem myši klikněte na složku **Kontrolery** a vyberte **Přidat > Kontroler**.
2.  Vyberte **Kontroler MVC (verze .NET) – prázdný**.
3.  Vyberte **Přidat**.
4.  Pojmenujte ho `HomeController` a vyberte **Přidat**.
5.  Přidejte obory názvů **OWIN** do této třídy:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Do kontroleru přidejte následující metody, které budou zajišťovat přihlášení a odhlášení. Uděláte to tak, že prostřednictvím kódu spustíte výzvu ověřování:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/HomeController.cs?name=SigInAndSignOut "HomeController.cs")]

## <a name="step-6-create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Krok 6: Vytvoření aplikace domovskou stránku pro přihlášení prostřednictvím tlačítka přihlašování uživatelů

V sadě Visual Studio vytvořte nové zobrazení, do kterého přidáte tlačítko pro přihlášení a ve kterém se po ověření zobrazí informace o uživateli:

1. Pravým tlačítkem myši klikněte na složku **Views\Home** a vyberte **Přidat zobrazení**.
1. Pojmenujte ho **Index**.
1. Do souboru přidejte následující kód HTMP, který obsahuje tlačítko pro přihlášení:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse--> Tato stránka přidá tlačítko pro přihlášení ve formátu SVG s černým pozadím:<br/>![Přihlásit se účtem Microsoft](./media/quickstart-v1-aspnet-webapp/aspnetsigninbuttonsample.png)<br/> Další informace o tlačítkách pro přihlášení najdete v článku [Pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md).
<!--end-collapse-->

## <a name="step-7-display-users-claims-by-adding-a-controller"></a>Krok 7: Přidání kontroleru zobrazení deklarací identity uživatele

Tento kontroler demonstruje použití atributu `[Authorize]` k ochraně kontroleru. Tento atribut omezuje přístup ke kontroleru tím, že povolí pouze ověřené uživatele. Následující kód využívá atribut k zobrazení deklarací identity uživatelů, které se načetly při přihlášení.

1. Pravým tlačítkem myši klikněte na složku **Kontrolery** a vyberte **Přidat > Kontroler**.
1. Vyberte **Kontroler MVC {version} – prázdný**.
1. Vyberte **Přidat**.
1. Pojmenujte ho **ClaimsController**.
1. Nahraďte kód třídy kontroleru následujícím kódem – tím se do třídy přidá atribut `[Authorize]`:

    [!code-csharp[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Controllers/ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> Protože je použit atribut `[Authorize]`, všechny metody tohoto kontroleru lze spustit pouze v případě, že je uživatel ověřen. Pokud se uživatel neověří a pokusí se o přístup ke kontroleru, OWIN spustí výzvu ověřování a přinutí uživatele, aby se ověřil. Výše uvedený kód vyhledá v kolekci deklarací identity uživatele konkrétní atributy, které jsou součástí tokenu uživatele. Tyto atributy zahrnují celé jméno uživatele a jeho uživatelské jméno, ale také subjekt globálního identifikátoru uživatele. Obsahuje také *ID tenanta*, které představuje ID organizace uživatele.
<!--end-collapse-->

## <a name="step-8-create-a-view-to-display-the-users-claims"></a>Krok 8: Vytvoření zobrazení pro zobrazení deklarací identity uživatele

V sadě Visual Studio vytvořte nové zobrazení, ve kterém se budou zobrazovat deklarace identity uživatele na webové stránce:

1. Pravým tlačítkem myši klikněte na složku **Views\Claims** a vyberte **Přidat zobrazení**.
1. Pojmenujte ho **Index**.
1. Přidejte do souboru následující kód HTML:

    [!code-html[main](../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]

<!--end-use-->

<!--start-configure-->

## <a name="step-9-configure-your-webconfig-and-register-an-application"></a>Krok 9: Konfigurace vašeho *web.config* a zaregistrovat aplikaci

1. V sadě Visual Studio přidejte následující kód do souboru `web.config` (nachází se v kořenové složce) v části `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" />
    ```
2. V Průzkumníku řešení vyberte projekt a přejděte do okna <i>Vlastnosti</i> (pokud toto okno není zobrazené, stiskněte F4).
3. Možnost Protokol SSL je povolený změňte na <code>True</code>.
4. Zkopírujte adresu URL protokolu SSL projektu do schránky:<br/><br/>![Vlastnosti projektu](./media/quickstart-v1-aspnet-webapp/visual-studio-project-properties.png)<br />
5. V souboru <code>web.config</code> nahraďte řetězec <code>Enter_the_Redirect_URL_here</code> adresou URL protokolu SSL projektu.

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Zaregistrujte aplikaci na webu Azure Portal a potom přidejte její informace do souboru *web.config*.

1. Abyste mohli zaregistrovat aplikaci, přejděte na [Microsoft Azure Portal – Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
2. Vyberte **Registrace nové aplikace**.
3. Zadejte název své aplikace.
4. Vložte *adresu URL protokolu SSL* projektu sady Visual Studio do pole **Přihlašovací adresa URL**. Tato adresa URL se také automaticky přidá do seznamu adres URL pro odpověď pro aplikaci, kterou registrujete.
5. Aplikaci zaregistrujte výběrem možnosti **Vytvořit**. Tato akce vás převede zpět do seznamu aplikací.
6. Nyní vyhledejte a vyberte aplikaci, kterou jste právě vytvořili, a otevřete její vlastnosti.
7. Zkopírujte identifikátor GUID v části **ID aplikace** do schránky.
8. Vraťte se zpět do sady Visual Studio a v souboru `web.config` nahraďte řetězec `Enter_the_Application_Id_here` ID aplikace, kterou jste právě zaregistrovali.

> [!TIP]
> Pokud máte účet nakonfigurovaný na přístup k více adresářům, nezapomeňte vybrat správný adresář pro organizaci, pro kterou chcete aplikaci zaregistrovat. Klikněte na název účtu v pravém horním rohu webu Azure Portal a potom zkontrolujte vybraný adresář, jak je uvedeno na obrázku:<br/>![Výběr správného adresáře](./media/quickstart-v1-aspnet-webapp/tenantselector.png)

## <a name="step-10-configure-sign-in-options"></a>Krok 10: Konfigurovat možnosti přihlášení

Aplikaci můžete nakonfigurovat tak, aby povolovala přihlášení pouze uživatelů, kteří patří do instance Azure AD jedné organizace, nebo aby přijímala přihlášení od uživatelů z libovolné organizace. Postupujte podle pokynů pro některou z následujících možností:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Konfigurace aplikace na povolení přihlášení pracovním a školním účtem z libovolné společnosti nebo organizace (více tenantů)

Podle následujících pokynů postupujte, pokud chcete povolit přihlášení pracovním a školním účtem z libovolné společnosti nebo organizace, které jsou integrovány s Azure AD. Tento scénář je společný pro *aplikace SaaS*:

1. Přejděte zpět na [Microsoft Azure Portal – Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) a vyhledejte aplikaci, kterou jste právě zaregistrovali.
2. V části **Všechna nastavení** vyberte **Vlastnosti**.
3. Změňte vlastnost **Více tenantů** na **Ano** a vyberte **Uložit**.

Další informace o tomto nastavení a konceptu aplikací s více tenanty najdete v článku, který se zabývá [přehledem pro více tenantů](howto-convert-app-to-be-multi-tenant.md).

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Omezení přihlášení k aplikaci na uživatele pouze z instance Active Directory jedné organizace (jeden tenant)

Tato možnost představuje společný scénář pro obchodní aplikace.

Pokud chcete, aby aplikace povolovala přihlášení pouze z účtů, které patří do konkrétní instance Azure AD (včetně *účtů hostů* dané instance), postupujte takto:

1. V parametru `Tenant` v souboru *web.config* zadejte místo hodnoty `Common` název tenanta organizace, například *contoso.onmicrosoft.com*.
1. Změňte argument `ValidateIssuer` v [*počáteční třídě OWIN*](#step-4-configure-the-authentication-pipeline) na `true`.

Pokud chcete povolit přihlášení pouze uživatelů, kteří jsou uvedeni v seznamu konkrétních organizací, postupujte takto:

1. Nastavte `ValidateIssuer` na true.
1. K zadání seznamu organizací použijte parametr `ValidIssuers`.

Další možností je implementovat vlastní metodu pro ověření vystavitelů pomocí parametru *IssuerValidator*. Další informace o parametrech `TokenValidationParameters`, najdete v [tomto článku MSDN](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "článek MSDN o parametrech TokenValidationParameters").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](./media/quickstart-v1-aspnet-webapp/vsprojectproperties.png)<br />
4.  Add the following in `web.config` file located in root’s folder, under section `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
```
-->
<!--end-configure-arp-->
<!--start-test-->

## <a name="step-11-test-your-code"></a>Krok 11: Testování kódu

1. Stisknutím klávesy **F5** spusťte projekt v sadě Visual Studio. Otevře se prohlížeč a přesměruje vás na `http://localhost:{port}`, kde uvidíte tlačítko **Přihlásit se účtem Microsoft**.
1. Kliknutím na toto tlačítko se přihlaste.

### <a name="sign-in"></a>Přihlášení

Až budete připraveni kód otestovat, přihlaste se pracovním účtem (Azure AD).

![Okno prohlížeče Přihlásit se účtem Microsoft](./media/quickstart-v1-aspnet-webapp/aspnetbrowsersignin.png)

![Okno prohlížeče Přihlásit se účtem Microsoft](./media/quickstart-v1-aspnet-webapp/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Očekávané výsledky

Po přihlášení je uživatel přesměrován na domovskou stránku webu s adresou URL protokolu HTTPS, kterou jste zadali při registraci aplikace na portálu Microsoft pro registraci aplikací. Tato stránka nyní zobrazuje *Vítejte, {User}*, odkaz pro odhlášení a odkaz pro zobrazení deklarací identity uživatele. To je odkaz na ověřovací kontroler, který jste vytvořili dříve.

### <a name="see-users-claims"></a>Zobrazení deklarací identity uživatele

Výběrem odkazu zobrazíte deklarace identity uživatele. Tato akce vás převede do kontroleru a zobrazení, které je dostupné pouze ověřeným uživatelům.

#### <a name="expected-results"></a>Očekávané výsledky

 Měli byste vidět tabulku obsahující základní vlastnosti přihlášeného uživatele:

| Vlastnost | Hodnota | Popis |
|---|---|---|
| Název | {User Full Name} | Jméno a příjmení uživatele |
| Uživatelské jméno | <span>user@domain.com</span> | Uživatelské jméno identifikující přihlášeného uživatele |
| Subjekt| {Subject} |Řetězec pro jednoznačnou identifikaci přihlášení uživatele na webu |
| ID tenanta | {Guid} | Identifikátor *guid*, který jedinečně identifikuje organizaci Azure AD uživatele |

Kromě toho uvidíte tabulku obsahující všechny deklarace identity uživatele, které jsou obsaženy v žádosti o ověření. Seznam všech deklarací identity v tokenu ID a jejich vysvětlení najdete v [seznamu deklarací identity v tokenu ID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="optional-access-a-method-that-has-an-authorize-attribute"></a>(Volitelné) Přístup k metodě obsahující atribut *[Authorize]*

V tomto kroku otestujete přístup ke kontroleru deklarací identity jako anonymní uživatel:<br/>
Výběrem odkazu odhlaste uživatele a dokončete proces odhlášení.<br/>
V prohlížeči zadejte http://localhost:{port}/claims, abyste získali přístup ke kontroleru, který je chráněn atributem `[Authorize]`.

#### <a name="expected-results"></a>Očekávané výsledky

Měla by se zobrazit výzva, abyste se ověřili a zpřístupnilo se vám zobrazení.

## <a name="additional-information"></a>Další informace

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Ochrana celého webu

Pokud chcete chránit celý web, přidejte `AuthorizeAttribute` do `GlobalFilters` v `Global.asax` metodu `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->

## <a name="next-steps"></a>Další postup

Můžete teď přejít na další scénáře.

> [!div class="nextstepaction"]
> [Kurz ASP.NET](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-asp-webapp)
