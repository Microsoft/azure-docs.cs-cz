---
title: Typy aplikací, které lze použít v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o typech aplikací, které můžete použít v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7671a0a99e12463fcce5ff33fbcba7e8677dde05
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006190"
---
# <a name="applications-types-that-can-be-used-in-active-directory-b2c"></a>Typy aplikací, které lze použít v Active Directory B2C

Azure Active Directory (Azure AD) B2C podporuje ověřování pro celou řadu architektur moderních aplikací. Všechny jsou založeny na standardních oborových protokolech [OAuth 2.0](active-directory-b2c-reference-protocols.md) nebo [OpenID Connect](active-directory-b2c-reference-protocols.md). Tento dokument popisuje typy aplikací, které můžete sestavit, nezávisle na jazyk nebo platformu dáváte přednost. Také pomáhá pochopit scénáře vysoké úrovně, než začnete sestavovat aplikace.

Každá aplikace, která používá Azure AD B2C musí být zaregistrovaný ve vaší [tenanta Azure AD B2C](active-directory-b2c-get-started.md) pomocí [webu Azure Portal](https://portal.azure.com/). Proces registrace aplikace shromáždí a přiřadí hodnoty, jako například:

* **ID aplikace** , který jednoznačně identifikuje vaši aplikaci.
* A **adresy URL odpovědi** , který lze použít k cílení odpovědí zpět do vaší aplikace.

Každý požadavek zaslaný do Azure AD B2C určuje **zásadu**. Zásady řídí chování Azure AD. Pomocí těchto koncových bodů můžete vytvořit vysoce přizpůsobitelnou sadu činností koncového uživatele. Mezi běžné zásady patří zásady registrace, přihlášení a úpravy profilu. Pokud nejste obeznámeni se zásadami, měli byste si před pokračováním přečíst o [rozšiřitelném rozhraní zásad](active-directory-b2c-reference-policies.md) Azure AD B2C.

Interakce každé aplikace probíhá podle podobného vzoru:

1. Aplikace uživatele přesměruje na koncový bod v2.0 ke spuštění [zásady](active-directory-b2c-reference-policies.md).
2. Uživatel vykoná zásadu podle její definice.
3. Aplikace obdrží z koncového bodu v2.0 token zabezpečení.
4. Aplikace používá token zabezpečení pro přístup k chráněným informacím nebo chráněného prostředku.
5. Server prostředků ověří token zabezpečení, aby ověřil, zda lze udělit přístup.
6. Aplikace token zabezpečení pravidelně aktualizuje.

Tyto kroky může mírně lišit v závislosti na typu aplikace, kterou vytváříte.

## <a name="web-applications"></a>Webové aplikace

U webových aplikací (včetně .NET, PHP, Java, Ruby, Python a Node.js), které jsou hostované na serveru a přístupných prostřednictvím prohlížeče Azure AD B2C podporuje [OpenID Connect](active-directory-b2c-reference-protocols.md) pro všechny uživatelské prostředí. To zahrnuje přihlášení, registraci a správu profilů. Webové aplikace v Azure AD B2C implementaci OpenID Connect, inicializuje tyto uživatelské prostředí pomocí žádosti o ověření do služby Azure AD. Výsledkem požadavku je `id_token`. Tento token zabezpečení představuje identitu uživatele. Poskytuje také informace o uživateli ve formě deklarací identity:

```
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

Další informace o typech tokenů a deklaracích identity přístupných aplikaci během [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md).

Ve webové aplikaci se každé spuštění [zásady](active-directory-b2c-reference-policies.md) trvá těchto kroků:

1. Uživatel prochází k webové aplikaci.
2. Webová aplikace přesměruje uživatele na Azure AD B2C označující zásady ke spuštění.
3. Uživatel vykoná zásadu.
4. Azure AD B2C vrátí `id_token` do prohlížeče.
5. `id_token` Identifikátor URI přesměrování nový tweet.
6. `id_token` Je ověřen a nastavení souboru cookie relace.
7. Zabezpečenou stránku se vrátí uživateli.

Ověření `id_token` pomocí veřejného podpisového klíče přijatého z Azure AD je dostačující k ověření identity uživatele. Zároveň se nastaví soubor cookie relace, který lze použít k identifikaci uživatele pro požadavky na dalších stránkách.

Pokud chcete zobrazit tento scénář v akci, zkuste použít jeden z ukázek kódu přihlášení webové aplikace v našem [oddílu Začínáme](active-directory-b2c-overview.md).

Kromě usnadnění snadného přihlášení může potřebovat aplikací webového serveru pro přístup k back endové webové službě. V takovém případě může webová aplikace provádět mírně odlišný [tok OpenID Connect](active-directory-b2c-reference-oidc.md) a získávat tokeny pomocí autorizačních kódů a obnovovacích tokenů. Tento scénář je znázorněn v následujícím [oddílu Webová rozhraní API](#web-apis).

## <a name="web-apis"></a>Webová rozhraní API

Azure AD B2C můžete použít k zabezpečení webových služeb, jako je například RESTful webová rozhraní API vaší aplikace. Webové rozhraní API může využívat OAuth 2.0 k zabezpečení dat ověřováním příchozích žádostí HTTP pomocí tokenů. Volající webového rozhraní API připojí token v hlavičce autorizace požadavku HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webové rozhraní API pak může pomocí tokenu ověřit identitu volajícího a extrahovat informace o volajícím z deklarací identity zakódovaných v tokenu. Další informace o typech tokenů a deklaracích identity přístupných aplikaci najdete v tématu [Odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md).

Webové rozhraní API může přijímat tokeny z řady typů klientů, včetně webových aplikací, desktop a mobilní aplikace, jednostránkové aplikace, démonů na straně serveru a dalších webových rozhraní API. Tady je příklad celého toku u webové aplikace, která volá webové rozhraní API:

1. Webová aplikace spouští zásady a uživatel dokončí činnost koncového uživatele.
2. Azure AD B2C vrátí `access_token` a autorizační kód v prohlížeči.
3. V prohlížeči příspěvcích `access_token` a autorizační kód identifikátor URI přesměrování.
4. Webový server ověřuje, `access token` a nastaví soubor cookie relace.
5. `access_token` Je součástí autorizační kód, ID klienta aplikace služby Azure AD B2C a přihlašovací údaje.
6. `access_token` a `refresh_token` jsou odesílány zpět do webového serveru.
7. Webové rozhraní API je volána s `access_token` v hlavičce autorizace.
8. Webové rozhraní API ověří daný token.
9. Zabezpečení dat se vrátí do webového serveru.

Pro další informace o kódech autorizace a obnovovacích tokenech a návod, jak získat tokeny, si přečtěte o [protokolu OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Chcete-li zjistit, jak zabezpečit webové rozhraní API pomocí Azure AD B2C, podívejte se na kurzy o webovém rozhraní API v [oddílu Začínáme](active-directory-b2c-overview.md).

## <a name="mobile-and-native-applications"></a>Mobilní a nativní aplikace

Aplikace, které jsou nainstalované na zařízeních, jako jsou mobilní i desktopové aplikace, často potřebují přístup k back endovým službám nebo webovým rozhraním API jménem uživatele. Můžete přidat vlastní činnosti správy identity pro vaše nativní aplikace a bezpečně volat back endové služby pomocí Azure AD B2C a [tok autorizačního kódu OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

V tomto toku, aplikace provede [zásady](active-directory-b2c-reference-policies.md) a přijímá `authorization_code` z Azure AD poté, co uživatel vykoná zásadu. `authorization_code` Představuje oprávnění aplikace volat back endové služby jménem uživatele, který je aktuálně přihlášený. Aplikace můžete potom exchange `authorization_code` na pozadí pro `id_token` a `refresh_token`.  Aplikace můžete použít `id_token` ověřování do back endové webové rozhraní API v požadavcích HTTP. Může také použít `refresh_token` k získání nového `id_token` po vypršení platnosti toho starého.

## <a name="current-limitations"></a>Aktuální omezení

### <a name="application-not-supported"></a>Aplikace není podporován 

#### <a name="daemonsserver-side-applications"></a>Démoni nebo serverové aplikace

Aplikace, které obsahují dlouho běžící procesy nebo které pracují bez přítomnosti uživatele také potřebují způsob, jak přistupovat k zabezpečeným prostředkům, jako je například webové rozhraní API. Tyto aplikace můžete ověřit a získat tokeny pomocí identity aplikace (nikoli uživatelovy delegované identity) a pomocí klienta OAuth 2.0 tok přihlašovacích údajů. Tok přihlašovacích údajů klienta není stejný jako tok na za vás a tok na vás nemělo používat pro ověřování na serveru.

I když tok přihlašovacích údajů klienta se službou Azure AD B2C momentálně nepodporuje, můžete nastavit tok přihlašovacích údajů klienta pomocí služby Azure AD. Tenanta služby Azure AD B2C s Azure AD organizace sdílí některé funkce tenantů.  Tok přihlašovacích údajů klienta se podporuje, pomocí funkce Azure AD pro tenanta Azure AD B2C. 

Pokud chcete nastavit tok přihlašovacích údajů klienta, přečtěte si [tok přihlašovacích údajů Azure Active Directory v2.0 a klienta OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). Po provedení úspěšného ověření výsledkem přijetí tokenu ve formátu tak, aby jej mohou využívat služby Azure AD jak je popsáno v [odkaz tokenu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Řetězení webových rozhraní API (tok on-behalf-of)

Mnoho architektur zahrnuje webové rozhraní API, které potřebuje volat podřízené webové rozhraní API, přičemž obě jsou zabezpečené pomocí Azure AD B2C. Tento scénář je častý u nativních klientů s back-endem v podobě webového rozhraní API. To poté zavolá online službu Microsoftu, jako je například Azure AD Graph API.

Tento scénář zřetězených webových rozhraní API může být podporován pomocí udělení přihlašovacích údajů nosiče OAuth 2.0 JWT, označovaného také jako tok on-behalf-of.  Nicméně tok on-behalf-of není v současné době v Azure AD B2C implementován.

### <a name="reply-url-values"></a>Hodnoty adresy URL odpovědi

Aktuálně je u aplikací zaregistrovaných pomocí Azure AD B2C omezená sada hodnot adresy URL odpovědi. Adresa URL odpovědi pro webové aplikace a služby musí začínat schématem `https` a všechny adresy URL odpovědi musí sdílet jednu doménu DNS. Například nemůžete zaregistrovat webovou aplikaci s některou z těchto adres URL odpovědi:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Registrační systém porovnává celý název DNS stávající adresy URL odpovědi s názvem DNS adresy URL odpovědi, kterou přidáváte. Požadavek na přidání názvu DNS selže, pokud platí některá z následujících podmínek:

- Celý název DNS nové adresy URL odpovědi neodpovídá názvu DNS stávající adresy URL odpovědi.
- Celý název DNS nové adresy URL odpovědi není subdoménou stávající adresy URL odpovědi.

Pokud má aplikace například tuto adresu URL odpovědi:

`https://login.contoso.com`

Můžete ji přidat tímto způsobem:

`https://login.contoso.com/new`

V tomto případě se název DNS přesně shoduje. Nebo můžete provést toto:

`https://new.login.contoso.com`

V tomto případě odkazujete na subdoménu DNS login.contoso.com. Pokud chcete mít aplikaci s adresami URL odpovědi login-east.contoso.com a login-west.contoso.com, musíte tyto adresy URL odpovědi přidat v následujícím pořadí:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Druhé dvě adresy URL odpovědi můžete přidat, protože jsou subdoménami první adresy URL odpovědi contoso.com. 

Při vytváření mobilní/nativní aplikace, můžete definovat **identifikátor URI pro přesměrování** místo **opětovného přehrání URL**. Existují dva důležité aspekty při výběru identifikátor URI přesměrování:

- **Jedinečnost:** Schéma identifikátoru URI přesměrování by mělo být pro každou aplikaci jedinečné. V příkladu `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` schéma com.onmicrosoft.contoso.appname. Tento model má následovat. Pokud dvě aplikace sdílejí stejné schéma, uživateli se zobrazí **zvolte aplikaci,** dialogového okna. Pokud uživatel použije nesprávnou volbu, přihlášení se nezdaří.
- **Úplnost:** Identifikátor URI přesměrování musí mít schéma a cestu. Cesta musí obsahovat za doménou alespoň jedno lomítko. Například `//contoso/` funguje a `//contoso` selže. Ujistěte se, že nejsou žádné speciální znaky jako podtržítka identifikátor URI pro přesměrování.

### <a name="faulted-apps"></a>Chybné aplikace

Aplikace Azure AD B2C se nesmí upravovat:

- Na jiných portálech pro správu aplikací, jako [portál pro registraci aplikací](https://apps.dev.microsoft.com/).
- Pomocí rozhraní Graph API nebo Powershellu.

Pokud upravíte aplikace Azure AD B2C mimo na webu Azure portal, stane se chybnou aplikací a už není použitelná s Azure AD B2C. Je nutné aplikaci odstranit a znovu ji vytvořit.

Pokud chcete aplikaci odstranit, přejděte na [portál pro registraci aplikací](https://apps.dev.microsoft.com/) a tam ji odstraňte. Aby byla aplikace viditelná, musíte být vlastníkem aplikace (nestačí být pouze správcem tenanta).

