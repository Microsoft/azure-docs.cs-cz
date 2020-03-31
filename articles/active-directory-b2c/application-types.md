---
title: Typy aplikací podporované Azure AD B2C
titleSuffix: Azure AD B2C
description: Přečtěte si o typech aplikací, které můžete používat ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e8328db12bde531c2e27936c09247611ff1a3583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190139"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Typy aplikací, které lze použít ve službě Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) podporuje ověřování pro celou řadu moderních architektur aplikací. Všechny jsou založeny na standardních oborových protokolech [OAuth 2.0](protocols-overview.md) nebo [OpenID Connect](protocols-overview.md). Tento článek popisuje typy aplikací, které můžete vytvořit, nezávisle na jazyku nebo platformě, kterou upřednostňujete. Také pomáhá pochopit scénáře vysoké úrovně před tím, než začnete sestavovat aplikace.

Každá aplikace, která používá Azure AD B2C, musí být registrovaná ve vašem [tenantovi Azure AD B2C](tutorial-create-tenant.md) pomocí [portálu Azure.](https://portal.azure.com/) Proces registrace žádosti shromažďuje a přiřazuje hodnoty, například:

* **ID aplikace,** které jednoznačně identifikuje vaši aplikaci.
* Adresa **URL odpovědi,** kterou lze použít k přímému vrácení odpovědí zpět do aplikace.

Každý požadavek, který se odesílá do Azure AD B2C určuje **tok uživatele** (předdefinované zásady) nebo **vlastní zásady,** které řídí chování Azure AD B2C. Oba typy zásad umožňují vytvořit vysoce přizpůsobitelnou sadu uživatelských prostředí.

Interakce každé aplikace probíhá podle podobného vzoru:

1. Aplikace přesměruje uživatele do koncového bodu v2.0 k provedení [zásady](user-flow-overview.md).
2. Uživatel vykoná zásadu podle její definice.
3. Aplikace obdrží token zabezpečení z koncového bodu v2.0.
4. Aplikace používá token zabezpečení pro přístup k chráněným informacím nebo chráněnému prostředku.
5. Server prostředků ověří token zabezpečení, aby ověřil, zda lze udělit přístup.
6. Aplikace token zabezpečení pravidelně aktualizuje.

Tyto kroky se mohou mírně lišit v závislosti na typu aplikace, kterou vytváříte.

## <a name="web-applications"></a>Webové aplikace

Pro webové aplikace (včetně .NET, PHP, Java, Ruby, Python a Node.js), které jsou hostované na serveru a přístupné prostřednictvím prohlížeče, azure AD B2C podporuje [OpenID Connect](protocols-overview.md) pro všechna uživatelská prostředí. V implementaci Azure AD B2C OpenID Connect, vaše webová aplikace iniciuje uživatelské prostředí vydáváním žádostí o ověření do Azure AD. Výsledkem požadavku je `id_token`. Tento token zabezpečení představuje identitu uživatele. Poskytuje také informace o uživateli ve formě deklarací identity:

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

Další informace o typech tokenů a deklarací, které jsou k dispozici pro aplikaci v [odkazu tokenu Azure AD B2C](tokens-overview.md).

Ve webové aplikaci každé spuštění [zásady](user-flow-overview.md) provede tyto kroky na vysoké úrovni:

1. Uživatel přejde do webové aplikace.
2. Webová aplikace přesměruje uživatele na Azure AD B2C označující zásady ke spuštění.
3. Uživatel dokončí zásady.
4. Azure AD B2C `id_token` vrátí do prohlížeče.
5. Je `id_token` zaúčtován do identifikátoru URI přesměrování.
6. Je `id_token` ověřena a soubor cookie relace je nastaven.
7. Uživateli je vrácena zabezpečená stránka.

Ověření `id_token` pomocí veřejného podpisového klíče přijatého z Azure AD je dostačující k ověření identity uživatele. Tento proces také nastaví soubor cookie relace, který lze použít k identifikaci uživatele na následujících požadavcích na stránku.

Chcete-li zobrazit tento scénář v akci, zkuste jeden z ukázky kódu přihlášení webové aplikace v naší [části Začínáme](overview.md).

Kromě usnadnění jednoduchého přihlášení může být nutné, aby aplikace webového serveru měla také přístup k webové službě back-end. V tomto případě může webová aplikace provádět mírně odlišný [tok OpenID Connect](openid-connect.md) a získávat tokeny pomocí autorizačních kódů a aktualizačních tokenů. Tento scénář je znázorněn v následujícím [oddílu Webová rozhraní API](#web-apis).

## <a name="web-apis"></a>Webová rozhraní API

Azure AD B2C můžete použít k zabezpečení webových služeb, jako je vaše aplikace RESTful webové rozhraní API. Webové rozhraní API může využívat OAuth 2.0 k zabezpečení dat ověřováním příchozích žádostí HTTP pomocí tokenů. Volající webového rozhraní API připojí token v hlavičce autorizace požadavku HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webové rozhraní API pak může pomocí tokenu ověřit identitu volajícího a extrahovat informace o volajícím z deklarací identity zakódovaných v tokenu. Další informace o typech tokenů a deklaracích identity přístupných aplikaci najdete v tématu [Odkaz tokenu Azure AD B2C](tokens-overview.md).

Webové rozhraní API může přijímat tokeny od mnoha typů klientů, včetně webových aplikací, desktopových a mobilních aplikací, jednostránkových aplikací, daemonů na straně serveru a dalších webových rozhraní API. Tady je příklad kompletního toku pro webovou aplikaci, která volá webové rozhraní API:

1. Webová aplikace spustí zásadu a uživatel dokončí uživatelské prostředí.
2. Azure AD B2C vrátí (OpenID Connect) `id_token` a autorizační kód do prohlížeče.
3. Prohlížeč zaúčtuje `id_token` a autorizační kód na identifikátor URI přesměrování.
4. Webový server ověří `id_token` soubor cookie relace a nastaví soubor cookie relace.
5. Webový server požádá Azure AD B2C o `access_token` poskytnutí autorizačního kódu, ID klienta aplikace a pověření klienta.
6. A `access_token` `refresh_token` jsou vráceny na webový server.
7. Webové rozhraní API je `access_token` volána s hlavičkou autorizace.
8. Webové rozhraní API ověří token.
9. Zabezpečená data jsou vrácena do webové aplikace.

Pro další informace o kódech autorizace a obnovovacích tokenech a návod, jak získat tokeny, si přečtěte o [protokolu OAuth 2.0](authorization-code-flow.md).

Chcete-li zjistit, jak zabezpečit webové rozhraní API pomocí Azure AD B2C, podívejte se na kurzy o webovém rozhraní API v [oddílu Začínáme](overview.md).

## <a name="mobile-and-native-applications"></a>Mobilní a nativní aplikace

Aplikace, které jsou nainstalovány na zařízeních, jako jsou mobilní a desktopové aplikace, často potřebují přístup k back-endovým službám nebo webovým sítím API jménem uživatelů. Můžete přidat vlastní prostředí správy identit do nativních aplikací a bezpečně volat back-end ové služby pomocí Azure AD B2C a [toku autorizačního kódu OAuth 2.0](authorization-code-flow.md).

V tomto toku aplikace spustí [zásady](user-flow-overview.md) a obdrží `authorization_code` z Azure AD poté, co uživatel dokončí zásady. Představuje `authorization_code` oprávnění aplikace k volání back-endových služeb jménem uživatele, který je aktuálně přihlášen. Aplikace pak můžete `authorization_code` vyměnit na pozadí `access_token` pro `refresh_token`a .  Aplikace můžete použít `access_token` k ověření back-end webové rozhraní API v požadavcích HTTP. Může také použít `refresh_token` k získání nového `access_token` po vypršení platnosti toho starého.

## <a name="current-limitations"></a>Aktuální omezení

### <a name="unsupported-application-types"></a>Nepodporované typy aplikací

#### <a name="daemonsserver-side-applications"></a>Daemons/server-side aplikace

Aplikace, které obsahují dlouhotrvající procesy nebo které pracují bez přítomnosti uživatele, také potřebují způsob přístupu k zabezpečeným prostředkům, jako jsou webová rozhraní API. Tyto aplikace můžete ověřit a získat tokeny pomocí identity aplikace (spíše než delegované identity uživatele) a pomocí toku pověření klienta OAuth 2.0. Tok pověření klienta není stejný jako tok jménem a tok jménem by neměl být používán pro ověřování mezi servery.

I když tok pověření klienta není aktuálně podporován Azure AD B2C, můžete nastavit tok přihlašovacích údajů klienta pomocí Azure AD. Tenant Azure AD B2C sdílí některé funkce s podnikovými tenanty Azure AD.  Tok pověření klienta je podporovaný pomocí funkce Azure AD klienta Azure AD B2C.

Informace o nastavení toku pověření klienta najdete [v tématu Azure Active Directory v2.0 a Tok pověření klienta OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). Úspěšné ověření má za následek přijetí tokenu formátovaného tak, aby ho mohl používat Azure AD, jak je popsáno v [odkazu na token Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Řetězení webových rozhraní API (tok on-behalf-of)

Mnoho architektur zahrnuje webové rozhraní API, které potřebuje volat podřízené webové rozhraní API, přičemž obě jsou zabezpečené pomocí Azure AD B2C. Tento scénář je běžný u nativních klientů, kteří mají back-end webového rozhraní API a volá online službu společnosti Microsoft, například rozhraní Microsoft Graph API.

Tento scénář zřetězených webových rozhraní API může být podporován pomocí udělení přihlašovacích údajů nosiče OAuth 2.0 JWT, označovaného také jako tok on-behalf-of.  Nicméně tok on-behalf-of není v současné době v Azure AD B2C implementován.

### <a name="faulted-apps"></a>Chybné aplikace

Neupravujte aplikace Azure AD B2C těmito způsoby:

- Na jiných portálech pro správu aplikací, jako je [portál registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
- Použití rozhraní GRAPH API nebo PowerShellu.

Pokud upravíte aplikaci Azure AD B2C mimo portál Azure, stane se chybnou aplikací a už se nepoužívá s Azure AD B2C. Odstraňte aplikaci a vytvořte ji znovu.

Chcete-li aplikaci odstranit, přejděte na [portál registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) a aplikaci odstraňte. Aby byla aplikace viditelná, musíte být vlastníkem aplikace (nestačí být pouze správcem tenanta).

## <a name="next-steps"></a>Další kroky

Další informace o integrovaných zásadách poskytovaných [uživatelskými toky ve službě Azure Active Directory B2C](user-flow-overview.md).
