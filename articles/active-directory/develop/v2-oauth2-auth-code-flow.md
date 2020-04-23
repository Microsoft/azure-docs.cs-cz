---
title: Tok autorizačního kódu OAuth - platforma identit microsoftu | Azure
description: Vytvářejte webové aplikace pomocí implementace ověřovacího protokolu OAuth 2.0 platformou microsoftu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: fcd80c052edf659f93f97800da3112c1f11309cc
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868496"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Platforma identit Microsoftu a tok autorizačního kódu OAuth 2.0

Udělení autorizačního kódu OAuth 2.0 lze použít v aplikacích nainstalovaných v zařízení k získání přístupu k chráněným prostředkům, jako jsou webová rozhraní API. Pomocí implementace platformy Microsoft identity oOUth 2.0 můžete přidat přihlášení a přístup k rozhraní API pro vaše mobilní a desktopové aplikace. Tato příručka je jazykově nezávislá a popisuje, jak odesílat a přijímat zprávy HTTP bez použití libovolné [knihovny ověřování s otevřeným zdrojovým kódem Azure](reference-v2-libraries.md).

Tento článek popisuje, jak programovat přímo proti protokolu ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny ověřování společnosti Microsoft (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Také se podívejte na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

Tok autorizačního kódu OAuth 2.0 je popsán v [bodu 4.1 specifikace OAuth 2.0](https://tools.ietf.org/html/rfc6749). Používá se k ověřování a autorizaci ve většině typů aplikací, včetně [webových aplikací](v2-app-types.md#web-apps) a [nativně nainstalovaných aplikací](v2-app-types.md#mobile-and-native-apps). Tok umožňuje aplikacím bezpečně získat access_tokens, které lze použít pro přístup k prostředkům zabezpečeným koncovým bodem platformy identit Microsoftu.

## <a name="protocol-diagram"></a>Protokolový diagram

Na vysoké úrovni celý tok ověřování pro nativní/mobilní aplikace vypadá trochu takto:

![Tok kódu OAuth Auth](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>Žádost o autorizační kód

Tok autorizačního kódu začíná tím, že `/authorize` klient nasměruje uživatele do koncového bodu. V tomto požadavku klient požaduje `openid` `offline_access`, `https://graph.microsoft.com/mail.read ` a oprávnění od uživatele.  Některá oprávnění jsou omezena správcem, například zápis dat `Directory.ReadWrite.All`do adresáře organizace pomocí . Pokud vaše aplikace požaduje přístup k jednomu z těchto oprávnění od uživatele organizace, uživatel obdrží chybovou zprávu, že není oprávněn souhlasit s oprávněními vaší aplikace. Chcete-li požádat o přístup k oborům s omezeným přístupem správce, měli byste si je vyžádat přímo od správce společnosti.  Další informace naleznete v [části Oprávnění s omezenýmpřístupem správce](v2-permissions-and-consent.md#admin-restricted-permissions).

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
> Klikněte na níže uvedený odkaz pro provedení tohoto požadavku! Po přihlášení by měl být prohlížeč `https://localhost/myapp/` přesměrován na adresu s a `code` v adresním řádku.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr    | Povinné/nepovinné | Popis |
|--------------|-------------|--------------|
| `tenant`    | Požadovaný    | Hodnotu `{tenant}` v cestě požadavku lze použít k řízení, kdo se může přihlásit do aplikace. Povolené hodnoty `common`jsou `organizations` `consumers`identifikátory , , a tenant. Další podrobnosti naleznete v [základech protokolu](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | Požadovaný    | **ID aplikace (klienta),** které je k vaší aplikaci přiřazené [na portálu Azure – možnosti registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908)  |
| `response_type` | Požadovaný    | Musí `code` obsahovat tok autorizačního kódu.       |
| `redirect_uri`  | Požadovaný | Redirect_uri vaší aplikace, kde mohou být odpovědi na ověření odesílány a přijímány vaší aplikací. Musí přesně odpovídat jednomu z redirect_uris, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódována adresou URL. Pro nativní & mobilní aplikace byste `https://login.microsoftonline.com/common/oauth2/nativeclient`měli použít výchozí hodnotu aplikace .   |
| `scope`  | Požadovaný    | Mezery oddělený seznam [oborů,](v2-permissions-and-consent.md) které chcete, aby uživatel souhlas.  Pro `/authorize` část žádosti to může zahrnovat více prostředků, což umožňuje vaší aplikaci získat souhlas pro více webových api, kterým chcete volat. |
| `response_mode`   | Doporučené | Určuje metodu, která by měla být použita k odeslání výsledného tokenu zpět do vaší aplikace. Může to být jedna z následujících možností:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query`poskytuje kód jako parametr řetězce dotazu na identifikátorURI přesměrování. Pokud požadujete token ID pomocí implicitního toku, `query` nemůžete použít, jak je uvedeno ve [specifikaci OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Pokud požadujete pouze kód, můžete použít `query` `fragment`, `form_post`nebo . `form_post`spustí post obsahující kód pro přesměrování URI. Další informace najdete v tématu [OpenID Connect protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | Doporučené | Hodnota zahrnutá v požadavku, která bude také vrácena v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně generovaná jedinečná hodnota se obvykle používá k [zabránění útokům padělání požadavků na příčce webu](https://tools.ietf.org/html/rfc6749#section-10.12). Hodnota může také zakódovat informace o stavu uživatele v aplikaci před došlo k požadavku na ověření, jako je například stránka nebo zobrazení, na které se naněmuje. |
| `prompt`  | optional    | Označuje typ interakce uživatele, která je požadována. Platné hodnoty jsou `login`v tuto `none`chvíli `consent`pouze , a .<br/><br/>- `prompt=login`vynutí uživateli zadat své přihlašovací údaje na tuto žádost, negovat single-sign on.<br/>- `prompt=none`je naopak - zajistí, že uživatel nebude prezentován s žádnou interaktivní výzvou. Pokud požadavek nelze dokončit bezobslužně prostřednictvím jednotného přihlášení, koncový `interaction_required` bod platformy identity Microsoftvrátí chybu.<br/>- `prompt=consent`spustí dialogové okno souhlasu OAuth po přihlášení uživatele a požádá jej o udělení oprávnění k aplikaci.<br/>- `prompt=select_account`přeruší možnosti jednotného přihlášení s výběrem účtu, které uvádí všechny účty v relaci nebo jakýkoli zapamatovaný účet, nebo možnost zvolit si použití jiného účtu úplně.<br/> |
| `login_hint`  | optional    | Lze použít k předběžnému vyplnění pole uživatelského jména/e-mailové adresy přihlašovací stránky uživatele, pokud znáte jeho uživatelské jméno předem. Aplikace často budou používat tento parametr při opětovném ověřování, protože již extrahoval uživatelské jméno z předchozího přihlášení pomocí deklarace. `preferred_username`   |
| `domain_hint`  | optional    | Může být `consumers` jeden `organizations`z nebo .<br/><br/>Pokud je zahrnuta, přeskočí proces zjišťování na základě e-mailu, který uživatel prochází na přihlašovací stránce, což vede k mírně efektivnějšímu uživatelskému prostředí. Aplikace často budou používat tento parametr při opětovném ověřování `tid` extrahováním z předchozího přihlášení. Pokud `tid` je `9188040d-6c67-4c5b-b112-36a304b66dad`hodnota deklarace `domain_hint=consumers`pohledávky , měli byste použít . V opačném `domain_hint=organizations`případě použijte .  |
| `code_challenge_method` | optional    | Metoda použitá `code_verifier` ke kódování `code_challenge` parametru. Může se na nich vyvěšovat jedna z následujících hodnot:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Je-li `code_challenge` vyloučeno, předpokládá se, že je prostý text, pokud `code_challenge` je zahrnuta. Platforma identit microsoftu `S256`podporuje obě aplikace a `plain` . Další informace naleznete v [pkce RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | optional | Používá se k zabezpečení udělení autorizačního kódu prostřednictvím kódu Proof Key for Code Exchange (PKCE) od nativního klienta. Povinné, `code_challenge_method` pokud je součástí dodávky. Další informace naleznete v [pkce RFC](https://tools.ietf.org/html/rfc7636). |

V tomto okamžiku bude uživatel vyzván k zadání svých přihlašovacích údajů a dokončení ověřování. Koncový bod platformy identit microsoft také zajistí, že uživatel má souhlas `scope` s oprávněními uvedenými v parametru dotazu. Pokud uživatel nesouhlasil s žádným z těchto oprávnění, požádá jej o souhlas s požadovanými oprávněními. Podrobnosti o [oprávněních, souhlasu a víceklientských aplikacích jsou uvedeny zde](v2-permissions-and-consent.md).

Jakmile uživatel ověří a udělí souhlas, koncový bod platformy identit microsoftu `redirect_uri`vrátí odpověď do vaší `response_mode` aplikace na uvedené , pomocí metody zadané v parametru.

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď `response_mode=query` pomocí vypadá takto:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametr | Popis  |
|-----------|--------------|
| `code` | Authorization_code, které aplikace požadovala. Aplikace můžete použít autorizační kód požádat o přístupový token pro cílový prostředek. Authorization_codes jsou krátkodobé, obvykle vyprší asi po 10 minutách. |
| `state` | Pokud je parametr stavu zahrnut a požadavek, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v požadavku a odpovědi jsou identické. |

#### <a name="error-response"></a>Odpověď na chybu

Odpovědi na chyby mohou `redirect_uri` být také odeslány, aby je aplikace mohla správně zpracovat:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis  |
|----------|------------------|
| `error`  | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází, a lze jej použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může vývojáři pomoci identifikovat hlavní příčinu chyby ověřování. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Chybové kódy chyb pro chyby autorizačního koncového bodu

Následující tabulka popisuje různé kódy chyb, které `error` mohou být vráceny v parametru chybové odpovědi.

| Kód chyby  | Popis    | Akce klienta   |
|-------------|----------------|-----------------|
| `invalid_request` | Chyba protokolu, například chybějící požadovaný parametr. | Opravte a znovu odešlete požadavek. Jedná se o chybu vývoje, která byla obvykle zachycena během počátečního testování. |
| `unauthorized_client` | Klientská aplikace není oprávněna požadovat autorizační kód. | K této chybě obvykle dochází, když klientská aplikace není registrovaná ve službě Azure AD nebo není přidána do klienta Azure AD uživatele. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Služby Azure AD. |
| `access_denied`  | Vlastník prostředku odepřel souhlas  | Klientská aplikace může upozornit uživatele, že nemůže pokračovat, pokud uživatel souhlasí. |
| `unsupported_response_type` | Autorizační server nepodporuje typ odpovědi v požadavku. | Opravte a znovu odešlete požadavek. Jedná se o chybu vývoje, která byla obvykle zachycena během počátečního testování.  |
| `server_error`  | Na serveru došlo k neočekávané chybě.| Opakujte požadavek. Tyto chyby mohou být důsledkem dočasných podmínek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna na dočasnou chybu. |
| `temporarily_unavailable`   | Server je dočasně příliš zaneprázdněn pro zpracování požadavku. | Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
| `invalid_resource`  | Cílový prostředek je neplatný, protože neexistuje, Azure AD ho nemůže najít nebo není správně nakonfigurovaný. | Tato chyba označuje, že prostředek, pokud existuje, nebyl v tenantovi nakonfigurován. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Služby Azure AD. |
| `login_required` | Bylo nalezeno příliš mnoho nebo žádné uživatele. | Klient požádal o`prompt=none`tiché ověření ( ), ale jeden uživatel nebyl nalezen. To může znamenat, že v relaci je aktivních více uživatelů nebo žádní uživatelé. To bere v úvahu zvoleného klienta (například pokud existují dva účty `consumers` Azure AD aktivní a jeden účet Microsoft a je vybrán, bude fungovat tiché ověřování). |
| `interaction_required` | Požadavek vyžaduje interakci uživatele. | Je vyžadován další krok ověřování nebo souhlas. Opakujte požadavek `prompt=none`bez . |

## <a name="request-an-access-token"></a>Žádost o přístupový token

Nyní, když jste získali authorization_code a uživatel vám udělil oprávnění, můžete uplatnit `code` pro `access_token` požadovaný prostředek. To provést odesláním `POST` požadavku `/token` do koncového bodu:

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
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> Zkuste provést tento požadavek v Pošťák! (Nezapomeňte vyměnit `code`) [Zkuste spustit tento požadavek v Pošťáku. ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| Parametr  | Povinné/nepovinné | Popis     |
|------------|-------------------|----------------|
| `tenant`   | Požadovaný   | Hodnotu `{tenant}` v cestě požadavku lze použít k řízení, kdo se může přihlásit do aplikace. Povolené hodnoty `common`jsou `organizations` `consumers`identifikátory , , a tenant. Další podrobnosti naleznete v [základech protokolu](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | Požadovaný  | ID aplikace (klienta), které je k vaší aplikaci přiřazena stránka [Registrace aplikací azure– registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `grant_type` | Požadovaný   | Musí `authorization_code` být pro tok autorizačního kódu.   |
| `scope`      | Požadovaný   | Mezera-oddělený seznam oborů. Obory požadované v této etapě musí být rovnocenné nebo podmnožinu oborů požadovaných v první etapě. Obory musí podoby jednoho prostředku spolu s obory`profile` `openid`OIDC ( , , `email`). Podrobnější vysvětlení oborů naleznete v [části oprávnění, souhlas a obory](v2-permissions-and-consent.md). |
| `code`          | Požadovaný  | authorization_code, které jste získali v první části toku. |
| `redirect_uri`  | Požadovaný  | Stejná hodnota redirect_uri, která byla použita k získání authorization_code. |
| `client_secret` | vyžadováno pro webové aplikace | Tajný klíč aplikace, který jste vytvořili na portálu pro registraci aplikací pro vaši aplikaci. Tajný klíč aplikace byste neměli používat v nativní aplikaci, protože client_secrets spolehlivě nelze uložit na zařízeních. Je vyžadována pro webové aplikace a webová api, které mají možnost bezpečně ukládat client_secret na straně serveru.  Tajný klíč klienta musí být před odesláním kódován adresou URL. Pro více informací klikněte [zde](https://tools.ietf.org/html/rfc3986#page-12). |
| `code_verifier` | optional  | Stejný code_verifier, který byl použit k získání authorization_code. Povinné, pokud byla pkce použita v žádosti o udělení autorizačního kódu. Další informace naleznete v [pkce RFC](https://tools.ietf.org/html/rfc7636). |

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
| `access_token`  | Požadovaný přístupový token. Aplikace můžete použít tento token k ověření zabezpečeného prostředku, jako je například webové rozhraní API.  |
| `token_type`    | Označuje hodnotu typu tokenu. Jediný typ, který podporuje Azure AD je Nosič |
| `expires_in`    | Jak dlouho je přístupový token platný (v sekundách). |
| `scope`         | Obory, pro které je access_token platný. |
| `refresh_token` | Obnovovací token OAuth 2.0. Aplikace můžete použít tento token získat další přístupové tokeny po vypršení platnosti aktuálního přístupového tokenu. Refresh_tokens jsou dlouhodobé a lze je použít k zachování přístupu k prostředkům po delší dobu. Další podrobnosti o aktualizaci přístupového tokenu naleznete v [části níže](#refresh-the-access-token). <br> **Poznámka:** Pouze pokud `offline_access` byl požadován obor. |
| `id_token`      | Webový token JSON (JWT). Aplikace může dekódovat segmenty tohoto tokenu a požádat o informace o uživateli, který se přihlásil. Aplikace může ukládat hodnoty do mezipaměti a zobrazovat je, ale neměla by se na ně spoléhat pro žádné hranice autorizace nebo zabezpečení. Další informace o id_tokens [`id_token reference`](id-tokens.md)naleznete v tématu . <br> **Poznámka:** Pouze pokud `openid` byl požadován obor. |

### <a name="error-response"></a>Odpověď na chybu

Odpovědi na chyby budou vypadat takto:

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
| `error`       | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází, a lze jej použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může vývojáři pomoci identifikovat hlavní příčinu chyby ověřování. |
| `error_codes` | Seznam kódů chyb specifických pro STS, které mohou pomoci při diagnostice.  |
| `timestamp`   | Čas, kdy došlo k chybě. |
| `trace_id`    | Jedinečný identifikátor požadavku, který může pomoci při diagnostice. |
| `correlation_id` | Jedinečný identifikátor požadavku, který může pomoci při diagnostice napříč součástmi. |

### <a name="error-codes-for-token-endpoint-errors"></a>Kódy chyb pro chyby koncového bodu tokenu

| Kód chyby         | Popis        | Akce klienta    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Chyba protokolu, například chybějící požadovaný parametr. | Oprava a opětovné odeslání žádosti   |
| `invalid_grant`    | Autorizační kód nebo ověřovatel kódu PKCE je neplatný nebo vypršela jeho platnost. | Zkuste nový požadavek `/authorize` na koncový bod a ověřte, zda byl parametr code_verifier správný.  |
| `unauthorized_client` | Ověřený klient není oprávněn používat tento typ udělení autorizace. | K tomu obvykle dochází, když klientská aplikace není registrovaná ve službě Azure AD nebo není přidána do klienta Azure AD uživatele. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Služby Azure AD. |
| `invalid_client` | Ověření klienta se nezdařilo.  | Pověření klienta nejsou platná. Chcete-li opravit, správce aplikace aktualizuje pověření.   |
| `unsupported_grant_type` | Autorizační server nepodporuje typ udělení autorizace. | Změňte typ grantu v žádosti. K tomuto typu chyby by mělo dojít pouze během vývoje a být zjištěna během počátečního testování. |
| `invalid_resource` | Cílový prostředek je neplatný, protože neexistuje, Azure AD ho nemůže najít nebo není správně nakonfigurovaný. | To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Služby Azure AD.  |
| `interaction_required` | Požadavek vyžaduje interakci uživatele. Je například vyžadován další krok ověřování. | Opakujte požadavek se stejným prostředkem.  |
| `temporarily_unavailable` | Server je dočasně příliš zaneprázdněn pro zpracování požadavku. | Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |

## <a name="use-the-access-token"></a>Použití přístupového tokenu

Nyní, když jste úspěšně `access_token`získali , můžete použít token v požadavcích na `Authorization` webová api jeho zahrnutím do záhlaví:

> [!TIP]
> Proveďte tento požadavek v Pošťák! (Nejprve `Authorization` nahraďte záhlaví) [Zkuste spustit tento požadavek v Pošťáku. ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Aktualizace přístupového tokenu

Access_tokens jsou krátkodobé a je nutné je aktualizovat po vypršení jejich platnosti, chcete-li pokračovat v přístupu k prostředkům. Můžete tak učinit odesláním `POST` jiného `/token` požadavku do koncového `refresh_token` bodu, `code`tentokrát poskytuje místo .  Obnovovací tokeny jsou platné pro všechna oprávnění, pro která váš klient již získal `scope=mail.read` souhlas – proto lze aktualizovat `scope=api://contoso.com/api/UseResource`token vydaný na žádost pro lze požádat o nový přístupový token pro .

Obnovovací tokeny nemají zadané doby životnosti. Obvykle životnost impoziumů obnovovacítokeny jsou obvykle poměrně dlouhé. V některých případech však platnost tokenů aktualizace vyprší, jsou odvolány nebo postrádají dostatečná oprávnění pro požadovanou akci. Vaše aplikace musí očekávat a zpracovávat [chyby vrácené koncovým bodem vystavování tokenu](#error-codes-for-token-endpoint-errors) správně.

Přestože obnovovací tokeny nejsou odvolány při použití k získání nových přístupových tokenů, očekává se, že zahodíte starý obnovovací token. [OAuth 2.0 spec](https://tools.ietf.org/html/rfc6749#section-6) říká: "Autorizační server může vydat nový obnovovací token, v takovém případě musí klient zahodit starý obnovovací token a nahradit jej novým obnovovacím tokenem. Autorizační server může odvolat starý obnovovací token po vydání nového obnovovacího tokenu klientovi."

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
> Zkuste provést tento požadavek v Pošťák! (Nezapomeňte vyměnit `refresh_token`) [Zkuste spustit tento požadavek v Pošťáku. ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| Parametr     |                | Popis        |
|---------------|----------------|--------------------|
| `tenant`        | Požadovaný     | Hodnotu `{tenant}` v cestě požadavku lze použít k řízení, kdo se může přihlásit do aplikace. Povolené hodnoty `common`jsou `organizations` `consumers`identifikátory , , a tenant. Další podrobnosti naleznete v [základech protokolu](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | Požadovaný    | **ID aplikace (klienta),** které je k vaší aplikaci přiřazené [na portálu Azure – možnosti registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `grant_type`    | Požadovaný    | Musí `refresh_token` být pro tuto část toku autorizačního kódu. |
| `scope`         | Požadovaný    | Mezera-oddělený seznam oborů. Obory požadované v této části musí být rovnocenné nebo podmnožinu oborů požadovaných v původním authorization_code požadavku. Pokud obory zadané v tomto požadavku pokrývají více serverů prostředků, pak koncový bod platformy identity společnosti Microsoft vrátí token pro prostředek zadaný v prvním oboru. Podrobnější vysvětlení oborů naleznete v [části oprávnění, souhlas a obory](v2-permissions-and-consent.md). |
| `refresh_token` | Požadovaný    | refresh_token, které jste získali ve druhé části toku. |
| `client_secret` | vyžadováno pro webové aplikace | Tajný klíč aplikace, který jste vytvořili na portálu pro registraci aplikací pro vaši aplikaci. Neměl by se používat v nativní aplikaci, protože client_secrets nelze spolehlivě uložit na zařízeních. Je vyžadována pro webové aplikace a webová api, které mají možnost bezpečně ukládat client_secret na straně serveru. Tento tajný klíč musí být URL-kódován, pro více informací klikněte [zde](https://tools.ietf.org/html/rfc3986#page-12). |

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
| `access_token`  | Požadovaný přístupový token. Aplikace můžete použít tento token k ověření zabezpečeného prostředku, jako je například webové rozhraní API. |
| `token_type`    | Označuje hodnotu typu tokenu. Jediný typ, který podporuje Azure AD je Nosič |
| `expires_in`    | Jak dlouho je přístupový token platný (v sekundách).   |
| `scope`         | Obory, pro které je access_token platný.    |
| `refresh_token` | Nový obnovovací token OAuth 2.0. Starý obnovovací token byste měli nahradit tímto nově získaným obnovovacím tokenem, abyste zajistili, že vaše obnovovací tokeny zůstanou platné co nejdéle. <br> **Poznámka:** Pouze pokud `offline_access` byl požadován obor.|
| `id_token`      | Nepodepsaný webový token JSON (JWT). Aplikace může dekódovat segmenty tohoto tokenu a požádat o informace o uživateli, který se přihlásil. Aplikace může ukládat hodnoty do mezipaměti a zobrazovat je, ale neměla by se na ně spoléhat pro žádné hranice autorizace nebo zabezpečení. Další informace o id_tokens [`id_token reference`](id-tokens.md)naleznete v tématu . <br> **Poznámka:** Pouze pokud `openid` byl požadován obor. |

#### <a name="error-response"></a>Odpověď na chybu

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
| `error`           | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází, a lze jej použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může vývojáři pomoci identifikovat hlavní příčinu chyby ověřování.           |
| `error_codes` |Seznam kódů chyb specifických pro STS, které mohou pomoci při diagnostice. |
| `timestamp` | Čas, kdy došlo k chybě. |
| `trace_id` | Jedinečný identifikátor požadavku, který může pomoci při diagnostice. |
| `correlation_id` | Jedinečný identifikátor požadavku, který může pomoci při diagnostice napříč součástmi. |

Popis kódů chyb a doporučenou akci klienta naleznete v [tématu Chybové kódy pro chyby koncového bodu tokenu](#error-codes-for-token-endpoint-errors).
