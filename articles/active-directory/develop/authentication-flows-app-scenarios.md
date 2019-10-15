---
title: Scénáře ověřování pro Microsoft Identity Platform | Azure
description: Přečtěte si o tocích ověřování a scénářích aplikací pro platformu Microsoft identity. Seznamte se s různými typy aplikací, které můžou ověřovat identity, získávat tokeny a volat chráněná rozhraní API.
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
ms.date: 09/27/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71557a2776bae36508beec8d5af9e00923393163
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324759"
---
# <a name="authentication-flows-and-application-scenarios"></a>Toky ověřování a scénáře aplikací

Koncový bod Microsoft Identity Platform (v 2.0) podporuje ověřování pro různé druhy moderních architektur aplikací. Všechny architektury jsou založené na standardních protokolech [OAuth 2,0 a OpenID Connect](active-directory-v2-protocols.md)v oborech Standard.  Pomocí [knihoven pro ověřování](reference-v2-libraries.md)aplikace ověřují identity a získávají tokeny pro přístup k chráněným rozhraním API.

Tento článek popisuje různé toky ověřování a scénáře aplikací, které jsou používány v nástroji. Tento článek obsahuje také seznam:
- [Scénáře aplikací a podporované toky ověřování](#scenarios-and-supported-authentication-flows).
- [Scénáře aplikací a podporované platformy a jazyky](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Kategorie aplikací

Tokeny lze získat z několika typů aplikací, mezi které patří:

- Web Apps
- Mobilní aplikace
- Desktopové aplikace
- Webová rozhraní API

Můžou se taky získat z aplikací běžících na zařízeních, která nemají prohlížeč nebo běží na IoT.

Aplikace je možné kategorizovat jako v následujícím seznamu:

- [Chráněné prostředky vs. klientské aplikace](#protected-resources-vs-client-applications): některé scénáře mají na starosti ochranu prostředků, jako jsou webové aplikace nebo webová rozhraní API. Další scénáře se týkají získání tokenu zabezpečení pro volání chráněného webového rozhraní API.
- [S uživateli nebo bez uživatelů](#with-users-or-without-users): některé scénáře zahrnují přihlášeného uživatele, jiné jako scénáře démona nezahrnují uživatele.
- [Jedna stránka, veřejný klient a důvěrné klientské aplikace](#single-page-public-client-and-confidential-client-applications): Jedná se o tři velké kategorie typů aplikací. Každý se používá s různými knihovnami a objekty.
- [Cílová skupina pro přihlášení](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): dostupné toky ověřování se liší v závislosti na cílové skupině přihlášení. Některé toky jsou k dispozici pouze pro pracovní nebo školní účty. A některé jsou k dispozici pro pracovní i školní účty i pro osobní účty Microsoft. Povolená cílová skupina závisí na tocích ověřování.
- [Podporované toky OAuth 2,0](#scenarios-and-supported-authentication-flows): toky ověřování se používají k implementaci scénářů aplikace, které vyžadují tokeny. Mezi scénáři aplikace a toky ověřování neexistuje mapování 1:1.
- [Podporované platformy](#scenarios-and-supported-platforms-and-languages): ne všechny scénáře aplikací jsou k dispozici pro všechny platformy.

### <a name="protected-resources-vs-client-applications"></a>Chráněné prostředky vs. klientské aplikace

Scénáře ověřování zahrnují dvě aktivity:

- **Získání tokenů zabezpečení pro chráněné webové rozhraní API**: Microsoft doporučuje použít knihovny pro [ověřování](reference-v2-libraries.md#microsoft-supported-client-libraries) k získání tokenů, zejména v řadě Microsoft Authentication Library (MSAL).
- **Ochrana webového rozhraní API nebo webové aplikace**: na základě jedné výzvy k ochraně webového rozhraní API nebo prostředku webové aplikace se token zabezpečení ověřuje. Na některých platformách Microsoft nabízí [knihovny middlewaru](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>S uživateli nebo bez uživatelů

Většina scénářů ověřování získá tokeny jménem přihlášeného uživatele.

![Scénáře s uživateli](media/scenarios/scenarios-with-users.svg)

Existují však také scénáře aplikace démona, ve kterých aplikace získávají tokeny jménem sebe bez uživatele.

![Scénáře s aplikacemi démon](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Jedna stránka, veřejný klient a důvěrné klientské aplikace

Tokeny zabezpečení lze získat z více typů aplikací. Tyto aplikace jsou obvykle rozdělené do tří kategorií:

- **Jednostránkové aplikace**: taky označované jako jednostránkové, jedná se o webové aplikace, ve kterých se tokeny získávají z JavaScriptu nebo aplikace TypeScriptu spuštěné v prohlížeči. Mnoho moderních aplikací má front-end aplikační aplikaci, která je primárně napsaná v JavaScriptu. Aplikace často používá rozhraní, jako je například úhlová, reakce nebo Vue. MSAL. js je jediná knihovna Microsoft Authentication Library, která podporuje jednostránkové aplikace.

- **Veřejné klientské aplikace**: tyto aplikace se vždycky přihlašovat uživatelům:
  - Desktopové aplikace volající webová rozhraní API jménem přihlášeného uživatele
  - Mobilní aplikace
  - Aplikace běžící na zařízeních, která nemají prohlížeč, podobně jako u aplikací využívajících iOT

  Tyto aplikace jsou reprezentovány třídou MSAL [PublicClientApplication](msal-client-applications.md) .

- **Důvěrné klientské aplikace**:
  - Webové aplikace, které volají webové rozhraní API
  - Webová rozhraní API, která volají webové rozhraní API
  - Aplikace démona, i když jsou implementovány jako služba konzoly, jako je třeba démon pro Linux nebo služba systému Windows
 
  Tyto typy aplikací používají třídu [ConfidentialClientApplication](msal-client-applications.md) .

## <a name="application-scenarios"></a>Scénáře aplikací

Koncový bod platformy Microsoft identity podporuje ověřování pro různé druhy architektur aplikací:

- Jednostránkové aplikace
- Web Apps
- Webová rozhraní API
- Mobilní aplikace
- Nativní aplikace
- Aplikace démonů
- Aplikace na straně serveru

Aplikace používají k přihlašování uživatelů různé toky ověřování a získávají tokeny pro volání chráněných rozhraní API.

### <a name="a-single-page-application"></a>Jednostránkové aplikace

Mnohé moderní webové aplikace jsou sestavené jako jednostránkové aplikace na straně klienta napsané pomocí JavaScriptu nebo rozhraní zabezpečeného hesla, jako je například úhlový Vue. js, a reagují na ni. js. Tyto aplikace běží ve webovém prohlížeči. Jejich charakteristiky ověřování se liší od tradičních webových aplikací na straně serveru. Pomocí Microsoft Identity Platform se můžou jednostránkové aplikace přihlašovat uživatelům a získat tokeny pro přístup k back-endové službě nebo webovým rozhraním API.

![Jednostránkové aplikace](media/scenarios/spa-app.svg)

Další informace najdete v tématu [jednostránkové aplikace](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>Webová aplikace, která se přihlašuje uživateli

![Webová aplikace, která se podepisuje uživateli](media/scenarios/scenario-webapp-signs-in-users.svg)

Ochrana webové aplikace, která se přihlašuje uživateli:

- Pokud vyvíjíte v .NET, použijete ASP.NET nebo ASP.NET Core se middlewarem ASP.NET Open ID Connect. Ochrana prostředku zahrnuje ověření tokenu zabezpečení, který provádí [rozšíření IdentityModel pro knihovnu .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , a ne knihovny MSAL.

- Pokud vyvíjíte v Node. js, použijte Passport. js.

Další informace najdete v tématu [Webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Webová aplikace, která se podepisuje uživateli a volá webové rozhraní API jménem uživatele

![Webová aplikace, která volá webová rozhraní API](media/scenarios/web-app.svg)

Chcete-li volat webové rozhraní API z webové aplikace jménem uživatele, použijte třídu MSAL **ConfidentialClientApplication** . Použijete tok autorizačního kódu a uložíte získané tokeny do mezipaměti tokenů. V případě potřeby MSAL aktualizuje tokeny a kontroler z mezipaměti tiše získává tokeny.

Další informace najdete v tématu [Webová aplikace volající webová rozhraní API](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Aplikace klasické pracovní plochy, která jménem přihlášeného uživatele zavolá webové rozhraní API

Pokud chcete, aby aplikace klasické pracovní plochy volala webové rozhraní API, které přihlašuje uživatele, použijte metody pro získání interaktivních tokenů třídy MSAL **PublicClientApplication** . Pomocí těchto interaktivních metod můžete řídit prostředí uživatelského rozhraní pro přihlašování. MSAL používá pro tuto interakci webový prohlížeč.

![Aplikace klasické pracovní plochy, která volá webové rozhraní API](media/scenarios/desktop-app.svg)

K dispozici je další možnost pro aplikace hostované v systému Windows v počítačích, které jsou připojeny k doméně systému Windows nebo Azure Active Directory (Azure AD). Tyto aplikace mohou tiše získat token pomocí [integrovaného ověřování systému Windows](https://aka.ms/msal-net-iwa).

Aplikace spuštěné v zařízení bez prohlížeče mohou stále volat rozhraní API jménem uživatele. Aby se uživatel mohl ověřit, musí se přihlásit na jiném zařízení, které má webový prohlížeč. Tento scénář vyžaduje, abyste používali [tok kódu zařízení](https://aka.ms/msal-net-device-code-flow).

![Tok kódu zařízení](media/scenarios/device-code-flow-app.svg)

I když ho nedoporučujeme používat, je [tok uživatelského jména a hesla](https://aka.ms/msal-net-up) k dispozici ve veřejných klientských aplikacích. Tento tok je stále potřeba v některých scénářích, jako je DevOps.

Tento tok ale používá omezení pro vaše aplikace. Například aplikace používající tento tok se nemohou přihlásit uživatele, který potřebuje službu Multi-Factor Authentication nebo podmíněný přístup. Vaše aplikace také nemají výhodu jednotného přihlašování.

Ověřování s uživatelským jménem nebo heslem přechází na zásady moderního ověřování a poskytuje se jenom z původních důvodů.

Pokud chcete, aby byla mezipaměť tokenu trvalá, měli byste v desktopových aplikacích [přizpůsobit serializaci mezipaměti tokenů](https://aka.ms/msal-net-token-cache-serialization). Implementací [duální serializace mezipaměti tokenů](https://aka.ms/msal-net-dual-cache-serialization)můžete použít zpětně kompatibilní a dopředné mezipaměti tokenů kompatibilní s předchozími generace knihoven ověřování. Konkrétní knihovny zahrnují knihovnu ověřování Azure AD pro .NET (ADAL.NET) verze 3 a verze 4.

Další informace najdete v tématu [aplikace klasické pracovní plochy, která volá webová rozhraní API](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Mobilní aplikace, která volá webové rozhraní API jménem interaktivního uživatele

Podobně jako desktopová aplikace volá mobilní aplikace metody získání interaktivních tokenů třídy MSAL **PublicClientApplication** , aby získala token pro volání webového rozhraní API.

![Mobilní aplikace, která volá webové rozhraní API](media/scenarios/mobile-app.svg)

MSAL iOS a MSAL Android ve výchozím nastavení používají webový prohlížeč systému. Můžete je ale směrovat na místo toho vloženého webového zobrazení. Existují konkrétní zvláštnosti, které závisí na mobilní platformě: Univerzální platforma Windows (UWP), iOS nebo Android.

Některé scénáře, například ty, které zahrnují podmíněný přístup týkající se ID zařízení nebo registrace zařízení, vyžadují, aby byl na zařízení nainstalovaný [zprostředkovatel](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) . Příklady zprostředkovatelů jsou Microsoft Portál společnosti v Androidu a Microsoft Authenticator v Androidu a iOS. MSAL teď můžou spolupracovat se zprostředkovateli.

> [!NOTE]
> Vaše mobilní aplikace, která používá MSAL. iOS, MSAL. Pro Android nebo MSAL.NET na Xamarin můžou být na ni aplikovány zásady ochrany aplikací. Zásady mohou například zabránit uživateli v kopírování chráněného textu. Mobilní aplikace se [spravuje přes Intune](https://docs.microsoft.com/intune/app-sdk) a jako spravovaná aplikace je rozpoznaná službou Intune. [Intune App SDK](https://docs.microsoft.com/intune/app-sdk-get-started) je oddělená od knihoven MSAL a komunikuje s Azure AD sám o sobě.

Další informace najdete v tématu [mobilní aplikace, která volá webová rozhraní API](scenario-mobile-overview.md).

### <a name="a-protected-web-api"></a>Chráněné webové rozhraní API

Pomocí koncového bodu Microsoft Identity Platform můžete zabezpečit webové služby, jako je webové rozhraní API RESTful vaší aplikace. Chráněné webové rozhraní API se volá pomocí přístupového tokenu k zabezpečení dat rozhraní API a k ověřování příchozích požadavků. Volající webového rozhraní API připojí přístupový token v autorizační hlavičce požadavku HTTP.

Pokud chcete chránit rozhraní API ASP.NET nebo ASP.NET Core webového rozhraní API, musíte ověřit přístupový token. Pro toto ověření použijete middleware ASP.NET JWT. Ověřování provádí [rozšíření IdentityModel pro knihovnu .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) , nikoli MSAL.NET.

Další informace najdete v tématu [chráněné webové rozhraní API](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Webové rozhraní API, které jménem uživatele volá jiné webové rozhraní API

Aby vaše aplikace ASP.NET nebo ASP.NET Core chráněné webové rozhraní API zavolalo jiné webové rozhraní API jménem uživatele, musí získat token pro webové rozhraní API pro příjem dat. Provede to voláním metody [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) třídy **ConfidentialClientApplication** . Taková volání jsou také pojmenována jako volání služba-služba. Webová rozhraní API, která volají jiná webová rozhraní API, musí poskytovat vlastní serializaci mezipaměti.

  ![Webové rozhraní API, které volá jiné webové rozhraní API](media/scenarios/web-api.svg)

Další informace najdete v tématu [webové rozhraní API, které volá webová rozhraní API](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Aplikace démona volající webové rozhraní API v názvu démona

Aplikace, které mají dlouhotrvající procesy nebo které fungují bez interakce s uživatelem, potřebují také způsob, jak přistupovat k zabezpečeným webovým rozhraním API. Taková aplikace může ověřit a získat tokeny pomocí identity aplikace místo delegované identity uživatele. Aplikace prokáže svou identitu pomocí tajného klíče klienta nebo certifikátu.

Takové aplikace démona, které získávají token pro volání aplikace, můžete zapsat pomocí metod získání [přihlašovacích údajů klienta](https://aka.ms/msal-net-client-credentials) třídy MSAL **ConfidentialClientApplication** . Tyto metody vyžadují, aby volající aplikace zaregistrovala tajný klíč pomocí Azure AD. Aplikace potom tento tajný klíč sdílí s názvem démona. Příklady takových tajných klíčů zahrnují hesla aplikací, kontrolní výraz certifikátu nebo kontrolní výraz klienta.

![Aplikace démona volaná jinými aplikacemi a rozhraními API](media/scenarios/daemon-app.svg)

Další informace najdete v tématu [aplikace démona, která volá webová rozhraní API](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scénáře a podporované toky ověřování

Scénáře, které zahrnují získání tokenů, jsou také mapovány na toky ověřování OAuth 2,0, jak jsou popsány v [protokolech Microsoft Identity Platform](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Scénář</th> <th>Podrobný návod pro scénář – through</th> <th>Tok OAuth 2,0 a udělení</th> <th>Cílová skupina</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Jednostránková aplikace</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Nepřímo</a></td>
   <td>Pracovní nebo školní účty, osobní účty a Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Webová aplikace, která se přihlásí uživatelům</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorizační kód</a></td>
   <td>Pracovní nebo školní účty, osobní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Webová aplikace, která volá webová rozhraní API</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorizační kód</a></td>
   <td>Pracovní nebo školní účty, osobní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Aplikace klasické pracovní plochy, která volá webová rozhraní API</a></td>
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
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Kód zařízení</a></td>
   <td>Pracovní nebo školní účty</td>
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
   <td><a href=scenario-daemon-overview.md">Aplikace démona, která volá webová rozhraní API</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Přihlašovací údaje klienta</a></td>
   <td>Oprávnění jenom pro aplikace bez uživatele a používaná jenom v organizacích Azure AD</td>
 </tr>

  <tr>
   <td><a href=scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href=scenario-web-api-call-api-overview.md">Webové rozhraní API, které volá webová rozhraní API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Jménem</a></td>
   <td>Pracovní nebo školní účty a osobní účty</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scénáře a podporované platformy a jazyky

Knihovny Microsoft Authentication Library podporují více platforem:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- Nativní iOS
- macOS
- Nativní Android
- Java
- Python

K sestavování aplikací můžete použít také různé jazyky. Všimněte si, že některé typy aplikací nejsou k dispozici na všech platformách.

Ve sloupci Windows v následující tabulce se pokaždé, když je uvedeno rozhraní .NET Core, je také možné použít .NET Framework. Druhá je vynechána, aby nedocházelo k zbytečnému zaplnění tabulky.

|Scénář  | Windows | Linux | Počítač Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Jednostránková aplikace](scenario-spa-overview.md) <br/>[@no__t – 1Single – stránková aplikace](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md) <br/>[Aplikace @no__t 1Web, která přihlásí uživatele](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Webová aplikace, která volá webová rozhraní API](scenario-web-app-call-api-overview.md) <br/> <br/>[Aplikace @no__t 1Web, která volá webová rozhraní API](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Baňka + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Baňka + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Baňka + MSAL Python
| [Desktopová aplikace, které volá webová rozhraní API](scenario-desktop-overview.md) <br/> <br/>[aplikace ![Desktop, která volá webová rozhraní api](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) @no__t 2Device toku kódu @ no__t-3 | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> MSAL. objc |
| [Mobilní aplikace, která volá webová rozhraní API](scenario-mobile-overview.md) <br/> [aplikace @no__t 1Mobile, která volá webová rozhraní API](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![PODPORUJÍ](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/objektivní C nebo SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Svém
| [Aplikace démon](scenario-daemon-overview.md) <br/> [aplikace @no__t – 1Daemon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Webové rozhraní API, které volá webová rozhraní API](scenario-web-api-call-api-overview.md) <br/><br/> [rozhraní API @no__t 1Web, které volá webová rozhraní API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Viz také [knihovny podporované společností Microsoft podle operačního systému nebo jazyka](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [základech ověřování](authentication-scenarios.md) a [přístupových tokenech](access-tokens.md).
