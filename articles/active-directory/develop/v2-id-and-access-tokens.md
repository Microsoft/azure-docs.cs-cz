---
title: Azure Active Directory v2.0 tokeny odkaz | Dokumentace Microsoftu
description: Typy tokenů a deklaracích identity, protože ho vygeneroval koncového bodu Azure AD v2.0
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 7fcc39aed1599c1fa928736453082cbce6d7e27e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581281"
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Azure Active Directory v2.0 tokeny reference
Koncový bod Azure Active Directory (Azure AD) verze 2.0 vysílá několik typů tokeny zabezpečení v každém [tok ověřování](active-directory-v2-flows.md). Tento odkaz popisuje formátu, vlastnosti zabezpečení a obsah každého typu token.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny scénáře Azure Active Directory a funkce. Pokud chcete zjistit, zda by měl použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>Typy tokenů
Koncový bod verze 2.0 podporuje [protokol autorizace OAuth 2.0](active-directory-v2-protocols.md), který používá přístupové tokeny a obnovovacích tokenů. Koncový bod verze 2.0 podporuje také ověřování a přihlásit se přes [OpenID Connect](active-directory-v2-protocols.md). Třetí typ tokenu, ID token OpenID Connect zavádí. Každý z těchto prvků je vyjádřena jako *nosiče* token.

Nosný token je token zjednodušené zabezpečení, která uděluje přístup nosiče k chráněnému prostředku. Nositele je každá strana, která může představovat token. I když může strana musí ověřování pomocí Azure AD pro příjem nosný token, pokud nejsou kroky k zabezpečení během přenosu a uložení tokenu, můžete zachytit a používat nežádoucí osobou. Některé tokeny zabezpečení mají předdefinovaný mechanismus neoprávněnými osobami zabránit v jejich používání, ale nechcete nosné tokeny. Nosné tokeny musí přenášet v zabezpečený kanál, jako je zabezpečení transportní vrstvy (HTTPS). Pokud nosný token je přenesen bez tohoto typu zabezpečení, škodlivý stran použít "útok man-in-the-middle" získat token a používat ho pro neoprávněný přístup k chráněnému prostředku. Stejné zásady zabezpečení platí při ukládání nebo ukládání do mezipaměti nosné tokeny pro pozdější použití. Vždy zajistěte, aby aplikace bezpečně přenáší a ukládají nosné tokeny. Další informace o zabezpečení pro nosné tokeny, naleznete v tématu [5 část dokumentu RFC 6750](http://tools.ietf.org/html/rfc6750).

Mnoho tokeny vystavené službou koncovým bodem v2.0 jsou implementovány jako webové tokeny JSON (Jwt). Token JWT je kompaktní a adresu URL bezpečné způsob přenosu informací mezi dvěma stranami. Informace v token JWT se volá *deklarace identity*. Je kontrolní výraz informací o nosiče a předmět tokenu. Deklarace identity v token JWT jsou objekty zápis JSON (JavaScript Object), které jsou zakódovány a serializovat pro přenos. Protože tokeny Jwt vydaného koncovým bodem v2.0 jsou podepsané, nikoli však šifrován, můžete snadno kontrolovat obsah token JWT pro účely ladění. Další informace o tokeny Jwt, najdete v článku [JWT specifikace](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokeny typu ID
ID tokenu je formulář tokenu zabezpečení přihlášení, který vaše aplikace obdrží při pomocí provádí ověřování [OpenID Connect](active-directory-v2-protocols.md). ID tokeny jsou reprezentovány ve formě [tokeny Jwt](#types-of-tokens), a které obsahují deklarace identity, které můžete použít k přihlášení uživatele do vaší aplikace. Můžete použít deklarace identity v tokenu ID různými způsoby. Správci obvykle používají tokeny typu ID, k zobrazení informací o účtu nebo je rozhodnutí o řízení přístupu v aplikaci. Koncový bod v2.0 vydá pouze jeden typ ID token, který je konzistentní sadu deklarací identity, bez ohledu na typ uživatele, který je přihlášen. Formát a obsah tokenů ID jsou stejné pro osobní uživatele účtu Microsoft a pracovní nebo školní účty.

V současné době tokeny typu ID jsou podepsané, nikoli však šifrován. Když aplikace obdrží ID token, musíte [ověřit podpis](#validating-tokens) potvrzení pravosti tokenu a ověření několik deklarací identity v tokenu prokázat, že jeho platnost. Deklarace ověřené aplikací se liší v závislosti na požadavcích scénáře, ale vaše aplikace musí provést některé [běžné deklarace identity ověření](#validating-tokens) v každé situaci.

Poskytujeme vám úplné podrobnosti o deklarace identity v tokenech ID v následujících částech, kromě ukázkový token ID. Všimněte si, že deklarace identity v tokenech ID nebudou zobrazeny v určitém pořadí. Navíc nových deklarací identity je možné vytvářet do ID tokenů v každém okamžiku. Vaše aplikace by neměl přerušit při zavedení nových deklarací identity. Následující seznam obsahuje deklarace, které vaše aplikace právě spolehlivě interpretovat. Můžete najít další podrobnosti najdete [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Ukázkový token ID
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> Postupem je zkontrolovat deklarace identity v tokenu ID vzorku, vložte ukázkový token ID do [jwt.ms](http://jwt.ms/).
>
>

#### <a name="claims-in-id-tokens"></a>Deklarace identity v tokenech ID
| Název | Deklarovat | Příklad hodnoty | Popis |
| --- | --- | --- | --- |
| Cílová skupina |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |Identifikuje zamýšlený příjemce tokenu. V tokeny typu ID je cílová skupina ID vaší aplikace, přiřazené vaší aplikaci v portálu pro registraci aplikací společnosti Microsoft. Vaše aplikace by měl ověřit tuto hodnotu a odmítnout token, pokud hodnota se neshoduje. |
| Vystavitel |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Identifikuje službu tokenů zabezpečení (STS), který vytvoří a vrátí token a tenanta Azure AD, ve kterém byl uživatel ověřený. Vaše aplikace by měla ověření vystavitele deklarace identity k zajištění, že token pochází z koncového bodu v2.0. Je také používali část GUID deklarace identity k omezit počet klientů, které se můžete přihlásit k aplikaci. Identifikátor GUID, který označuje, že uživatel je příjemce uživatele z účtu Microsoft je `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Vystaveno |`iat` |`1452285331` |Čas, kdy byl vydán token, jsou reprezentovány v unixovém čase. |
| Čas vypršení platnosti |`exp` |`1452289231` |Čas, kdy token, který se stane neplatným, reprezentovány v unixovém čase. Vaše aplikace by pomocí této deklarace identity k ověření platnosti dobu životnosti tokenu. |
| Neplatný před |`nbf` |`1452285331` |Čas, kdy začne platit, token reprezentovány v unixovém čase. Je to obvykle stejné jako čas vystavení. Vaše aplikace by pomocí této deklarace identity k ověření platnosti dobu životnosti tokenu. |
| verze |`ver` |`2.0` |Verze token ID, jak jsou definovány službou Azure AD. Pro koncový bod verze 2.0, je hodnota `2.0`. |
| ID tenanta |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Identifikátor GUID, který představuje jejímž je uživatel z tenanta Azure AD. Pro pracovní a školní účty je identifikátor GUID ID neměnné tenanta organizace, které tento uživatel patří do. Pro osobní účty, je hodnota `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` Oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. |
| Kód hash |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Hodnota hash kódu je součástí tokeny typu ID, pouze v případě, že je vydaný ID token s autorizačního kódu OAuth 2.0. Slouží k ověření pravosti autorizační kód. Podrobnosti o provedení tohoto ověření naleznete v tématu [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html). |
| Hodnota hash tokenu přístupu |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Přístup, který je zahrnuta hodnota hash tokenu v ID tokeny, pouze pokud ID token vydaný s přístupovým tokenem OAuth 2.0. Slouží k ověření pravosti tokenu přístupu. Podrobnosti o provedení tohoto ověření naleznete v tématu [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html). |
| Hodnota Nonce |`nonce` |`12345` |Hodnota nonce je strategii pro minimalizaci útoků opětovného přehrání tokenu. Vaše aplikace může určit hodnotu nonce v žádost o autorizaci s použitím `nonce` parametr dotazu. Hodnota je zadat v požadavku je vygenerován v tokenu ID `nonce` deklarace identity, bez jakýchkoli úprav. Aplikace můžete ověřit hodnoty s hodnotou zadanou v požadavku, která přidruží konkrétní ID tokenu relace aplikace. Vaše aplikace by měl provést toto ověření během procesu ověření tokenu ID. |
| jméno |`name` |`Babe Ruth` |Deklarace názvu poskytuje čitelné hodnotu, která identifikuje předmětem token. Hodnota nemusí být jedinečný, je proměnlivé a je navržena tak, aby se dá použít jenom pro účely zobrazení. `profile` Oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. |
| e-mail |`email` |`thegreatbambino@nyy.onmicrosoft.com` |Primární e-mailovou adresu, přidružené k účtu uživatele, pokud existuje. Jeho hodnota je měnitelné a může v průběhu času měnit. `email` Oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. |
| upřednostňované uživatelské jméno |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |Primární uživatelské jméno, který reprezentuje uživatele v koncový bod verze 2.0. Může to být e-mailovou adresu, telefonní číslo nebo obecný uživatelské jméno bez zadaného formátu. Jeho hodnota je měnitelné a může v průběhu času měnit. Protože je proměnlivá, tato hodnota se nesmí používat pro autorizační rozhodnutí. `profile` Oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. |
| Předmět |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Objekt zabezpečení, o tom, které token vyhodnocuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze přiřadit nebo znovu použít. Můžete použít k provedení kontroly autorizace bezpečně, třeba když se používá token pro přístup k prostředku a můžete použít jako klíč v tabulkách databáze. Protože předmět je vždy k dispozici v tokenech, problémy s Azure AD, doporučujeme použít tuto hodnotu v systému pro obecné účely autorizace. Předmět je však identifikátor pairwise – je jedinečné ID konkrétní aplikace. Proto pokud jeden uživatel zaregistruje do dvou různých aplikací s využitím dva identifikátory ID jiného klienta, na aplikace, které se zobrazí dvě různé hodnoty pro deklarace identity subjektu. To může nebo nemusí být požadovaných v závislosti na požadavcích vaší architektury a ochrana osobních údajů. |
| ID objektu |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Neměnné identifikátor objektu v Microsoft systém identit, v tomto případě uživatelský účet. To lze použít také k provedení kontroly autorizace bezpečně a jako klíč v tabulkách databáze. Toto ID jednoznačně identifikuje uživatele v aplikacích – dva různé aplikace přihlášení stejného uživatele se zobrazí v stejnou hodnotu `oid` deklarací identity. To znamená, že ho lze použít při zadávání dotazů k online službám Microsoftu, jako je například Microsoft Graphu. Vrátí toto ID jako Microsoft Graph `id` vlastnost pro daný uživatelský účet. Protože `oid` umožňuje více aplikací ke koordinaci uživatelů, `profile` oboru se vyžaduje aby bylo možné dostávat tato deklarace identity. Všimněte si, že pokud jeden uživatel existuje v několika tenantech, uživatel bude obsahovat jiný objekt ID v jednotlivých tenantů – jsou považovány za různé účty, i když se uživatel přihlašuje každý účet pomocí stejných přihlašovacích údajů. |

### <a name="access-tokens"></a>Přístupové tokeny

Koncový bod verze 2.0 umožňuje aplikacím jiných výrobců, které jsou registrované v Azure AD a vystavovat tokeny přístupu k zabezpečeným prostředkům, jako je například webová rozhraní API. Další informace o nastavení aplikace vystavovat tokeny přístupu, najdete v tématu [postup registrace aplikace s koncovým bodem v2.0](quickstart-v2-register-an-app.md). Při registraci aplikace s koncovým bodem v2.0, vývojář může určit úrovně přístupu, volá **obory**, pro které přístup může být vydaný tokeny. Například **calendars.read** obor definované v rozhraní Microsoft Graph API uděluje oprávnění ke čtení kalendáře uživatele. Když aplikace obdrží přístupový token z koncového bodu v2.0, musíte ověřit podpis tokenu, Vystavitel, cílové skupiny, čas vypršení platnosti a další deklarace identity v závislosti na vašem scénáři. 

Když požádáte o přístupový token z koncového bodu v2.0, koncový bod v2.0 také vrátí metadata o přístupový token pro vaše aplikace bude moct používat. Tyto informace zahrnují čas vypršení platnosti přístupového tokenu a oborů, pro které je platný. Vaše aplikace používá tato metadata ukládání do mezipaměti inteligentních tokenů přístupu bez nutnosti parsovat otevřít přístupový token, samotného.

### <a name="refresh-tokens"></a>Obnovovacích tokenů
Aktualizovat tokeny jsou tokeny zabezpečení, které vaše aplikace může použít k získání nových přístupových tokenů ve toku OAuth 2.0. Aplikace můžete použít obnovovací tokeny k dosažení dlouhodobé přístup k prostředkům jménem uživatele bez nutnosti interakci s uživatelem.

Obnovovací tokeny jsou více prostředků. Pro přístupové tokeny pro úplně jiných prostředků je možné uplatnit obnovovací token přijatých během žádosti o token pro jeden prostředek.

Pokud chcete dostávat aktualizace v odpovědi tokenu, vaše aplikace musí požádat o a udělit `offline_access` oboru. Další informace o `offline_access` oboru, najdete v článku [vyjádření souhlasu a obory](v2-permissions-and-consent.md) článku.

Obnovovacích tokenů jsou a vždy budou, stane zcela neprůhledný do vaší aplikace. Se vydal koncového bodu Azure AD v2.0 a můžete být zkontroloval a interpretovat koncový bod verze 2.0. Jsou dlouhodobé, ale neměl by být zapsaný vaší aplikace můžete očekávat, že bude trvat obnovovací token pro libovolné časové období. Tokeny obnovení může být neplatné v daném okamžiku provádějí z různých důvodů – podrobnosti najdete v tématu [token zrušení](v1-id-and-access-tokens.md#token-revocation). Jedině pro vaši aplikaci vědět, jestli je platný token obnovení je pokus o uplatněte ho tak, že žádosti o token na koncový bod verze 2.0.

Když uplatníte obnovovací token pro nový přístupový token (a pokud vaše aplikace měla udělen `offline_access` obor), obdržíte nového tokenu obnovení v odpovědi tokenu. Uložte nově vydaný obnovovací token nahradit, který jste použili v požadavku. Zaručí se tak, že obnovovací tokeny jsou dál platné pro co nejdéle.

## <a name="validating-tokens"></a>Ověřování tokenů
V současné době je ověřování pouze tokenů, které vaše aplikace by mělo stačit provést ověřování ID tokenů. Ověřit ID token, by měla vaše aplikace ověřit ID token podpisu a deklarace identity v tokenu ID.

<!-- TODO: Link --> Společnost Microsoft poskytuje knihovny a ukázky kódu, které ukazují, jak snadno zpracovávat ověřování tokenů. V následujících částech popisujeme základního procesu. Několik open source knihoven třetích stran jsou také k dispozici pro ověřování tokenů JWT. Existuje nejméně jedna knihovna možnost pro téměř každou platformu a jazyk.

### <a name="validate-the-signature"></a>Ověření podpisu
Token JWT obsahuje tři segmenty, které jsou odděleny `.` znak. První segment se označuje jako *záhlaví*, je druhý segment *tělo*, a třetí segment *podpis*. Segment podpis slouží k ověření pravosti tokenu ID tak, aby může být důvěřuje vaší aplikace.

ID tokeny jsou podepsány pomocí asymetrického šifrování standardních algoritmů, například RSA 256. Záhlaví ID tokenu má informace o metodě a šifrovací klíče použitý k podpisu tokenu. Příklad:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

`alg` Deklarace identity označuje algoritmus, který se použil k podepsání token. `kid` Deklarace identity označuje veřejný klíč, který se použil k podepsání token.

Koncový bod v2.0 podepíše ID a přístupové tokeny pomocí některého z konkrétní sadu párů veřejného a privátního klíče. Koncový bod verze 2.0 pravidelně otočí možné sadu klíčů, takže vaše aplikace by měla napsané tak, aby tyto klíče změny automaticky. Přiměřené intervalech a kontrolují dostupnost aktualizací pro veřejné klíče používaného koncovým bodem v2.0 je každých 24 hodin.

Můžete získat podpisový klíčových dat, které potřebujete k ověření podpisu pomocí OpenID Connect dokumentů metadat umístěn na:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Vyzkoušejte adresu URL v prohlížeči.

Tento dokument metadat je objekt JSON, který má několik užitečné údaje, jako je například umístění různých koncových bodů požadovaných pro ověřování OpenID Connect. Dokument obsahuje také *jwks_uri*, což dává umístění sady veřejného klíče používané k podepisování tokenů. Dokument JSON na jwks_uri má všechny údaje o veřejném klíči, který se právě používá. Vaše aplikace může používat `kid` deklarací identity v hlavičce JWT k výběru, který veřejný klíč v tomto dokumentu se použil k podepsání token. Potom provede ověření podpisu pomocí správný veřejný klíč a označený algoritmus.

> [!NOTE]
> `x5t` Deklarací identity je zastaralý koncový bod verze 2.0. Doporučujeme použít `kid` deklarace identity k ověření tokenu.

Provádí se ověření podpisu je mimo rámec tohoto dokumentu. Mnoho knihoven open source jsou k dispozici vám s tím pomůžou.

### <a name="validate-the-claims"></a>Ověřit deklarace identity
Když aplikace obdrží token ID při přihlášení uživatele, má také provést několik kontroly před deklarací identity v tokenu ID. Patří mezi ně jsou mimo jiné tyto:

* **Cílová skupina** deklarace identity, chcete-li ověřit, že ID token měla předávat do aplikace
* **Ne dříve než** a **čas vypršení platnosti** deklarací, chcete-li ověřit, že nevypršela platnost tokenu ID
* **Vystavitel** deklarace identity, chcete-li ověřit, zda byl token vydán do vaší aplikace koncovým bodem v2.0
* **Hodnota Nonce**, jako token znovu přehrát omezení rizik útoků

Úplný seznam ověření deklarace identity, která by měla aplikace provádět, najdete v článku [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Podrobnosti o očekávané hodnoty pro tyto deklarace jsou součástí [tokeny typu ID](# ID tokens) oddílu.

## <a name="token-lifetimes"></a>Životnost tokenů
Nabízíme následující životností tokenů pouze pro vaši informaci. Informace vám může pomoct při vývoji a ladění aplikací. Můžete očekávat některé z těchto životnosti zůstal neměnný, neměl by být zapsaný vašich aplikací. Token doby života může a bude kdykoli změnit.

| Podpisový | Doba života | Popis |
| --- | --- | --- |
| Tokeny typu ID (pracovní nebo školní účty) |1 hodina |Tokeny typu ID obvykle platí za 1 hodinu. Webové aplikace můžete použít tento stejný životnost ke správě své vlastní relaci s uživatele (doporučeno), nebo můžete nastavit životnost úplně jiné relace. Pokud vaše aplikace musí získat nové ID tokenu, je potřeba vytvořit nové přihlášení žádost o v2.0 zajistí autorizaci koncového bodu. Pokud má uživatel platné prohlížeče relace s koncovým bodem v2.0, nemusí se uživatel muset znovu zadat své přihlašovací údaje. |
| Tokeny typu ID (osobních účtů) |24 hodin |Tokeny typu ID pro osobní účty obvykle jsou platné po dobu 24 hodin. Webové aplikace můžete použít tento stejný životnost ke správě své vlastní relaci s uživatele (doporučeno), nebo můžete nastavit životnost úplně jiné relace. Pokud vaše aplikace musí získat nové ID tokenu, je potřeba vytvořit nové přihlášení žádost o v2.0 zajistí autorizaci koncového bodu. Pokud má uživatel platné prohlížeče relace s koncovým bodem v2.0, nemusí se uživatel muset znovu zadat své přihlašovací údaje. |
| Přístupové tokeny (pracovní nebo školní účty) |1 hodina |Uvedené v tokenu odpovědi jako součást tokenu metadat. |
| Přístupové tokeny (osobních účtů) |1 hodina |Uvedené v tokenu odpovědi jako součást tokenu metadat. Přístupové tokeny, které jsou vydány jménem osobní účty je možné nakonfigurovat pro různé doby života, ale je typické, 1 hodina. |
| Obnovovacích tokenů (pracovní nebo školní účet) |Až do 14 dnů |Jedno aktualizační token je platný pro maximálně 14 dnů. Token obnovení však může být neplatný kdykoli z různých důvodů, takže vaše aplikace by měly být nadále pokusí použít obnovovací token, dokud se nezdaří, nebo aplikace ho nahradí znaménkem nového tokenu obnovení. Obnovovací token také stává neplatným, pokud to bylo 90 dnů, protože uživatel zadal svoje přihlašovací údaje. |
| Obnovovacích tokenů (osobních účtů) |Až 1 rok |Jedno aktualizační token je platný pro maximálně 1 rok. Obnovovací token však může být neplatný kdykoli z různých důvodů, takže vaše aplikace by měly být nadále pokusí použít obnovovací token, dokud se nezdaří. |
| Autorizační kódy (pracovní nebo školní účty) |10 minut |Autorizační kódy jsou záměrně krátkodobé a by měl být okamžitě uplatnit na přístupové tokeny a obnovovací tokeny při přijetí tokeny. |
| Autorizační kódy (osobních účtů) |5 minut |Autorizační kódy jsou záměrně krátkodobé a by měl být okamžitě uplatnit na přístupové tokeny a obnovovací tokeny při přijetí tokeny. Jsou určeny pro jednorázový autorizační kódy, které jsou vydány jménem osobní účty. |
