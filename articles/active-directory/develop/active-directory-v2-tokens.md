---
title: Azure Active Directory v2.0 tokeny odkaz | Microsoft Docs
description: Typy tokenů a deklaracích identity vysílaných koncového bodu Azure AD v2.0
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
ms.date: 04/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: d7b9ad5c76b0e20a3c58bddcc4947482b237fb8f
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164454"
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Přehled v2.0 tokeny služby Azure Active Directory
Koncový bod v2.0 Azure Active Directory (Azure AD) vysílá několik typů tokeny zabezpečení v každé [tok ověřování](active-directory-v2-flows.md). Tento odkaz popisuje formát, zabezpečení vlastnosti a obsah každého typu token.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny scénáře Azure Active Directory a funkce. Pokud chcete zjistit, zda byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>Typy tokenů
Koncový bod v2.0 podporuje [protokol autorizace OAuth 2.0](active-directory-v2-protocols.md), které používá přístup tokeny a obnovovacích tokenů. Koncový bod v2.0 také podporuje ověřování a přihlásit se přes [OpenID Connect](active-directory-v2-protocols.md). OpenID Connect zavádí třetí typ tokenu, ID token. Všechny tyto tokeny je reprezentován jako *nosiče* tokenu.

Je token nosiče tokenu lightweight zabezpečení, který uděluje přístup nosiče k chráněnému prostředku. Nosiče je jakékoli strany, který může být token. I když může strana musí ověřit s Azure AD pro příjem nosný token, pokud nejsou kroky prováděné zabezpečit během přenosu a uložení tokenu, může být zachyceny a použity nezamýšleným strana. Některé tokeny zabezpečení mají integrované mechanismus zabránit neoprávněným stranám jejich používání, ale nechcete nosné tokeny. Nosné tokeny musí přenosu v zabezpečený kanál, jako je například transport layer security (HTTPS). Pokud bez tento typ zabezpečení je přenesen token nosiče, škodlivý strany využít "útok man-in-the-middle" získat token a používat ho pro neoprávněný přístup k chráněnému prostředku. Při ukládání nebo ukládání do mezipaměti nosné tokeny pro pozdější použití platí stejné zásady zabezpečení. Vždy zajistěte, aby vaše aplikace bezpečně přenáší a ukládá nosné tokeny. Další informace o zabezpečení pro nosné tokeny, najdete v části [RFC 6750 část 5](http://tools.ietf.org/html/rfc6750).

Řadu tokeny vydané koncovým bodem v2.0 jsou implementované jako webové tokeny JSON (Jwt). Token JWT je způsob compact, adresa URL bezpečných k přenosu informací mezi dvěma účastníky. Informace v token JWT se nazývá *deklarace identity*. Je kontrolní výrazy informací o nosiče a předmět tokenu. Deklarace identity v token JWT jsou JavaScript Object Notation (JSON) objektů, které jsou zakódovány a serializovat pro přenos. Protože jsou tokeny Jwt vydaného koncového bodu v2.0 podepsán, ale nejsou šifrovaná, můžete snadno zkontrolovat obsah token JWT pro účely ladění. Další informace o Jwt, najdete v článku [JWT specifikace](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>ID tokeny
ID token je formulář tokenu zabezpečení přihlášení, která vaše aplikace obdrží při ověřování se provádí pomocí [OpenID Connect](active-directory-v2-protocols.md). ID tokeny jsou reprezentovány jako [Jwt](#types-of-tokens), a obsahují deklarace identity, které můžete použít pro přihlášení uživatele k vaší aplikace. Deklarace identity můžete použít v tokenu ID různými způsoby. Správci obvykle používají tokeny typu ID zobrazíte informace o účtu nebo je rozhodnutí o řízení přístupu v aplikaci. Koncový bod v2.0 vydává pouze jeden typ ID token, který má konzistentní sadu deklarací identity, bez ohledu na typ uživatele, který je přihlášený. Formát a obsah ID tokeny jsou stejné pro osobní uživatele účtu Microsoft a pro pracovní nebo školní účty.

V současné době jsou tokeny typu ID podepsán, ale nejsou šifrovaná. Pokud vaše aplikace obdrží ID token, musí [ověřit podpis](#validating-tokens) k potvrzení pravosti tokenu a ověření několik deklarací identity v tokenu k prokázání jeho platnosti. Deklarace identity ověřit aplikací lišit v závislosti na požadavcích scénáře, ale aplikace musí provést některé [běžné ověření deklarace identity](#validating-tokens) v každý scénář.

Můžeme poskytnout úplné podrobnosti o deklaracích identity v tokenech ID v následujících částech, kromě ukázkový token pro ID. Všimněte si, že deklarace identity v tokenech ID nebudou zobrazeny v určitém pořadí. Navíc můžete nových deklarací identity zavedená do tokenů ID kdykoli. Aplikace by neměl přerušení, když jsou zavedené nových deklarací identity. Následující seznam obsahuje deklarace identity, které vaše aplikace aktuálně můžete spolehlivě interpretovat. Můžete najít další podrobnosti najdete [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Ukázkový token pro ID
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> Pro postup, chcete-li prověřit deklarací identity ve ukázkový token pro ID, vložte ukázkový token pro ID do [jwt.ms](http://jwt.ms/).
>
>

#### <a name="claims-in-id-tokens"></a>Deklarace identity v tokenech ID
| Název | Deklarovat | Příklad hodnoty | Popis |
| --- | --- | --- | --- |
| Cílová skupina |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |Identifikuje zamýšlený příjemce tokenu. V ID tokeny cílová skupina je ID aplikace vaší aplikace, které jsou přiřazené vaší aplikaci v portálu pro registraci aplikace Microsoft. Vaše aplikace by měl ověřit tuto hodnotu a odmítnout token, pokud hodnota neodpovídá. |
| Vystavitel |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Identifikuje služby tokenů na zabezpečení (STS), která vytvoří a vrátí token a klient Azure AD, ve kterém byl uživatel ověřený. Aplikace by měl ověřit vystavitele deklarace zajistit, že token pochází od koncového bodu v2.0. Také se by měl používat příslušné část GUID deklarace identity pro omezit počet klientů, které se můžete přihlásit k aplikaci. Identifikátor GUID, který označuje, že uživatel je uživatelem příjemce z účtu Microsoft je `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| vydané v |`iat` |`1452285331` |Čas, kdy byl token vydán, reprezentované v čase epoch. |
| čas vypršení platnosti |`exp` |`1452289231` |V čase epoch reprezentované okamžikem, kdy se token platný. Aplikace by měl ověřit správnost dobu životnosti tokenu pomocí této deklarace identity. |
| Neplatný před |`nbf` |`1452285331` |Čas, kdy začne platit, token reprezentované v čase epoch. Je obvykle stejné jako čas vystavení. Aplikace by měl ověřit správnost dobu životnosti tokenu pomocí této deklarace identity. |
| verze |`ver` |`2.0` |Verze ID tokenu, jak je definované ve službě Azure AD. Pro koncový bod v2.0, hodnota je `2.0`. |
| ID tenanta |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Identifikátor GUID, který představuje, do které uživatel z tenanta Azure AD. Identifikátor GUID pro pracovní a školní účty, je ID neměnné klienta organizace, které uživatel patří. Pro osobní účty, hodnota je `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile` Obor je vyžadováno k přijímat tuto deklaraci. |
| Kód hash |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Kód hash je součástí tokeny typu ID jenom v případě, že je ID token vydán s kódem autorizace OAuth 2.0. Slouží k ověření pravosti autorizační kód. Podrobnosti o provedení tohoto ověření naleznete v tématu [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html). |
| Hodnota hash tokenu přístupu |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |Přístup k tokenu hash je součástí ID tokeny pouze, pokud je ID token vydán s přístupový token OAuth 2.0. Slouží k ověření pravosti tokenu přístupu. Podrobnosti o provedení tohoto ověření naleznete v tématu [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html). |
| hodnotu Nonce |`nonce` |`12345` |Hodnotu nonce je strategie pro minimalizaci útoky opětovného přehrání tokenu. Vaše aplikace může určit hodnotu nonce v jednom požadavku autorizace pomocí `nonce` parametr dotazu. Hodnota zadáte v požadavku je vygenerované v ID token `nonce` deklarace identity, ponechat beze změny. Aplikace můžete ověřit hodnoty s hodnotou zadanou v žádosti, která přidruží token konkrétní ID relace aplikace. Aplikace by měla provést toto ověření během procesu ověření tokenu ID. |
| jméno |`name` |`Babe Ruth` |Deklarace identity názvu poskytuje čitelná pro člověka hodnotu, která identifikuje předmět tokenu. Hodnota není musí být jedinečný, je měnitelný a je určený pro použití pouze pro účely zobrazení. `profile` Obor je vyžadováno k přijímat tuto deklaraci. |
| e-mail |`email` |`thegreatbambino@nyy.onmicrosoft.com` |Primární e-mailovou adresu, přidružené k účtu uživatele, pokud existuje. Její hodnota je měnitelný a může časem změnit. `email` Obor je vyžadováno k přijímat tuto deklaraci. |
| upřednostňované uživatelské jméno |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |Primární uživatelské jméno představující uživatele v koncového bodu v2.0. To může být e-mailovou adresu, telefonní číslo nebo obecné uživatelské jméno bez zadaného formátu. Její hodnota je měnitelný a může časem změnit. Vzhledem k tomu, že je měnitelný, tato hodnota se nesmí používat pro autorizační rozhodnutí. `profile` Obor je vyžadováno k přijímat tuto deklaraci. |
| Předmět |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Objekt zabezpečení, o tom, které vyhodnotí token informace, například uživatele aplikace. Tato hodnota se nedá změnit a nemůže být přiřazeny nebo znovu použít. To lze použít ke kontrole autorizace bezpečně, například když token slouží pro přístup k prostředkům a slouží jako klíč v tabulkách databáze. Protože subjektem je vždy k dispozici v tokeny, problémy s Azure AD, doporučujeme používat tuto hodnotu v systému pro obecné účely povolení. Předmět je však identifikátor pairwise – je jedinečné pro ID konkrétní aplikace. Proto pokud jeden uživatel přihlásí do dvou různých aplikací pomocí dva identifikátory ID jiného klienta, tyto aplikace se zobrazí dvě různé hodnoty pro deklarace identity subjektu. To se může nebo nemusí být potřeby v závislosti na požadavcích vaší architektury a ochrana osobních údajů. |
| ID objektu |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Neměnné identifikátor pro objekt sady Microsoft identity, v takovém případě uživatelský účet. Je také slouží ke kontrole autorizace bezpečně a jako klíč v tabulkách databáze. Toto ID jednoznačně identifikuje uživatele ve všech aplikacích – dvě různé aplikace přihlášení stejný uživatel obdrží stejnou hodnotu do `oid` deklarací identity. To znamená, že ho lze použít při vytváření dotazů na služeb Microsoft online services, jako je například Microsoft Graph. Microsoft Graph vrátí toto ID `id` vlastnost pro daný uživatelský účet. Protože `oid` umožňuje více aplikací ke koordinaci uživatelů, `profile` obor je vyžadováno k přijímat tuto deklaraci. Všimněte si, že pokud jeden uživatel existuje v několika klientech, uživatel bude obsahovat ID jiný objekt v každém klientovi – i když se uživatel přihlašuje do každého účtu se stejnými pověřeními, jsou považovány za různé účty. |

### <a name="access-tokens"></a>Přístupové tokeny

Koncový bod v2.0 umožňuje aplikacím jiných výrobců, které jsou registrovány k vydání tokenů přístupu k zabezpečeným prostředkům, například webovým rozhraním API Azure AD. Další informace o nastavení aplikace k vydání tokenů přístupu najdete v tématu [postup registrace aplikace s koncovým bodem v2.0](active-directory-v2-app-registration.md). Při registraci aplikace s koncovým bodem v2.0, můžete zadat vývojář úrovně přístupu, názvem **obory**, pro které přístup může vydávány tokeny. Například **calendars.read** obor definovaný v Microsoft Graph API uděluje oprávnění ke čtení kalendáře uživatele. Když aplikace obdrží z koncového bodu v2.0 token přístupu, musíte ověřit podpis tokenu, vystavitele, cílové skupiny, čas vypršení platnosti a další deklarace identity, v závislosti na vašem scénáři. 

Pokud budete požadovat token přístupu z koncového bodu v2.0, koncový bod v2.0 také vrátí hodnotu metadata o tokenu přístupu pro vaši aplikaci používat. Tyto informace zahrnují čas vypršení platnosti přístupového tokenu a oborů, pro které je platný. Vaše aplikace používá tato metadata provést inteligentní ukládání do mezipaměti přístupové tokeny bez nutnosti analyzovat otevřete přístupový token, sám sebe.

### <a name="refresh-tokens"></a>Obnovovacích tokenů
Aktualizujte tokeny jsou tokeny zabezpečení, které aplikace můžete použít k získání nové přístupové tokeny v tok OAuth 2.0. Aplikace můžete použít tokeny obnovení zajistit dlouhodobou přístup k prostředkům jménem uživatele bez nutnosti interakci s uživatelem.

Tokeny obnovení jsou více prostředků. Token obnovení obdržel během žádosti o token pro jeden prostředek lze uplatnit přístup tokenů úplně jiných prostředků.

Pokud chcete získat aktualizace v odpovědi tokenu, vaše aplikace musí požádat a udělit `offline_access` oboru. Další informace o `offline_access` obor, najdete v článku [souhlasu a obory](active-directory-v2-scopes.md) článku.

Obnovovacích tokenů jsou a vždy budou, zcela neprůhledný do vaší aplikace. Se vydávají koncového bodu v2.0 Azure AD a můžete být prověřovány a interpretovat koncového bodu v2.0. Jsou dlohotrvající, ale aplikace nemá zapisovat očekávat, že token obnovení vydrží období. Tokeny obnovení může být zneplatněné v každém okamžiku z různých důvodů – podrobnosti najdete v tématu [token odvolání](active-directory-token-and-claims.md#token-revocation). Jediný způsob, jakým aplikace potřebujete vědět, jestli je platný token obnovení je pokus o uplatněte ho tak, že žádosti o token koncový bod v2.0.

Když uplatnit obnovovací token pro nový přístupový token (a pokud byl udělen vaší aplikace `offline_access` oboru), se zobrazí nový token aktualizace v odpovědi tokenu. Uložte nově vydané aktualizace tokenu, nahradit, které jste použili v požadavku. To zaručuje, že jsou dál platné pro stejně dlouho obnovovacích tokenů.

## <a name="validating-tokens"></a>Ověřování tokenů
V současné době pouze tokenu ověření, které vaše aplikace by měly splnit ověřuje tokeny typu ID. K ověření tokenu ID, aplikace by měl ověřit ID token podpisu a deklarace identity v ID tokenu.

<!-- TODO: Link -->
Společnost Microsoft poskytuje knihovny a ukázky kódu, které ukazují, jak snadno zpracovat ověření tokenu. V následujících částech kterou jsou popsané v podkladovém procesu. Několik knihovny open-source třetí strany jsou také k dispozici pro ověřování JWT. Není k dispozici nejméně jedna knihovna možnost pro téměř každé platformě a jazyk.

### <a name="validate-the-signature"></a>Ověření podpisu
Token JWT obsahuje tři segmenty, které jsou odděleny `.` znak. První segment se označuje jako *záhlaví*, druhý segment *textu*, a třetí segment *podpis*. Segment podpis slouží k ověření pravosti tokenu ID tak, aby důvěryhodné aplikace.

ID tokeny jsou podepsány pomocí standardní asymetrických šifrovacích algoritmů, například RSA 256. Hlavička ID token obsahuje informace o metodě klíč a šifrování používaný k podepisování token. Příklad:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

`alg` Deklarace identity Určuje algoritmus, který se použil k podepsání token. `kid` Deklarace identity označuje veřejný klíč, který se použil k podepsání token.

V každém okamžiku může koncový bod v2.0 podepsat ID token pomocí kterékoli z konkrétní sadu páry klíčů veřejný soukromý. Koncový bod v2.0 pravidelně otočí možné sada klíčů, tak vaše aplikace by měly být zapsány pro zpracování těchto změn klíče automaticky. Přiměřené frekvence ke kontrole aktualizací do veřejných klíčů používaných koncového bodu v2.0 je každých 24 hodin.

Můžete získat podpisového klíče dat, která je nutné ověřit podpis pomocí dokument metadat OpenID Connect, který se nachází v:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Vyzkoušejte adresu URL v prohlížeči.
>
>

Tento dokument metadat je objekt JSON, který má několik užitečné informací, jako je například umístění různých koncových bodů, které jsou požadované pro ověřování OpenID Connect. Také obsahuje dokument *jwks_uri*, což dává umístění sady veřejných klíčů, které se používá k podepisování tokenů. Na jwks_uri dokumentu JSON má všechny veřejné klíče informace, které se právě používá. Aplikace můžete použít `kid` deklarací identity v hlavičce JWT vybrat, které veřejný klíč v tomto dokumentu jsou využívány k podepsání token. Potom provede ověření podpisu pomocí správný veřejný klíč a algoritmus uvedené.

Provádění ověřování podpisu je mimo rámec tohoto dokumentu. Mnoho knihovny open-source jsou k dispozici vám pomůžou.

### <a name="validate-the-claims"></a>Ověřit deklarace identity
Pokud vaše aplikace obdrží token ID při přihlášení uživatele, má také provést několik kontrol proti deklarace identity v ID tokenu. Patří mezi ně nejsou omezeny na:

* **Cílová skupina** deklarace identity, chcete-li ověřit, že ID token mělo má být poskytnut do vaší aplikace
* **Neplatný před** a **čas vypršení platnosti** deklarací, chcete-li ověřit, zda nevypršela platnost tokenu ID
* **Vystavitel** deklarace identity, chcete-li ověřit, zda byl token vydán do vaší aplikace koncovým bodem v2.0
* **hodnotu Nonce**, jako token přehráním snížení rizika útoku

Úplný seznam ověření deklarace identity, které vaše aplikace by měla provádět, najdete v článku [OpenID Connect specifikace](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Podrobnosti o očekávaných hodnot pro tyto deklarace identity jsou součástí [tokeny typu ID](# ID tokens) části.

## <a name="token-lifetimes"></a>Doby života tokenů
Poskytujeme následující životnosti tokenu pro vás pouze informační charakter. Informace vám můžou pomoct při vývoji a ladění aplikací. Aplikace nemá zapisovat očekávat některé z těchto životnosti nezměnila. Token může životnosti a bude kdykoli změnit.

| Podpisový | Doba platnosti | Popis |
| --- | --- | --- |
| ID tokeny (pracovní nebo školní účty) |1 hodina |Tokeny typu ID obvykle jsou platné po dobu 1 hodina. Webové aplikace můžete použít tento stejný životnost k udržování své vlastní relaci s uživatele (doporučeno) nebo můžete vybrat dobu platnosti úplně jiné relace. Pokud aplikace potřebuje k získání tokenu nové ID, musí se na nové přihlášení požádat o v2.0 zajistí autorizaci koncového bodu. Pokud má uživatel relace platná prohlížeče s koncovým bodem v2.0, uživatel pravděpodobně nutné znovu zadat své přihlašovací údaje. |
| ID tokeny (osobní účty) |24 hodin |ID tokeny pro osobní účty obvykle jsou platné po dobu 24 hodin. Webové aplikace můžete použít tento stejný životnost k udržování své vlastní relaci s uživatele (doporučeno) nebo můžete vybrat dobu platnosti úplně jiné relace. Pokud aplikace potřebuje k získání tokenu nové ID, musí se na nové přihlášení požádat o v2.0 zajistí autorizaci koncového bodu. Pokud má uživatel relace platná prohlížeče s koncovým bodem v2.0, uživatel pravděpodobně nutné znovu zadat své přihlašovací údaje. |
| Přístupové tokeny (pracovní nebo školní účty) |1 hodina |Jako součást tokenu metadata uvedené v odpovědi tokenu. |
| Přístupové tokeny (osobní účty) |1 hodina |Jako součást tokenu metadata uvedené v odpovědi tokenu. Přístupové tokeny, které jsou vydány jménem osobní účty je možné nakonfigurovat pro různé životnost, ale je typické 1 hodina. |
| Obnovovacích tokenů (pracovní nebo školní účet) |Až 14 dní |Jeden obnovovací token je platný pro maximálně 14 dní. Token obnovení však může stane neplatnou kdykoli z různých důvodů, vaše aplikace by měly být nadále zkuste použít obnovovací token, dokud se nezdaří, nebo dokud aplikace ho nahradí nový token obnovení. Token obnovení také stává neplatným, pokud to bylo 90 dnů, protože uživatel zadal své přihlašovací údaje. |
| Obnovovacích tokenů (osobní účty) |Až 1 rok |Jeden obnovovací token je platný pro nesmí být delší než 1 rok. Token obnovení však může stane neplatnou kdykoli z různých důvodů, aby vaše aplikace by měly být nadále pokoušet použít obnovovací token, dokud se nezdaří. |
| Autorizačních kódů (pracovní nebo školní účty) |10 minut |Autorizace kódy jsou účelově krátkodobou a by měl být okamžitě uplatněn přístupové tokeny a obnovovacích tokenů při přijetí tokenů. |
| Autorizačních kódů (osobní účty) |5 minut |Autorizace kódy jsou účelově krátkodobou a by měl být okamžitě uplatněn přístupové tokeny a obnovovacích tokenů při přijetí tokenů. Autorizace kódy, které jsou vydány jménem osobní účty, jsou pro jednorázové použití. |
