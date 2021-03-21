---
title: Toky ověřování platformy Microsoft Identity & scénáře aplikací | Azure
description: Přečtěte si o scénářích aplikací pro platformu Microsoft identity, včetně ověřování identit, získávání tokenů a volání chráněných rozhraní API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: ffa52805a5e2680d534b2b24a210465cb3fc7cac
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100557851"
---
# <a name="authentication-flows-and-application-scenarios"></a>Toky ověřování a scénáře aplikací

Platforma Microsoft identity podporuje ověřování pro různé druhy moderních architektur aplikací. Všechny architektury jsou založené na standardních protokolech [OAuth 2,0 a OpenID Connect](active-directory-v2-protocols.md)v oborech Standard. Pomocí [knihoven ověřování pro platformu Microsoft Identity Platform](reference-v2-libraries.md)aplikace ověřují identity a získávají tokeny pro přístup k chráněným rozhraním API.

Tento článek popisuje toky ověřování a scénáře aplikací, které jsou používány v nástroji.

## <a name="application-categories"></a>Kategorie aplikací

Tokeny lze získat z několika typů aplikací, včetně:

- Webové aplikace
- Mobilní aplikace
- Desktopové aplikace
- Webová rozhraní API

Tokeny mohou být také získány aplikacemi spuštěnými na zařízeních, která nemají prohlížeč nebo jsou spuštěny v Internet věcí (IoT).

V následujících částech jsou popsány kategorie aplikací.

### <a name="protected-resources-vs-client-applications"></a>Chráněné prostředky vs. klientské aplikace

Scénáře ověřování zahrnují dvě aktivity:

- **Získání tokenů zabezpečení pro chráněné webové rozhraní API**: Doporučujeme, abyste používali [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md)vyvinutou a podporovanou Microsoftem.
- **Ochrana webového rozhraní API nebo webové aplikace**: jedna výzva k ochraně těchto prostředků ověřuje token zabezpečení. Na některých platformách Microsoft nabízí [knihovny middlewaru](reference-v2-libraries.md).

### <a name="with-users-or-without-users"></a>S uživateli nebo bez uživatelů

Většina scénářů ověřování získá tokeny jménem přihlášeného uživatele.

![Scénáře s uživateli](media/scenarios/scenarios-with-users.svg)

Existují však také aplikace démona. V těchto scénářích aplikace získávají tokeny jménem sebe bez uživatele.

![Scénáře s aplikacemi démon](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Jedna stránka, veřejný klient a důvěrné klientské aplikace

Tokeny zabezpečení lze získat pomocí více typů aplikací. Tyto aplikace jsou obvykle rozdělené do následujících tří kategorií. Každý se používá s různými knihovnami a objekty.

- **Jednostránkové aplikace**: označují se také jako jednostránkové, jedná se o webové aplikace, ve kterých jsou tokeny získány pomocí JavaScriptu nebo aplikace TypeScriptu spuštěné v prohlížeči. Mnohé moderní aplikace mají jednostránkovou aplikaci na front-endu, která je primárně napsaná v JavaScriptu. Aplikace často používá rozhraní, jako je například úhlová, reakce nebo Vue. MSAL.js je jediná knihovna Microsoft Authentication Library, která podporuje jednostránkové aplikace.

- **Veřejné klientské aplikace**: aplikace v této kategorii, například následující typy, vždy přihlašovat uživatele:
  - Aplikace klasické pracovní plochy, které volají webová rozhraní API jménem přihlášeného uživatele
  - Mobilní aplikace
  - Aplikace běžící na zařízeních, která nemají prohlížeč, podobně jako u aplikací využívajících IoT

- **Důvěrné klientské aplikace**: aplikace v této kategorii zahrnují:
  - Webové aplikace, které volají webové rozhraní API
  - Webová rozhraní API, která volají webové rozhraní API
  - Aplikace démona, i když jsou implementovány jako služba konzoly, jako je třeba démon pro Linux nebo služba systému Windows

### <a name="sign-in-audience"></a>Cílová skupina přihlášení

Dostupné toky ověřování se liší v závislosti na cílové skupině přihlášení. Některé toky jsou k dispozici pouze pro pracovní nebo školní účty. Další jsou k dispozici pro pracovní i školní účty i pro osobní účty Microsoft.

Další informace najdete v tématu [podporované typy účtů](v2-supported-account-types.md#account-type-support-in-authentication-flows).

## <a name="application-scenarios"></a>Scénáře aplikací

Platforma Microsoft identity podporuje ověřování pro tyto architektury aplikací:

- Jednostránkové aplikace
- Webové aplikace
- Webová rozhraní API
- Mobilní aplikace
- Nativní aplikace
- Aplikace démonů
- Aplikace na straně serveru

Aplikace používají k přihlašování uživatelů různé toky ověřování a získávají tokeny pro volání chráněných rozhraní API.

### <a name="single-page-application"></a>Jednostránková aplikace

Mnohé moderní webové aplikace jsou sestavené jako jednostránkové aplikace na straně klienta. Tyto aplikace používají jazyk JavaScript nebo rozhraní, jako je například úhlová, Vue a reakce. Tyto aplikace běží ve webovém prohlížeči.

Jednostránkové aplikace se liší od tradičních webových aplikací na straně serveru z hlediska charakteristik ověřování. Pomocí Microsoft Identity Platform se můžou jednostránkové aplikace přihlašovat uživatelům a získat tokeny pro přístup k back-endové službě nebo webovým rozhraním API. Platforma Microsoft Identity nabízí dva typy udělení pro aplikace JavaScriptu:

| MSAL.js (2. x) | MSAL.js (1. x) |
|---|---|
| ![Ověřování jednou stránkou aplikace](media/scenarios/spa-app-auth.svg) | ![Jedna stránková aplikace je implicitní.](media/scenarios/spa-app.svg) |

### <a name="web-app-that-signs-in-a-user"></a>Webová aplikace, která se podepisuje uživateli

![Webová aplikace, která se podepisuje uživateli](media/scenarios/scenario-webapp-signs-in-users.svg)

Ochrana webové aplikace, která se podepisuje uživateli:

- Pokud vyvíjíte v .NET, používáte ASP.NET nebo ASP.NET Core s middlewarem ASP.NET OpenID Connect. Ochrana prostředku zahrnuje ověření tokenu zabezpečení, který provádí [rozšíření IdentityModel pro .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , a ne knihovny MSAL.

- Pokud vyvíjíte v Node.js, použijete [uzel MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) nebo [Passport.js](https://github.com/AzureAD/passport-azure-ad).

Další informace najdete v tématu [Webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md).

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>Webová aplikace, která se podepisuje uživateli a volá webové rozhraní API jménem uživatele

![Webová aplikace, která volá webová rozhraní API](media/scenarios/web-app.svg)

Chcete-li volat webové rozhraní API z webové aplikace jménem uživatele, použijte tok autorizačního kódu a uložte získané tokeny do mezipaměti tokenů. V případě potřeby MSAL aktualizuje tokeny a kontroler z mezipaměti tiše získává tokeny.

Další informace najdete v tématu [Webová aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md).

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>Aplikace klasické pracovní plochy, která volá webové rozhraní API jménem přihlášeného uživatele

Pokud chcete, aby desktopová aplikace volala webové rozhraní API, které přihlašuje uživatele, využijte interaktivní metody získání tokenu MSAL. Pomocí těchto interaktivních metod můžete řídit prostředí uživatelského rozhraní pro přihlašování. MSAL používá pro tuto interakci webový prohlížeč.

![Aplikace klasické pracovní plochy, která volá webové rozhraní API](media/scenarios/desktop-app.svg)

K dispozici je další možnost pro aplikace hostované v systému Windows v počítačích, které jsou připojeny k doméně systému Windows nebo Azure Active Directory (Azure AD). Tyto aplikace mohou tiše získat token pomocí [integrovaného ověřování systému Windows](https://aka.ms/msal-net-iwa).

Aplikace spuštěné v zařízení bez prohlížeče mohou stále volat rozhraní API jménem uživatele. Aby se uživatel mohl ověřit, musí se přihlásit na jiném zařízení, které má webový prohlížeč. Tento scénář vyžaduje, abyste používali [tok kódu zařízení](https://aka.ms/msal-net-device-code-flow).

![Tok kódu zařízení](media/scenarios/device-code-flow-app.svg)

I když to nedoporučujeme používat, je [tok uživatelského jména a hesla](scenario-desktop-acquire-token.md#username-and-password) k dispozici ve veřejných klientských aplikacích. Tento tok je stále potřeba v některých scénářích, jako je DevOps.

Použití toku uživatelského jména a hesla omezuje vaše aplikace. Například aplikace se nemohou přihlašovat uživateli, který potřebuje použít vícefaktorové ověřování nebo nástroj pro podmíněný přístup ve službě Azure AD. Vaše aplikace také nemají výhodu jednotného přihlašování. Ověřování s uživatelským jménem nebo heslem přechází na zásady moderního ověřování a poskytuje se jenom z původních důvodů.

Pokud chcete, aby mezipaměť tokenu trvalá, můžete v desktopových aplikacích upravit [serializaci mezipaměti tokenů](scenario-desktop-acquire-token.md#file-based-token-cache). Implementací [duální serializace mezipaměti tokenů](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3)můžete použít zpětně kompatibilní a mezipaměti tokenů kompatibilní s přesměrováním. Tyto tokeny podporují předchozí generace knihoven ověřování. Konkrétní knihovny zahrnují knihovnu ověřování Azure AD pro .NET (ADAL.NET) verze 3 a verze 4.

Další informace najdete v tématu [aplikace klasické pracovní plochy, která volá webová rozhraní API](scenario-desktop-overview.md).

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>Mobilní aplikace, která volá webové rozhraní API jménem interaktivního uživatele

Podobně jako desktopová aplikace volá mobilní aplikace metody získání interaktivních tokenů MSAL k získání tokenu pro volání webového rozhraní API.

![Mobilní aplikace, která volá webové rozhraní API](media/scenarios/mobile-app.svg)

MSAL iOS a MSAL Android ve výchozím nastavení používají webový prohlížeč systému. Můžete je ale směrovat na místo toho vloženého webového zobrazení. Existují konkrétní zvláštnosti, které závisí na mobilní platformě: Univerzální platforma Windows (UWP), iOS nebo Android.

Některé scénáře, například ty, které zahrnují podmíněný přístup týkající se ID zařízení nebo registrace zařízení, vyžadují, aby byl na zařízení nainstalovaný zprostředkovatel. Příklady zprostředkovatelů jsou Microsoft Portál společnosti v Androidu a Microsoft Authenticator v Androidu a iOS. MSAL teď může komunikovat se zprostředkovateli. Další informace o zprostředkovatelích najdete v tématu [využití zprostředkovatelů v Androidu a iOS](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Další informace najdete v tématu [mobilní aplikace, která volá webová rozhraní API](scenario-mobile-overview.md).

> [!NOTE]
> Mobilní aplikace, která používá MSAL. iOS, MSAL. Pro Android nebo MSAL.NET na Xamarin můžou být na ni aplikovány zásady ochrany aplikací. Zásady mohou například zabránit uživateli v kopírování chráněného textu. Mobilní aplikace je spravovaná pomocí Intune a jako spravovaná aplikace ji rozpozná Intune. Další informace najdete v tématu [Microsoft Intune App SDK Overview](/intune/app-sdk).
>
> [Intune App SDK](/intune/app-sdk-get-started) je oddělená od knihoven MSAL a komunikuje s Azure AD sám o sobě.

### <a name="protected-web-api"></a>Chráněné webové rozhraní API

Pomocí koncového bodu Microsoft Identity Platform můžete zabezpečit webové služby, jako je webové rozhraní API RESTful vaší aplikace. Chráněné webové rozhraní API se volá prostřednictvím přístupového tokenu. Token pomáhá zabezpečit data rozhraní API a ověřovat příchozí požadavky. Volající webového rozhraní API připojí přístupový token v autorizační hlavičce požadavku HTTP.

Pokud chcete chránit rozhraní API ASP.NET nebo ASP.NET Core webového rozhraní API, ověřte přístupový token. Pro toto ověření použijete middleware ASP.NET JWT. Ověřování provádí [rozšíření IdentityModel pro knihovnu .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , nikoli MSAL.NET.

Další informace najdete v tématu [chráněné webové rozhraní API](scenario-protected-web-api-overview.md).

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>Webové rozhraní API, které volá jiné webové rozhraní API jménem uživatele

Aby vaše aplikace chráněného webového rozhraní API volala jiné webové rozhraní API jménem uživatele, musí získat token pro webové rozhraní API pro příjem dat. Taková volání se někdy označují jako volání typu *služba-služba* . Webová rozhraní API, která volají jiná webová rozhraní API, musí poskytovat vlastní serializaci mezipaměti.

![Webové rozhraní API, které volá jiné webové rozhraní API](media/scenarios/web-api.svg)

Další informace najdete v tématu [webové rozhraní API, které volá webová rozhraní API](scenario-web-api-call-api-overview.md).

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>Aplikace démona, která volá webové rozhraní API v názvu démona

Aplikace, které mají dlouhotrvající procesy nebo které fungují bez interakce s uživatelem, potřebují také způsob, jak přistupovat k zabezpečeným webovým rozhraním API. Taková aplikace může ověřit a získat tokeny pomocí identity aplikace. Aplikace prokáže svou identitu pomocí tajného klíče klienta nebo certifikátu.

Takové aplikace démona, které získávají token volající aplikace, můžete napsat pomocí metod získání [přihlašovacích údajů klienta](scenario-daemon-acquire-token.md#acquiretokenforclient-api) v MSAL. Tyto metody vyžadují tajný klíč klienta, který přidáte do registrace aplikace v Azure AD. Aplikace potom tento tajný klíč sdílí s názvem démona. Příklady takových tajných klíčů zahrnují hesla aplikací, kontrolní výraz certifikátu a kontrolní výraz klienta.

![Aplikace démona volaná jinými aplikacemi a rozhraními API](media/scenarios/daemon-app.svg)

Další informace najdete v tématu [aplikace démona, která volá webová rozhraní API](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scénáře a podporované toky ověřování

Toky ověřování se používají k implementaci scénářů aplikace, které vyžadují tokeny. Mezi scénáři aplikace a toky ověřování neexistuje mapování 1:1.

Scénáře, které zahrnují získání tokenů, jsou také mapovány na toky ověřování OAuth 2,0. Další informace najdete v tématu [protokoly OAuth 2,0 a OpenID Connect na platformě Microsoft Identity Platform](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Scenario</th> <th>Podrobný návod pro scénář – through</th> <th>Tok OAuth 2,0 a udělení</th> <th>Cílová skupina</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Auth code" src="media/scenarios/spa-app-auth.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Jednostránková aplikace</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorizační kód</a> s PKCE</td>
   <td>Pracovní nebo školní účty, osobní účty a Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Implicit" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Jednostránková aplikace</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implicitní</a></td>
   <td>Pracovní nebo školní účty, osobní účty a Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Webová aplikace, která přihlašuje uživatele</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorizační kód</a></td>
   <td>Pracovní nebo školní účty, osobní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that calls web APIs" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Webová aplikace, která volá webová rozhraní API</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorizační kód</a></td>
   <td>Pracovní nebo školní účty, osobní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Desktopová aplikace, které volá webová rozhraní API</a></td>
   <td>Interaktivní pomocí <a href="v2-oauth2-auth-code-flow.md">autorizačního kódu</a> s PKCE</td>
   <td>Pracovní nebo školní účty, osobní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td>Integrované ověřování systému Windows</td>
   <td>Pracovní nebo školní účty</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Heslo vlastníka prostředku</a></td>
   <td>Pracovní nebo školní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Kód zařízení</a></td>
   <td>Pracovní nebo školní účty, osobní účty a Azure AD B2C</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Mobilní aplikace, která volá webová rozhraní API</a></td>
   <td>Interaktivní pomocí <a href="v2-oauth2-auth-code-flow.md">autorizačního kódu</a> s PKCE</td>
   <td>Pracovní nebo školní účty, osobní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Heslo vlastníka prostředku</a></td>
   <td>Pracovní nebo školní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Aplikace démona, která volá webová rozhraní API</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Přihlašovací údaje klienta</a></td>
   <td>Oprávnění jenom pro aplikace, která nemají žádného uživatele a používají se jenom v organizacích Azure AD.</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Webové rozhraní API, které volá webová rozhraní API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Jménem</a></td>
   <td>Pracovní nebo školní účty a osobní účty</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scénáře a podporované platformy a jazyky

Knihovny Microsoft Authentication Library podporují více platforem:

- .NET Core
- .NET Framework
- Java
- JavaScript
- macOS
- Nativní Android
- Nativní iOS
- Node.js
- Python
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android

K sestavování aplikací můžete použít také různé jazyky.

> [!NOTE]
> Některé typy aplikací nejsou k dispozici na všech platformách.

Ve sloupci Windows v následující tabulce se pokaždé, když je uvedeno rozhraní .NET Core, je také možné použít .NET Framework. Druhá je vynechána, aby nedocházelo k zbytečnému zaplnění tabulky.

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Jednostránková aplikace](scenario-spa-overview.md) <br/>[![Ověřování jednostránkové aplikace](media/scenarios/spa-app-auth.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Jednostránková aplikace](scenario-spa-overview.md) <br/>[![Aplikace s jednou stránkou je implicitní](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Webová aplikace, která přihlašuje uživatele](scenario-web-app-sign-user-overview.md) <br/>[![Webová aplikace, která přihlásí uživatele](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>
| [Webová aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md) <br/> <br/>[![Webová aplikace, která volá webová rozhraní API](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png) <br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Baňka + MSAL Python ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Baňka + MSAL Python ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Baňka + MSAL Python ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>
| [Desktopová aplikace, které volá webová rozhraní API](scenario-desktop-overview.md) <br/> <br/>[ ![ Aplikace klasické pracovní plochy, která volá webová rozhraní API](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![ Tok kódu zařízení](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/> ![iOS/objektivní C nebo SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc |
| [Mobilní aplikace, která volá webová rozhraní API](scenario-mobile-overview.md) <br/> [![Mobilní aplikace, která volá webová rozhraní API](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/objektivní C nebo SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Svém
| [Aplikace démona](scenario-daemon-overview.md) <br/> [![Aplikace démona](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>
| [Webové rozhraní API, které volá webová rozhraní API](scenario-web-api-call-api-overview.md) <br/><br/> [![Webové rozhraní API, které volá webová rozhraní API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![Uzel MSAL](media/sample-v2-code/small-logo-nodejs.png) <br/>Uzel MSAL<br/>

Další informace najdete v tématu [knihovny ověřování platformy Microsoft Identity Platform](reference-v2-libraries.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [základech ověřování](./authentication-vs-authorization.md) a [přístupových tokenech na platformě Microsoft Identity](access-tokens.md).
* Přečtěte si další informace o [zabezpečení přístupu k aplikacím IoT](/azure/architecture/example-scenario/iot-aad/iot-aad).
