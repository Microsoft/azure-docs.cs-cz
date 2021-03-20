---
title: Microsoft Identity Platform a tok autorizačního kódu OAuth 2,0 | Azure
titleSuffix: Microsoft identity platform
description: Sestavujte webové aplikace pomocí implementace ověřovacího protokolu OAuth 2,0 od Microsoft Identity Platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: aeed031025b9c494b35886861c273e2a7f9d2ac4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653724"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft Identity Platform a tok autorizačního kódu OAuth 2,0

Udělení autorizačního kódu OAuth 2,0 se dá použít v aplikacích, které jsou nainstalované na zařízení, aby získal přístup k chráněným prostředkům, například k webovým rozhraním API. Pomocí implementace OAuth 2,0 pro Microsoft Identity Platform můžete přidat přihlašování a přístup k rozhraní API pro mobilní a desktopové aplikace.

Tento článek popisuje, jak programovat přímo s protokolem ve vaší aplikaci pomocí libovolného jazyka.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny Microsoft Authentication Library (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Podívejte se také na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

Tok autorizačního kódu OAuth 2,0 je popsaný v [části 4,1 specifikace oauth 2,0](https://tools.ietf.org/html/rfc6749). Používá se k ověřování a autorizaci ve většině typů aplikací, včetně [aplikací s jednou stránkou](v2-app-types.md#single-page-apps-javascript), [webových aplikací](v2-app-types.md#web-apps)a [nativně nainstalovaných aplikací](v2-app-types.md#mobile-and-native-apps). Tok umožňuje aplikacím bezpečně získat access_tokens, které se dají použít k přístupu k prostředkům zabezpečeným platformou Microsoft identity a také k aktualizaci tokenů pro získání dalších access_tokens a tokenů ID přihlášeného uživatele.

## <a name="protocol-diagram"></a>Diagram protokolu

V nejvyšší úrovni má celý tok ověřování pro aplikaci podobný bit jako tento:

![Tok kódu ověřování OAuth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Pro jednostránkové aplikace se vyžaduje nastavení identifikátoru URI přesměrování.

Tok autorizačního kódu pro aplikace s jednou stránkou vyžaduje další instalaci.  Postupujte podle pokynů pro [Vytvoření jednostránkové aplikace](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) pro správné označení identifikátoru URI přesměrování, který je povolený pro CORS. Pokud chcete aktualizovat existující identifikátor URI pro přesměrování a povolit CORS, otevřete editor manifestu a `type` v části nastavte pole pro identifikátor URI přesměrování `spa` `replyUrlsWithType` . Můžete také kliknout na URI přesměrování v části Web na kartě ověřování a vybrat identifikátory URI, které chcete migrovat, pomocí toku autorizačního kódu.

`spa`Typ přesměrování je zpětně kompatibilní s implicitním tokem. Aplikace, které aktuálně používají implicitní tok k získání tokenů, se můžou přesunout na `spa` typ URI přesměrování bez problémů a dál používat implicitní tok.

Pokud se pokusíte použít tok autorizačního kódu a zobrazit tuto chybu:

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

Potom navštivte registraci vaší aplikace a aktualizujte identifikátor URI přesměrování pro vaši aplikaci na typ `spa` .

## <a name="request-an-authorization-code"></a>Vyžádání autorizačního kódu

Tok autorizačního kódu začíná klientem, který uživatele přesměruje na `/authorize` koncový bod. V této žádosti klient požaduje `openid` `offline_access` oprávnění, a `https://graph.microsoft.com/mail.read ` od uživatele.  Některá oprávnění jsou omezená na správce, například zápis dat do adresáře organizace pomocí `Directory.ReadWrite.All` . Pokud vaše aplikace požaduje přístup k některé z těchto oprávnění od uživatele organizace, obdrží chybová zpráva s oznámením, že nemají oprávnění k souhlasu s oprávněními vaší aplikace. Chcete-li požádat o přístup k oborům s omezeným oprávněním správce, měli byste je požádat přímo od globálního správce.  Další informace najdete v tématu [oprávnění s omezeným oprávněním pro správu](v2-permissions-and-consent.md#admin-restricted-permissions).

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read%20api%3A%2F%2F
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> Pro spuštění této žádosti klikněte na odkaz níže. Po přihlášení by měl být v prohlížeči přesměrován na adresu na adresním `https://localhost/myapp/` `code` řádku.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr    | Požadováno/volitelné | Description |
|--------------|-------------|--------------|
| `tenant`    | vyžadováno    | `{tenant}`Hodnotu v cestě k požadavku lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common` `organizations` `consumers` identifikátory klientů,, a. Další podrobnosti najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | vyžadováno    | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci.  |
| `response_type` | vyžadováno    | Musí zahrnovat `code` tok autorizačního kódu. Může také zahrnovat `id_token` nebo `token` při použití [hybridního toku](#request-an-id-token-as-well-hybrid-flow). |
| `redirect_uri`  | vyžadováno | Redirect_uri vaší aplikace, ve které vaše aplikace může odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z redirect_uris, který jste zaregistrovali na portálu, s výjimkou musí být zakódovaný URL. Pro nativní & mobilní aplikace byste měli použít jednu z doporučených hodnot –  `https://login.microsoftonline.com/common/oauth2/nativeclient` (pro aplikace používající vložené prohlížeče) nebo `http://localhost` (pro aplikace, které používají systémové prohlížeče). |
| `scope`  | vyžadováno    | Mezerou oddělený seznam [oborů](v2-permissions-and-consent.md) , ke kterým má uživatel udělit souhlas.  Pro `/authorize` nohy žádosti to může pokrývat více prostředků, což vaší aplikaci umožní získat souhlas s více webovými rozhraními API, která chcete volat. |
| `response_mode`   | doporučil | Určuje metodu, která se má použít k odeslání výsledného tokenu zpátky do vaší aplikace. Může to být jedna z následujících:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` poskytuje kód jako parametr řetězce dotazu v identifikátoru URI přesměrování. Pokud požadujete token ID pomocí implicitního toku, nemůžete použít, `query` jak je uvedeno ve [specifikaci OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Pokud požadujete pouze kód, můžete použít `query` , `fragment` nebo `form_post` . `form_post` provede příspěvek obsahující kód pro identifikátor URI přesměrování. |
| `state`                 | doporučil | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně vygenerovaná jedinečná hodnota se obvykle používá k [prevenci útoků proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Hodnota může také kódovat informace o stavu uživatele v aplikaci před tím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
| `prompt`  | optional    | Určuje typ interakce uživatele, která je povinná. Jediné platné hodnoty jsou v tomto okamžiku `login` , `none` a `consent` .<br/><br/>- `prompt=login` vynutí, aby uživatel zadal přihlašovací údaje k danému požadavku, přičemž se pro ně použije negace jednotného přihlašování.<br/>- `prompt=none` je opakem, zajistí, že se uživateli nebude zobrazovat žádná interaktivní výzva. Pokud se žádost nedá v tichém režimu dokončit pomocí jednotného přihlašování, bude platforma Microsoft Identity platformou vracet `interaction_required` chybu.<br/>- `prompt=consent` aktivuje dialog souhlasu OAuth po přihlášení uživatele a vyzve uživatele, aby aplikaci udělil oprávnění.<br/>- `prompt=select_account` dojde k přerušení jednotného přihlašování, které poskytuje možnosti výběru účtu, které uvádějí všechny účty buď v relaci, nebo v jakémkoli zašifrovaném účtu, nebo možnost zvolit, že se má úplně použít jiný účet.<br/> |
| `login_hint`  | optional    | Dá se použít k předvyplnění pole uživatelské jméno a e-mailová adresa přihlašovací stránky pro uživatele, pokud znáte své uživatelské jméno předem. Aplikace budou často používat tento parametr během opakovaného ověřování, kteří už z předchozího přihlášení extrahovali uživatelské jméno, a to pomocí `preferred_username` deklarace identity.   |
| `domain_hint`  | optional    | Pokud se tato možnost zahrne, přeskočí proces zjišťování e-mailu, který uživatel prochází na přihlašovací stránce, což vede k poněkud efektivnějšímu uživatelskému rozhraní, které je třeba odeslat do svého federovaného poskytovatele identity. Aplikace budou často používat tento parametr během opakovaného ověřování, a to extrakcí `tid` z předchozího přihlášení. Pokud `tid` je hodnota deklarace identity `9188040d-6c67-4c5b-b112-36a304b66dad` , měli byste použít `domain_hint=consumers` . V opačném případě použijte `domain_hint=organizations` .  |
| `code_challenge`  | Doporučené/povinné | Slouží k zabezpečení autorizačního kódu prostřednictvím ověřovacího klíče pro výměnu kódu (PKCE). Požadováno `code_challenge_method` , pokud je zahrnuto. Další informace najdete v [dokumentu RFC PKCE](https://tools.ietf.org/html/rfc7636). To se teď doporučuje u všech typů aplikací – veřejných i důvěrných klientů, které vyžaduje platforma Microsoft identity pro jednostránkové aplikace, a to [pomocí toku autorizačního kódu](reference-third-party-cookies-spas.md). |
| `code_challenge_method` | Doporučené/povinné | Metoda použitá k zakódování `code_verifier` pro `code_challenge` parametr. To *by mělo* být `S256` , ale specifikace umožňuje použití z `plain` nějakého důvodu, když klient nepodporuje SHA256. <br/><br/>Pokud je vyloučený, předpokládá se, že je v `code_challenge` případě zahrnutí prostý text `code_challenge` . Platforma Microsoft identity podporuje i `plain` `S256` . Další informace najdete v [dokumentu RFC PKCE](https://tools.ietf.org/html/rfc7636). To je nutné pro [jednostránkové aplikace pomocí toku autorizačního kódu](reference-third-party-cookies-spas.md).|


V tomto okamžiku se uživateli zobrazí výzva k zadání přihlašovacích údajů a dokončení ověřování. Platforma Microsoft Identity také zajistí, že uživatel souhlasí s oprávněními uvedenými v `scope` parametru dotazu. Pokud uživatel nesouhlasí s žádným z těchto oprávnění, požádá uživatele o souhlas s požadovanými oprávněními. Podrobnosti o [oprávněních, souhlasu a víceklientské aplikacích najdete tady](v2-permissions-and-consent.md).

Jakmile se uživatel ověří a udělí souhlas, platforma Microsoftu identity vrátí odpověď do vaší aplikace na uvedené adrese `redirect_uri` pomocí metody uvedené v `response_mode` parametru.

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď pomocí `response_mode=query` vypadá takto:

```HTTP
GET http://localhost?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametr | Popis  |
|-----------|--------------|
| `code` | Authorization_code, kterou aplikace požadovala Aplikace může pomocí autorizačního kódu požádat o přístupový token pro cílový prostředek. Authorization_codes jsou krátkodobé, obvykle vyprší po 10 minutách. |
| `state` | Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

Token ID můžete získat také v případě, že si ho vyžádáte a máte v registraci vaší aplikace povolený implicitní grant.  Tento postup se někdy označuje jako ["hybridní tok"](#request-an-id-token-as-well-hybrid-flow)a používá se v rozhraních, jako je ASP.NET.

#### <a name="error-response"></a>Chybová odezva

Odpovědi na chyby mohou být také odesílány do, `redirect_uri` aby je aplikace mohla správně zpracovat:

```HTTP
GET http://localhost?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis  |
|----------|------------------|
| `error`  | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Chybové kódy pro chyby koncového bodu autorizace

Následující tabulka popisuje různé chybové kódy, které mohou být vráceny v `error` parametru chybové odpovědi.

| Kód chyby  | Description    | Akce klienta   |
|-------------|----------------|-----------------|
| `invalid_request` | Chyba protokolu, například chybějící požadovaný parametr. | Opravte a odešlete požadavek znovu. Toto je chyba vývoje obvykle zachycena při počátečním testování. |
| `unauthorized_client` | Klientská aplikace nemá oprávnění vyžadovat autorizační kód. | K této chybě obvykle dochází, když klientská aplikace není registrovaná v Azure AD nebo není přidaná do tenanta Azure AD uživatele. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| `access_denied`  | Vlastník prostředku zamítl souhlas.  | Klientská aplikace může uživatele informovat, že nemůže pokračovat, dokud se uživatel nesouhlasí. |
| `unsupported_response_type` | Autorizační Server v žádosti nepodporuje typ odpovědi. | Opravte a odešlete požadavek znovu. Toto je chyba vývoje obvykle zachycena při počátečním testování. Při pohledu v [hybridním toku](#request-an-id-token-as-well-hybrid-flow)signalizuje, že je nutné povolit nastavení implicitního udělení tokenu ID v registraci klientské aplikace. |
| `server_error`  | Na serveru došlo k neočekávané chybě.| Opakujte požadavek. Tyto chyby mohou být způsobeny dočasnými podmínkami. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděná na dočasnou chybu. |
| `temporarily_unavailable`   | Server je dočasně zaneprázdněný pro zpracování žádosti. | Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
| `invalid_resource`  | Cílový prostředek není platný, protože neexistuje, služba Azure AD ho nemůže najít nebo není správně nakonfigurovaná. | Tato chyba označuje prostředek, pokud existuje, není v tenantovi nakonfigurovaný. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| `login_required` | Bylo nalezeno příliš mnoho uživatelů. | Klient požadoval tiché ověřování ( `prompt=none` ), ale nenašlo se jeden uživatel. To může znamenat, že je v relaci aktivní více uživatelů, nebo žádní uživatelé. Tím se vezme v úvahu zvolený tenant (například pokud jsou aktivní dva účty Azure AD a jedna účet Microsoft a `consumers` je zvolena možnost tiché ověřování funguje). |
| `interaction_required` | Požadavek vyžaduje zásah uživatele. | Vyžaduje se další krok ověřování nebo souhlas. Opakujte požadavek bez `prompt=none` . |

### <a name="request-an-id-token-as-well-hybrid-flow"></a>Vyžádat i token ID (hybridní tok)

Chcete-li zjistit, kdo je před uplatněním autorizačního kódu, je běžné, že aplikace také vyžádají token ID, když požadují autorizační kód. Tento postup se nazývá *hybridní tok* , protože kombinuje implicitní udělení s tokem autorizačního kódu. Hybridní tok se běžně používá ve webových aplikacích, které chtějí vykreslovat stránku pro uživatele bez blokování uplatnění kódu, zejména [ASP.NET](quickstart-v2-aspnet-core-webapp.md). V tomto modelu využívají jednostránkové aplikace i tradiční webové aplikace výhodu se sníženou latencí.

Hybridní tok je stejný jako tok autorizačního kódu popsaný výše, ale se třemi dodatky, které jsou nutné k vyžádání tokenu ID: nové obory, nový response_type a nový `nonce` parametr dotazu.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code%20id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=fragment
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
&nonce=abcde
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Aktualizovaný parametr | Požadováno/volitelné | Popis |
|---------------|-------------|--------------|
|`response_type`| Povinné | Přidání `id_token` označuje serveru, který by aplikace chtěla jako token ID v odpovědi z `/authorize` koncového bodu.  |
|`scope`| Vyžadováno | Pro tokeny ID se musí aktualizovat tak, aby zahrnovaly obory tokenů ID – `openid` a volitelně `profile` a `email` . |
|`nonce`| Vyžadováno|     Hodnota obsažená v požadavku, která se vygenerovala aplikací, která se zahrne do výsledného id_token jako deklarace. Aplikace pak může tuto hodnotu ověřit a zmírnit tak útoky na opakované přehrání tokenů. Hodnota je obvykle náhodný jedinečný řetězec, který lze použít k identifikaci původu žádosti. |
|`response_mode`| Doporučeno | Určuje metodu, která se má použít k odeslání výsledného tokenu zpátky do vaší aplikace. Ve výchozím nastavení se `query` používá jenom autorizační kód, ale `fragment` Pokud požadavek obsahuje id_token `response_type` .  Doporučuje se ale použít aplikace `form_post` , zejména při použití `http:/localhost` jako identifikátoru URI přesměrování. |

Použití `fragment` jako režim odezvy způsobuje problémy pro webové aplikace, které čtou kód z přesměrování, protože prohlížeče neprojde fragmentem na webový server.  V těchto situacích by aplikace měly použít `form_post` režim odezvy k zajištění toho, aby byla všechna data odeslána na server. 

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď pomocí `response_mode=fragment` vypadá takto:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient#
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&id_token=eYj...
&state=12345
```

| Parametr | Popis  |
|-----------|--------------|
| `code` | Autorizační kód, který požadovaná aplikace požaduje. Aplikace může pomocí autorizačního kódu požádat o přístupový token pro cílový prostředek. Autorizační kódy jsou krátkodobé, obvykle vyprší po 10 minutách. |
| `id_token` | Token ID pro uživatele, který byl vydán prostřednictvím *implicitního udělení*. Obsahuje speciální `c_hash` deklaraci identity, která je hodnotou hash `code` ve stejné žádosti. |
| `state` | Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

## <a name="request-an-access-token"></a>Vyžádání přístupového tokenu

Teď, když jste získali authorization_code a máte mu udělené oprávnění, můžete uplatnit uplatnění `code` pro `access_token` na požadovaný prostředek. Provedete to tak, že odešlete `POST` požadavek na `/token` koncový bod:

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Zkuste tento požadavek provést v nástroji post! (Nezapomeňte nahradit `code` ) [ ![ Pokus o spuštění této žádosti v předzálohovacím](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parametr  | Požadováno/volitelné | Description     |
|------------|-------------------|----------------|
| `tenant`   | vyžadováno   | `{tenant}`Hodnotu v cestě k požadavku lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common` `organizations` `consumers` identifikátory klientů,, a. Další podrobnosti najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | vyžadováno  | ID aplikace (klienta), které stránka [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) přiřazená k vaší aplikaci. |
| `grant_type` | vyžadováno   | Musí být `authorization_code` pro tok autorizačního kódu.   |
| `scope`      | optional   | Mezerou oddělený seznam oborů. Rozsahy musí být z jednoho prostředku, společně s OIDC obory ( `profile` , `openid` , `email` ). Podrobnější vysvětlení oborů najdete v tématu [oprávnění, souhlas a obory](v2-permissions-and-consent.md). Toto je rozšíření Microsoftu pro tok autorizačního kódu, které umožňuje aplikacím deklarovat prostředek, který chce mít token při uplatnění tokenu.|
| `code`          | vyžadováno  | Authorization_code, kterou jste získali v první nožkě toku. |
| `redirect_uri`  | vyžadováno  | Stejná redirect_uri hodnota, která byla použita k získání authorization_code. |
| `client_secret` | vyžadováno pro důvěrné webové aplikace | Tajný klíč aplikace, který jste vytvořili na portálu pro registraci aplikací pro vaši aplikaci. Nepoužívejte tajný klíč aplikace v nativní aplikaci nebo v jedné stránce, protože client_secrets nejde spolehlivě uložit na zařízení nebo webové stránky. Vyžaduje se pro webové aplikace a webová rozhraní API, které mají možnost bezpečně ukládat client_secret na straně serveru.  Stejně jako všechny parametry, které jsou zde popsané, musí mít tajný klíč klienta zakódovaný URL před odesláním, což je krok obvykle proveden sadou SDK. Další informace o kódování identifikátoru URI naleznete v tématu [Obecná specifikace syntaxe identifikátoru URI](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | doporučil  | Stejný code_verifier, který byl použit k získání authorization_code. Vyžaduje se, pokud se v žádosti o udělení autorizačního kódu použil PKCE. Další informace najdete v [dokumentu RFC PKCE](https://tools.ietf.org/html/rfc7636). |

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
| `scope`         | Rozsahy, pro které je access_token platný. Volitelné – Toto je nestandardní. Pokud je tento parametr vynechán, bude tento token pro rozsahy požadované při počátečním navýšení toku. |
| `refresh_token` | Obnovovací token OAuth 2,0. Aplikace může tento token použít k získání dalších přístupových tokenů po vypršení platnosti aktuálního přístupového tokenu. Refresh_tokens jsou dlouhodobé a dají se použít k uchování přístupu k prostředkům po delší dobu. Další podrobnosti o aktualizaci přístupového tokenu najdete v [níže uvedené části](#refresh-the-access-token). <br> **Poznámka:** Zadáno pouze v případě `offline_access` , že byl požadován obor. |
| `id_token`      | JSON Web Token (JWT). Aplikace může dekódovat segmenty tohoto tokenu a vyžádat si informace o uživateli, který se přihlásil. Aplikace může hodnoty ukládat do mezipaměti a zobrazovat je a důvěrní klienti můžou tuto možnost použít k autorizaci. Další informace o id_tokens najdete v tématu [`id_token reference`](id-tokens.md) . <br> **Poznámka:** Zadáno pouze v případě `openid` , že byl požadován obor. |

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

| Kód chyby         | Description        | Akce klienta    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Chyba protokolu, například chybějící požadovaný parametr. | Opravte registraci žádosti nebo aplikace a odešlete žádost znovu.   |
| `invalid_grant`    | Autorizační kód nebo ověřovatel kódu PKCE je neplatný nebo vypršela jeho platnost. | Vyzkoušejte nový požadavek na `/authorize` koncový bod a ověřte, zda byl zadán správný parametr code_verifier.  |
| `unauthorized_client` | Ověřený klient nemá autorizaci k použití tohoto autorizačního typu udělení. | K tomu obvykle dochází, když klientská aplikace není registrovaná v Azure AD nebo není přidaná do tenanta Azure AD uživatele. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| `invalid_client` | Ověření klienta se nezdařilo.  | Pověření klienta nejsou platná. Chcete-li opravit, správce aplikace aktualizuje pověření.   |
| `unsupported_grant_type` | Autorizační Server nepodporuje typ udělení autorizace. | Změňte typ udělení v žádosti. Tento typ chyby by měl nastat pouze během vývoje a zjištěn při počátečním testování. |
| `invalid_resource` | Cílový prostředek není platný, protože neexistuje, služba Azure AD ho nemůže najít nebo není správně nakonfigurovaná. | To znamená, že pokud tento prostředek existuje, není v tenantovi nakonfigurovaný. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD.  |
| `interaction_required` | Nestandardní, protože specifikace OIDC volá pouze pro tento `/authorize` koncový bod. Požadavek vyžaduje zásah uživatele. Například je vyžadován další krok ověřování. | Opakujte `/authorize` požadavek se stejnými obory. |
| `temporarily_unavailable` | Server je dočasně zaneprázdněný pro zpracování žádosti. | Opakujte požadavek po krátké prodlevě. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
|`consent_required` | Požadavek vyžaduje souhlas uživatele. Tato chyba je nestandardní, protože se obvykle vrací jenom pro `/authorize` specifikace Endpoint na OIDC Specification. Vrátí se, když se `scope` v toku pro uplatnění kódu použil parametr, ke kterému klientská aplikace nemá oprávnění pro vyžádání.  | Klient by měl poslat uživateli zpátky do `/authorize` koncového bodu se správným oborem, aby mohl aktivovat souhlas. |
|`invalid_scope` | Rozsah požadovaný aplikací je neplatný.  | Aktualizujte hodnotu parametru scope v požadavku na ověření na platnou hodnotu. |

> [!NOTE]
> U jednostránkovéch aplikací se může zobrazit `invalid_request` Chyba s oznámením, že je povolená možnost uplatnění tokenu mezi zdroji jenom pro typ klienta s jednou stránkou.  To znamená, že identifikátor URI přesměrování použitý k vyžádání tokenu nebyl označen jako `spa` identifikátor URI přesměrování.  Projděte si [Postup registrace aplikace](#redirect-uri-setup-required-for-single-page-apps) , jak povolit tento tok.

## <a name="use-the-access-token"></a>Použití přístupového tokenu

Teď, když jste úspěšně získali `access_token` , můžete token v žádosti do webových rozhraní API použít tak, že ho zahrnete do `Authorization` hlavičky:

> [!TIP]
> Provést tento požadavek v nástroji post! (Nejdřív nahraďte `Authorization` hlavičku) [ ![ zkuste tuto žádost spustit v poli post](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d) .

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Aktualizace přístupového tokenu

Access_tokens jsou krátkodobé a po vypršení jejich platnosti je musíte aktualizovat, aby bylo možné pokračovat v přístupu k prostředkům. Můžete to udělat tak, že odešlete další `POST` požadavek na `/token` koncový bod a tentokrát zadáte `refresh_token` místo `code` .  Aktualizační tokeny jsou platné pro všechna oprávnění, která klient již obdržel souhlas. proto je `scope=mail.read` možné pro vyžádání nového přístupového tokenu použít obnovovací token vydaný na žádost pro `scope=api://contoso.com/api/UseResource` .

Aktualizace tokenů pro webové aplikace a nativní aplikace nemají zadané doby života. Obvykle jsou životnosti aktualizačních tokenů poměrně dlouhé. V některých případech ale platnost tokenů aktualizace vyprší, odvolají se nebo nemají dostatečná oprávnění pro požadovanou akci. Vaše aplikace musí očekávat a zpracovat [chyby vrácené koncovým bodem vystavení tokenu](#error-codes-for-token-endpoint-errors) správně. Jednostránkové aplikace ale získají token s životností po dobu 24 hodin, což vyžaduje nové ověřování každý den.  To se dá udělat v tichém režimu v elementu IFRAME, pokud jsou povolené soubory cookie třetích stran, ale je potřeba je udělat v prohlížečích na nejvyšší úrovni (v případě úplné navigace na stránce nebo v místní nabídce) v prohlížečích bez souborů cookie třetích stran, jako je Safari.

I když se tokeny aktualizace neodvolává při použití k získání nových přístupových tokenů, očekává se, že se starý obnovovací token zruší. [Specifikace OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-6) říká: "AUTORIZAČNÍ Server může vydat nový obnovovací token. v takovém případě musí klient zrušit starý obnovovací token a nahradit ho novým obnovovacím tokenem. Po vydání nového obnovovacího tokenu klientovi může autorizační Server odvolat starý obnovovací token. "

>[!IMPORTANT]
> Pro aktualizační tokeny odeslané na identifikátor URI přesměrování, který se zaregistruje jako `spa` , platnost tokenu obnovení vyprší za 24 hodin. Další obnovovací tokeny získané pomocí počátečního obnovovacího tokenu budou přenášet po uplynutí této doby, takže aplikace musí být připravené k opětovnému spuštění toku autorizačního kódu pomocí interaktivního ověřování pro získání nového obnovovacího tokenu každých 24 hodin. Uživatelé nemusejí zadávat své přihlašovací údaje, obvykle se nezobrazuje žádné uživatelské rozhraní, stačí znovu načíst aplikaci – ale prohlížeč musí navštívit přihlašovací stránku v horním limitu, aby se zobrazila relace přihlášení.  To je způsobeno [funkcemi ochrany osobních údajů v prohlížečích, které blokují soubory cookie třetích stran](reference-third-party-cookies-spas.md).

```HTTP

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
> Zkuste tento požadavek provést v nástroji post! (Nezapomeňte nahradit `refresh_token` ) [ ![ Pokus o spuštění této žádosti v předzálohovacím](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parametr     | Typ           | Description        |
|---------------|----------------|--------------------|
| `tenant`        | vyžadováno     | `{tenant}`Hodnotu v cestě k požadavku lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common` `organizations` `consumers` identifikátory klientů,, a. Další podrobnosti najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | vyžadováno    | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `grant_type`    | vyžadováno    | Musí být `refresh_token` pro tuto nožku toku autorizačního kódu. |
| `scope`         | vyžadováno    | Mezerou oddělený seznam oborů. Rozsahy požadované v této nožkě musí být stejné jako podmnožina oborů požadovaných v původní nožkě authorization_code žádosti. Pokud obory zadané v tomto požadavku vynásobí více prostředků serveru, pak bude platforma Microsoft Identity vracet token pro prostředek zadaný v prvním oboru. Podrobnější vysvětlení oborů najdete v tématu [oprávnění, souhlas a obory](v2-permissions-and-consent.md). |
| `refresh_token` | vyžadováno    | Refresh_token, kterou jste získali v druhé nožkě toku. |
| `client_secret` | vyžadováno pro webové aplikace | Tajný klíč aplikace, který jste vytvořili na portálu pro registraci aplikací pro vaši aplikaci. Neměl by se používat v nativní aplikaci, protože client_secrets nemůže být spolehlivě uložená na zařízeních. Vyžaduje se pro webové aplikace a webová rozhraní API, které mají možnost bezpečně ukládat client_secret na straně serveru. Tento tajný klíč musí být kódovaný pomocí adresy URL. Další informace naleznete v tématu [Obecná specifikace syntaxe identifikátoru URI](https://tools.ietf.org/html/rfc3986#page-12). |

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
| `refresh_token` | Nový token pro obnovení OAuth 2,0. Starý obnovovací token byste měli nahradit tímto nově získaným aktualizačním tokenem, abyste zajistili, že obnovovací tokeny zůstanou platné i tak dlouho. <br> **Poznámka:** Zadáno pouze v případě `offline_access` , že byl požadován obor.|
| `id_token`      | Nepodepsaný JSON Web Token (JWT). Aplikace může dekódovat segmenty tohoto tokenu a vyžádat si informace o uživateli, který se přihlásil. Aplikace může hodnoty ukládat do mezipaměti a zobrazovat je, ale nemělo by je spoléhat na jakékoli autorizace nebo hranice zabezpečení. Další informace o id_tokens najdete v tématu [`id_token reference`](id-tokens.md) . <br> **Poznámka:** Zadáno pouze v případě `openid` , že byl požadován obor. |

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
