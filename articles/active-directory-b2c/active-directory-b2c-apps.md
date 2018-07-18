---
title: Typy aplikací, které lze použít v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o typech aplikací, které můžete použít v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/13/2018
ms.author: davidmu1
ms.component: B2C
ms.openlocfilehash: 0055da12689fcbe14de9dd537db2b20358a7273b
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113449"
---
# <a name="applications-types-that-can-be-used-in-active-directory-b2c"></a>Typy aplikací, které lze použít v Active Directory B2C

Azure Active Directory (Azure AD) B2C podporuje ověřování pro celou řadu architektur moderních aplikací. Všechny jsou založeny na standardních oborových protokolech [OAuth 2.0](active-directory-b2c-reference-protocols.md) nebo [OpenID Connect](active-directory-b2c-reference-protocols.md). Tento dokument popisuje typy aplikací, které můžete sestavit, nezávisle na jazyk nebo platformu dáváte přednost. Také pomáhá pochopit scénáře vysoké úrovně, než začnete sestavovat aplikace.

Každá aplikace, která používá Azure AD B2C musí být zaregistrovaný ve vaší [tenanta Azure AD B2C](active-directory-b2c-get-started.md) pomocí [webu Azure Portal](https://portal.azure.com/). Proces registrace aplikace shromáždí a přiřadí hodnoty, jako například:

* **ID aplikace** , který jednoznačně identifikuje vaši aplikaci.
* A **identifikátor URI pro přesměrování** , který lze použít k cílení odpovědí zpět do vaší aplikace.

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

![Obrázek plaveckých drah webové aplikace](./media/active-directory-b2c-apps/webapp.png)

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

> [!NOTE]
> Azure AD B2C v současné době podporuje pouze webová rozhraní API, ke kterým přistupují vlastní, dobře známí klienti. Výsledné aplikace mohou být pro instanci aplikace pro iOS, aplikaci pro Android a back endové webové rozhraní API. Tato architektura je plně podporovaná. Povolení partnerského klienta, jako je například další aplikace pro iOS, pro přístup k stejnému webovému rozhraní API v tuto chvíli nepodporuje. Všechny komponenty kompletní aplikace musí sdílet ID jedné aplikace.
>
>

Webové rozhraní API může přijímat tokeny z řady typů klientů, včetně webových aplikací, desktop a mobilní aplikace, jednostránkové aplikace, démonů na straně serveru a dalších webových rozhraní API. Tady je příklad celého toku u webové aplikace, která volá webové rozhraní API:

![Obrázek plaveckých drah webového rozhraní API webové aplikace](./media/active-directory-b2c-apps/webapi.png)

Pro další informace o kódech autorizace a obnovovacích tokenech a návod, jak získat tokeny, si přečtěte o [protokolu OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Chcete-li zjistit, jak zabezpečit webové rozhraní API pomocí Azure AD B2C, podívejte se na kurzy o webovém rozhraní API v [oddílu Začínáme](active-directory-b2c-overview.md).

## <a name="mobile-and-native-applications"></a>Mobilní a nativní aplikace

Aplikace, které jsou nainstalované na zařízeních, jako jsou mobilní i desktopové aplikace, často potřebují přístup k back endovým službám nebo webovým rozhraním API jménem uživatele. Můžete přidat vlastní činnosti správy identity pro vaše nativní aplikace a bezpečně volat back endové služby pomocí Azure AD B2C a [tok autorizačního kódu OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

V tomto toku, aplikace provede [zásady](active-directory-b2c-reference-policies.md) a přijímá `authorization_code` z Azure AD poté, co uživatel vykoná zásadu. `authorization_code` Představuje oprávnění aplikace volat back endové služby jménem uživatele, který je aktuálně přihlášený. Aplikace můžete potom exchange `authorization_code` na pozadí pro `id_token` a `refresh_token`.  Aplikace můžete použít `id_token` ověřování do back endové webové rozhraní API v požadavcích HTTP. Může také použít `refresh_token` k získání nového `id_token` po vypršení platnosti toho starého.

> [!NOTE]
> Azure AD B2C momentálně podporuje pouze tokeny, které se používají pro přístup k aplikaci prvku vlastní back endové webové službě. Kompletní aplikace mohou být pro instanci aplikace pro iOS, aplikaci pro Android a back endové webové rozhraní API. Tato architektura je plně podporovaná. Tím umožní vaší aplikaci iOS k partnerskému webovému rozhraní API pomocí přístupových tokenů OAuth 2.0 se momentálně nepodporuje. Všechny komponenty kompletní aplikace musí sdílet ID jedné aplikace.
>
>

![Obrázek plaveckých drah nativní aplikace](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Aktuální omezení

Azure AD B2C momentálně nepodporuje následující typy aplikací, ale nachází se na roadmapě. 

### <a name="daemonsserver-side-applications"></a>Démoni nebo serverové aplikace

Aplikace, které obsahují dlouho běžící procesy nebo které pracují bez přítomnosti uživatele také potřebují způsob, jak přistupovat k zabezpečeným prostředkům, jako je například webové rozhraní API. Tyto aplikace můžete ověřit a získat tokeny pomocí identity aplikace (nikoli uživatelovy delegované identity) a pomocí klienta OAuth 2.0 tok přihlašovacích údajů. Tok přihlašovacích údajů klienta není stejný jako tok na za vás a tok na vás nemělo používat pro ověřování na serveru.

I když tok přihlašovacích údajů klienta se službou Azure AD B2C momentálně nepodporuje, můžete nastavit tok přihlašovacích údajů klienta pomocí služby Azure AD. Tenanta služby Azure AD B2C s Azure AD organizace sdílí některé funkce tenantů.  Tok přihlašovacích údajů klienta se podporuje, pomocí funkce Azure AD pro tenanta Azure AD B2C. 

Pokud chcete nastavit tok přihlašovacích údajů klienta, přečtěte si [tok přihlašovacích údajů Azure Active Directory v2.0 a klienta OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). Po provedení úspěšného ověření výsledkem přijetí tokenu ve formátu tak, aby jej mohou využívat služby Azure AD jak je popsáno v [odkaz tokenu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="web-api-chains-on-behalf-of-flow"></a>Řetězení webových rozhraní API (tok on-behalf-of)

Mnoho architektur zahrnuje webové rozhraní API, které potřebuje volat podřízené webové rozhraní API, přičemž obě jsou zabezpečené pomocí Azure AD B2C. Tento scénář je častý u nativních klientů s back-endem v podobě webového rozhraní API. To poté zavolá online službu Microsoftu, jako je například Azure AD Graph API.

Tento scénář zřetězených webových rozhraní API může být podporován pomocí udělení přihlašovacích údajů nosiče OAuth 2.0 JWT, označovaného také jako tok on-behalf-of.  Nicméně tok on-behalf-of není v současné době v Azure AD B2C implementován.
