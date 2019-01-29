---
title: Toku kódu autorizace OAuth Azure AD v2.0 | Dokumentace Microsoftu
description: Vytváření webových aplikací pomocí služby Azure AD implementaci ověřovacího protokolu OAuth 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 25b2e90c6293b93a15aeae2fbf08b70cb191cef0
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098710"
---
# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>Protokoly v2.0 - tok autorizačního kódu OAuth 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Udělení autorizačního kódu OAuth 2.0 je možné v aplikacích, které jsou nainstalované v zařízení získat přístup k chráněným prostředkům, jako je například webové rozhraní API. Pomocí implementace v2.0 modelu aplikace OAuth 2.0, můžete přidat přihlášení a přístup k rozhraní API do vašich mobilních a desktopových aplikací. Tato příručka je nezávislá na jazyce a popisuje, jak posílat a přijímat zprávy HTTP bez použití [ověřování Azure open source knihoven](active-directory-authentication-libraries.md).

> [!NOTE]
> Ne všechny scénáře Azure Active Directory a funkce jsou podporovány bodem v2.0. Pokud chcete zjistit, pokud je vhodné použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).

Tok autorizačního kódu OAuth 2.0 je popsaný v [části 4.1 specifikace OAuth 2.0](https://tools.ietf.org/html/rfc6749). Používá se k provedení ověřování a autorizace ve většině typů aplikací, včetně [webové aplikace](v2-app-types.md#web-apps) a [nativně nainstalované aplikace](v2-app-types.md#mobile-and-native-apps). Tento tok umožňuje aplikacím bezpečně získat access_tokens, který slouží pro přístup k prostředkům zabezpečeným přes koncový bod verze 2.0. 

## <a name="protocol-diagram"></a>Diagram protokolu

Na vysoké úrovni tok celý ověření pro nativní/mobilní aplikace vypadá trochu takto:

![Tok kódu ověřování OAuth](./media/v2-oauth2-auth-code-flow/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Žádost o autorizační kód

Tok autorizačního kódu začíná klienta směruje uživatele `/authorize` koncového bodu. V této žádosti klient naznačuje oprávnění, která je potřeba získat od uživatele:

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
> Klikněte na odkaz níže k provedení tohoto požadavku. Po přihlášení by měl prohlížeč přesměrován na `https://localhost/myapp/` s `code` do adresního řádku.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr    | Požadované a volitelné | Popis |
|--------------|-------------|--------------|
| `tenant`    | povinné    | `{tenant}` Hodnota v cestě požadavku je možné řídit, kdo se můžete přihlásit do aplikace. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátorů klienta. Další podrobnosti najdete v části [protokol Základy](active-directory-v2-protocols.md#endpoints).  |
| `client_id`   | povinné    | ID aplikace (klient) na portál pro registraci ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) nebo **registrace aplikací (Preview)** prostředí na webu Azure Portal přiřazené vaší aplikaci.  |
| `response_type` | povinné    | Musí zahrnovat `code` pro tok autorizačního kódu.       |
| `redirect_uri`  | Doporučené | Redirect_uri vaší aplikace, kde můžete odesílat a přijímat aplikací pro žádosti o ověření. Musí odpovídat přesně jeden z redirect_uris, které jste zaregistrovali na portálu, s tím rozdílem, musí být kódování url. Pro nativní a mobilní aplikace, byste měli použít výchozí hodnotu `https://login.microsoftonline.com/common/oauth2/nativeclient`.   |
| `scope`  | povinné    | Místo oddělený seznam [obory](v2-permissions-and-consent.md) , že má uživatel vyjádřit souhlas. |
| `response_mode`   | Doporučené | Určuje metodu, která se má použít k odeslání výsledný token zpátky do vaší aplikace. Může být jedna z následujících akcí:<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` poskytuje kód jako parametru řetězce dotazu na váš identifikátor URI pro přesměrování. Pokud se požaduje token ID pomocí implicitního toku, nemůžete použít `query` podle [OpenID specifikace](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Pokud požadujete přesně takový kód, můžete použít `query`, `fragment`, nebo `form_post`. `form_post` provede příspěvek, který obsahuje kód, který váš identifikátor URI pro přesměrování. Další informace najdete v tématu [protokolu OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  |
| `state`                 | Doporučené | Hodnota v požadavku, která se také vrátit v odpovědi tokenu. Může být řetězec jakéhokoli obsahu, který chcete. Náhodně generované jedinečná hodnota se obvykle používá pro [prevence útoků proti padělání žádosti více webů](https://tools.ietf.org/html/rfc6749#section-10.12). Hodnota můžete kódovat také informace o stavu uživatele v aplikaci, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které byly na. |
| `prompt`  | nepovinné    | Určuje typ interakce s uživatelem, který je požadován. V tuto chvíli pouze platné hodnoty jsou `login`, `none`, a `consent`.<br/><br/>- `prompt=login` Vynutí uživatele k zadání přihlašovacích údajů tohoto požadavku negace jednotného přihlašování.<br/>- `prompt=none` je opakem – zajistí, že uživatel se nezobrazí se žádné interaktivní výzvu jakýmkoli způsobem. Pokud nemůže požadavek dokončit tiše prostřednictvím jednotného přihlašování, koncový bod verze 2.0 vrátí `interaction_required` chyby.<br/>- `prompt=consent` Dialogové okno souhlasu OAuth se aktivuje, až se uživatel přihlásí, s výzvou uživateli udělit oprávnění k aplikaci. |
| `login_hint`  | nepovinné    | Umožňuje předem vyplnit pole uživatelské jméno nebo e-mailová adresa stránky přihlášení pro uživatele, pokud znáte svoje uživatelské jméno předem. Často aplikace bude používat tento parametr během opětovné ověření uživatelského jména s již extrahovat z předchozí přihlášení pomocí `preferred_username` deklarací identity.   |
| `domain_hint`  | nepovinné    | Může být jedna z `consumers` nebo `organizations`.<br/><br/>Pokud zahrnutý, budou přeskočeny procesu zjišťování na základě e-mailu uživatele prochází na v2.0 přihlašovací stránku, což vede k poněkud jednodušší činnost koncového uživatele. Často aplikace bude používat tento parametr během opětovné ověření extrahováním `tid` z předchozí přihlášení. Pokud `tid` deklarace identity, je hodnota `9188040d-6c67-4c5b-b112-36a304b66dad`, měli byste použít `domain_hint=consumers`. Jinak použijte `domain_hint=organizations`.  |
| `code_challenge_method` | nepovinné    | Metoda použitá pro kódování `code_verifier` pro `code_challenge` parametru. Může být jedna z následujících hodnot:<br/><br/>- `plain` <br/>- `S256`<br/><br/>Pokud vyloučeny, `code_challenge` předpokládá, že je možné ve formátu prostého textu, pokud `code_challenge` je součástí. Azure AAD v2.0 podporuje obě `plain` a `S256`. Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| `code_challenge`  | nepovinné | Používá k zabezpečení udělení autorizace kódu pomocí testování klíče pro výměnu kód (PKCE) z nativního klienta. Požadováno pokud `code_challenge_method` je součástí. Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

V tomto okamžiku uživatele vyzve k zadání přihlašovacích údajů a bylo možné ověření dokončit. Koncový bod v2.0 také pomohou zajistit, aby uživatel vyjádřil souhlas se oprávnění uvedená v `scope` parametr dotazu. Pokud uživatele. nevyjádřil souhlas některý z těchto oprávnění, požádá o souhlas požadovaná oprávnění. Podrobnosti o [zde uvedené aplikace s více tenanty, oprávnění a souhlas](v2-permissions-and-consent.md).

Jakmile se uživatel ověří a udělí svůj souhlas, koncový bod verze 2.0 vrátí odpověď na vaši aplikaci na označený `redirect_uri`, pomocí metody popsané v `response_mode` parametru.

#### <a name="successful-response"></a>Úspěšné odpovědi

Úspěšné odpovědi pomocí `response_mode=query` vypadá jako:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametr | Popis  |
|-----------|--------------|
| `code` | Authorization_code požadovanou aplikaci. Aplikace může používat autorizační kód k vyžádání tokenu pro cílový prostředek. Authorization_codes jsou krátkodobé, obvykle platnost souhlasu vyprší po přibližně 10 minut. |
| `state` | Pokud parametr stavu je zahrnutý v požadavku, by se zobrazit stejnou hodnotu v odpovědi. Aplikace by měl ověřit, že jsou identické hodnoty stavu v požadavku a odpovědi. |

#### <a name="error-response"></a>Odpověď na chybu

Chybové odpovědi může také být odeslán `redirect_uri` tak aplikaci můžete odpovídajícím způsobem zpracovat:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis  |
|----------|------------------|
| `error`  | Řetězec kódu chyby, která slouží ke klasifikaci typy chyb, ke kterým dochází a je možné reagovat na chyby. |
| `error_description` | Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kódy chyb pro chyby koncový bod autorizace

Následující tabulka popisuje různé kódy chyb, které mohou být vráceny v `error` parametr odpovědi na chybu.

| Kód chyby  | Popis    | Akce klienta   |
|-------------|----------------|-----------------|
| `invalid_request` | Chyba protokolu, jako je například chybí povinný parametr. | Opravte a odešlete požadavek znovu. Jedná se o chybu vývoj obvykle zachycena během počátečního testování. |
| `unauthorized_client` | Klientská aplikace není povoleno požádat o autorizační kód. | K této chybě obvykle dochází, když klientská aplikace není zaregistrovaný ve službě Azure AD nebo se nepřidal do tenanta služby Azure AD daného uživatele. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a jeho přidání do služby Azure AD. |
| `access_denied`  | Odepřen souhlas vlastníka prostředku  | Klientská aplikace může upozornit uživatele, který nemůže pokračovat, dokud uživatel souhlasí. |
| `unsupported_response_type` | Autorizační server nepodporuje typ odpovědi v požadavku. | Opravte a odešlete požadavek znovu. Toto je vývojový chyby je obvykle zachycena během počátečního testování.  |
| `server_error`  | Na serveru došlo k neočekávané chybě.| Zkuste požadavek. Tyto chyby můžou být výsledkem dočasné situace. Klientská aplikace může vysvětlit uživatelům, že odpověď je zpožděno k dočasné chybě. |
| `temporarily_unavailable`   | Server je dočasně příliš zaneprázdněn a nemůže žádost zpracovat. | Zkuste požadavek. Klientská aplikace může vysvětlit uživateli, odpověď se zpožďuje kvůli dočasnému chybovému stavu. |
| `invalid_resource`  | Cílový prostředek je neplatný, protože buď neexistuje, Azure AD nelze najít nebo není správně nakonfigurovaný. | Tato chyba označuje, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a jeho přidání do služby Azure AD. |
| `login_required` | Příliš mnoho nebo nebyli nalezeni žádní uživatelé | Klient vyžádal bezobslužné ověření (`prompt=none`), ale nepodařilo najít u jednoho uživatele. To může znamenat, že v relaci nebo žádní uživatelé jsou aktivní více uživatelů. To bere v úvahu tenanta zvolit (například, pokud existují 2 aktivní účty AAD a jeden MSA, a `consumers` je vybrána, bude fungovat bezobslužné ověření). |
| `interaction_required` | Požadavek vyžaduje zásah uživatele. | Je potřeba další ověřovací krok nebo souhlasu. Zkuste požadavek bez `prompt=none`. |

## <a name="request-an-access-token"></a>Žádost o přístupový token

Teď, když jste získali authorization_code a udělil oprávnění uživatele, můžete uplatnit `code` pro `access_token` u požadovaného prostředku. K tomu odesílání `POST` požádat o `/token` koncový bod:

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
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Pokuste se spustit tuto žádost do Postman! (Nezapomeňte nahradit `code`) [ ![spustit v nástroji Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parametr  | Požadované a volitelné | Popis     |
|------------|-------------------|----------------|
| `tenant`   | povinné   | `{tenant}` Hodnota v cestě požadavku je možné řídit, kdo se můžete přihlásit do aplikace. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátorů klienta. Další podrobnosti najdete v části [protokol Základy](active-directory-v2-protocols.md#endpoints).  |
| `client_id` | povinné  | ID aplikace, které na portál pro registraci ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) přiřazené vaší aplikaci. |
| `grant_type` | povinné   | Musí být `authorization_code` pro tok autorizačního kódu.   |
| `scope`      | povinné   | Seznam oborů oddělených mezerami. Požadovaný v této fáze obory musí být ekvivalentní, nebo jen některé obory požadovaný v první fáze. Pokud span obory zadané v této žádosti více prostředků serveru, koncový bod verze 2.0 vrátí token pro prostředek určený v rámci první. Podrobnější vysvětlení oborů, najdete v tématu [oprávnění, vyjádření souhlasu a obory](v2-permissions-and-consent.md). |
| `code`          | povinné  | Authorization_code, kterou jste získali v první fáze toku. |
| `redirect_uri`  | povinné  | Stejné redirect_uri hodnota, která byla použita k získání authorization_code. |
| `client_secret` | vyžaduje se pro webové aplikace | Tajný klíč aplikace, kterou jste vytvořili v portálu pro registraci aplikace pro vaši aplikaci. Není vhodné jej použít v nativní aplikaci, protože client_secrets nemůže být spolehlivě uložená na zařízeních. Vyžaduje se pro webové aplikace a webová rozhraní API, které se budou moct bezpečně uložit hodnotu client_secret na straně serveru.  Tajný kód klienta musí být kódovaná adresou URL před odesláním.  |
| `code_verifier` | nepovinné  | Stejné code_verifier, který se používá k získání authorization_code. Povinné, pokud PKCE použila žádost o udělení autorizace kódu. Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

### <a name="successful-response"></a>Úspěšné odpovědi

Úspěšné odpovědi tokenu bude vypadat takto:

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
| `access_token`  | Požadovaný přístupový token. Aplikace můžete používat tento token k ověření k zabezpečeným prostředkům, jako je například webové rozhraní API.  |
| `token_type`    | Určuje hodnotu pro typ tokenu. Nosiče je jediným typem, který podporuje Azure AD |
|` expires_in`    | Jak dlouho je přístupový token platný (v sekundách). |
| `scope`         | Obory, které je platný pro access_token. |
| `refresh_token` | Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token získat dodatečné přístupové tokeny, až vyprší platnost aktuálního tokenu přístupu. Refresh_tokens jsou dlouhodobé a slouží k přístupu k prostředkům uchovávat po dlouhou dobu. Další podrobnosti o aktualizaci tokenu přístupu, najdete [níže v části](#refresh-the-access-token). <br> **Poznámka:** Pouze zadaný if `offline_access` byl vyžádán oboru. |
| `id_token`      | JSON Web Token (JWT). Aplikace může dekódovat segmenty tento token na žádost o informace o uživateli, který přihlášení. Aplikaci můžete ukládat do mezipaměti hodnoty a jejich zobrazení, ale na ně neměli spoléhat pro povolení nebo hranice zabezpečení. Další informace o id_tokens, najdete v článku [ `id_token reference` ](id-tokens.md). <br> **Poznámka:** Pouze zadaný if `openid` byl vyžádán oboru. |

### <a name="error-response"></a>Odpověď na chybu

Chybové odpovědi bude vypadat takto:

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
| `error`       | Řetězec kódu chyby, která slouží ke klasifikaci typy chyb, ke kterým dochází a je možné reagovat na chyby. |
| `error_description` | Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování. |
| `error_codes` | Seznam kódy chyb specifické pro službu tokenů zabezpečení, které vám můžou pomoct při diagnostiky.  |
| `timestamp`   | Čas, kdy došlo k chybě. |
| `trace_id`    | Jedinečný identifikátor pro požadavek, který vám pomůže v diagnostice. |
| `correlation_id` | Jedinečný identifikátor pro požadavek, který vám pomůže v diagnostice mezi komponentami. |

### <a name="error-codes-for-token-endpoint-errors"></a>Kódy chyb pro koncový bod tokenu chyby

| Kód chyby         | Popis        | Akce klienta    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Chyba protokolu, jako je například chybí povinný parametr. | Opravte a odešlete požadavek znovu   |
| `invalid_grant`    | Autorizační kód nebo ověřovač PKCE kódu je neplatná nebo vypršela platnost. | Vyzkoušejte nový požadavek `/authorize` koncového bodu a ověřte, že parametr code_verifier byla správná.  |
| `unauthorized_client` | Ověřený klient nemá oprávnění používat tento typ udělení autorizace. | K tomu obvykle dojde, když klientská aplikace není zaregistrovaný ve službě Azure AD nebo se nepřidal do tenanta služby Azure AD daného uživatele. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a jeho přidání do služby Azure AD. |
| `invalid_client` | Ověření klienta se nezdařilo.  | Pověření klienta nejsou platné. Pokud chcete vyřešit, správce aplikace aktualizuje přihlašovací údaje.   |
| `unsupported_grant_type` | Autorizační server nepodporuje typ udělení autorizace. | Změňte typ udělení v požadavku. Tento typ chyby se budou objevovat pouze během vývoje a zjistili při počátečním testování. |
| `invalid_resource` | Cílový prostředek je neplatný, protože buď neexistuje, Azure AD nelze najít nebo není správně nakonfigurovaný. | To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a jeho přidání do služby Azure AD.  |
| `interaction_required` | Požadavek vyžaduje zásah uživatele. Například další ověřovací krok je nutný. | Zkuste požadavek s stejný prostředek.  |
| `temporarily_unavailable` | Server je dočasně příliš zaneprázdněn a nemůže žádost zpracovat. | Zkuste požadavek. Klientská aplikace může vysvětlit uživateli, odpověď se zpožďuje kvůli dočasnému chybovému stavu. |

## <a name="use-the-access-token"></a>Použití přístupového tokenu

Teď, když jste úspěšně získala `access_token`, můžete token v žádosti k webovým rozhraním API zahrnutím ho `Authorization` záhlaví:

> [!TIP]
> Provedení tohoto požadavku v nástroji Postman! (Nahradit `Authorization` záhlaví první) [ ![spustit v nástroji Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Obnovení přístupového tokenu

Access_tokens jsou krátké životnost a po vypršení platnosti i nadále přístup k prostředkům, musíte je aktualizovat. Můžete tak učinit odesláním jiného `POST` požádat o `/token` koncový bod, v tuto chvíli poskytující `refresh_token` místo `code`.  Obnovovací tokeny jsou platné pro všechna oprávnění, které už klient přijal souhlas pro – díky tomu se obnovovací token vydaný na žádost o `scope=mail.read` umožňuje požádat o nový přístupový token pro `scope=api://contoso.com/api/UseResource`.  

Aktualizovat tokeny nemají zadaný životnosti. Životní cyklus obnovovací tokeny jsou obvykle poměrně dlouho. Ale v některých případech tokeny obnovení vyprší, byly odvolány nebo nemají dostatečná oprávnění pro požadovanou akci. Vaše aplikace potřebuje očekávat a zpracovat [chyby vrácené systémem koncový bod vystavování tokenů](#error-codes-for-token-endpoint-errors) správně. 

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Pokuste se spustit tuto žádost do Postman! (Nezapomeňte nahradit `refresh_token`) [ ![spustit v nástroji Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Parametr     |                | Popis        |
|---------------|----------------|--------------------|
| `tenant`        | povinné     | `{tenant}` Hodnota v cestě požadavku je možné řídit, kdo se můžete přihlásit do aplikace. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátorů klienta. Další podrobnosti najdete v části [protokol Základy](active-directory-v2-protocols.md#endpoints).   |
| `client_id`     | povinné    | **ID aplikace (klient)** , který portál pro registraci aplikace ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) nebo **registrace aplikací (Preview)** má prostředí na webu Azure Portal přiřazené aplikace.  |
| `grant_type`    | povinné    | Musí být `refresh_token` pro tuto větev tok autorizačního kódu. |
| `scope`         | povinné    | Seznam oborů oddělených mezerami. Obory požadovaný v této fáze musí být ekvivalentní, nebo podmnožina obory požadovaný v původní požadavek fáze authorization_code. Pokud span obory zadané v této žádosti více prostředků serveru, koncový bod verze 2.0 vrátí token pro prostředek určený v rámci první. Podrobnější vysvětlení oborů, najdete v tématu [oprávnění, vyjádření souhlasu a obory](v2-permissions-and-consent.md). |
| `refresh_token` | povinné    | Refresh_token, kterou jste získali v druhé fáze toku. |
| `redirect_uri`  | povinné    | Stejné redirect_uri hodnota, která byla použita k získání authorization_code. |
| `client_secret` | vyžaduje se pro webové aplikace | Tajný klíč aplikace, kterou jste vytvořili v portálu pro registraci aplikace pro vaši aplikaci. Není vhodné jej použít v nativní aplikaci, protože client_secrets nemůže být spolehlivě uložená na zařízeních. Vyžaduje se pro webové aplikace a webová rozhraní API, které se budou moct bezpečně uložit hodnotu client_secret na straně serveru.                                                                                                                                                    |

#### <a name="successful-response"></a>Úspěšné odpovědi

Úspěšné odpovědi tokenu bude vypadat takto:

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
| `access_token`  | Požadovaný přístupový token. Aplikace můžete používat tento token k ověření k zabezpečeným prostředkům, jako je například webové rozhraní API. |
| `token_type`    | Určuje hodnotu pro typ tokenu. Nosiče je jediným typem, který podporuje Azure AD |
| `expires_in`    | Jak dlouho je přístupový token platný (v sekundách).   |
| `scope`         | Obory, které je platný pro access_token.    |
| `refresh_token` | Nového tokenu obnovení OAuth 2.0. Staré obnovovací token měli nahradit tento nově získaná aktualizační token k zajištění, že obnovovací tokeny jsou dál platné pro co nejdéle. <br> **Poznámka:** Pouze zadaný if `offline_access` byl vyžádán oboru.|
| `id_token`      | Bez znaménka JSON Web Token (JWT). Aplikace může dekódovat segmenty tento token na žádost o informace o uživateli, který přihlášení. Aplikaci můžete ukládat do mezipaměti hodnoty a jejich zobrazení, ale na ně neměli spoléhat pro povolení nebo hranice zabezpečení. Další informace o id_tokens, najdete v článku [ `id_token reference` ](id-tokens.md). <br> **Poznámka:** Pouze zadaný if `openid` byl vyžádán oboru. |

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
| `error`           | Řetězec kódu chyby, která slouží ke klasifikaci typy chyb, ke kterým dochází a je možné reagovat na chyby. |
| `error_description` | Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování.           |
| `error_codes` |Seznam kódy chyb specifické pro službu tokenů zabezpečení, které vám můžou pomoct při diagnostiky. |
| `timestamp` | Čas, kdy došlo k chybě. |
| `trace_id` | Jedinečný identifikátor pro požadavek, který vám pomůže v diagnostice. |
| c`orrelation_id` | Jedinečný identifikátor pro požadavek, který vám pomůže v diagnostice mezi komponentami. |

Popis kódy chyb a akci doporučenou klientskou, naleznete v tématu [kódy chyb pro koncový bod tokenu chyby](#error-codes-for-token-endpoint-errors).
