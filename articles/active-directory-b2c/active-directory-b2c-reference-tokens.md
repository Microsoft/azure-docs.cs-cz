---
title: Token odkaz – Azure AD B2C | Microsoft Docs
description: Typy tokeny vydané v Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: 09d776b54941e33979d7969b25c35e67a53cf8f0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C: Odkaz tokenu

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) vysílá několik typů tokeny zabezpečení, jako zpracovává každý [tok ověřování](active-directory-b2c-apps.md). Tento dokument popisuje formát, zabezpečení vlastnosti a obsah každého typu token.

## <a name="types-of-tokens"></a>Typy tokenů
Azure AD B2C podporuje [protokol autorizace OAuth 2.0](active-directory-b2c-reference-protocols.md), které využívá oba přístup tokeny a obnovovacích tokenů. Také podporuje ověřování a přihlásit se přes [OpenID Connect](active-directory-b2c-reference-protocols.md), který představuje třetí typ tokenu: ID token. Všechny tyto tokeny je reprezentován jako token nosiče.

Je token nosiče tokenu lightweight zabezpečení, který uděluje přístup "nosiče" k chráněnému prostředku. Nosiče je jakékoli strany, který může být token. Azure AD B2C musí je nejdřív ověřit a stranou předtím, než mohl přijímat token nosiče. Ale požadované kroky nebudou přijata zabezpečit token v přenosu a úložiště, můžete zachytit a použít nezamýšleným strana. Některé tokeny zabezpečení mají integrované mechanismus, který brání neoprávněným stranám jejich používání, ale nosné tokeny nemají tento mechanismus. Musí být přenosu v zabezpečený kanál, jako je například transport layer security (HTTPS).

Pokud token nosiče je přenesen mimo zabezpečený kanál, škodlivý strany slouží útok man-in-the-middle k získání tokenu a použít ho k získání neoprávněného přístupu k chráněnému prostředku. Stejné zásady zabezpečení platí, když jsou nosné tokeny uložené nebo ukládání do mezipaměti pro pozdější použití. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny zabezpečeným způsobem.

Další důležité informace o zabezpečení na nosné tokeny, najdete v části [RFC 6750 část 5](http://tools.ietf.org/html/rfc6750).

Řadu tokeny, které Azure AD B2C problémy jsou implementované jako webové tokeny JSON (Jwt). Token JWT je compact, adresa URL bezpečných způsob přenosu informací mezi dvěma účastníky. Tokeny Jwt obsahují informace, které jsou známé jako deklarace identity. Jsou to kontrolní výrazy informací o nosiče a předmět tokenu. Deklarací identity ve Jwt jsou objekty JSON, které jsou zakódovány a serializovat pro přenos. Protože Jwt vydaného Azure AD B2C je podepsán, ale nejsou šifrovaná, si můžete prohlédnout snadno obsah token JWT k ladění ho. K dispozici je několik nástrojů, můžete to provést, včetně [jwt.ms](https://jwt.ms). Další informace o Jwt, najdete v části [JWT specifikace](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>ID tokeny

ID token je formulář tokenu zabezpečení, která vaše aplikace přijímá z Azure AD B2C `/authorize` a `/token` koncové body. ID tokeny jsou reprezentovány jako [Jwt](#types-of-tokens), a obsahují deklarace identity, které můžete použít k identifikaci uživatelů ve vaší aplikaci. Když jsou tokeny typu ID získali z `/authorize` koncový bod, se provádí pomocí použít [implicitního toku](active-directory-b2c-reference-spa.md), což se často používá pro uživatele podepisování, do které javascript na základě webové aplikace. Když jsou tokeny typu ID získali z `/token` koncový bod, se provádí tak pomocí [toku důvěrné kódu](active-directory-b2c-reference-oidc.md), který udržuje token skryté z prohlížeče. To umožňuje token, který má být odeslány bezpečně v požadavcích HTTP pro komunikaci mezi dvěma součástí stejné aplikace nebo služby. Deklarace identity můžete použít v tokenu ID podle svých potřeb. Běžně se používají k zobrazení informací o účtu nebo je rozhodnutí o řízení přístupu v aplikaci.  

Jsou podepsané tokeny typu ID, ale nejsou aktuálně zašifrovány. Když aplikaci nebo API obdrží ID token, musí [ověřit podpis](#token-validation) prokázat, že token je platná. Aplikaci nebo rozhraní API musíte také ověřit pár deklarací identity v tokenu k prokázání, že je platné. V závislosti na požadavcích scénáře, se může lišit deklarace ověřené aplikací, ale aplikace musí provést některé [běžné ověření deklarace identity](#token-validation) v každý scénář.

#### <a name="sample-id-token"></a>Ukázkový token pro ID
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

Přístupový token je také formulář tokenu zabezpečení, která vaše aplikace přijímá z Azure AD B2C `/authorize` a `/token` koncové body. Přístupové tokeny, které jsou také reprezentované jako [Jwt](#types-of-tokens), a obsahují deklarace identity, které můžete použít k identifikaci udělená oprávnění pro vaše rozhraní API. Přístupové tokeny jsou podepsané, ale nejsou aktuálně zašifrovány. Přístupové tokeny slouží k poskytování přístupu k rozhraní API a prostředků servery. Další informace o tom, jak [pomocí přístupových tokenů](active-directory-b2c-access-tokens.md). 

Rozhraní API obdrží token přístupu, musí [ověřit podpis](#token-validation) prokázat, že token je platná. Rozhraní API musíte také ověřit pár deklarací identity v tokenu k prokázání, že je platné. V závislosti na požadavcích scénáře, se může lišit deklarace ověřené aplikací, ale aplikace musí provést některé [běžné ověření deklarace identity](#token-validation) v každý scénář.

### <a name="claims-in-id-and-access-tokens"></a>Deklarace identity v ID a přístupové tokeny

Pokud používáte Azure AD B2C, máte jemně odstupňovanou kontrolu nad obsah z vašich tokenů. Můžete nakonfigurovat [zásady](active-directory-b2c-reference-policies.md) odeslat určité sady dat uživatele v deklarace identity, které vaše aplikace vyžaduje pro její operace. Tyto deklarace identity můžou obsahovat standardní vlastnosti, například uživatele `displayName` a `emailAddress`. Může také obsahovat [vlastní uživatelské atributy](active-directory-b2c-reference-custom-attr.md) definující ve svém adresáři B2C. Každý ID a přístup token, který se zobrazí obsahuje sadu deklarací identity související se zabezpečením. Aplikace můžete použít tyto deklarace k bezpečně ověření uživatelů a požadavky.

Všimněte si, že deklarace identity v tokenech ID nebudou zobrazeny v libovolném pořadí. Kromě toho může být nových deklarací identity zavedená v tokeny typu ID kdykoli. Aplikace by neměl rozdělit zavedeném nových deklarací identity. Tady jsou deklarace identity, které by měla existovat v ID a přístupové tokeny vydané pomocí Azure AD B2C. Zásady jsou určeny žádné další deklarace identity. Postup kontroly deklarací identity ve ukázkový token pro ID vložením do vyzkoušejte [jwt.ms](https://jwt.ms). Další podrobnosti najdete v [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html).

| Název | Deklarovat | Příklad hodnoty | Popis |
| --- | --- | --- | --- |
| Cílová skupina |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |Deklaraci identity cílovou skupinu identifikuje zamýšlený příjemce tokenu. Pro Azure AD B2C cílová skupina je ID aplikace vaší aplikace, jako přiřazené vaší aplikaci v portálu pro registraci aplikace. Vaše aplikace by měl ověřit tuto hodnotu a odmítnout token, pokud neodpovídá. |
| Vystavitel |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |Toto tvrzení identifikuje služby tokenů na zabezpečení (STS), která vytvoří a vrátí token. Také identifikuje adresář Azure AD, ve kterém byl uživatel ověřený. Aplikace by měl ověřit vystavitele deklarace zajistit, že token pochází od koncového bodu v2.0 Azure Active Directory. |
| vydané v |`iat` |`1438535543` |Tato deklarace identity je čas, kdy byl token vydán, v čase epoch. |
| Čas vypršení platnosti |`exp` |`1438539443` |Čas vypršení platnosti, které deklarace identity je čas, kdy stává neplatným, token znázorněná epoch čas. Aplikace by měl ověřit správnost dobu životnosti tokenu pomocí této deklarace identity. |
| Neplatný před |`nbf` |`1438535543` |Tento požadavek je okamžikem, kdy se token platný, reprezentována v čase epoch. Je to obvykle stejné jako čas, který byl vydán token. Aplikace by měl ověřit správnost dobu životnosti tokenu pomocí této deklarace identity. |
| Verze |`ver` |`1.0` |Toto je verze ID tokenu, jak je definované ve službě Azure AD. |
| Kód hash |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Hodnota hash kódu je součástí ID token jenom v případě, že je token vydán společně s autorizační kód OAuth 2.0. Kód hash slouží k ověření pravosti autorizační kód. Další podrobnosti o tom, jak toto ověření proveďte najdete v tématu [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html).  |
| Hodnota hash tokenu přístupu |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Algoritmus hash tokenu přístupu je součástí ID token jenom v případě, že je token vydán společně s přístupový token OAuth 2.0. Algoritmus hash tokenu přístupu slouží k ověření pravosti tokenu přístupu. Další podrobnosti o tom, jak toto ověření proveďte najdete v tématu [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html)  |
| hodnotu Nonce |`nonce` |`12345` |Hodnotu nonce je strategie umožňuje snížit riziko útoků opětovného přehrání tokenu. Vaše aplikace může určit hodnotu nonce v jednom požadavku autorizace pomocí `nonce` parametr dotazu. Hodnota zadáte v žádosti se vygenerované ponechat beze změny v `nonce` deklarací z tokenu ID. To umožňuje aplikaci, můžete ověřit hodnoty s hodnotou zadanou v žádosti, která přidruží dané ID token aplikace relace. Aplikace by měla provést toto ověření během procesu ověření tokenu ID. |
| Předmět |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |Toto je hlavní o tom, které vyhodnotí token informace, například uživatele aplikace. Tato hodnota se nedá změnit a nemůže být přiřazeny nebo znovu použít. Slouží ke kontrole autorizace bezpečně, například když token slouží pro přístup k prostředkům. Ve výchozím nastavení je deklarace identity subjektu naplněna s ID objektu uživatele v adresáři. Další informace najdete v tématu [Azure Active Directory B2C: Token, relace a konfigurace přihlášení](active-directory-b2c-token-session-sso.md). |
| Informace o ověřování kontextu – třída |`acr` |Neuvedeno |Není právě používána, s výjimkou starší zásady. Další informace najdete v tématu [Azure Active Directory B2C: Token, relace a konfigurace přihlášení](active-directory-b2c-token-session-sso.md). |
| Framework zásady důvěryhodnosti |`tfp` |`b2c_1_sign_in` |Toto je název zásady, která byla použita k získání tokenu ID. |
| Doba ověřování |`auth_time` |`1438535543` |Tento požadavek je čas, kdy uživatel poslední zadaná pověření v epoch čase. |

### <a name="refresh-tokens"></a>Obnovovacích tokenů
Aktualizujte tokeny jsou tokeny zabezpečení, které aplikace můžete použít k získání nové ID tokeny a přístup k tokeny v tok OAuth 2.0. Poskytují aplikace dlouhodobé přístup k prostředkům jménem uživatelů bez nutnosti interakci s tyto uživatele.

Pro příjem aktualizace tokenu v odpovědi tokenu, aplikace musí požádat `offline_acesss` oboru. Další informace o `offline_access` obor, naleznete [referenční informace o Azure AD B2C protokolu](active-directory-b2c-reference-protocols.md).

Obnovovacích tokenů jsou a bude vždy, zcela neprůhledný do vaší aplikace. Vydal Azure AD a mohou být prověřovány a interpretovat jenom služby Azure AD. Jsou dlohotrvající, ale aplikace nemá zapisovat s tím, který bude poslední obnovovací token pro určité časové období. V každém okamžiku pro celou řadu důvodů může být zneplatněné obnovovacích tokenů. Jediný způsob, jakým aplikace potřebujete vědět, jestli je platný token obnovení je pokus o uplatněte ho tak, že žádosti o token do služby Azure AD.

Když uplatnit obnovovací token pro nový token (a v případě, že aplikace byla udělena `offline_access` oboru), zobrazí se nové obnovovací token v odpovědi tokenu. Uložte nově vydané aktualizace tokenu. By mělo nahradit token obnovení, které jste dříve použili v požadavku. To pomáhá zaručit, že jsou dál platné pro stejně dlouho obnovovacích tokenů.

## <a name="token-validation"></a>Ověření tokenu
K ověření tokenu, měli zkontrolovat aplikace podpis i deklarace identity tokenu.

Mnoho opensourcové knihovny jsou k dispozici pro ověření tokeny Jwt, v závislosti na vašem preferovaném jazyce. Doporučujeme místo před implementaci vlastní logiky ověřování prozkoumat tyto možnosti. Informace v tomto průvodci můžete další informace o správné použití těchto knihovny.

### <a name="validate-the-signature"></a>Ověření podpisu
Token JWT obsahuje tři segmenty, oddělených `.` znak. Je první segment *záhlaví*, druhý *textu*, a je třetí *podpis*. Segment podpis slouží k ověření pravosti tokenu, aby důvěryhodné aplikace.

Azure AD B2C tokeny jsou podepsány pomocí standardní asymetrických šifrovacích algoritmů, například RSA 256. Hlavička token obsahuje informace o metodě klíč a šifrování používaný k podepisování token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

`alg` Deklarace identity Určuje algoritmus, který se použil k podepsání token. `kid` Deklarace identity označuje konkrétní veřejný klíč, který se použil k podepsání token.

V každém okamžiku může Azure AD přihlášení token pomocí jedné sady páry klíčů veřejný soukromý. Azure AD otočí možné sada klíčů pravidelně, tak vaše aplikace by měly být zapsány pro zpracování těchto změn klíče automaticky. Přiměřené frekvence ke kontrole aktualizací do veřejných klíčů používaných službou Azure AD je každých 24 hodin.

Azure AD B2C má koncový bod metadat OpenID Connect. To umožňuje aplikacím načíst informace o Azure AD B2C za běhu. Tyto informace zahrnují koncových bodů, obsah tokenu a token podpisových klíčů. Dokument metadat JSON pro každou zásadu obsahuje adresáři B2C. Například dokument metadat pro `b2c_1_sign_in` zásad v `fabrikamb2c.onmicrosoft.com` se nachází na:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` je adresář B2C používá k ověření uživatele, a `b2c_1_sign_in` zásady použité k získání tokenu. Chcete-li zjistit, jaké zásady se použil k podepsání token (a kde lze načíst metadata), máte dvě možnosti. Nejprve je součástí název zásady `acr` deklarací identity v tokenu. Pomocí kódování base-64 dekódování textu a deserializaci řetězce formátu JSON, která způsobí, že můžete analyzovat deklarace identity z textu token JWT. `acr` Deklarací identity bude název zásady, která byla použita k vydání tokenu.  Vaše další možností je určený ke kódování zásad v hodnotě `state` parametr při vydání požadavku a potom ji k určení, jaké zásady byl použit dekódovat. Buď metoda je platná.

Dokument metadat je objekt JSON, který obsahuje několik užitečné informací. Mezi ně patří umístění koncových bodů potřebná k provedení ověřování OpenID Connect. Zahrnují taky, že `jwks_uri`, což dává umístění sady veřejných klíčů, který slouží k podepisování tokenů. Umístění je tady uvedené, že je vhodné se dynamicky načíst umístění pomocí dokument metadat a analýze se `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

Dokument JSON, který je umístěný na této adrese URL obsahuje všechny veřejné klíče informace používán v určitém okamžiku. Aplikace můžete použít `kid` deklarací identity v hlavičce JWT vyberte veřejný klíč v dokumentu JSON, který se používá k podepisování konkrétní token. Ověření podpisu je pak můžete provádět pomocí správný veřejný klíč a algoritmus uvedené.

Popis toho, jak provést ověření podpisu je mimo rámec tohoto dokumentu. Mnoho opensourcové knihovny jsou k dispozici pro případ s tím, že to potřebujete.

### <a name="validate-the-claims"></a>Ověřit deklarace identity
Když aplikaci nebo API obdrží ID token, má také provést několik kontrol proti deklarace identity v ID tokenu. Ty zahrnují, ale nejsou omezeny na:

* **Cílovou skupinu** deklarací identity: tím ověříte, že ID token mělo má být poskytnut do vaší aplikace.
* **Neplatný před** a **čas vypršení platnosti** deklarací identity: tyto ověřte, zda nevypršela platnost tokenu ID.
* **Vystavitele** deklarací: Tato ověřuje, zda byl token vydán do aplikace Azure AD.
* **Hodnotu nonce**: Toto je strategie pro snížení rizika útoku opětovného přehrání tokenu.

Úplný seznam ověření proveďte vaší aplikace, najdete v části [OpenID Connect specifikace](https://openid.net). Podrobnosti o očekávaných hodnot pro tyto deklarace identity jsou zahrnuty v předchozím [token části](#types-of-tokens).  

## <a name="token-lifetimes"></a>Doby života tokenů
Následující token životnosti jsou k dispozici pro další vašeho vědomí. Můžou vám pomoct při vývoji a ladění aplikací. Všimněte si, že vaše aplikace nemá zapisovat očekávat některé z těchto životnosti nezměnila. Mohou a změní. Další informace o [přizpůsobení tokenu životnosti](active-directory-b2c-token-session-sso.md) v Azure AD B2C.

| Podpisový | Doba platnosti | Popis |
| --- | --- | --- |
| ID tokeny |Jedna hodina. |ID tokeny jsou obvykle platný jednu hodinu. Webové aplikace můžete použít tato doba platnosti udržovat svůj vlastní relací uživatelů (doporučeno). Můžete také dobu platnosti jiné relace. Pokud aplikace potřebuje k získání tokenu nové ID, jednoduše potřebuje k vytvoření nové žádosti o přihlášení do služby Azure AD. Pokud má uživatel relace platná prohlížeče s Azure AD, tento uživatel nemusí nutné znovu zadat přihlašovací údaje. |
| Obnovovacích tokenů |Až 14 dní |Jeden obnovovací token je platný pro maximálně 14 dní. Token obnovení však můžete stane neplatnou kdykoli z několika příčin. Aplikace by měly být nadále pokoušet použít obnovovací token, dokud žádost skončí s chybou, nebo dokud aplikace nahradí nový token obnovení. Obnovovací token se může stát také neplatné, pokud od uživatele naposledy zadali přihlašovací údaje, uplynulo 90 dní. |
| Autorizačních kódů |Pět minut |Kódy ověřování jsou záměrně krátkodobou. Jejich by měl být uplatněn okamžitě přístupové tokeny, tokeny typu ID nebo tokeny obnovení při příjmu. |

