---
title: Tok autorizačního kódu OAuth – platforma Microsoftu identity | Azure
description: Sestavujte webové aplikace pomocí implementace ověřovacího protokolu OAuth 2,0 od Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 366389ddf88cfb72c9ed9d0543c9985eb25f47ae
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226937"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft Identity Platform a tok autorizačního kódu OAuth 2,0

Udělení autorizačního kódu OAuth 2,0 se dá použít v aplikacích, které jsou nainstalované na zařízení, aby získal přístup k chráněným prostředkům, například k webovým rozhraním API. Pomocí implementace OAuth 2,0 pro Microsoft Identity Platform můžete přidat přihlašování a přístup k rozhraní API pro mobilní a desktopové aplikace. Tato příručka je nezávislá na jazyce a popisuje, jak odesílat a přijímat zprávy HTTP bez použití žádné knihovny pro [ověřování Azure Open Source](reference-v2-libraries.md).

Tento článek popisuje, jak programovat přímo s protokolem ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny Microsoft Authentication Library (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Podívejte se také na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

> [!NOTE]
> Ne všechny scénáře Azure Active Directory & funkce jsou podporovány koncovým bodem platformy Microsoft Identity Platform. Pokud chcete zjistit, jestli byste měli použít koncový bod platformy Microsoft identity, přečtěte si informace o [omezeních platformy Microsoft Identity](active-directory-v2-limitations.md).

Tok autorizačního kódu OAuth 2,0 je popsaný v [části 4,1 specifikace oauth 2,0](https://tools.ietf.org/html/rfc6749). Používá se k ověřování a autorizaci ve většině typů aplikací, včetně [webových aplikací](v2-app-types.md#web-apps) a [nativně nainstalovaných aplikací](v2-app-types.md#mobile-and-native-apps). Tok umožňuje aplikacím bezpečně získat access_tokens, které se dají použít k přístupu k prostředkům zabezpečeným koncovým bodem platformy Microsoft identity.

## <a name="protocol-diagram"></a>Diagram protokolu

V nejvyšší úrovni vypadá celý tok ověřování pro nativní nebo mobilní aplikace podobným způsobem:

![Tok kódu ověřování OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Vyžádání autorizačního kódu

Tok autorizačního kódu začíná klientem, který přesměruje uživatele do koncového bodu `/authorize`. V této žádosti klient požaduje od uživatele oprávnění `openid`, `offline_access`a `https://graph.microsoft.com/mail.read `.  Některá oprávnění jsou omezená na správce, například zápis dat do adresáře organizace pomocí `Directory.ReadWrite.All`. Pokud vaše aplikace požaduje přístup k některé z těchto oprávnění od uživatele organizace, obdrží chybová zpráva s oznámením, že nemají oprávnění k souhlasu s oprávněními vaší aplikace. Chcete-li požádat o přístup k oborům s omezeným oprávněním správce, měli byste je požádat přímo od správce společnosti.  Další informace najdete v tématu [oprávnění s omezeným oprávněním pro správu](v2-permissions-and-consent.md#admin-restricted-permissions).

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [!TIP]
> Pro spuštění této žádosti klikněte na odkaz níže. Po přihlášení by měl být váš prohlížeč přesměrován na `https://localhost/myapp/` s `code` na adresním řádku.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr    | Požadováno/volitelné | Popis |
|--------------|-------------|--------------|
| `tenant`    | požadováno    | Hodnotu `{tenant}` v cestě k žádosti lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů. Další podrobnosti najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | požadováno    | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci.  |
| `response_type` | požadováno    | Musí zahrnovat `code` pro tok autorizačního kódu.       |
| `redirect_uri`  | požadováno | Redirect_uri vaší aplikace, ve které vaše aplikace může odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z redirect_uris, který jste zaregistrovali na portálu, s výjimkou musí být zakódovaný URL. Pro nativní & mobilní aplikace byste měli použít výchozí hodnotu `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| `scope`  | požadováno    | Mezerou oddělený seznam [oborů](v2-permissions-and-consent.md) , ke kterým má uživatel udělit souhlas.  Pro `/authorize` fáze žádosti to může pokrývat více prostředků, což vaší aplikaci umožní získat souhlas s více webovými rozhraními API, která chcete volat. |
| `response_mode`   | doporučil | Určuje metodu, která se má použít k odeslání výsledného tokenu zpátky do vaší aplikace. Může být jedna z následujících akcí:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` poskytuje kód jako parametr řetězce dotazu v identifikátoru URI přesměrování. Pokud požadujete token ID pomocí implicitního toku, nemůžete použít `query`, jak je uvedeno ve [specifikaci OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Pokud požadujete pouze kód, můžete použít `query`, `fragment`nebo `form_post`. `form_post` spustí příspěvek obsahující kód pro identifikátor URI přesměrování. Další informace najdete v tématu [protokol OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | doporučil | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně vygenerovaná jedinečná hodnota se obvykle používá k [prevenci útoků proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Hodnota může také kódovat informace o stavu uživatele v aplikaci před tím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
| `prompt`  | volitelné    | Určuje typ interakce uživatele, která je povinná. V tuto chvíli jsou k dispozici pouze platné hodnoty `login`, `none`a `consent`.<br/><br/>- `prompt=login` vynutí, aby uživatel zadal přihlašovací údaje k danému požadavku, přičemž se pro ně použije negace jednotného přihlašování.<br/>- `prompt=none` je opakem, zajistí, že se uživateli nebude zobrazovat žádná interaktivní výzva. Pokud se žádost nedá v tichém režimu dokončit pomocí jednotného přihlašování, vrátí koncový bod platformy Microsoft Identity `interaction_required` chybu.<br/>- `prompt=consent` spustí dialog souhlasu OAuth po přihlášení uživatele a požádá uživatele, aby aplikaci udělil oprávnění.<br/>- `prompt=select_account` přeruší jednotné přihlašování, které poskytuje možnosti výběru účtu, které uvádějí všechny účty buď v relaci, nebo v jakémkoli zašifrovaném účtu, nebo možnost zvolit, že se má úplně použít jiný účet.<br/> |
| `login_hint`  | volitelné    | Dá se použít k předvyplnění pole uživatelské jméno a e-mailová adresa přihlašovací stránky pro uživatele, pokud znáte své uživatelské jméno předem. Aplikace budou často používat tento parametr během opakovaného ověřování, kteří již rozvěděli uživatelské jméno z předchozího přihlášení pomocí `preferred_username` deklarace identity.   |
| `domain_hint`  | volitelné    | Může to být jedna z `consumers` nebo `organizations`.<br/><br/>Pokud je zahrnuto, přeskočí proces zjišťování na základě e-mailu, který uživatel prochází na přihlašovací stránce, což vede k poněkud efektivnějšímu uživatelskému prostředí. Aplikace budou často používat tento parametr během opakovaného ověřování, a to extrakcí `tid` z předchozího přihlášení. Pokud je hodnota deklarace `tid` `9188040d-6c67-4c5b-b112-36a304b66dad`, měli byste použít `domain_hint=consumers`. V opačném případě použijte `domain_hint=organizations`.  |
| `code_challenge_method` | volitelné    | Metoda použitá ke kódování `code_verifier` pro parametr `code_challenge` Může to být jedna z následujících hodnot:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Pokud je tato hodnota vyloučená, `code_challenge` se při zahrnutí `code_challenge` považovat za prostý text. Platforma Microsoft Identity Platform podporuje `plain` i `S256`. Další informace najdete v [dokumentu RFC PKCE](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | volitelné | Slouží k zabezpečení autorizačního kódu prostřednictvím kontrolního klíče pro výměnu kódu (PKCE) z nativního klienta. Vyžaduje se, pokud je zahrnut `code_challenge_method`. Další informace najdete v [dokumentu RFC PKCE](https://tools.ietf.org/html/rfc7636). |

V tomto okamžiku se uživateli zobrazí výzva k zadání přihlašovacích údajů a dokončení ověřování. Koncový bod platformy Microsoft Identity také zajistí, že uživatel souhlasí s oprávněními uvedenými v parametru `scope` dotazu. Pokud uživatel nesouhlasí s žádným z těchto oprávnění, požádá uživatele o souhlas s požadovanými oprávněními. Podrobnosti o [oprávněních, souhlasu a víceklientské aplikacích najdete tady](v2-permissions-and-consent.md).

Jakmile se uživatel ověří a udělí souhlas, koncový bod platformy Microsoft Identity vrátí odpověď do vaší aplikace na uvedené `redirect_uri`pomocí metody zadané v parametru `response_mode`.

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď pomocí `response_mode=query` vypadá takto:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametr | Popis  |
|-----------|--------------|
| `code` | Authorization_code, kterou aplikace požadovala Aplikace může pomocí autorizačního kódu požádat o přístupový token pro cílový prostředek. Authorization_codes jsou krátkodobé, obvykle vyprší po 10 minutách. |
| `state` | Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

#### <a name="error-response"></a>Chybová odezva

Do `redirect_uri` lze také odeslat odpovědi na chyby, aby je aplikace mohla správně zpracovat:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis  |
|----------|------------------|
| `error`  | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Chybové kódy pro chyby koncového bodu autorizace

Následující tabulka popisuje různé chybové kódy, které lze vrátit v parametru `error` chybové odpovědi.

| Kód chyby  | Popis    | Akce klienta   |
|-------------|----------------|-----------------|
| `invalid_request` | Chyba protokolu, například chybějící požadovaný parametr. | Opravte a odešlete požadavek znovu. Toto je chyba vývoje obvykle zachycena při počátečním testování. |
| `unauthorized_client` | Klientská aplikace nemá oprávnění vyžadovat autorizační kód. | K této chybě obvykle dochází, když klientská aplikace není registrovaná v Azure AD nebo není přidaná do tenanta Azure AD uživatele. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| `access_denied`  | Vlastník prostředku zamítl souhlas.  | Klientská aplikace může uživatele informovat, že nemůže pokračovat, dokud se uživatel nesouhlasí. |
| `unsupported_response_type` | Autorizační Server v žádosti nepodporuje typ odpovědi. | Opravte a odešlete požadavek znovu. Toto je chyba vývoje obvykle zachycena při počátečním testování.  |
| `server_error`  | Na serveru došlo k neočekávané chybě.| Opakujte požadavek. Tyto chyby mohou být způsobeny dočasnými podmínkami. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděná na dočasnou chybu. |
| `temporarily_unavailable`   | Server je dočasně zaneprázdněný pro zpracování žádosti. | Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
| `invalid_resource`  | Cílový prostředek není platný, protože neexistuje, služba Azure AD ho nemůže najít nebo není správně nakonfigurovaná. | Tato chyba označuje prostředek, pokud existuje, není v tenantovi nakonfigurovaný. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| `login_required` | Bylo nalezeno příliš mnoho uživatelů. | Klient požadoval bezobslužné ověření (`prompt=none`), ale jeden uživatel se nenašel. To může znamenat, že je v relaci aktivní více uživatelů, nebo žádní uživatelé. Tato možnost vezme v úvahu vybraný tenant (například pokud jsou aktivní dva účty Azure AD a jeden účet Microsoft a `consumers` je zvolena možnost tiché ověřování bude fungovat). |
| `interaction_required` | Požadavek vyžaduje zásah uživatele. | Vyžaduje se další krok ověřování nebo souhlas. Opakujte požadavek bez `prompt=none`. |

## <a name="request-an-access-token"></a>Vyžádání přístupového tokenu

Teď, když jste získali authorization_code a máte mu udělené oprávnění, můžete `code` pro `access_token` uplatnit na požadovaný prostředek. Provedete to tak, že do koncového bodu `/token` odešlete žádost o `POST`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Zkuste tento požadavek provést v nástroji post! (Nezapomeňte nahradit `code`) [![zkuste tento požadavek spustit v nástroji post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d) .

| Parametr  | Požadováno/volitelné | Popis     |
|------------|-------------------|----------------|
| `tenant`   | požadováno   | Hodnotu `{tenant}` v cestě k žádosti lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů. Další podrobnosti najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | požadováno  | ID aplikace (klienta), které stránka [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) přiřazená k vaší aplikaci. |
| `grant_type` | požadováno   | Pro tok autorizačního kódu musí být `authorization_code`.   |
| `scope`      | požadováno   | Mezerou oddělený seznam oborů. Rozsahy požadované v této nožkě musí být stejné jako podmnožina oborů požadovaných v první nožkě. Rozsahy musí být z jednoho prostředku, společně s OIDC obory (`profile`, `openid`, `email`). Podrobnější vysvětlení oborů najdete v tématu [oprávnění, souhlas a obory](v2-permissions-and-consent.md). |
| `code`          | požadováno  | Authorization_code, kterou jste získali v první nožkě toku. |
| `redirect_uri`  | požadováno  | Stejná redirect_uri hodnota, která byla použita k získání authorization_code. |
| `client_secret` | vyžadováno pro webové aplikace | Tajný klíč aplikace, který jste vytvořili na portálu pro registraci aplikací pro vaši aplikaci. Nepoužívejte tajný klíč aplikace v nativní aplikaci, protože client_secrets nemůže být spolehlivě uložená na zařízeních. Vyžaduje se pro webové aplikace a webová rozhraní API, které mají možnost bezpečně ukládat client_secret na straně serveru.  Tajný klíč klienta musí být před odesláním zakódovaný na adrese URL. Další informace získáte kliknutím [sem](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | volitelné  | Stejný code_verifier, který byl použit k získání authorization_code. Vyžaduje se, pokud se v žádosti o udělení autorizačního kódu použil PKCE. Další informace najdete v [dokumentu RFC PKCE](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď tokenu bude vypadat takto:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| Parametr     | Popis   |
|---------------|------------------------------|
| `access_token`  | Požadovaný přístupový token Aplikace může tento token použít k ověření zabezpečeného prostředku, jako je například webové rozhraní API.  |
| `token_type`    | Určuje hodnotu typu tokenu. Jediný typ, který podporuje Azure AD, je nosič. |
| `expires_in`    | Jak dlouho je přístupový token platný (v sekundách). |
| `scope`         | Rozsahy, pro které je access_token platný. |
| `refresh_token` | Obnovovací token OAuth 2,0. Aplikace může tento token použít k získání dalších přístupových tokenů po vypršení platnosti aktuálního přístupového tokenu. Refresh_tokens jsou dlouhodobé a dají se použít k uchování přístupu k prostředkům po delší dobu. Další podrobnosti o aktualizaci přístupového tokenu najdete v [níže uvedené části](#refresh-the-access-token). <br> **Poznámka:** Zadáno pouze v případě, že byl požadován obor `offline_access`. |
| `id_token`      | JSON Web Token (JWT). Aplikace může dekódovat segmenty tohoto tokenu a vyžádat si informace o uživateli, který se přihlásil. Aplikace může hodnoty ukládat do mezipaměti a zobrazovat je, ale nemělo by je spoléhat na jakékoli autorizace nebo hranice zabezpečení. Další informace o id_tokens najdete v [`id_token reference`](id-tokens.md). <br> **Poznámka:** Zadáno pouze v případě, že byl požadován obor `openid`. |

### <a name="error-response"></a>Chybová odezva

Chybové odpovědi budou vypadat takto:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr         | Popis    |
|-------------------|----------------|
| `error`       | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování. |
| `error_codes` | Seznam chybových kódů specifických pro službu STS, které mohou být užitečné při diagnostice.  |
| `timestamp`   | Čas, kdy došlo k chybě. |
| `trace_id`    | Jedinečný identifikátor pro požadavek, který může pomáhat při diagnostice. |
| `correlation_id` | Jedinečný identifikátor pro požadavek, který může pomáhat při diagnostice napříč komponentami. |

### <a name="error-codes-for-token-endpoint-errors"></a>Chybové kódy pro chyby koncového bodu tokenu

| Kód chyby         | Popis        | Akce klienta    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Chyba protokolu, například chybějící požadovaný parametr. | Opravte a znovu odešlete žádost.   |
| `invalid_grant`    | Autorizační kód nebo ověřovatel kódu PKCE je neplatný nebo vypršela jeho platnost. | Zkuste nový požadavek na koncový bod `/authorize` a ověřte správnost parametru code_verifier.  |
| `unauthorized_client` | Ověřený klient nemá autorizaci k použití tohoto autorizačního typu udělení. | K tomu obvykle dochází, když klientská aplikace není registrovaná v Azure AD nebo není přidaná do tenanta Azure AD uživatele. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| `invalid_client` | Ověření klienta se nezdařilo.  | Pověření klienta nejsou platná. Chcete-li opravit, správce aplikace aktualizuje pověření.   |
| `unsupported_grant_type` | Autorizační Server nepodporuje typ udělení autorizace. | Změňte typ udělení v žádosti. Tento typ chyby by měl nastat pouze během vývoje a zjištěn při počátečním testování. |
| `invalid_resource` | Cílový prostředek není platný, protože neexistuje, služba Azure AD ho nemůže najít nebo není správně nakonfigurovaná. | To znamená, že pokud tento prostředek existuje, není v tenantovi nakonfigurovaný. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD.  |
| `interaction_required` | Požadavek vyžaduje zásah uživatele. Například je vyžadován další krok ověřování. | Opakujte požadavek se stejným prostředkem.  |
| `temporarily_unavailable` | Server je dočasně zaneprázdněný pro zpracování žádosti. | Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |

## <a name="use-the-access-token"></a>Použití přístupového tokenu

Po úspěšném získání `access_token`můžete token v žádosti do webových rozhraní API použít tak, že ho zahrnete do hlavičky `Authorization`:

> [!TIP]
> Provést tento požadavek v nástroji post! (Nahradit první hlavičku `Authorization`) [![zkuste tento požadavek spustit v nástroji post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d) .

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Aktualizace přístupového tokenu

Access_tokens jsou krátkodobé a po vypršení jejich platnosti je musíte aktualizovat, aby bylo možné pokračovat v přístupu k prostředkům. Můžete to udělat tak, že odešlete další `POST` žádost do koncového bodu `/token` a tentokrát zadáte `refresh_token` místo `code`.  Aktualizační tokeny jsou platné pro všechna oprávnění, která klient již získal souhlas. proto je možné k vyžádání nového přístupového tokenu pro `scope=api://contoso.com/api/UseResource`použít obnovovací token vydaný pro žádost o `scope=mail.read`.  

Aktualizační tokeny nemají zadané životnosti. Obvykle jsou životnosti aktualizačních tokenů poměrně dlouhé. V některých případech ale platnost tokenů aktualizace vyprší, odvolají se nebo nemají dostatečná oprávnění pro požadovanou akci. Vaše aplikace musí očekávat a zpracovat [chyby vrácené koncovým bodem vystavení tokenu](#error-codes-for-token-endpoint-errors) správně. 

I když se tokeny aktualizace neodvolává při použití k získání nových přístupových tokenů, očekává se, že se starý obnovovací token zruší. [Specifikace OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-6) říká: "AUTORIZAČNÍ Server může vydat nový obnovovací token. v takovém případě musí klient zrušit starý obnovovací token a nahradit ho novým obnovovacím tokenem. Po vydání nového obnovovacího tokenu klientovi může autorizační Server odvolat starý obnovovací token. "  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> Zkuste tento požadavek provést v nástroji post! (Nezapomeňte nahradit `refresh_token`) [![zkuste tento požadavek spustit v nástroji post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d) .
> 

| Parametr     |                | Popis        |
|---------------|----------------|--------------------|
| `tenant`        | požadováno     | Hodnotu `{tenant}` v cestě k žádosti lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů. Další podrobnosti najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | požadováno    | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `grant_type`    | požadováno    | Pro tuto nožku toku autorizačního kódu musí být `refresh_token`. |
| `scope`         | požadováno    | Mezerou oddělený seznam oborů. Rozsahy požadované v této nožkě musí být stejné jako podmnožina oborů požadovaných v původní nožkě authorization_code žádosti. Pokud obory zadané v tomto požadavku vynásobí více prostředků serveru, pak koncový bod platformy Microsoft Identity vrátí token pro prostředek zadaný v prvním oboru. Podrobnější vysvětlení oborů najdete v tématu [oprávnění, souhlas a obory](v2-permissions-and-consent.md). |
| `refresh_token` | požadováno    | Refresh_token, kterou jste získali v druhé nožkě toku. |
| `client_secret` | vyžadováno pro webové aplikace | Tajný klíč aplikace, který jste vytvořili na portálu pro registraci aplikací pro vaši aplikaci. Neměl by se používat v nativní aplikaci, protože client_secrets nemůže být spolehlivě uložená na zařízeních. Vyžaduje se pro webové aplikace a webová rozhraní API, které mají možnost bezpečně ukládat client_secret na straně serveru. Tento tajný klíč musí být kódovaný pomocí adresy URL, další informace získáte kliknutím [sem](https://tools.ietf.org/html/rfc3986#page-12). |

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď tokenu bude vypadat takto:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametr     | Popis         |
|---------------|-------------------------------------------------------------|
| `access_token`  | Požadovaný přístupový token Aplikace může tento token použít k ověření zabezpečeného prostředku, jako je například webové rozhraní API. |
| `token_type`    | Určuje hodnotu typu tokenu. Jediný typ, který podporuje Azure AD, je nosič. |
| `expires_in`    | Jak dlouho je přístupový token platný (v sekundách).   |
| `scope`         | Rozsahy, pro které je access_token platný.    |
| `refresh_token` | Nový token pro obnovení OAuth 2,0. Starý obnovovací token byste měli nahradit tímto nově získaným aktualizačním tokenem, abyste zajistili, že obnovovací tokeny zůstanou platné i tak dlouho. <br> **Poznámka:** Zadáno pouze v případě, že byl požadován obor `offline_access`.|
| `id_token`      | Nepodepsaný JSON Web Token (JWT). Aplikace může dekódovat segmenty tohoto tokenu a vyžádat si informace o uživateli, který se přihlásil. Aplikace může hodnoty ukládat do mezipaměti a zobrazovat je, ale nemělo by je spoléhat na jakékoli autorizace nebo hranice zabezpečení. Další informace o id_tokens najdete v [`id_token reference`](id-tokens.md). <br> **Poznámka:** Zadáno pouze v případě, že byl požadován obor `openid`. |

#### <a name="error-response"></a>Chybová odezva

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr         | Popis                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování.           |
| `error_codes` |Seznam chybových kódů specifických pro službu STS, které mohou být užitečné při diagnostice. |
| `timestamp` | Čas, kdy došlo k chybě. |
| `trace_id` | Jedinečný identifikátor pro požadavek, který může pomáhat při diagnostice. |
| `correlation_id` | Jedinečný identifikátor pro požadavek, který může pomáhat při diagnostice napříč komponentami. |

Popis chybových kódů a doporučené akce klienta najdete v tématu [kódy chyb pro chyby koncového bodu tokenu](#error-codes-for-token-endpoint-errors).
