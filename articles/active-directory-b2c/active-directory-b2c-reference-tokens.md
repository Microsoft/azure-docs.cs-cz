---
title: Přehled tokenů – Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o tokeny použité v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 11361bc6ab75e873e1b4081dcfc6492abc093b54
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680260"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Přehled tokenů v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD) B2C vyzařuje několik typů tokeny zabezpečení, které zpracovává každý [tok ověřování](active-directory-b2c-apps.md). Tento dokument popisuje formátu, vlastnosti zabezpečení a obsah každého typu token.

## <a name="token-types"></a>Typy tokenů

Azure AD B2C podporuje [protokoly OAuth 2.0 a OpenID Connect](active-directory-b2c-reference-protocols.md), které využívá tokeny pro ověřování a zabezpečený přístup k prostředkům. Všechny tokeny, jež jsou používány v Azure AD B2C jsou [webové tokeny JSON (Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) , které obsahují výrazy informace o nositele a předmětem token.

Při komunikaci s Azure AD B2C se používají následující tokeny:

- *ID tokenu* -tokenů JWT A obsahující deklarace identity, můžete použít k identifikaci uživatelů ve vaší aplikaci. Tento token je bezpečně poslaná požadavků HTTP pro komunikaci mezi dvě komponenty stejné aplikace nebo služby. Deklarace identity v tokenu ID můžete použít podle svých potřeb. Běžně se používají k zobrazení informací o účtu nebo je rozhodnutí o řízení přístupu v aplikaci. Jsou podepsané tokeny typu ID, ale nejsou šifrovány. Pokud vaše aplikace nebo API obdrží ID token, musí ověřit podpis prokázat, že je token platná. Vaše aplikace nebo rozhraní API musí také ověřit několik deklarací identity v tokenu k prokázání, že se jedná o platný. V závislosti na požadavcích scénáře deklarací ověřený aplikace se může lišit, ale vaše aplikace musí provádět některé běžné ověřování deklarací identity v každé situaci.
- *Přístupový token* -tokenů JWT A obsahující deklarace identity, můžete použít k identifikaci udělená oprávnění k rozhraním API. Přístupové tokeny jsou podepsané, ale nejsou šifrovány. Přístupové tokeny se používají k zajištění přístupu k rozhraní API a prostředků servery.  Přístupový token přijetí rozhraní API musí ověřit podpis prokázat, že je token platná. Vaše rozhraní API musí také ověřit několik deklarací identity v tokenu k prokázání, že se jedná o platný. V závislosti na požadavcích scénáře deklarací ověřený aplikace se může lišit, ale vaše aplikace musí provádět některé běžné ověřování deklarací identity v každé situaci.
- *Obnovovací token* -obnovovací tokeny se používají k získání nové tokeny typu ID a přístupové tokeny ve toku OAuth 2.0. Poskytují aplikace dlouhodobé přístup k prostředkům jménem uživatelů bez nutnosti interakce s těmito uživateli. Obnovovací tokeny jsou neprůhledné pro vaši aplikaci. Jsou vydány službou Azure AD B2C a mohou být zkontrolovány a interpretovat jenom pomocí Azure AD B2C. Jsou dlouhodobé, ale vaše aplikace, neměl by být zapsaný s předpokladem, že bude trvat obnovovací token pro určité časové období. Tokeny obnovení může být neplatné v daném okamžiku provádějí pro celou řadu důvodů. Jedině pro vaši aplikaci vědět, jestli je platný token obnovení je pokus o uplatněte ho tak, že žádosti o token na Azure AD B2C. Když uplatníte obnovovací token pro nový token, zobrazí se v odpovědi tokenu nového tokenu obnovení. Uložení nového tokenu obnovení. Nahrazuje token obnovení, který jste dříve použili v požadavku. Tato akce pomáhá zaručit, že obnovovací tokeny jsou dál platné pro co nejdéle. 

## <a name="endpoints"></a>Koncové body

A [registrované aplikace](tutorial-register-applications.md) přijímá tokeny a komunikuje s Azure AD B2C pomocí zasílání požadavků na tyto koncové body:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Tokeny zabezpečení, které vaše aplikace obdrží z Azure AD B2C můžou pocházet z `/authorize` nebo `/token` koncových bodů. Když jsou tokeny typu ID získaných z `/authorize` koncového bodu, se provádí pomocí [implicitní tok](active-directory-b2c-reference-spa.md), což se často používá pro uživatele se přihlaste do webové aplikace založené na jazyce javascript. Když jsou tokeny typu ID získaných z `/token` koncového bodu, se provádí pomocí [toku kódu](active-directory-b2c-reference-oidc.md), který udržuje token skryté z prohlížeče.

## <a name="claims"></a>Deklarace identity

Pokud používáte Azure AD B2C, budete mít přesnou kontrolu nad obsahem z vašich tokenů. Můžete nakonfigurovat [toky uživatelů](active-directory-b2c-reference-policies.md) a [vlastní zásady](active-directory-b2c-overview-custom.md) odesílat určité sady dat uživatele v deklaracích identity, které jsou požadovány pro vaši aplikaci. Tato deklarace identity můžou obsahovat například standardní vlastnosti **displayName** a **emailAddress**. Aplikace můžete použít tyto deklarace mohla bezpečně ověřit uživatele a požadavků. 

Deklarace identity v tokenech ID nebudou zobrazeny v libovolném pořadí. Nová deklarace identity může být zavedeno v tokeny typu ID kdykoli. Vaše aplikace by neměl přerušit zavedeném nových deklarací identity. Můžete také zahrnout [vlastní uživatelské atributy](active-directory-b2c-reference-custom-attr.md) ve vaší deklarací identity.

V následující tabulce jsou uvedeny deklarace identity, můžete očekávat v tokeny typu ID a přístupové tokeny vydané službou Azure AD B2C.

| Název | Deklarovat | Příklad hodnoty | Popis |
| ---- | ----- | ------------- | ----------- |
| Cílová skupina | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifikuje zamýšlený příjemce tokenu. Cílová skupina pro Azure AD B2C je ID aplikace. Vaše aplikace by měl ověřit tuto hodnotu a odmítnout token, pokud neodpovídá. Cílové skupiny je synonymní s prostředkem. |
| Vystavitel | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifikuje službu tokenů zabezpečení (STS), který vytvoří a vrátí token. Také určuje adresář, ve kterém byl uživatel ověřený. Vaše aplikace měla ověřit deklarace identity vystavitele tak, abyste měli jistotu, že token pochází od vhodný koncový bod. |
| Vystaveno | `iat` | `1438535543` | Čas, kdy byl vydán token, jsou reprezentovány v unixovém čase. |
| Čas vypršení platnosti | `exp` | `1438539443` | Čas, kdy token, který se stane neplatným, reprezentovány v unixovém čase. Aplikace by měla používat tato deklarace identity k ověření platnosti dobu životnosti tokenu. |
| Neplatný před | `nbf` | `1438535543` | Čas, kdy začne platit, token reprezentovány v unixovém čase. Tentokrát je obvykle stejné jako čas, kdy byl token vydán. Aplikace by měla používat tato deklarace identity k ověření platnosti dobu životnosti tokenu. |
| Verze | `ver` | `1.0` | Verze token ID, jak jsou definovány pomocí Azure AD B2C. |
| Kód hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Kód hash zahrnuté v tokenu ID jenom v případě, že token, který vydává spolu s autorizačního kódu OAuth 2.0. Hodnota hash kódu slouží k ověření pravosti autorizační kód. Další informace o tom, jak toto ověření proveďte najdete v tématu [OpenID Connect specifikace](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hodnota hash tokenu přístupu | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Hash tokenu přístupu zahrnuté v tokenu ID jenom v případě, že token, který vydává spolu s přístupového tokenu OAuth 2.0. Algoritmus hash tokenu přístupu slouží k ověření pravosti tokenu přístupu. Další informace o tom, jak toto ověření proveďte najdete v tématu [specifikace OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Hodnota Nonce | `nonce` | `12345` | Hodnotu nonce je strategie zmírnit útoky opětovného přehrání tokenu. Aplikace může zadat hodnotu nonce v žádost o autorizaci s použitím `nonce` parametr dotazu. Hodnota je zadat v požadavku je vygenerován bez jakýchkoli úprav v `nonce` pouze ID tokenu deklarací identity. Tato deklarace identity umožňuje vaší aplikaci můžete ověřit hodnoty na hodnotu zadanou v požadavku. Toto ověření během procesu ověření tokenu ID má vaše aplikace provést. |
| Subjekt | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Objekt zabezpečení, o tom, které token vyhodnocuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze přiřadit nebo znovu použít. Slouží k provádění kontroly autorizace bezpečně, třeba když se používá token pro přístup k prostředku. Ve výchozím nastavení deklarace identity subjektu se vyplní ID objektu uživatele v adresáři. |
| Informace o třídě kontext ověřování | `acr` | Neuvedeno | Použít pouze se staršími zásadami. |
| Rozhraní framework zásady důvěryhodnosti | `tfp` | `b2c_1_signupsignin1` | Název zásad, která byla použita k získání tokenu ID. |
| Čas ověřování | `auth_time` | `1438535543` | Čas, kdy uživatel naposledy zadali přihlašovací údaje, je vyjádřena v unixovém čase. |
| Rozsah | `scp` | `Read`| Oprávnění udělená prostředek pro přístupový token. Více udělená oprávnění jsou oddělené mezerou. |
| Autorizované stran | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | **ID aplikace** klientské aplikace, který inicioval žádost. |

## <a name="configuration"></a>Konfigurace

Následující vlastnosti se používají pro [správa životnosti tokenů zabezpečení](configure-tokens.md) vygenerován pomocí Azure AD B2C:

- **Přístup k tokenu a tokenu ID životnost (minuty)** – dobu života nosného tokenu OAuth 2.0, který se používá k získání přístupu k chráněnému prostředku. Výchozí hodnota je 60 minut. (Včetně) minimální hodnota je 5 minut. (Včetně) maximální hodnota je 1440 minut.

- **Doba života obnovovacího tokenu (dny)** – maximální dobu, před kterým obnovovací token slouží k získání nového přístupového tokenu nebo tokenu ID. Časové období platí i pro získání nového tokenu obnovení, pokud vaše aplikace získala `offline_access` oboru. Výchozí hodnota je 14 dní. Minimální (včetně) je jeden den. Maximální počet (včetně) je 90 dní.

- **Posuvné okno doba života obnovovacího tokenu (dny)** – po uplynutí tohoto času období uživatel bude muset donutit, bez ohledu na období platnosti posledního obnovovací token získaný aplikace. To lze zadat pouze pokud přepínač nastavený na **ohraničená**. Musí být větší než nebo rovna hodnotě **aktualizace životnost tokenu (dny)** hodnotu. Pokud přepínač nastavený na **nástrojů Unbounded**, nemůže poskytnout konkrétní hodnotu. Výchozí hodnota je 90 dní. Minimální (včetně) je jeden den. Maximální počet (včetně) je 365 dnů.

Tyto případy použití jsou povolené použití těchto vlastností:

- Umožní uživateli se zachovat po neomezenou dobu, přihlášení k mobilní aplikaci, jako je uživatel stále aktivní na aplikaci. Můžete nastavit **obnovovací token doba života posuvného okna (dny)** k **nástrojů Unbounded** ve svém toku přihlášení uživatele.
- Splňovat požadavky na dodržování předpisů zabezpečení vaše odvětví a nastavením životností tokenů odpovídající přístup.

Tato nastavení nejsou k dispozici pro toky uživatelů pro resetování hesla. 

## <a name="compatibility"></a>Kompatibilita

Následující vlastnosti se používají pro [Správa kompatibility tokenů](configure-tokens.md):

- **Deklarace identity vystavitele (iss)** – tato vlastnost identifikuje tenanta Azure AD B2C, která token vydala. Výchozí hodnota je `https://<domain>/{B2C tenant GUID}/v2.0/`. Hodnota `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` zahrnuje ID tenanta Azure AD B2C a tok uživatele, která byla použita v žádosti o token. Pokud vaše aplikace nebo knihovna vyžaduje Azure AD B2C má být zajištěn soulad [zjišťování OpenID Connect 1.0 specifikace](https://openid.net/specs/openid-connect-discovery-1_0.html), používejte tuto hodnotu.

- **Deklarace identity subjektu (sub)** – tato vlastnost identifikuje entitu, pro kterou token vyhodnocuje informace. Výchozí hodnota je **ObjectID**, která naplní `sub` deklarací identity v tokenu s ID objektu uživatele. Hodnota **nepodporuje** je k dispozici pouze z důvodů zpětné kompatibility. Doporučuje se, že přejdete na **ObjectID** Jakmile máte možnost.

- **Deklarace identity představující ID zásady** – tato vlastnost identifikuje typ deklarace identity, do které se vyplní název zásady použitý v požadavku na token. Výchozí hodnota je `tfp`. Hodnota `acr` je k dispozici pouze z důvodů zpětné kompatibility.

## <a name="pass-through"></a>Průchod

Při spuštění cesty uživatele Azure AD B2C obdrží přístupový token od zprostředkovatele identity. Azure AD B2C používá tento token k načtení informací o uživateli. Můžete [povolit deklarace identity v toku uživatele si](idp-pass-through-user-flow.md) nebo [definovat deklarace identity ve vlastních zásadách](idp-pass-through-custom.md) předat token prostřednictvím aplikace, které zaregistrujete v Azure AD B2C. Aplikace musí používat [tok uživatele v2](user-flow-versions.md) využívat předat token jako deklarace identity.

Azure AD B2C momentálně podporuje pouze předávání přístupový token OAuth 2.0 zprostředkovatelů identity, mezi které patří službě Facebook a Google. Pro všechny ostatní poskytovatele identity se vrátí prázdná deklarace identity. 

## <a name="validation"></a>Ověření

Ověřit token, by měla vaše aplikace provádět podpis a deklarace identity tokenu. Řada open source knihovny jsou dostupné pro ověřování tokeny Jwt, v závislosti na vašem preferovaném jazyce. Doporučuje se, že můžete prozkoumat tyto možnosti spíše než implementovat vlastní logiku ověřování.

### <a name="validate-signature"></a>Ověření podpisu

Token JWT obsahuje tři segmentů *záhlaví*, *tělo*a *podpis*. Segment podpis slouží k ověření pravosti tokenu tak, aby důvěryhodné aplikace. Azure AD B2C tokeny jsou podepsány pomocí asymetrického šifrování standardních algoritmů, například RSA 256. 

Záhlaví token, který obsahuje informace o metodu šifrování a klíče použitý k podpisu tokenu:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Hodnota **alg** deklarace identity je algoritmus, který se použil k podepsání token. Hodnota **dětský** deklarace identity je veřejný klíč, který se použil k podepsání token. V každém okamžiku Azure AD B2C se můžete přihlásit token pomocí některého z sada párů veřejného a privátního klíče. Azure AD B2C bude pravidelně otáčet možné sadu klíčů. Vaše aplikace by měla napsané tak, aby tyto klíče změny automaticky. Přiměřené intervalech a kontrolují dostupnost aktualizací pro veřejné klíče používané službou Azure AD B2C je každých 24 hodin.

Azure AD B2C má koncový bod metadat OpenID Connect. Pomocí tohoto koncového bodu, aplikace může požadovat informace o Azure AD B2C v době běhu. Tyto informace zahrnují koncových bodů, obsah tokenu a tokenu podpisových klíčů. Svého tenanta Azure AD B2C obsahuje dokument metadat JSON pro jednotlivé zásady. Dokument metadat je objekt JSON, který obsahuje několik užitečné údaje. Obsahuje metadata **jwks_uri**, což dává umístění sady veřejných klíčů, které se používají k podepisování tokenů. Že umístění je tady, ale to je nejlepší dynamicky načíst umístění pomocí dokument metadat a analýze **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
Dokument JSON umístěný na této adrese URL obsahuje všechny údaje o veřejném klíči používá v určitém okamžiku. Vaše aplikace může používat `kid` deklarací identity v hlavičce tokenů JWT můžete vybrat v dokumentu JSON, který se používá k podepsání konkrétní token veřejného klíče. Ověření podpisu je pak můžete provádět pomocí správný veřejný klíč a označený algoritmus.

Dokument metadat pro `B2C_1_signupsignin1` zásad `contoso.onmicrosoft.com` tenanta se nachází na:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Chcete-li zjistit, jaké zásady se použil k podepsání token (a kam jít o metadata), máte dvě možnosti. Nejprve je součástí názvu zásady `acr` deklarací identity v tokenu. Deklarace mimo tělo tokenů JWT můžete analyzovat podle base-64 dekódování text a deserializaci řetězec JSON, který výsledky. `acr` Deklarace identity je název zásad, která byla použita k vydání tokenu. Další možností je určený ke kódování zásady v hodnotě `state` parametr vydávat žádosti, a pak ji k určení, jaké zásady se použil dekódovat. Některé z metod je platný.

Popis toho, jak provádět ověření podpisu je mimo rámec tohoto dokumentu. Řada open source knihovny jsou dostupné při ověření tokenu.

### <a name="validate-claims"></a>Ověřování deklarací identity

Pokud vaše aplikace nebo API obdrží ID token, má také provést několik kontrol před deklarací identity v tokenu ID. Následující deklarace by měly být porovnány:

- **Cílová skupina** – ověřuje, že ID token měla předávat do vaší aplikace.
- **Ne dříve než** a **čas vypršení platnosti** – ověřuje, že nevypršela platnost tokenu ID.
- **Vystavitel** – ověřuje, zda byl token vydán pro vaši aplikaci pomocí Azure AD B2C.
- **Hodnota Nonce** -strategii omezení rizik útoků opětovného přehrání tokenu.

Úplný seznam ověření by měla vaše aplikace provádět, najdete [OpenID Connect specifikace](https://openid.net).  

## <a name="next-steps"></a>Další postup

Další informace o tom, jak [použití přístupových tokenů](active-directory-b2c-access-tokens.md).

