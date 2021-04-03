---
title: Typy aplikací podporované nástrojem Azure AD B2C
titleSuffix: Azure AD B2C
description: Seznamte se s typy aplikací, které můžete používat s Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 07897823a3ba3b83e240e8e8dc005ea13b036fce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94952042"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Typy aplikací, které lze použít v Active Directory B2C
 
Azure Active Directory B2C (Azure AD B2C) podporuje ověřování pro celou řadu moderních architektur aplikací. Všechny jsou založeny na standardních oborových protokolech [OAuth 2.0](protocols-overview.md) nebo [OpenID Connect](protocols-overview.md). Tento článek popisuje typy aplikací, které můžete sestavit, nezávisle na jazyku nebo platformě, které dáváte přednost. Také pomáhá pochopit scénáře vysoké úrovně před tím, než začnete sestavovat aplikace.

Každá aplikace, která používá Azure AD B2C, musí být zaregistrovaná v [Azure AD B2C tenant](tutorial-create-tenant.md) pomocí [Azure Portal](https://portal.azure.com/). Proces registrace aplikace shromažďuje a přiřazuje hodnoty, jako například:

* **ID aplikace** , které jedinečně identifikuje vaši aplikaci.
* **Adresa URL odpovědi** , kterou lze použít k přímému směrování odpovědí zpět do vaší aplikace.

Každý požadavek odeslaný do Azure AD B2C Určuje **tok uživatele** (předdefinované zásady) nebo **vlastní zásady** , které řídí chování Azure AD B2C. Oba typy zásad umožňují vytvořit vysoce přizpůsobitelnou sadu uživatelských prostředí.

Interakce každé aplikace probíhá podle podobného vzoru:

1. Aplikace přesměruje uživatele na koncový bod v 2.0, aby bylo možné spustit [zásadu](user-flow-overview.md).
2. Uživatel vykoná zásadu podle její definice.
3. Aplikace obdrží token zabezpečení z koncového bodu verze 2.0.
4. Aplikace používá token zabezpečení pro přístup k chráněným informacím nebo chráněnému prostředku.
5. Server prostředků ověří token zabezpečení, aby ověřil, zda lze udělit přístup.
6. Aplikace token zabezpečení pravidelně aktualizuje.

Tyto kroky se mohou mírně lišit v závislosti na typu aplikace, kterou vytváříte.

## <a name="web-applications"></a>Webové aplikace

Pro webové aplikace (včetně .NET, PHP, Java, Ruby, Pythonu a Node.js), které jsou hostované na serveru a jsou dostupné prostřednictvím prohlížeče, Azure AD B2C podporuje [OpenID Connect](protocols-overview.md) pro všechna prostředí uživatele. V Azure AD B2C implementaci OpenID Connect inicializuje webová aplikace uživatelské prostředí tím, že vydává žádosti o ověření do služby Azure AD. Výsledkem požadavku je `id_token`. Tento token zabezpečení představuje identitu uživatele. Poskytuje také informace o uživateli ve formě deklarací identity:

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Přečtěte si další informace o typech tokenů a deklaracích dostupných pro aplikaci v [odkazu na token Azure AD B2C](tokens-overview.md).

V rámci webové aplikace má každé spuštění [zásady](user-flow-overview.md) tyto kroky vysoké úrovně:

1. Uživatel přejde k webové aplikaci.
2. Webová aplikace přesměruje uživatele na Azure AD B2C, což znamená, že se má zásada spustit.
3. Uživatel dokončí zásadu.
4. Azure AD B2C vrátí `id_token` do prohlížeče.
5. `id_token`Je zveřejněn do identifikátoru URI přesměrování.
6. `id_token`Je ověřen a je nastaven soubor cookie relace.
7. Uživateli se vrátí zabezpečená stránka.

Ověření `id_token` pomocí veřejného podpisového klíče přijatého z Azure AD je dostačující k ověření identity uživatele. Tento proces také nastaví soubor cookie relace, který se dá použít k identifikaci uživatele na dalších požadavcích na stránku.

Pokud chcete zobrazit tento scénář v akci, zkuste jednu z ukázek přihlašovacích kódů webové aplikace v [části Začínáme](overview.md).

Kromě usnadnění jednoduchého přihlašování může aplikace webového serveru také vyžadovat přístup k back-endové webové službě. V takovém případě může webová aplikace provádět mírně odlišný [tok OpenID Connect](openid-connect.md) a získat tokeny pomocí autorizačních kódů a aktualizovat tokeny. Tento scénář je znázorněn v následujícím [oddílu Webová rozhraní API](#web-apis).

## <a name="single-page-applications"></a>Jednostránkové aplikace
Mnohé moderní webové aplikace jsou sestavené jako jednostránkové aplikace na straně klienta ("jednostránkové"). Vývojáři si je zapisují pomocí JavaScriptu nebo architektury SPA, jako je například úhlová, Vue a reakce. Tyto aplikace běží ve webovém prohlížeči a mají různé charakteristiky ověřování než tradiční webové aplikace na straně serveru.

Azure AD B2C poskytuje **dvě** možnosti, jak povolit jednostránkové aplikace pro přihlašování uživatelů a získat tokeny pro přístup k back-endové službě nebo k webovým rozhraním API:

### <a name="authorization-code-flow-with-pkce"></a>Tok autorizačního kódu (s PKCE)
- [Tok autorizačního kódu OAuth 2,0 (s PKCE)](./authorization-code-flow.md). Tok autorizačního kódu umožňuje aplikaci výměnu autorizačního kódu pro tokeny **ID** , které reprezentují ověřeného uživatele a **přístupové** tokeny potřebné pro volání chráněných rozhraní API. Kromě toho vrací **aktualizační** tokeny, které poskytují dlouhodobý přístup k prostředkům jménem uživatelů bez nutnosti interakce s těmito uživateli. 

Toto je **doporučený** postup. Pokud máte s omezenými obnovovacími tokeny, pomůže vaše aplikace přizpůsobovat se na [moderní omezení ochrany osobních údajů souborů cookie v prohlížeči](../active-directory/develop/reference-third-party-cookies-spas.md), jako je

Pokud chcete využít tento tok, vaše aplikace může používat knihovnu ověřování, která ho podporuje, například [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

<!-- ![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg) -->
![Jednostránkové aplikace – ověřování](./media/tutorial-single-page-app/active-directory-oauth-code-spa.png)

### <a name="implicit-grant-flow"></a>Implicitní tok udělení
- [Implicitní tok OAuth 2,0](implicit-flow-single-page-application.md). Některá rozhraní, například [MSAL.js 1. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core), podporují pouze implicitní tok udělení. Tok implicitního udělení umožňuje aplikaci získat **ID** a **přístupové** tokeny. Na rozdíl od toku autorizačního kódu nevrátí tok implicitního udělení **obnovovací token**. 

Tento tok ověřování nezahrnuje scénáře aplikací, které používají rozhraní JavaScript pro různé platformy, jako jsou například elektronicky a reagují – nativní. Tyto scénáře vyžadují další možnosti pro interakci s nativními platformami.

## <a name="web-apis"></a>Webová rozhraní API

Pomocí Azure AD B2C můžete zabezpečit webové služby, jako je webové rozhraní API RESTful vaší aplikace. Webové rozhraní API může využívat OAuth 2.0 k zabezpečení dat ověřováním příchozích žádostí HTTP pomocí tokenů. Volající webového rozhraní API připojí token v hlavičce autorizace požadavku HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
``` 

Webové rozhraní API pak může pomocí tokenu ověřit identitu volajícího a extrahovat informace o volajícím z deklarací identity zakódovaných v tokenu. Další informace o typech tokenů a deklaracích identity přístupných aplikaci najdete v tématu [Odkaz tokenu Azure AD B2C](tokens-overview.md).

Webové rozhraní API může přijímat tokeny z mnoha typů klientů, včetně webových aplikací, desktopových a mobilních aplikací, aplikací s jednou stránkou, démonů na straně serveru a dalších webových rozhraní API. Tady je příklad kompletního toku webové aplikace, která volá webové rozhraní API:

1. Webová aplikace provede zásadu a uživatel dokončí činnost koncového uživatele.
2. Azure AD B2C vrátí (OpenID Connect) `id_token` a autorizační kód do prohlížeče.
3. Prohlížeč odešle `id_token` autorizační kód do identifikátoru URI pro přesměrování.
4. Webový server ověří `id_token` a nastaví soubor cookie relace.
5. Webový server si vyžádá Azure AD B2C pro `access_token` poskytnutím autorizačního kódu, ID klienta aplikace a přihlašovacích údajů klienta.
6. `access_token`A `refresh_token` se vrátí na webový server.
7. Webové rozhraní API se volá `access_token` v autorizační hlavičce.
8. Webové rozhraní API ověřuje token.
9. Zabezpečená data se vrátí do webové aplikace.

Pro další informace o kódech autorizace a obnovovacích tokenech a návod, jak získat tokeny, si přečtěte o [protokolu OAuth 2.0](authorization-code-flow.md).

Chcete-li zjistit, jak zabezpečit webové rozhraní API pomocí Azure AD B2C, podívejte se na kurzy o webovém rozhraní API v [oddílu Začínáme](overview.md).

## <a name="mobile-and-native-applications"></a>Mobilní a nativní aplikace

Aplikace, které jsou nainstalované na zařízeních, například mobilní a desktopové aplikace, často potřebují přístup k back-endové službě nebo k webovým rozhraním API jménem uživatele. Do nativních aplikací můžete přidat přizpůsobené prostředí pro správu identit a bezpečně volat back-endové služby pomocí Azure AD B2C a [toku autorizačního kódu OAuth 2,0](authorization-code-flow.md).

V tomto toku aplikace spustí [zásady](user-flow-overview.md) a přijme `authorization_code` službu Azure AD poté, co uživatel dokončí zásadu. `authorization_code`Představuje oprávnění aplikace pro volání back-endové služby jménem uživatele, který je aktuálně přihlášený. Aplikace pak může vyměnit na `authorization_code` pozadí pro `access_token` a `refresh_token` .  Aplikace může použít `access_token` k ověření pro back-endové webové rozhraní API v požadavcích http. Může také použít `refresh_token` k získání nového `access_token` po vypršení platnosti toho starého.

## <a name="current-limitations"></a>Aktuální omezení

### <a name="unsupported-application-types"></a>Nepodporované typy aplikací

#### <a name="daemonsserver-side-applications"></a>Démoni/aplikace na straně serveru

Aplikace, které obsahují dlouhotrvající procesy nebo které fungují bez přítomnosti uživatele, potřebují také způsob, jak přistupovat k zabezpečeným prostředkům, jako jsou webová rozhraní API. Tyto aplikace můžou ověřovat a získávat tokeny pomocí identity aplikace (místo delegované identity uživatele) a pomocí toku přihlašovacích údajů klienta OAuth 2,0. Tok přihlašovacích údajů klienta není stejný jako tok, který by měl být použit pro ověřování mezi servery a tok za vás.

I když služba Azure AD B2C Authentication Service aktuálně nepodporuje tok udělení přihlašovacích údajů klienta OAuth 2,0, můžete nastavit tok přihlašovacích údajů klienta pomocí Azure AD a koncového bodu Microsoft Identity Platform/token pro aplikaci ve vašem tenantovi Azure AD B2C. Tenant Azure AD B2C sdílí některé funkce s klienty Azure AD Enterprise.

Pokud chcete nastavit tok přihlašovacích údajů klienta, přečtěte si téma [Azure Active Directory v 2.0 a tok přihlašovacích údajů klienta OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Výsledkem úspěšného ověření je příjem tokenu, který se naformátoval, aby ho služba Azure AD mohla používat, jak je popsáno v tématu [Reference k tokenům Azure AD](../active-directory/develop/id-tokens.md).

Pokyny k registraci aplikace pro správu najdete v tématu [správa Azure AD B2C s](microsoft-graph-get-started.md)využitím Microsoft Graph.

#### <a name="web-api-chains-on-behalf-of-flow"></a>Řetězení webových rozhraní API (tok on-behalf-of)

Mnoho architektur zahrnuje webové rozhraní API, které potřebuje volat podřízené webové rozhraní API, přičemž obě jsou zabezpečené pomocí Azure AD B2C. Tento scénář je běžný u nativních klientů, které mají back-end webového rozhraní API, a volá online službu Microsoftu, jako je Microsoft Graph API.

Tento scénář zřetězených webových rozhraní API může být podporován pomocí udělení přihlašovacích údajů nosiče OAuth 2.0 JWT, označovaného také jako tok on-behalf-of.  Nicméně tok on-behalf-of není v současné době v Azure AD B2C implementován.

### <a name="faulted-apps"></a>Chybné aplikace

Neupravujte Azure AD B2C aplikace těmito způsoby:

- Na jiných portálech pro správu aplikací, jako je [Portál pro registraci aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
- Použití Graph API nebo PowerShellu.

Pokud aplikaci Azure AD B2C upravíte mimo Azure Portal, stane se chybnou aplikací a již nebude použitelná s Azure AD B2C. Odstraňte aplikaci a znovu ji vytvořte.

Pokud chcete aplikaci odstranit, použijte portál pro [registraci aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) a odstraňte aplikaci. Aby byla aplikace viditelná, musíte být vlastníkem aplikace (nestačí být pouze správcem tenanta).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o předdefinovaných zásadách poskytovaných [uživatelskými toky v Azure Active Directory B2C](user-flow-overview.md).