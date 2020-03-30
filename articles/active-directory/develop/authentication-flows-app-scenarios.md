---
title: Toky ověřování platformy Microsoft pro identity & scénářích aplikací | Azure
description: Informace o scénářích aplikací pro platformu identit Microsoftu, včetně ověřování identit, získávání tokenů a volání chráněných rozhraní API.
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
ms.openlocfilehash: e73da5e7b440a216841fffd65ca2e1b95de7a609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480925"
---
# <a name="authentication-flows-and-application-scenarios"></a>Toky ověřování a scénáře aplikací

Koncový bod platformy identit microsoftu (v2.0) podporuje ověřování pro různé druhy moderních aplikačních architektur. Všechny architektury jsou založeny na standardních protokolech [OAuth 2.0 a OpenID Connect](active-directory-v2-protocols.md).  Pomocí [knihoven ověřování platformy identit Microsoftu](reference-v2-libraries.md)aplikace ověřují identity a získávají tokeny pro přístup k chráněným rozhraním API.

Tento článek popisuje toky ověřování a scénáře aplikací, ve kterých se používají:

- [Scénáře aplikací a podporované toky ověřování](#scenarios-and-supported-authentication-flows).
- [Scénáře aplikací a podporované platformy a jazyky](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Kategorie aplikací

Tokeny lze získat z několika typů aplikací, včetně:

- Webové aplikace
- Mobilní aplikace
- Aplikace klasické pracovní plochy
- Webová rozhraní API

Tokeny lze také získat z aplikací spuštěných na zařízeních, která nemají prohlížeč nebo jsou spuštěny na IoT.

Aplikace lze kategorizovat jako v následujícím seznamu:

- [Chráněné prostředky vs klientské aplikace](#protected-resources-vs-client-applications): Některé scénáře jsou o ochraně prostředků, jako jsou webové aplikace nebo webová api. Další scénáře jsou o získání tokenu zabezpečení pro volání chráněnéwebové rozhraní API.
- [S uživateli nebo bez uživatelů](#with-users-or-without-users): Některé scénáře zahrnují přihlášeného uživatele, ale jiné, jako jsou scénáře daemonu, nezahrnují uživatele.
- [Jednostránkové, veřejné klientské a důvěrné klientské aplikace](#single-page-public-client-and-confidential-client-applications): Tyto typy jsou tři velké kategorie aplikací. Každý se používá s různými knihovnami a objekty.
- [Cílová skupina pro přihlášení](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): Dostupné toky ověřování se liší v závislosti na přihlašovací cílové skupině. Některé toky jsou k dispozici pouze pro pracovní nebo školní účty. A některé jsou k dispozici jak pro pracovní nebo školní účty, tak pro osobní účty Microsoft. Povolená cílová skupina závisí na tocích ověřování.
- [Podporované toky OAuth 2.0](#scenarios-and-supported-authentication-flows): Toky ověřování se používají k implementaci scénářů aplikace, které požadují tokeny. Neexistuje mapování 1:1 mezi scénáři aplikace a toky ověřování.
- [Podporované platformy](#scenarios-and-supported-platforms-and-languages): Ne všechny scénáře aplikací jsou k dispozici pro každou platformu.

### <a name="protected-resources-vs-client-applications"></a>Chráněné prostředky vs. klientské aplikace

Scénáře ověřování zahrnují dvě aktivity:

- **Získání tokenů zabezpečení pro chráněné webové rozhraní API**: Doporučujeme používat [klientské knihovny podporované společností Microsoft](reference-v2-libraries.md#microsoft-supported-client-libraries) k získání tokenů, zejména řady Microsoft Authentication Library (MSAL).
- **Ochrana webového rozhraní API nebo webové aplikace**: Jednou z výzev ochrany webového rozhraní API nebo prostředku webové aplikace je ověření tokenu zabezpečení. Na některých platformách nabízí společnost Microsoft [middleware knihovny](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>S uživateli nebo bez uživatelů

Většina scénářů ověřování získává tokeny jménem přihlášených uživatelů.

![Scénáře s uživateli](media/scenarios/scenarios-with-users.svg)

Existují však také scénáře aplikace daemon, ve kterých aplikace získat tokeny jménem sebe s žádným uživatelem.

![Scénáře s aplikacemi pro daemon](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Jednostránkové, veřejné klientské a důvěrné klientské aplikace

Tokeny zabezpečení lze získat z více typů aplikací. Tyto aplikace mají tendenci být rozděleny do tří kategorií:

- **Jednostránkové aplikace**: Tyto aplikace, známé také jako spa, jsou webové aplikace, ve kterých jsou tokeny získávány z JavaScriptu nebo typescriptové aplikace spuštěné v prohlížeči. Mnoho moderních aplikací má jednostránkový front-end aplikace, který je primárně napsán v JavaScriptu. Aplikace často používá rozhraní jako Angular, React nebo Vue. Soubor MSAL.js je jedinou ověřovací knihovnou společnosti Microsoft, která podporuje jednostránkové aplikace.

- **Veřejné klientské aplikace**: Tyto aplikace vždy přihlašují uživatele:
  - Desktopové aplikace volající webová rozhraní API jménem přihlášeného uživatele
  - Mobilní aplikace
  - Aplikace spuštěné na zařízeních, která nemají prohlížeč, jako ty, které běží na iOT

  Tyto aplikace jsou reprezentovány třídou MSAL [PublicClientApplication.](/dotnet/api/microsoft.identity.client.publicclientapplication) Další informace naleznete [v tématu Veřejné klientské a důvěrné klientské aplikace](msal-client-applications.md).

- **Důvěrné klientské aplikace**:
  - Webové aplikace volající webové rozhraní API
  - Webová rozhraní API volající webové rozhraní API
  - Aplikace Daemon, i když jsou implementovány jako konzolová služba, jako je linuxový daemon nebo služba Windows

  Tyto typy aplikací používají třídu [ConfidentialClientApplication.](/dotnet/api/microsoft.identity.client.confidentialclientapplication) Další informace naleznete [v tématu Veřejné klientské a důvěrné klientské aplikace](msal-client-applications.md).

## <a name="application-scenarios"></a>Scénáře aplikací

Koncový bod platformy identit Microsoftpodporuje ověřování pro různé architektury aplikací:

- Jednostránkové aplikace
- Webové aplikace
- Webová rozhraní API
- Mobilní aplikace
- Nativní aplikace
- Aplikace démonů
- Aplikace na straně serveru

Aplikace používají různé toky ověřování k přihlášení uživatelů a získání tokenů pro volání chráněných souborů API.

### <a name="a-single-page-application"></a>Jednostránková aplikace

Mnoho moderních webových aplikací je vytvořeno jako jednostránkové aplikace na straně klienta. Tyto aplikace používají JavaScript nebo jednostránkový aplikační rámec jako Angular, Vue.js a React.js. Tyto aplikace běží ve webovém prohlížeči.

Jejich charakteristiky ověřování se liší od tradičních webových aplikací na straně serveru. Pomocí platformy microsoft identity mohou jednostránkové aplikace přihlašovat uživatele a získat tokeny pro přístup k back-endovým službám nebo webovým rozhraním API.

![Jednostránková aplikace](media/scenarios/spa-app.svg)

Další informace naleznete [v tématu Jednostránkové aplikace](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>Webová aplikace, která se přihlašuje k uživateli

![Webová aplikace, která se připojedí k uživateli](media/scenarios/scenario-webapp-signs-in-users.svg)

Ochrana webové aplikace, která se přihlašuje k uživateli:

- Pokud vyvíjíte v rozhraní .NET, používáte ASP.NET nebo ASP.NET Core s middleware ASP.NET Open ID Connect. Ochrana prostředku zahrnuje ověření tokenu zabezpečení, který se provádí [rozšíření identitymodel pro knihovnu .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) a ne Knihovny MSAL.

- Pokud se vyvíjíte v souboru Node.js, použijete soubor Passport.js.

Další informace naleznete [v tématu Webová aplikace, která se připojuje k uživatelům](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Webová aplikace, která se přihlásí k uživateli a zavolá webové rozhraní API jménem uživatele

![Webová aplikace volající na webová api](media/scenarios/web-app.svg)

Chcete-li volat webové rozhraní API z webové aplikace jménem uživatele, použijte třídu MSAL **ConfidentialClientApplication.** Použijete tok autorizačního kódu a uložte získané tokeny do mezipaměti tokenů. V případě potřeby MSAL aktualizuje tokeny a řadič tiše získává tokeny z mezipaměti.

Další informace naleznete [v tématu Webová aplikace volající webová api](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Desktopová aplikace volající webové rozhraní API jménem přihlášeného uživatele

Pro desktopové aplikace volat webové rozhraní API, které se přihlásí k uživatelům, použijte interaktivní metody získávání tokenů třídy MSAL **PublicClientApplication.** Pomocí těchto interaktivních metod můžete řídit prostředí uživatelského prostředí pro přihlášení. MSAL používá webový prohlížeč pro tuto interakci.

![Desktopová aplikace volající webové rozhraní API](media/scenarios/desktop-app.svg)

Existuje další možnost pro aplikace hostované systémem Windows v počítačích spojených s doménou Windows nebo službou Azure Active Directory (Azure AD). Tyto aplikace mohou bezobslužně získat token pomocí [integrovaného ověřování systému Windows](https://aka.ms/msal-net-iwa).

Aplikace spuštěné na zařízení bez prohlížeče mohou stále volat rozhraní API jménem uživatele. Chcete-li ověřit, musí se uživatel přihlásit na jiném zařízení, které má webový prohlížeč. Tento scénář vyžaduje použití [toku kódu zařízení](https://aka.ms/msal-net-device-code-flow).

![Tok kódu zařízení](media/scenarios/device-code-flow-app.svg)

I když nedoporučujeme používat, [tok uživatelského jména /hesla](https://aka.ms/msal-net-up) je k dispozici ve veřejných klientských aplikacích. Tento tok je stále potřeba v některých scénářích, jako je DevOps.

Ale pomocí tohoto toku omezuje vaše aplikace. Aplikace se například nemohou přihlásit k uživateli, který potřebuje používat vícefaktorové ověřování nebo podmíněný přístup. Vaše aplikace také nemají prospěch z jednotného přihlášení.

Ověřování s tokem uživatelského jména/hesla je v rozporu se zásadami moderního ověřování a je poskytováno pouze z důvodů starší verze.

Pokud chcete, aby mezipaměť tokenů v aplikacích klasické pracovní plochy přetrvávala, přizpůsobte [serializaci mezipaměti tokenů](https://aka.ms/msal-net-token-cache-serialization). Implementací [serializace mezipaměti dvou tokenů](https://aka.ms/msal-net-dual-cache-serialization)můžete použít zpětně kompatibilní mezipaměti tokenů kompatibilní s dopředem. Tyto tokeny podporují předchozí generace ověřovacích knihoven. Mezi konkrétní knihovny patří Azure AD Authentication Library pro .NET (ADAL.NET) verze 3 a verze 4.

Další informace naleznete v [tématu Desktopová aplikace, která volá webová api](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Mobilní aplikace volající webové rozhraní API jménem interaktivního uživatele

Podobně jako aplikace pro stolní počítače, mobilní aplikace volá interaktivní metody získávání tokenů třídy MSAL **PublicClientApplication,** aby získala token pro volání webového rozhraní API.

![Mobilní aplikace volající webové rozhraní API](media/scenarios/mobile-app.svg)

MSAL iOS a MSAL Android ve výchozím nastavení používají systémový webový prohlížeč. Můžete je však nasměrovat k použití vloženého webového zobrazení. Existují specifika, která závisí na mobilní platformě: Univerzální platforma Windows (UPW), iOS nebo Android.

Některé scénáře, například ty, které zahrnují podmíněný přístup související s ID zařízení nebo registraci zařízení, vyžadují, aby byl v zařízení nainstalován zprostředkovatel. Příklady makléřů jsou Portál společnosti Microsoft v systému Android a Microsoft Authenticator v systémech Android a iOS. MSAL nyní může komunikovat s makléři. Další informace naleznete v [tématu Využití brokerů v systémech Android a iOS](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Další informace najdete v [tématu Mobilní aplikace, která volá webová api](scenario-mobile-overview.md).

> [!NOTE]
> Vaše mobilní aplikace, která používá MSAL.iOS, MSAL. Android nebo MSAL.NET na Xamarinu můžou mít na něj použít zásady ochrany aplikací. Zásady mohou například zabránit uživateli v kopírování chráněného textu. Mobilní aplikace je spravovaná intune a intune ji uznává jako spravovanou aplikaci. Další informace najdete v tématu [Microsoft Intune App SDK overview](https://docs.microsoft.com/intune/app-sdk).
>
> Sada [Intune App SDK](https://docs.microsoft.com/intune/app-sdk-get-started) je oddělená od knihoven MSAL a sama spolupracuje s Azure AD.

### <a name="a-protected-web-api"></a>Chráněné webové rozhraní API

Koncový bod platformy identit Microsoftu můžete použít k zabezpečení webových služeb, jako je webové rozhraní RESTful vaší aplikace. Chráněné webové rozhraní API se nazývá pomocí přístupového tokenu. Token zabezpečuje data rozhraní API a ověřuje příchozí požadavky. Volající webového rozhraní API připojí přístupový token v hlavičce autorizace požadavku HTTP.

Pokud chcete chránit ASP.NET nebo ASP.NET základní webové rozhraní API, je třeba ověřit přístupový token. Pro toto ověření použijete ASP.NET middleware JWT. Ověření se provádí [rozšíření identitymodel pro knihovnu .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) a nikoli MSAL.NET.

Další informace naleznete [v tématu Protected web API](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Webové rozhraní API volající jiné webové rozhraní API jménem uživatele

Aby vaše ASP.NET nebo ASP.NET webové rozhraní API chráněné mandatorním prostředím Core pro volání jiného webového rozhraní API jménem uživatele, musí vaše aplikace získat token pro webové rozhraní API pro příjem dat. Chcete-li získat token, vaše aplikace volá **ConfidentialClientApplication** třídy [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) metoda. Tato volání jsou také *pojmenována volání služby služby.* Webová api, která volají jiná webová api, musí poskytovat vlastní serializaci mezipaměti.

  ![Webové rozhraní API volající jiné webové rozhraní API](media/scenarios/web-api.svg)

Další informace naleznete v [tématu Web API, které volá webová rozhraní API](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Aplikace pro daemon, která volá webové rozhraní API v názvu daemonu

Aplikace, které mají dlouhotrvající procesy nebo fungují bez interakce s uživatelem, také potřebují způsob, jak získat přístup k zabezpečeným webovým rozhraním API. Taková aplikace může ověřit a získat tokeny pomocí identity aplikace. Aplikace prokáže svou identitu pomocí tajného klíče klienta nebo certifikátu.

Můžete napsat takové daemon aplikace, které získají token pro volající aplikace pomocí metody získávání [pověření klienta třídy](https://aka.ms/msal-net-client-credentials) MSAL **ConfidentialClientApplication.** Tyto metody vyžadují, aby volající aplikace zaregistrovala tajný klíč s Azure AD. Aplikace pak sdílí tajemství s volaným daemonem. Příklady těchto tajných kódů patří hesla aplikace, kontrolní výraz certifikátu nebo kontrolní výraz klienta.

![Aplikace pro daemon volaná jinými aplikacemi a api](media/scenarios/daemon-app.svg)

Další informace naleznete v [tématu Daemon aplikace, která volá webová api](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scénáře a podporované toky ověřování

Scénáře, které zahrnují získání tokenů také mapovat na oauth 2.0 toky ověřování. Další informace naleznete [v protokolech OAuth 2.0 a OpenID Connect na platformě microsoft identity .](active-directory-v2-protocols.md)

<table>
 <thead>
  <tr><th>Scénář</th> <th>Podrobný návod na scénář</th> <th>OAuth 2.0 tok a grant</th> <th>Cílová skupina</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Jednostránková aplikace</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implicitní</a></td>
   <td>Pracovní nebo školní účty, osobní účty a Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Webová aplikace, která se připisuje k uživatelům</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorizační kód</a></td>
   <td>Pracovní nebo školní účty, osobní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Webová aplikace, která volá webová api</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorizační kód</a></td>
   <td>Pracovní nebo školní účty, osobní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Desktopová aplikace, která volá webová api</a></td>
   <td>Interaktivní pomocí <a href="v2-oauth2-auth-code-flow.md">autorizačního kódu</a> s PKCE</td>
   <td>Pracovní nebo školní účty, osobní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td>Integrované windowsové auth</td>
   <td>Pracovní nebo školní účty</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Heslo vlastníka prostředku</a></td>
   <td>Pracovní nebo školní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Kód zařízení</a></td>
   <td>Pracovní nebo školní účty</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Mobilní aplikace, která volá webová api</a></td>
   <td>Interaktivní pomocí <a href="v2-oauth2-auth-code-flow.md">autorizačního kódu</a> s PKCE</td>
   <td>Pracovní nebo školní účty, osobní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Heslo vlastníka prostředku</a></td>
   <td>Pracovní nebo školní účty a Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Aplikace pro daemon, která volá webová api</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Přihlašovací údaje klienta</a></td>
   <td>Oprávnění pouze pro aplikace bez uživatele a používaná jenom v organizacích Azure AD</td>
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

Knihovny Microsoft Authentication podporují více platforem:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UpWP
- Xamarin.iOS
- Xamarin.Android
- Nativní iOS
- macOS
- Nativní Android
- Java
- Python

K vytváření aplikací můžete také použít různé jazyky.

> [!NOTE]
> Některé typy aplikací nejsou k dispozici na všech platformách.

Ve sloupci Windows v následující tabulce pokaždé, když je uvedeno .NET Core, rozhraní .NET Framework je také možné. Ten je vynechán, aby se zabránilo zahlcení tabulky.

|Scénář  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Jednostránková aplikace](scenario-spa-overview.md) <br/>[![Jednostránková aplikace](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [WebOvá aplikace, která se připisuje k uživatelům](scenario-web-app-sign-user-overview.md) <br/>[![WebOvá aplikace, která připisuje uživatele](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Webová aplikace, která volá webová api](scenario-web-app-call-api-overview.md) <br/> <br/>[![Webová aplikace, která volá webová api](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET jádro + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png) <br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Baňka + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET jádro + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Baňka + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET jádro + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Baňka + MSAL Python
| [Desktopová aplikace, které volá webová rozhraní API](scenario-desktop-overview.md) <br/> <br/>Desktopová aplikace, která volá tok kódu zařízení webových rozhraní API [ ![](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS / Cíl C nebo rychlý](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Mobilní aplikace, která volá webová rozhraní API](scenario-mobile-overview.md) <br/> [![Mobilní aplikace, která volá webová rozhraní API](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Cíl C nebo rychlý](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Aplikace démona](scenario-daemon-overview.md) <br/> [![Aplikace démona](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Webové rozhraní API, které volá webová rozhraní API](scenario-web-api-call-api-overview.md) <br/><br/> [![Webové rozhraní API, které volá webová rozhraní API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET jádro + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET jádro + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET jádro + MSAL.NET ![MSAL v Javě](media/sample-v2-code/small_logo_java.png)<br/>MSAL v Javě<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Další informace naleznete v [knihovnách podporovaných společností Microsoft podle operačního systému / jazyka](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [základech ověřování](authentication-scenarios.md) a [přístupových tokenech platformy microsoftu pro identity](access-tokens.md).
* Přečtěte si další informace o [zabezpečení přístupu k aplikacím IoT](/azure/architecture/example-scenario/iot-aad/iot-aad).
