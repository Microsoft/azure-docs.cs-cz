---
title: "Azure AD v1 webového serveru technologie ASP.NET Začínáme | Microsoft Docs"
description: "Implementace přihlašování společnosti Microsoft na řešení technologie ASP.NET s tradiční webovou aplikací využívajících prohlížeč pomocí OpenID Connect standard"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/08/2017
ms.author: andret
ms.openlocfilehash: b23afd26f7ac1828381a0410d2455206c8f43c88
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
<!--start-intro-->
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Přidání přihlášení se společností Microsoft do webové aplikace ASP.NET

Tato příručka ukazuje, jak implementovat přihlásit se pomocí rozhraní ASP.NET MVC řešení s tradiční webové aplikace založené na prohlížeči pomocí OpenID Connect společností Microsoft. 

Na konci tohoto průvodce, aplikace bude přijímat přihlášení pracovní a školní účty z organizací, které mají integrované s Azure Active Directory.

> [!NOTE]
> Tuto s průvodcem instalací vám umožní povolit přihlášení z pracovní a školní účty v aplikaci ASP.NET. Pokud vás zajímá povolit přihlášení pro osobní účty kromě pracovním a školním účtům, můžete použít [koncový bod v2](../active-directory-v2-compare.md). V tématu [této aplikace rozhraní ASP.NET na základě nastavení pro koncový bod v2](./active-directory-aspnetwebapp.md) a také [tento dokument](../active-directory-v2-limitations.md) vysvětlením aktuální omezení v2 koncového bodu.
<br/><br/>

<!--separator-->

> Tato příručka vyžaduje Visual Studio 2015 Update 3 nebo Visual Studio 2017.  Nemáte ho?  [Stáhněte si Visual Studio 2017 zdarma](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Jak funguje tato příručka

![Jak funguje tato příručka](media/active-directory-aspnetwebapp-v1/aspnet-intro.png)

Tato příručka je založena na scénář, kde prohlížeče přistupuje ke webovou stránku ASP.NET požaduje uživatele k ověřování prostřednictvím tlačítko přihlášení. V tomto scénáři proběhne většinu práce k vykreslení webové stránky na straně serveru.

> [!NOTE]
> Tato instalace s asistencí ukazuje, jak přihlásit uživatele na spuštění z prázdné šablony webové aplikace ASP.NET a zahrnout kroky, jako je například přidávání znaménkem v tlačítko a každý řadič a metody, při také která vysvětluje některé koncepty. Alternativně můžete také vytvořit projekt k Azure Active Directory přihlášení uživatele (pracovní a školní účty) pomocí [šablony webové aplikace Visual Studio](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) a výběrem *účty organizace* a potom jednu z možností cloudu – tato možnost používá šablonu bohatší s další řadiče, metod a zobrazení.

## <a name="libraries"></a>Knihovny

Tato příručka používá následující balíčky:

|Knihovna|Popis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware, který umožňuje aplikaci používat OpenIdConnect pro ověřování|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware, který umožňuje aplikaci spravovat pomocí souborů cookie relace uživatele|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Umožňuje aplikacím na základě OWIN běží ve službě IIS pomocí kanál požadavku|


<!--end-intro-->

<!--start-setup-->

## <a name="set-up-your-project"></a>Nastavení projektu

Tato část uvádí kroky pro instalaci a konfiguraci kanálu ověřování prostřednictvím middlewaru OWIN v projektu ASP.NET pomocí OpenID Connect. 

> Místo toho stáhněte projekt Visual Studio Tato ukázka dávají přednost? [Stažení projektu](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip) a pokračujte [krok konfigurace](#configure-your-webconfig-and-register-an-application) před provedením konfigurace ukázka kódu.

## <a name="create-your-aspnet-project"></a>Vytvoření projektu ASP.NET
1. V sadě Visual Studio: `File` > `New` > `Project`<br/>
2. V části *Visual C# \Web*, vyberte `ASP.NET Web Application (.NET Framework)`.
3. Název aplikace a klikněte na tlačítko *OK*
4. Vyberte `Empty` a pak zaškrtněte políčko Přidat `MVC` odkazy

## <a name="add-authentication-components"></a>Přidat ověřování součásti

1. V sadě Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Přidat *balíčky NuGet middleware OWIN* pomocí následujícího příkazu v okně konzoly Správce balíčků:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>O tyto balíčky
>Výše uvedené knihovny povolit jednotné přihlašování (SSO) prostřednictvím ověřování na základě souborů cookie pomocí OpenID Connect. Po dokončení ověření a token představující uživatele je odeslána do vaší aplikace, middlewaru OWIN, který vytvoří soubor cookie relace. V prohlížeči pak použije tento soubor cookie na následné žádosti, tak, aby uživatel nepotřebuje k novému ověření, a je potřeba žádné další ověření.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurace ověřování kanálu
Následující postup slouží k vytváření OWIN middleware *třída při spuštění* konfigurace ověřování OpenID Connect. Tato třída se spustí automaticky.

> [!TIP]
> Pokud nemá projektu `Startup.cs` souboru v kořenové složce:<br/>
> 1. Klikněte pravým tlačítkem na kořenové složky projektu: >    `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Název `Startup.cs`<br/>
>
>> Zajistěte, aby byl vybranou třídu třídy pro spuštění OWIN a není standardní C# třídu. To můžete ověřit kontrolou, pokud se zobrazí `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` výše obor názvů.


1. Přidat *OWIN* a *Microsoft.IdentityModel* obory názvů do `Startup.cs`:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Třída při spuštění nahraďte následujícím kódem:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=Startup "Startup.cs")]
    
<!--start-collapse-->
> [!NOTE]
> Parametry, zadejte v *OpenIDConnectAuthenticationOptions* sloužit jako souřadnice aplikace komunikovat s Azure AD. Protože middleware OpenID Connect používá soubory cookie, musíte taky nastavit ověřování souborů cookie, jak ukazuje předchozí kód. *Atribut ValidateIssuer* hodnota informuje OpenIdConnect není omezit přístup k jedné konkrétní organizaci.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Přidat řadič pro zpracování požadavků na přihlášení a odhlášení

Tento krok ukazuje, jak vytvořit nový řadič vystavit metody přihlášení a odhlášení.

1.  Klikněte pravým tlačítkem myši `Controllers` složky a vyberte `Add` > `Controller`
2.  Vyberte `MVC (.NET version) Controller – Empty`.
3.  Klikněte na tlačítko *přidat*
4.  Pojmenujte ji `HomeController` a klikněte na tlačítko *přidat*
5.  Přidat *OWIN* obory názvů pro třídu:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Přidejte následující metody pro zpracování přihlášení a odhlášení řadiče inicializaci výzvu ověřování prostřednictvím kódu:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=SigInAndSignOut "HomeController.cs")]
    
## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Vytvoření aplikace domovskou stránku pro přihlášení uživatele prostřednictvím přihlášení tlačítko

V sadě Visual Studio vytvořte nové zobrazení přidat tlačítko přihlásit a zobrazit informace o uživateli po ověření:

1.  Klikněte pravým tlačítkem myši `Views\Home` složky a vyberte `Add View`
2.  Pojmenujte ji `Index`.
3.  Přidejte následující kód HTML, která obsahuje tlačítko přihlášení, do souboru:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
> [!NOTE]
> Tato stránka přidá tlačítko přihlášení ve formátu SVG s černým pozadí:<br/>![Přihlášení se společností Microsoft](media/active-directory-aspnetwebapp-v1/aspnetsigninbuttonsample.png)<br/> Pro tlačítka Další přihlášení, přejděte na [tuto stránku](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Branding pokyny").
<!--end-collapse-->

## <a name="display-users-claims-by-adding-a-controller"></a>Zobrazit deklarací identity uživatele přidáním řadič
Tento řadič ukazuje použití `[Authorize]` atribut k ochraně řadiči. Tento atribut omezuje přístup k řadiči tím, že se pouze ověřené uživatele. Následující kód díky zobrazíte deklarace identity uživatelů, které byly získány v rámci přihlášení pomocí atributu.

1.  Klikněte pravým tlačítkem myši `Controllers` složky: `Add` > `Controller`
2.  Vyberte `MVC {version} Controller – Empty`.
3.  Klikněte na tlačítko *přidat*
4.  Název `ClaimsController`
5.  Nahraďte kód vaší řadiče třídy následujícím kódem – tím se přidá `[Authorize]` atribut třídy:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> Protože se používá `[Authorize]` atribut, všechny metody tohoto řadiče lze spustit pouze pokud je uživatel ověřený. Pokud uživatel není ověřen a pokusí o přístup k kontroleru, zahájí výzvu ověřování OWIN a nutí uživatele k ověření. Výše uvedený kód vypadá v kolekci deklarací identity uživatele pro konkrétní atributy, které jsou součástí token uživatele. Tyto atributy zahrnují úplné uživatelské jméno a uživatelské jméno, jakož i subjektu identifikátor globální uživatele. Obsahuje taky *ID klienta*, která reprezentuje ID pro uživatele organizaci. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Vytvoření zobrazení deklaracích identity uživatele

V sadě Visual Studio vytvořte nové zobrazení zobrazíte deklaracích identity uživatele na webové stránce:

1.  Klikněte pravým tlačítkem myši `Views\Claims` složky a: `Add View`
2.  Pojmenujte ji `Index`.
3.  Do souboru přidejte následující kód HTML:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]
    
<!--end-use-->

<!--start-configure-->
## <a name="configure-your-webconfig-and-register-an-application"></a>Konfigurace vaší *web.config* a registrace aplikace

1. V sadě Visual Studio, přidejte následující `web.config` (umístěné v kořenové složce) v části `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
    ```
2. V Průzkumníku řešení, vyberte projekt a podívejte se na <i>vlastnosti</i> okno (Pokud se nezobrazí okno Vlastnosti stisknutím klávesy F4)
3. Změna SSL povoleno <code>True</code>
4. Adresa URL projektu SSL zkopírujte do schránky:<br/><br/>![Vlastnosti projektu](media/active-directory-aspnetwebapp-v1/visual-studio-project-properties.png)<br />
5. V <code>web.config</code>, nahraďte <code>Enter_the_Redirect_URL_here</code> pomocí adresy URL protokolu SSL vašeho projektu 

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Registrace vaší aplikace na portálu Azure a pak přidejte informace o jeho *souboru web.config*

1. Přejděte na [portálu Microsoft Azure – registrace aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) zaregistrovat aplikaci
2. Vyberte `New application registration`
3. Zadejte název pro vaši aplikaci
4. Vložit projekt Visual Studio *SSL URL* v `Sign-on URL` (Tato adresa URL je také automaticky přidat do seznamu adresy URL odpovědí pro registraci aplikace)
5. Klikněte na tlačítko `Create` k registraci aplikace. Tato akce přejde zpět do seznamu aplikací
6. Nyní hledání nebo vyberte aplikace, které jste právě vytvořili, otevřete jeho vlastnosti
7. Zkopírovat identifikátor guid v části `Application ID` do schránky.
8. Přejděte zpět na Visual Studio a v `web.config`, nahraďte `Enter_the_Application_Id_here` s ID aplikace z aplikace, které jste právě zaregistrovali

> [!TIP]
> Pokud váš účet je nakonfigurovaný pro přístup k více adresářů, že jste vybrali správné adresář pro organizaci chcete aplikaci zaregistrovat kliknutím na název účtu v horní části přímo na portálu Azure a pak ověřování Vybraný adresář jako uvedené:<br/>![Výběr správné adresáře](media/active-directory-aspnetwebapp-v1/tenantselector.png)

## <a name="configure-sign-in-options"></a>Konfigurovat možnosti přihlášení

Můžete nakonfigurovat aplikaci povolit pouze uživatelé, kteří patří do jedné organizaci instanci Azure Active Directory k přihlášení, nebo přijměte přihlášení od uživatelů, které patří do některé z organizací. Postupujte podle pokynů jednoho z následujících možností:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Konfigurace aplikace pro povolit přihlášení k pracovním a školním účtům v jakémkoli společnosti nebo organizace (víceklientské)

Postupujte podle následujících kroků, pokud chcete, aby přijímal přihlášení pracovní a školní účty v jakémkoli společnosti nebo organizace, která má integrované s Azure Active Directory. Toto je běžný scénář pro *aplikace SaaS*:

1. Přejděte zpět na [portálu Microsoft Azure – registrace aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) a vyhledejte aplikace, které jste právě zaregistrovali
2. V části `All Settings` vyberte `Properties`
3. Změna `Multi-tenanted` vlastnost `Yes` a klikněte na tlačítko `Save`

Další informace o toto nastavení a konceptu víceklientským aplikacím, najdete v části [v tomto článku](../active-directory-devhowto-multi-tenant-overview.md "víceklientské přehled").

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Omezit uživatele z instance služby Active Directory pouze jedna organizace pro přihlášení k aplikaci (single klienta)

Tato možnost je běžný scénář pro *obchodních aplikací*: Pokud chcete, aby aplikace tak, aby přijímal přihlášení pouze z účtů, které patří ke konkrétní instanci Azure Active Directory (včetně *účet hosta*této instance), nahraďte `Tenant` parametr v *web.config* z `Common` s názvem klienta organizace – například *contoso.onmicrosoft.com*. Potom změnit `ValidateIssuer` argument ve vaší [ *třídy pro spuštění OWIN* ](#configure-the-authentication-pipeline) k `true`.

Chcete-li povolit uživatelům jenom seznamu konkrétní organizací, nastavte `ValidateIssuer` na hodnotu true a použít `ValidIssuers` parametr pro zadání seznamu organizací.

Další možností je implementovat vlastní metodu k ověření vystavitelů pomocí *IssuerValidator* parametr. Další informace o `TokenValidationParameters`, najdete v tématu [tohoto článku na webu MSDN](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "článku na webu MSDN parametry tokenvalidationparameters").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](media/active-directory-aspnetwebapp-v1/vsprojectproperties.png)<br />
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
## <a name="test-your-code"></a>Otestujte svůj kód

Stiskněte klávesu `F5` spustit projekt v sadě Visual Studio. Otevře se prohlížeč a přesměruje vám *http://localhost:{port}* kde uvidíte *přihlásit pomocí Microsoft* tlačítko. Pokračujte a klikněte na něj pro přihlášení.

Jakmile budete připraveni k testování, přihlaste se pomocí pracovní účet (Azure Active Directory). 

![Přihlaste se pomocí okna prohlížeče Microsoft](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin.png)

![Přihlaste se pomocí okna prohlížeče Microsoft](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Očekávané výsledky
Po přihlášení se uživatel přesměruje na domovskou stránku vašeho webu, což je adresa URL HTTPS zadaná v informace o registraci vaší aplikace v portálu pro registraci aplikace společnosti Microsoft. Tato stránka se teď zobrazuje *Hello {uživatele}* a odkaz na Odhlásit se a na odkaz zobrazíte deklaracích identity uživatele –, což je odkaz na kontroler autorizovat vytvořili dříve.

### <a name="see-users-claims"></a>V tématu deklarací identity uživatele
Vyberte hypertextový odkaz zobrazíte deklaracích identity uživatele. Tato akce vás řadiče a zobrazení, která je k dispozici pro uživatele, kteří se ověřují.

#### <a name="expected-results"></a>Očekávané výsledky
 Měli byste vidět tabulku obsahující základní vlastnosti pro přihlášeného uživatele:

| Vlastnost | Hodnota | Popis|
|---|---|---|
| Název | {Úplné uživatelské jméno} | Jméno a příjmení uživatele
|Uživatelské jméno | <span>user@domain.com</span>| Uživatelské jméno sloužící k identifikaci pro přihlášeného uživatele
| Předmět| {Subjektu}|Řetězec k jednoznačné identifikaci přihlášení uživatele na webu|
| ID tenanta| {Guid}| A *guid* jednoznačně představující uživatele organizaci Azure Active Directory.|

Kromě toho zobrazí tabulku včetně všech deklarací identity uživatele zahrnuta v žádosti o ověření. Seznam všech deklarací identity v tokenu ID a jejich vysvětlení najdete [článku](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "seznam deklarací identity ve ID tokenu").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Test přístupu k metodě, která má *[Authorize]* atribut (volitelné)
V tomto kroku byste otestovat přístup k řadiči deklarace identity jako anonymní uživatel:<br/>
Vyberte propojení na odhlášení uživatele a dokončete proces přihlášení.<br/>
Teď v prohlížeči zadejte http://localhost:{port}/deklarace identity pro přístup k řadiči, která je chráněná pomocí `[Authorize]` atribut

#### <a name="expected-results"></a>Očekávané výsledky
Měli byste obdržet řádku by bylo potřeba ověření zobrazíte zobrazení.

## <a name="additional-information"></a>Další informace

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Chránit celý web
Chcete-li chránit celý web, přidejte `AuthorizeAttribute` k `GlobalFilters` v `Global.asax` `Application_Start` metoda:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->