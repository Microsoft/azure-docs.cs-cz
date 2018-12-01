---
title: Token odkaz v Azure Active Directory B2C | Dokumentace Microsoftu
description: Typy tokenů vydaných v Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 10de56ac8945be4bb0920f95774b469d283f575b
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721370"
---
# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C: Referenci tokenu

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) vyzařuje několik typů tokeny zabezpečení, které zpracovává každý [tok ověřování](active-directory-b2c-apps.md). Tento dokument popisuje formátu, vlastnosti zabezpečení a obsah každého typu token.

## <a name="types-of-tokens"></a>Typy tokenů
Azure AD B2C podporuje [protokol autorizace OAuth 2.0](active-directory-b2c-reference-protocols.md), které využívá oba přístupové tokeny a obnovovacích tokenů. Podporuje také ověřování a přihlásit se přes [OpenID Connect](active-directory-b2c-reference-protocols.md), který představuje třetí typ tokenu: ID token. Každý z těchto prvků představuje nosný token.

Nosný token je token zjednodušené zabezpečení, která uděluje "nosiče" přístup k chráněnému prostředku. Nositele je každá strana, která může představovat token. Azure AD B2C musí nejdřív ověřit strana předtím, než může přijímat nosný token. Ale pokud nejsou požadované kroky k zabezpečení token v přenos a ukládání, můžete zachytit a používá nežádoucí osobou. Některé tokeny zabezpečení mají předdefinovaný mechanismus brání neoprávněným strany je ale nosné tokeny není nutné tento mechanismus. Musí být přenášet v zabezpečeného kanálu, například zabezpečení transportní vrstvy (HTTPS).

Pokud nosný token je přenesen mimo zabezpečený kanál, můžete použít škodlivý stran útok man-in-the-middle k získání tokenu a použije ho k získání neoprávněného přístupu k chráněnému prostředku. Stejné zásady zabezpečení platí při nosné tokeny jsou uložená nebo ukládání do mezipaměti pro pozdější použití. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny bezpečným způsobem.

Další důležité informace o zabezpečení na nosné tokeny, naleznete v tématu [5 část dokumentu RFC 6750](http://tools.ietf.org/html/rfc6750).

Mnohé z tokenů, které problémy s Azure AD B2C jsou implementovány jako webové tokeny JSON (Jwt). Token JWT je compact, adresa URL typově bezpečný způsob přenosu informací mezi dvěma stranami. Tokeny Jwt obsahují informace, označované jako deklarace identity. Toto jsou kontrolní výrazy s informací o nositele a předmětem token. Deklarace identity v tokeny Jwt jsou objekty JSON, které jsou zakódovány a serializovat pro přenos. Protože tokeny Jwt vydaného Azure AD B2C jsou podepsané, nikoli však šifrován, můžete snadno kontrolovat obsah token JWT pro ladění. K dispozici několik nástrojů, které můžete udělat, včetně [jwt.ms](https://jwt.ms). Další informace o tokeny Jwt [JWT specifikace](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokeny ID

ID tokenu je forma token zabezpečení, který vaše aplikace obdrží z Azure AD B2C `/authorize` a `/token` koncových bodů. ID tokeny jsou reprezentovány ve formě [tokeny Jwt](#types-of-tokens), a které obsahují deklarace identity, které můžete použít k identifikaci uživatelů ve vaší aplikaci. Když jsou tokeny typu ID získaných z `/authorize` koncového bodu, se provádí pomocí [implicitní tok](active-directory-b2c-reference-spa.md), což se často používá pro uživatele, na které se přihlašujete do jazyka javasript na základě webové aplikace. Když jsou tokeny typu ID získaných z `/token` koncového bodu, se provádí pomocí [toku kódu](active-directory-b2c-reference-oidc.md), který udržuje token skryté z prohlížeče. To umožňuje token, který má být odeslány bezpečně v požadavcích HTTP pro komunikaci mezi dvě komponenty stejné aplikace nebo služby. Deklarace identity v tokenu ID můžete použít podle svých potřeb. Běžně se používají k zobrazení informací o účtu nebo je rozhodnutí o řízení přístupu v aplikaci.  

Jsou podepsané tokeny typu ID, ale nejsou aktuálně zašifrovaný. Když svou aplikaci nebo API obdrží ID token, musíte [ověřit podpis](#token-validation) prokázat, že je token platná. Několik deklarací identity v tokenu k prokázání, že se jedná o platný musí také ověřit vaši aplikaci nebo rozhraní API. V závislosti na požadavcích scénáře deklarace ověřené aplikací se může lišit, ale vaše aplikace musí provést některé [běžné deklarace identity ověření](#token-validation) v každé situaci.

#### <a name="sample-id-token"></a>Ukázkový token ID
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Přístupové tokeny

Přístupový token je také forma token zabezpečení, který vaše aplikace obdrží z Azure AD B2C `/authorize` a `/token` koncových bodů. Přístupové tokeny jsou vyjádřeny i jako [tokeny Jwt](#types-of-tokens), a které obsahují deklarace identity, které můžete použít k identifikaci udělená oprávnění k rozhraním API. Přístupové tokeny jsou podepsané, ale nejsou aktuálně zašifrovaný. Přístupové tokeny by měla sloužit k poskytnutí přístupu k rozhraní API a prostředků serverů. Další informace o tom, jak [použití přístupových tokenů](active-directory-b2c-access-tokens.md). 

Vaše rozhraní API přijímá token přístupu, musí [ověřit podpis](#token-validation) prokázat, že je token platná. Vaše rozhraní API musí také ověřit několik deklarací identity v tokenu k prokázání, že se jedná o platný. V závislosti na požadavcích scénáře deklarace ověřené aplikací se může lišit, ale vaše aplikace musí provést některé [běžné deklarace identity ověření](#token-validation) v každé situaci.

### <a name="claims-in-id-and-access-tokens"></a>Deklarace identity v tokenech ID a přístup

Pokud používáte Azure AD B2C, budete mít přesnou kontrolu nad obsahem z vašich tokenů. Můžete nakonfigurovat [toky uživatelů](active-directory-b2c-reference-policies.md) a vlastních zásad odesílat určité sady dat uživatele v deklaracích identity, které vaše aplikace vyžaduje pro jeho operace. Tato deklarace identity můžou obsahovat standardní vlastnosti, například uživatele `displayName` a `emailAddress`. Může také obsahovat [vlastní uživatelské atributy](active-directory-b2c-reference-custom-attr.md) definující ve svém adresáři B2C. Každé ID a access token, který se zobrazí obsahuje sadu deklarací identity související se zabezpečením. Aplikace můžete použít tyto deklarace mohla bezpečně ověřit uživatele a požadavků.

Všimněte si, že deklarace identity v tokenech ID nebudou zobrazeny v libovolném pořadí. Kromě toho nových deklarací identity můžete zavedena v ID tokenů v každém okamžiku. Vaše aplikace by neměl přerušit zavedeném nových deklarací identity. Tady jsou deklarace identity, které očekáváte, že existují v ID a přístupové tokeny vydané službou Azure AD B2C. Nějaké další deklarace identity se určují podle zásady. Postup, zkuste to zkontrolujete deklarací identity v tokenu ID ukázka vložením do [jwt.ms](https://jwt.ms). Další podrobnosti najdete v [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html).

| Název | Deklarovat | Příklad hodnoty | Popis |
| --- | --- | --- | --- |
| Cílová skupina |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |Deklaraci identity cílové skupiny identifikuje zamýšlený příjemce tokenu. Pro Azure AD B2C cílová skupina je ID aplikace vaší aplikace, protože přiřazené vaší aplikaci v portálu pro registraci aplikace. Vaše aplikace by měl ověřit tuto hodnotu a odmítnout token, pokud neodpovídá. Cílové skupiny je synonymní s prostředkem. |
| Vystavitel |`iss` |`https://{tenantname}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |Tato deklarace identity označuje službu tokenů zabezpečení (STS), který vytvoří a vrátí token. Také určuje adresář Azure AD, ve kterém byl uživatel ověřený. Vaše aplikace by měla ověření vystavitele deklarace identity k zajištění, že token pochází z koncového bodu Azure Active Directory v2.0. |
| Vystaveno |`iat` |`1438535543` |Tato deklarace identity je čas, kdy byl vydán token, v unixovém čase. |
| Čas vypršení platnosti |`exp` |`1438539443` |Čas vypršení platnosti, které deklarace identity je doba, jakou token, který se stane neplatným, vyjádřena v unixovém čase. Vaše aplikace by pomocí této deklarace identity k ověření platnosti dobu životnosti tokenu. |
| Neplatný před |`nbf` |`1438535543` |Tato deklarace identity je doba, jakou bude token platný, zastoupené v unixovém čase. To je obvykle stejná jako čas, kdy byl token vydán. Vaše aplikace by pomocí této deklarace identity k ověření platnosti dobu životnosti tokenu. |
| Verze |`ver` |`1.0` |Toto je verze ID tokenu, jak jsou definovány službou Azure AD. |
| Kód hash |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Hodnota hash kódu je součástí ID token jenom v případě, že token, který vydává spolu s autorizačního kódu OAuth 2.0. Hodnota hash kódu slouží k ověření pravosti autorizační kód. Další podrobnosti o tom, jak toto ověření proveďte najdete v tématu [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html).  |
| Hodnota hash tokenu přístupu |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Algoritmus hash tokenu přístupu je součástí ID token jenom v případě, že token, který vydává spolu s přístupového tokenu OAuth 2.0. Algoritmus hash tokenu přístupu slouží k ověření pravosti tokenu přístupu. Další podrobnosti o tom, jak toto ověření proveďte najdete v tématu [specifikace OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html)  |
| Hodnota Nonce |`nonce` |`12345` |Hodnotu nonce je strategie zmírnit útoky opětovného přehrání tokenu. Vaše aplikace může určit hodnotu nonce v žádost o autorizaci s použitím `nonce` parametr dotazu. Hodnota je zadat v požadavku bude vygenerován bez jakýchkoli úprav v `nonce` pouze ID tokenu deklarací identity. To umožňuje aplikaci, můžete ověřit hodnoty s hodnotou zadanou v požadavku, která přidruží dané ID tokenu relace aplikace. Vaše aplikace by měl provést toto ověření během procesu ověření tokenu ID. |
| Subjekt |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Toto je hlavní o tom, které token vyhodnocuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze přiřadit nebo znovu použít. Slouží k provádění kontroly autorizace bezpečně, třeba když se používá token pro přístup k prostředku. Ve výchozím nastavení deklarace identity subjektu se vyplní ID objektu uživatele v adresáři. Další informace najdete v tématu [Azure Active Directory B2C: Token, relace a konfigurace jednotného přihlašování](active-directory-b2c-token-session-sso.md). |
| Informace o třídě kontext ověřování |`acr` |Neuvedeno |Není v současné době nepoužívá, s výjimkou starší zásady. Další informace najdete v tématu [Azure Active Directory B2C: Token, relace a konfigurace jednotného přihlašování](active-directory-b2c-token-session-sso.md). |
| Rozhraní framework zásady důvěryhodnosti |`tfp` |`b2c_1_sign_in` |Jde o název, který se používá k získání tokenu ID zásad. |
| Čas ověřování |`auth_time` |`1438535543` |Tato deklarace identity je čas, kdy poslední zadané přihlašovací údaje uživatele, reprezentovaný v unixovém čase. |

### <a name="refresh-tokens"></a>Obnovovacích tokenů
Aktualizovat tokeny jsou tokeny zabezpečení, které vaše aplikace můžete použít k získání nové tokeny typu ID a přístupové tokeny ve toku OAuth 2.0. Poskytují dlouhodobé přístup k prostředkům jménem uživatelů vaší aplikace bez nutnosti interakce s těmito uživateli.

Pro příjem aktualizace tokenu v odpovědi tokenu, vaše aplikace musí požádat o `offline_acesss` oboru. Další informace o `offline_access` oboru, přečtěte si [referenční informace o protokolu Azure AD B2C](active-directory-b2c-reference-protocols.md).

Obnovovacích tokenů jsou a bude vždy, stane zcela neprůhledný do vaší aplikace. Vydává se službou Azure AD a mohou být zkontrolovány a interpretovat jenom služby Azure AD. Jsou dlouhodobé, ale vaše aplikace, neměl by být zapsaný s předpokladem, že bude trvat obnovovací token pro určité časové období. Tokeny obnovení může být neplatné v daném okamžiku provádějí pro celou řadu důvodů. Jedině pro vaši aplikaci vědět, jestli je platný token obnovení je pokus o uplatněte ho tak, že žádosti o token do služby Azure AD.

Když uplatníte obnovovací token pro nový token (a pokud vaše aplikace byla udělena `offline_access` obor), obdržíte nového tokenu obnovení v odpovědi tokenu. Uložte nově vydaný obnovovací token. By mělo nahradit token obnovení, který jste dříve použili v požadavku. To pomáhá zajistit, že obnovovací tokeny jsou dál platné pro co nejdéle.

## <a name="token-validation"></a>Ověření tokenu
Ověřit token, by měla vaše aplikace Zkontrolujte podpis a deklarace identity tokenu.

Mnoho open source knihovny jsou k dispozici pro ověřování tokeny Jwt, v závislosti na vašem preferovaném jazyce. Doporučujeme vám, že můžete prozkoumat tyto možnosti spíše než implementovat vlastní logiku ověřování. Informace v tomto průvodci můžete zjistěte, jak správně používat tyto knihovny.

### <a name="validate-the-signature"></a>Ověření podpisu
Token JWT obsahuje tři segmentů oddělených pomocí `.` znak. První segment *záhlaví*, druhá *tělo*, a třetí je *podpis*. Segment podpis slouží k ověření pravosti tokenu tak, aby může být důvěřuje vaší aplikace.

Azure AD B2C tokeny jsou podepsány pomocí asymetrického šifrování standardních algoritmů, například RSA 256. Záhlaví token, který obsahuje informace o metodu šifrování a klíče použitý k podpisu tokenu:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

`alg` Deklarace identity označuje algoritmus, který se použil k podepsání token. `kid` Deklarace identity označuje konkrétní veřejný klíč, který se použil k podepsání token.

V každém okamžiku Azure AD se můžete přihlásit token pomocí některého z sadu párů veřejného a privátního klíče. Azure AD otočí možné sadu klíčů pravidelně, takže vaše aplikace by měla napsané tak, aby tyto klíče změny automaticky. Přiměřené intervalech a kontrolují dostupnost aktualizací pro veřejné klíče používané službou Azure AD je každých 24 hodin.

Azure AD B2C má koncový bod metadat OpenID Connect. To umožňuje aplikacím načítat informace o Azure AD B2C v době běhu. Tyto informace zahrnují koncových bodů, obsah tokenu a tokenu podpisových klíčů. Váš adresář B2C obsahuje dokument metadat JSON pro jednotlivé zásady. Například dokument metadat pro `b2c_1_sign_in` zásad v `fabrikamb2c.onmicrosoft.com` se nachází na:

```
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` je adresář B2C používá k ověření uživatele, a `b2c_1_sign_in` jsou zásady použité k získání tokenu. Chcete-li zjistit, jaké zásady se použil k podepsání token (a kde lze načíst metadata), máte dvě možnosti. Nejprve je součástí názvu zásady `acr` deklarací identity v tokenu. Deklarace mimo tělo tokenů JWT můžete analyzovat podle base-64 dekódování text a deserializaci řetězec JSON, který výsledky. `acr` Deklarací identity bude název zásady, která byla použita k vydání tokenu.  Další možností je určený ke kódování zásady v hodnotě `state` parametr vydávat žádosti, a pak ji k určení, jaké zásady se použil dekódovat. Některé z metod je platný.

Dokument metadat je objekt JSON, který obsahuje několik užitečné údaje. Patří mezi ně umístění koncových bodů potřebná k provedení ověřování OpenID Connect. Zahrnují taky, že `jwks_uri`, což dává umístění sady veřejných klíčů, které se používají k podepisování tokenů. Umístění je k dispozici zde, že je nejvhodnější se dynamicky načíst umístění pomocí dokument metadat a analýze si `jwks_uri`:

```
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

Dokument JSON umístěný na této adrese URL obsahuje všechny údaje o veřejném klíči používá v určitém okamžiku. Vaše aplikace může používat `kid` deklarací identity v hlavičce tokenů JWT můžete vybrat v dokumentu JSON, který se používá k podepsání konkrétní token veřejného klíče. Ověření podpisu je pak můžete provádět pomocí správný veřejný klíč a označený algoritmus.

Popis toho, jak provádět ověření podpisu je mimo rámec tohoto dokumentu. Řada open source knihovny jsou k dispozici vám pomůžou s tím, pokud ho potřebujete.

### <a name="validate-the-claims"></a>Ověřit deklarace identity
Když svou aplikaci nebo API obdrží ID token, má také provést několik kontrol před deklarací identity v tokenu ID. Ty zahrnují, ale nejsou omezené na:

* **Cílovou skupinu** deklarace identity: ověří, že ID token měla předávat do aplikace.
* **Není před** a **čas vypršení platnosti** deklarací identity: tyto ověřte, že nevypršela platnost tokenu ID.
* **Vystavitele** deklarace identity: ověří, že byl token vydán do vaší aplikace pomocí Azure AD.
* **Nonce**: Toto je strategii omezení rizik útoků opětovného přehrání tokenu.

Úplný seznam ověření by měla aplikace provádět, najdete [OpenID Connect specifikace](https://openid.net). Podrobnosti o očekávané hodnoty pro tyto deklarace jsou zahrnuty v předchozím [token části](#types-of-tokens).  

## <a name="token-lifetimes"></a>Životnost tokenů
Ještě své znalosti v oblasti jsou k dispozici následující životností tokenů. Mohou vám pomoci při vývoji a ladění aplikací. Všimněte si, že vaše aplikace, neměl by být zapsaný můžete očekávat některé z těchto životnosti zůstat konstantní. Mohou a změní. Další informace najdete [přizpůsobení životností tokenů](active-directory-b2c-token-session-sso.md) v Azure AD B2C.

| Podpisový | Životnost | Popis |
| --- | --- | --- |
| Tokeny ID |Jedna hodina |Tokeny typu ID jsou obvykle platné hodiny. Webové aplikace můžete udržovat svůj vlastní relace s uživateli (doporučeno) tohoto životního cyklu. Můžete také na celý život jiné relaci. Jestli vaše aplikace musí získat nové ID tokenu, jednoduše je potřeba vytvořit novou žádost o přihlášení do služby Azure AD. Pokud má uživatel platné prohlížeče relace s Azure AD, tento uživatel nemusí muset znovu zadat přihlašovací údaje. |
| Obnovovacích tokenů |Až do 14 dnů |Jedno aktualizační token je platný pro maximálně 14 dnů. Token obnovení však mohou stát neplatnými kdykoli z několika důvodů. Vaše aplikace by měla pokračovat se pokouší použít obnovovací token, dokud žádost selže, nebo aplikace nahradí nový token obnovení. Obnovovací token se také mohou stát neplatnými, pokud od poslední zadání přihlašovacích údajů uplynulo 90 dnů. |
| Autorizační kódy |Pět minut |Autorizační kódy jsou záměrně krátkodobou. Se musí uplatnit okamžitě pro přístupové tokeny, tokeny typu ID nebo obnovovací tokeny při příjmu. |

