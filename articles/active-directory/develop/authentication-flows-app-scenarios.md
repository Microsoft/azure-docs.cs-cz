---
title: Scénáře ověřování pro Microsoft Identity Platform | Azure
description: Přečtěte si o tocích ověřování a scénářích aplikací pro platformu Microsoft Identity Platform. Seznamte se s různými typy aplikací, které můžou ověřovat identity, získávat tokeny a volat chráněná rozhraní API.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 507758831885bff7f61677ebae0224cc828dcb2e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879273"
---
# <a name="authentication-flows-and-application-scenarios"></a>Toky ověřování a scénáře aplikací

Koncový bod Microsoft Identity Platform (v 2.0) podporuje ověřování pro celou řadu moderních architektur aplikací, které jsou založené na standardních protokolech [OAuth 2,0 nebo OpenID Connect](active-directory-v2-protocols.md).  Pomocí [knihoven pro ověřování](reference-v2-libraries.md)aplikace ověřují identity a získávají tokeny pro přístup k chráněným rozhraním API. Tento článek popisuje různé toky ověřování a scénáře aplikací, které jsou používány v nástroji.  Tento článek také obsahuje seznam [scénářů aplikace a podporované toky ověřování](#scenarios-and-supported-authentication-flows) a [scénáře aplikací a podporované platformy a jazyky](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Kategorie aplikací

Tokeny lze získat z několika typů aplikací: Webové aplikace, mobilní aplikace nebo aplikace klasické pracovní plochy, webová rozhraní API a aplikace běžící na zařízeních, která nemají prohlížeč (nebo iOT). Aplikace je možné kategorizovat podle následujících pokynů:

- [Chráněné prostředky a klientské aplikace](#protected-resources-vs-client-applications). V některých scénářích se dozvíte o ochraně prostředků (Web Apps nebo webových rozhraní API). Další informace o získání tokenu zabezpečení pro volání chráněného webového rozhraní API.
- [S uživateli nebo bez uživatelů](#with-users-or-without-users). Některé scénáře zahrnují přihlášeného uživatele, zatímco jiné nezahrnují uživatele (scénáře démon).
- [Jednostránkové aplikace, veřejné klientské aplikace a důvěrné klientské aplikace](#single-page-applications-public-client-applications-and-confidential-client-applications). Jedná se o tři velké kategorie typů aplikací. Knihovny a objekty, které se používají k manipulaci s nimi, se budou lišit.
- [Cílová skupina přihlášení](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types). Některé toky ověřování nejsou k dispozici pro určité přihlašování do cílových skupin. Některé toky jsou dostupné jenom pro pracovní nebo školní účty a některé jsou dostupné pro pracovní nebo školní účty i pro osobní účty Microsoft. Povolená cílová skupina závisí na tocích ověřování.
- [Podporované toky OAuth 2,0](#scenarios-and-supported-authentication-flows).  Toky ověřování se používají k implementaci scénářů aplikace požadujících tokeny.  Mezi scénáři aplikace a toky ověřování neexistuje mapování 1:1.
- [Podporované platformy](#scenarios-and-supported-platforms-and-languages). Ne všechny scénáře aplikací jsou k dispozici pro všechny platformy.

### <a name="protected-resources-vs-client-applications"></a>Chráněné prostředky vs klientské aplikace

Scénáře ověřování zahrnují dvě aktivity:

- **Získávají se tokeny zabezpečení** pro chráněné webové rozhraní API. Microsoft doporučuje, abyste k získání tokenů používali [knihovny ověřování](reference-v2-libraries.md#microsoft-supported-client-libraries) , zejména rodinu Microsoft Authentication Library (MSAL).
- **Ochrana webového rozhraní API** (nebo webová aplikace). Jednou z výzev k ochraně prostředku (webová aplikace nebo webové rozhraní API) je ověření tokenu zabezpečení. Microsoft nabízí na některých platformách [middleware Libraries](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>S uživateli nebo bez uživatelů

Většina scénářů ověřování získá tokeny jménem (přihlášeného) **uživatele**.

![scénáře s uživateli](media/scenarios/scenarios-with-users.svg)

Existují však také scénáře (aplikace démona), kde aplikace získají tokeny jménem sebe (bez uživatele).

![aplikace démona](media/scenarios/daemon-app.svg)

### <a name="single-page-applications-public-client-applications-and-confidential-client-applications"></a>Jednostránkové aplikace, veřejné klientské aplikace a důvěrné klientské aplikace

Tokeny zabezpečení lze získat z několika typů aplikací. Aplikace jsou obvykle rozdělené do tří kategorií:

- **Jednostránkové aplikace** (SPA) jsou formuláře webových aplikací, ve kterých se tokeny získávají z aplikace spuštěné v prohlížeči (napsané v JavaScriptu nebo TypeScript). Mnohé moderní aplikace mají front-end jednostránkové aplikace, který je primárně napsán v JavaScriptu. Často je aplikace napsaná pomocí architektury, jako je například úhlová, reakce nebo Vue. MSAL. js je jediná knihovna Microsoft Authentication Library podporující jednu stránku aplikace.

- **Veřejné klientské aplikace** se vždycky přihlásí uživatelům. Tyto aplikace jsou:
  - Aplikace klasické pracovní plochy, které jménem přihlášeného uživatele volají webová rozhraní API
  - Mobilní aplikace.
  - Třetí kategorie aplikací spuštěná na zařízeních, která nemají prohlížeč (aplikace bez prohlížeče spuštěné v iOT pro instanci).

  Jsou reprezentovány třídou MSAL s názvem [PublicClientApplication](msal-client-applications.md).

- **Důvěrné klientské aplikace**
  - Webové aplikace, které volají webové rozhraní API
  - Webová rozhraní API, která volají webové rozhraní API
  - Aplikace démona (i když se implementuje jako služba konzoly, jako je démon na platformě Linux nebo služba systému Windows)
 
  Tyto typy aplikací používají rozhraní [ConfidentialClientApplication](msal-client-applications.md) .

## <a name="application-scenarios"></a>Scénáře aplikací

Koncový bod platformy Microsoft identity podporuje ověřování pro nejrůznější architektury aplikací: jednostránkové aplikace, webové aplikace, webová rozhraní API, mobilní a nativní aplikace a démony a aplikace na straně serveru.  Aplikace používají různé toky ověřování k přihlašování uživatelů a získání tokenů pro volání chráněných rozhraní API.

### <a name="single-page-application"></a>Jednostránková aplikace

Mnohé moderní webové aplikace jsou sestavené jako jednostránkové aplikace na straně klienta napsané pomocí JavaScriptu nebo rozhraní zabezpečeného hesla, jako je například úhlový Vue. js, a reagují. js. Tyto aplikace běží ve webovém prohlížeči a mají různé charakteristiky ověřování než tradiční webové aplikace na straně serveru. Platforma Microsoft Identity Platform umožňuje aplikacím s jednou stránkou přihlašovat uživatele a získat tokeny pro přístup k back-endové službě nebo k webovým rozhraním API.

![Jednostránková aplikace](media/scenarios/spa-app.svg)

Další informace najdete v [jednostránkovéch aplikacích](scenario-spa-overview.md).

### <a name="web-application-signing-in-a-user"></a>Přihlášení k webové aplikaci – uživatel

![Přihlašování uživatelů k webové aplikaci](media/scenarios/scenario-webapp-signs-in-users.svg)

Chcete-li **chránit webovou aplikaci** (přihlašování uživatele), použijte:

- V .NET World ASP.NET nebo ASP.NET Core se middlewarem ASP.NET Open ID Connect. V digestoře ochrana prostředku zahrnuje ověření tokenu zabezpečení, který provádí [rozšíření IdentityModel pro knihovnu .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , nikoli knihovny MSAL.

- Pokud vyvíjíte v Node. js, budete používat Passport. js.

Další informace najdete v článku [Webová aplikace, která](scenario-web-app-sign-user-overview.md)přihlašuje uživatele.

### <a name="web-application-signing-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Přihlášení k webové aplikaci – uživatel a volání webového rozhraní API jménem uživatele

![Webová aplikace volá webová rozhraní API.](media/scenarios/web-app.svg)

Z webové aplikace můžete **zavolat webové rozhraní API** jménem uživatele pomocí MSAL `ConfidentialClientApplication`. Použijete tok autorizačního kódu, který uloží získaný token do mezipaměti tokenů. Pak kontroler získá v případě potřeby tokeny v tichém režimu z mezipaměti. MSAL aktualizuje token v případě potřeby.

Další informace najdete v tématu [Webová aplikace volá rozhraní API pro web](scenario-web-app-call-api-overview.md).

### <a name="desktop-application-calling-a-web-api-on-behalf-of-the-signed-in-user"></a>Aplikace klasické pracovní plochy, která jménem přihlášeného uživatele volá webové rozhraní API

Chcete-li volat webové rozhraní API z desktopové aplikace, která přihlašuje uživatele, použijte metody získání interaktivního tokenu PublicClientApplication's MSAL. Tyto interaktivní metody umožňují řídit prostředí uživatelského rozhraní pro přihlašování. Aby bylo možné tuto interakci povolit, MSAL využívá webový prohlížeč.

![Desktop](media/scenarios/desktop-app.svg)

Pro aplikace hostované v systému Windows běžící na počítačích připojených k doméně systému Windows nebo AAD jsou k dispozici další možnosti. Pomocí [integrovaného ověřování systému Windows](https://aka.ms/msal-net-iwa)můžou tyto aplikace v tichém získání tokenu.

Aplikace spuštěné v zařízení bez prohlížeče budou nadále moci volat rozhraní API jménem uživatele. Aby se uživatel mohl ověřit, musí se přihlásit na jiném zařízení, které má webový prohlížeč. Pokud chcete tento scénář povolit, budete muset použít [tok kódu zařízení](https://aka.ms/msal-net-device-code-flow) .

![Tok kódu zařízení](media/scenarios/device-code-flow-app.svg)

A konečně, i když se to nedoporučuje, můžete použít [uživatelské jméno a heslo](https://aka.ms/msal-net-up) ve veřejných klientských aplikacích. Tento tok je stále potřeba v některých scénářích (například DevOps), ale Upozorňujeme, že jeho použití způsobí omezení pro vaši aplikaci. Například aplikace, které používají tento tok, se nebudou moct přihlašovat uživateli, který potřebuje službu Multi-Factor Authentication (podmíněný přístup). Neumožní vaší aplikaci těžit z libovolného jednotného přihlašování. Ověřování pomocí uživatelského jména a hesla přechází na zásady moderního ověřování a je poskytováno pouze z původních důvodů.

Pokud chcete, aby byla mezipaměť tokenu trvalá, měli byste v aplikacích klasické pracovní plochy [přizpůsobit serializaci mezipaměti tokenů](https://aka.ms/msal-net-token-cache-serialization). Pomocí předchozích generací knihoven ověřování (ADAL.NET 3. x a 4. x) můžete dokonce pomocí implementace [serializace mezipaměti](https://aka.ms/msal-net-dual-cache-serialization)s duálním tokenem povolit i zpětná a dopředně kompatibilní mezipaměť tokenů.

Pro další informace si přečtěte [desktopovou aplikaci, která volá webová rozhraní API](scenario-desktop-overview.md).

### <a name="mobile-application-calling-a-web-api-on-behalf-of-the-user-whos-signed-in-interactively"></a>Mobilní aplikace, která volá webové rozhraní API jménem uživatele, který je přihlášený interaktivně

Podobně jako aplikace klasické pracovní plochy mobilní aplikace k získání tokenu pro volání webového rozhraní API použije metody získání interaktivního tokenu PublicClientApplication's MSAL.

![Mobilní zařízení](media/scenarios/mobile-app.svg)

MSAL iOS a MSAL Android ve výchozím nastavení používají webový prohlížeč systému. Můžete ji ale také směrovat na použití vloženého webového zobrazení. V závislosti na mobilní platformě se liší: (UWP, iOS, Android).

Některé scénáře, zahrnující podmíněný přístup související s ID zařízení nebo registrované zařízení, vyžadují, aby byl na zařízení nainstalovaný [zprostředkovatel](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) . Příklady zprostředkovatelů jsou portál společnosti Microsoftu (na Androidu), Microsoft Authenticator (Android a iOS). MSAL je teď schopný spolupracovat se zprostředkovateli.

> [!NOTE]
> Mobilní aplikace (pomocí MSAL. iOS, MSAL. V Androidu nebo MSAL.NET/Xamarin) můžou být použité zásady ochrany aplikací (například brání uživateli zkopírovat nějaký chráněný text). [Spravuje ho Intune](https://docs.microsoft.com/intune/app-sdk) a jako spravovanou aplikaci ho rozpozná Intune. [Intune SDK](https://docs.microsoft.com/intune/app-sdk-get-started) je oddělená od knihoven MSAL a mluví se svým vlastním AAD.

Další informace najdete v [mobilní aplikaci, která volá webová rozhraní API](scenario-mobile-overview.md).

### <a name="protected-web-api"></a>Chráněné webové rozhraní API

Pomocí koncového bodu Microsoft Identity Platform můžete zabezpečit webové služby, například webové rozhraní API RESTful vaší aplikace. Chráněné webové rozhraní API se volá pomocí přístupového tokenu, který zabezpečuje jeho data a ověřuje příchozí požadavky. Volající webového rozhraní API připojí přístupový token v autorizační hlavičce požadavku HTTP. Pokud chcete chránit rozhraní API ASP.NET nebo ASP.NET Core webového rozhraní API, budete muset ověřit přístupový token. V takovém případě použijete middleware ASP.NET JWT. V digestoři se ověřování provádí pomocí [rozšíření IdentityModel pro knihovnu .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , nikoli MSAL.NET

Další informace najdete v [chráněném webovém rozhraní API](scenario-protected-web-api-overview.md)pro čtení.

### <a name="web-api-calling-another-downstream-web-api-on-behalf-of-the-user-for-whom-it-was-called"></a>Webové rozhraní API volá jiné podřízené webové rozhraní API jménem uživatele, pro kterého se zavolalo.

Pokud navíc chcete, aby vaše ASP.NET nebo ASP.NET Core chráněné webové rozhraní API zavolalo jiné webové rozhraní API jménem uživatele, bude muset aplikace získat token pro webové rozhraní API pro příjem dat pomocí metody ConfidentialClientApplication, která získá token [jménem uživatel](https://aka.ms/msal-net-on-behalf-of): Tento postup se také nazývá volání služeb.
Webová rozhraní API, která volají jiné webové rozhraní API, budou muset také poskytovat vlastní serializaci mezipaměti.

  ![Webové rozhraní API](media/scenarios/web-api.svg)

Další informace najdete v tématu [webové rozhraní API, které volá rozhraní API pro web](scenario-web-api-call-api-overview.md).

### <a name="desktopservice-or-web-daemon-application-calling-web-api-without-a-user-in-its-own-name"></a>Aplikace Desktop/Service nebo webového démona volající webové rozhraní API bez uživatele (ve vlastním názvu)

Aplikace, které mají dlouhotrvající procesy nebo které fungují bez zásahu uživatele, potřebují také způsob, jak přistupovat k zabezpečeným webovým rozhraním API. Tyto aplikace můžou ověřovat a získávat tokeny pomocí identity aplikace namísto delegované identity uživatele. Prokazují jejich identitu pomocí tajného klíče klienta nebo certifikátu.
Takové aplikace můžete napsat (aplikace démon), která pro aplikaci získá token, a to pomocí metod získání přihlašovacích [údajů klienta](https://aka.ms/msal-net-client-credentials) ConfidentialClientApplication's MSAL. Předpokládá se, že aplikace dříve zaregistrovala tajný klíč (heslo aplikace nebo certifikát nebo kontrolní výraz klienta) pomocí služby Azure AD, kterou pak sdílí s tímto voláním.

![Aplikace démona](media/scenarios/daemon-app.svg)

Další informace najdete v tématu [aplikace démona pro čtení, která volá webová rozhraní API](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scénáře a podporované toky ověřování

Scénáře, které zahrnují získání tokenů, jsou také mapovány na toky ověřování OAuth 2,0 popsané v podrobnostech v [protokolech Microsoft Identity Platform](active-directory-v2-protocols.md) .

|Scénář | Podrobný návod pro scénář – through | Tok OAuth 2,0/grant | Cílová skupina |
|--|--|--|--|
| [![Jedna stránka aplikace](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | [Jednostránková aplikace](scenario-spa-overview.md) | [Nepřímo](v2-oauth2-implicit-grant-flow.md) | Pracovní nebo školní účty a osobní účty, B2C
| [![Webová aplikace, která přihlásí uživatele](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | [Webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md) | [Autorizační kód](v2-oauth2-auth-code-flow.md) | Pracovní nebo školní účty a osobní účty, B2C |
| [![Webová aplikace, která volá webová rozhraní API](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | [Webová aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md) | [Autorizační kód](v2-oauth2-auth-code-flow.md) | Pracovní nebo školní účty a osobní účty, B2C |
| [![Aplikace klasické pracovní plochy, která volá webová rozhraní API](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) | [Desktopová aplikace, které volá webová rozhraní API](scenario-desktop-overview.md)| Interaktivní ([autorizační kód](v2-oauth2-auth-code-flow.md) s PKCE) | Pracovní nebo školní účty a osobní účty, B2C |
| | | Integrovaná okna | Pracovní nebo školní účty |
| | | [Heslo vlastníka prostředku](v2-oauth-ropc.md)  | Pracovní nebo školní účty, B2C |
| ![Tok kódu zařízení](media/scenarios/device-code-flow-app.svg)| [Desktopová aplikace, které volá webová rozhraní API](scenario-desktop-overview.md) | [Kód zařízení](v2-oauth2-device-code.md)  | Pracovní nebo školní účty * |
| [![Mobilní aplikace, která volá webová rozhraní API](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | [Mobilní aplikace, která volá webová rozhraní API](scenario-mobile-overview.md) | Interaktivní ([autorizační kód](v2-oauth2-auth-code-flow.md) s PKCE)  |   Pracovní nebo školní účty a osobní účty, B2C
| | | Heslo vlastníka prostředku  | Pracovní nebo školní účty, B2C |
| [![Aplikace démon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | [Aplikace démon](scenario-daemon-overview.md) | [Přihlašovací údaje klienta](v2-oauth2-client-creds-grant-flow.md)  |   Oprávnění pouze aplikace (bez uživatele) pouze v organizacích AAD
| [![Webové rozhraní API, které volá webová rozhraní API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | [Webové rozhraní API, které volá webová rozhraní API](scenario-web-api-call-api-overview.md)| [Jménem](v2-oauth2-on-behalf-of-flow.md) | Pracovní nebo školní účty a osobní účty |

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scénáře a podporované platformy a jazyky

Ne každý typ aplikace je k dispozici na všech platformách. K sestavování aplikací můžete použít také různé jazyky. Knihovny Microsoft Authentication Library podporují řadu **platforem** (JavaScript, .NET Framework, .NET Core, Windows 10/UWP, Xamarin. iOS, Xamarin. Android, Native iOS, nativní Android, Java, Python).

|Scénář  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Jednostránková aplikace](scenario-spa-overview.md) <br/>[![Jedna stránka aplikace](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js
| [Webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md) <br/>[![Webová aplikace, která přihlásí uživatele](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET](media/sample-v2-code/logo_NET.png)</br> ASP.NET ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core
| [Webová aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md) <br/> [![Webová aplikace, která volá webová rozhraní API](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET](media/sample-v2-code/logo_NET.png) </br> ASP.NET + MSAL.NET </br> ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Baňka + MSAL Python| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Baňka + MSAL Python| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Baňka + MSAL Python
| [Desktopová aplikace, které volá webová rozhraní API](scenario-desktop-overview.md) <br/> ![ [ Aplikaceklasicképracovníplochy,kterávoláwebové![rozhraní API](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) tok kódu zařízení](media/scenarios/device-code-flow-app.svg) | ![MSAL.NET](media/sample-v2-code/logo_NET.png)  MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python
| [Mobilní aplikace, která volá webová rozhraní API](scenario-mobile-overview.md) <br/> [![Mobilní aplikace, která volá webová rozhraní API](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/logo_xamarin.png) MSAL.NET | | | ![iOS/objektivní C nebo SWIFT](media/sample-v2-code/logo_iOS.png) MSAL. iOS | ![Android](media/sample-v2-code/logo_Android.png) MSAL. Svém
| [Aplikace démon](scenario-daemon-overview.md) <br/> [![Aplikace démon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET](media/sample-v2-code/logo_NET.png) MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python
| [Webové rozhraní API, které volá webová rozhraní API](scenario-web-api-call-api-overview.md) <br/> [![Webové rozhraní API, které volá webová rozhraní API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![.NET](media/sample-v2-code/logo_NET.png) <br/> ASP.NET + MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png) <br/> ASP.NET Core + MSAL.NET| ![.NET Core](media/sample-v2-code/logo_NETcore.png) <br/> ASP.NET Core + MSAL.NET| ![.NET Core](media/sample-v2-code/logo_NETcore.png)<br/> ASP.NET Core + MSAL.NET

Viz také [knihovny podporované společností Microsoft podle operačního systému/jazyka](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [základech ověřování](authentication-scenarios.md) a [přístupových tokenech](access-tokens.md).
