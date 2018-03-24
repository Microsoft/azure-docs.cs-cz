---
title: Toku kódu autorizace OAuth Azure AD v2.0 | Microsoft Docs
description: Vytváření webové aplikace pomocí Azure AD implementace ověřovacího protokolu OAuth 2.0.
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: c6af6fb6ab957d79d71c816e6a23b0526f016a71
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>Protokoly v2.0 - toku kódu autorizace OAuth 2.0
Udělení autorizačního kódu OAuth 2.0 lze použít v aplikacích, které jsou nainstalované na zařízení k získání přístupu k chráněným prostředkům, jako jsou webová rozhraní API.  Pomocí implementace v2.0 modelu aplikace OAuth 2.0, můžete přidat, přihlaste se a rozhraní API, přístup k mobilním a desktopovým aplikacím.  Tento průvodce je nezávislé na jazyku a popisuje, jak odesílat a přijímat zprávy HTTP bez použití žádné z [knihovny Azure ověřování open-source](active-directory-authentication-libraries.md).

> [!NOTE]
> Ne všechny scénáře Azure Active Directory a funkce jsou podporovány koncového bodu v2.0.  Pokud chcete zjistit, pokud byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
> 
> 

Toku kódu autorizace OAuth 2.0 je popsaná v [části 4.1 specifikace OAuth 2.0](http://tools.ietf.org/html/rfc6749).  Se používá k provedení ověřování a autorizace pro většinu typů aplikací, včetně [webové aplikace](active-directory-v2-flows.md#web-apps) a [nativně nainstalované aplikace](active-directory-v2-flows.md#mobile-and-native-apps).  Tok umožňuje aplikace bezpečně získat access_tokens, který slouží pro přístup k prostředkům zabezpečené koncového bodu v2.0.  

## <a name="protocol-diagram"></a>Diagram protokolu
Na vysoké úrovni tok celý proces ověřování pro nativní nebo mobilní aplikace trochu vypadat třeba takto:

![Ověřování kódu toku OAuth](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Žádost o autorizační kód
Tok autorizačního kódu začíná klienta odkazovat uživatele `/authorize` koncový bod.  V této žádosti o klient naznačuje oprávnění, které potřebuje získat od uživatele:

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
> Klikněte na odkaz níže k provedení této žádosti. Po přihlášení, prohlížeč přesměrováni na `https://localhost/myapp/` s `code` na panelu Adresa.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parametr             |             | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tenant                | Požadované    | `{tenant}` Hodnotu v cestě požadavku slouží k řízení, kdo může přihlásit k aplikaci.  Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů.  Další podrobnosti naleznete v [protokolu Základy](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                                                                                                                                                      |
| client_id             | Požadované    | Aplikace D, portálu pro registraci ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) přiřazené vaší aplikace.                                                                                                                                                                                                                                                                                                                                                                                                |
| response_type         | Požadované    | Musí zahrnovat `code` pro tok autorizačního kódu.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| redirect_uri          | Doporučená | O položku redirect_uri vaší aplikace, kde můžete odesílat a přijímat aplikace odpovědi ověřování.  Se musí přesně shodovat s jedním redirect_uris, které jste zaregistrovali na portálu, s výjimkou musí být kódovaná adresou url.  Pro nativní & mobilní aplikace, by měl použít výchozí hodnotu `https://login.microsoftonline.com/common/oauth2/nativeclient`.                                                                                                                                                                                                                                                              |
| Obor                 | Požadované    | Seznam oddělených mezerami [obory](active-directory-v2-scopes.md) že chcete uživatelům souhlas.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| response_mode         | Doporučená | Určuje metodu, která se má použít k odeslání výsledný token zpět do vaší aplikace.  Může být `query` nebo `form_post`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| state                 | Doporučená | Hodnota, zahrnuté v požadavku, který bude vrácen také v odpovědi tokenu.  Může být řetězec o délce veškerý obsah, který chcete.  Náhodně generované jedinečné hodnoty se obvykle používá u [prevence útoků padělání požadavku posílaného mezi weby](http://tools.ietf.org/html/rfc6749#section-10.12).  Hodnota může také kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky nebo zobrazení, které byly na.                                                                                                                                          |
| řádku                | nepovinné    | Označuje typ interakce s uživatelem, který je vyžadován.  Jedinými platnými hodnotami v tuto chvíli se "přihlášení", 'none' a 'souhlas'.  `prompt=login` Vynutí uživatele k zadání přihlašovacích údajů tohoto požadavku negace jednotného přihlašování.  `prompt=none` je opak - zajistí, že uživatel není uveden s žádné interaktivní výzvu jakkoli.  Pokud požadavek nemůže být dokončena bez upozornění pomocí jednotného přihlašování, koncový bod v2.0 vrátí chybu.  `prompt=consent` Aktivuje se v dialogovém okně souhlas OAuth po přihlášení uživatele, požádá uživatele a udělit oprávnění k aplikaci. |
| login_hint            | nepovinné    | Slouží k předem vyplnit pole uživatelské jméno nebo e-mailové adresy na stránce přihlášení pro uživatele, pokud znáte svoje uživatelské jméno předem.  Často aplikace bude používat tento parametr během opětovné ověření, uživatelské jméno s již extrahovat z předchozí přihlášení pomocí `preferred_username` deklarací identity.                                                                                                                                                                                                                                                                                                      |
| domain_hint           | nepovinné    | Může být jedna z `consumers` nebo `organizations`.  Pokud zahrnuty, přeskočí proces zjišťování na základě e-mailu tento uživatel prochází na v2.0 přihlašovací stránce, což mírně zefektivnění činnost koncového uživatele.  Často aplikace bude používat tento parametr během opětovné ověření extrahováním `tid` z předchozí přihlášení.  Pokud `tid` deklarace, hodnota je `9188040d-6c67-4c5b-b112-36a304b66dad`, měli byste použít `domain_hint=consumers`.  Jinak použijte `domain_hint=organizations`.                                                                                                                |
| code_challenge_method | nepovinné    | Metoda použitá ke kódování `code_verifier` pro `code_challenge` parametr. Může být jedna z `plain` nebo `S256`.  Pokud vyloučená, `code_challenge` bude ve formátu prostého textu, pokud hodnota `code_challenge` je součástí.  Azure AAD v2.0 podporuje obě `plain` a `S256`. Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | nepovinné    | Používá k zabezpečení uděluje autorizační kód prostřednictvím ověření klíče pro Exchange kódu (PKCE) z nativního klienta. Požadováno pokud `code_challenge_method` je součástí.  Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636).                                                                                                                                                                                                                                                                                                                                                                      |

V tomto okamžiku uživatel se vyzve k zadání přihlašovacích údajů a dokončení ověření.  Koncový bod v2.0 také zajistí, že uživatel souhlasí s tím oprávnění uvedené v `scope` parametr dotazu.  Pokud uživatel nebyla přijata některé z těchto oprávnění, požádá uživatele o souhlas pro požadovaná oprávnění.  Podrobnosti o [oprávnění, souhlasu a víceklientské aplikace jsou tady uvedené](active-directory-v2-scopes.md).

Jakmile se uživatel ověří a uděluje souhlas, koncový bod v2.0 vrátí odpověď na aplikaci ve uvedené `redirect_uri`, pomocí metody popsané v `response_mode` parametr.

#### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď pomocí `response_mode=query` vypadá jako:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametr | Popis                                                                                                                                                                                                                        |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| kód      | Authorization_code, který požadované aplikace. Aplikace můžete autorizační kód vyžádat token přístupu pro cílový prostředek.  Authorization_codes jsou velmi krátkodobé povahy, obvykle vyprší po přibližně 10 minut. |
| state     | Pokud parametr stavu je obsažena v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda jsou identické hodnoty stavu v požadavku a odpovědi.                                              |

#### <a name="error-response"></a>Chybové odpovědi
Chybové odpovědi se taky může odeslat do `redirect_uri` tak aplikace můžete správně zpracovat:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr         | Popis                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| error             | Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým došlo a slouží k reagovat na chyby. |
| error_description | Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování.            |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kódy chyb pro chyb koncový bod autorizace
Následující tabulka popisuje různé kódy chyb, které mohou být vráceny v `error` parametr odpovědi na chybu.

| Kód chyby                | Popis                                                                                                           | Akce klienta                                                                                                                                                                                                                               |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request           | Chyba protokolu, například chybějící povinný parametr.                                                                 | Opravte a odešlete požadavek znovu. Jedná se o chybu vývoj obvykle zachycena během počáteční testování.                                                                                                                                       |
| unauthorized_client       | Klientská aplikace nemá oprávnění k vyžádání autorizační kód.                                             | K této chybě obvykle dojde při klientskou aplikací není registrovaný ve službě Azure AD, nebo není přidáno do klienta Azure AD pro uživatele. Aplikace můžete vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Azure AD. |
| access_denied             | Vlastník prostředku odepřen souhlasu                                                                                         | Klientská aplikace může upozornit uživatele, který nemůže pokračovat, pokud uživatel souhlasí.                                                                                                                                                 |
| unsupported_response_type | Autorizace serveru nepodporuje typ odpovědi v požadavku.                                           | Opravte a odešlete požadavek znovu. Toto je vývojovém chyba se obvykle zachycena během počáteční testování.                                                                                                                                       |
| server_error              | Serveru došlo k neočekávané chybě.                                                                           | Opakujte žádost. Tyto chyby může být důsledkem dočasné stavy. Klientská aplikace mohou vysvětlit pro uživatele, že odpověď je zpožděno k dočasné chybě.                                                                  |
| temporarily_unavailable   | Server je dočasně zaneprázdněn pro zpracování požadavku.                                                             | Opakujte žádost. Klientská aplikace mohou vysvětlit pro uživatele, že odpověď se zpožďuje kvůli dočasné podmínce.                                                                                                                 |
| invalid_resource          | Cílový prostředek je neplatný, protože neexistuje, Azure AD ji nemůže najít, nebo není správně nakonfigurována. | Tato chyba znamená, že k prostředku, pokud existuje, není nakonfigurované v klientovi. Aplikace můžete vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Azure AD.                                            |

## <a name="request-an-access-token"></a>Žádost o token přístupu
Teď, když jste získali authorization_code a bylo uděleno oprávnění uživatelem, uplatnit `code` pro `access_token` požadovaného prostředku. To provedete odesláním `POST` žádost o `/token` koncový bod:

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
> Zkuste provést tuto žádost v Postman! (Nezapomeňte nahradit `code`) [ ![spustit v Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Parametr     |                       | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tenant        | Požadované              | `{tenant}` Hodnotu v cestě požadavku slouží k řízení, kdo může přihlásit k aplikaci.  Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů.  Další podrobnosti naleznete v [protokolu Základy](active-directory-v2-protocols.md#endpoints).                                                                                                                                                     |
| client_id     | Požadované              | ID aplikace, která na portál pro registraci ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) přiřazené vaší aplikace.                                                                                                                                                                                                                               |
| grant_type    | Požadované              | Musí být `authorization_code` pro tok autorizačního kódu.                                                                                                                                                                                                                                                                                                                                                                              |
| Obor         | Požadované              | Seznam obory oddělených mezerami.  Požadovaný v této větev obory musí být ekvivalent nebo podmnožinu obory požadované v první větev.  Pokud rozsahy určené v této žádosti o span více prostředků serveru, se vrátí koncový bod v2.0 token pro zadaný v oboru první prostředek.  Podrobnější vysvětlení oborů, najdete v části [oprávnění, souhlasu a obory](active-directory-v2-scopes.md). |
| kód          | Požadované              | Authorization_code, kterou jste získali v prvním větev toku.                                                                                                                                                                                                                                                                                                                                                                     |
| redirect_uri  | Požadované              | Stejné redirect_uri hodnota, která byla použita k získání authorization_code.                                                                                                                                                                                                                                                                                                                                                               |
| client_secret | vyžaduje se pro webové aplikace | Tajný klíč aplikace vytvořené v portálu pro registraci aplikace pro vaši aplikaci.  Není vhodné jej použít v nativní aplikaci, protože client_secrets nelze uložit spolehlivě na zařízení.  Je vyžadována pro webové aplikace a webové rozhraní API, které můžete bezpečně uložit tajný klíč client_secret na straně serveru.                                                                                                                        |
| code_verifier | nepovinné              | Stejné code_verifier, který se používá k získání authorization_code.  Vyžaduje, pokud PKCE byl použit v žádosti o udělení autorizační kód.  Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
#### <a name="successful-response"></a>Úspěšná odpověď
Úspěšné odpovědi tokenu bude vypadat jako:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametr     | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | Požadovaný přístupový token. Aplikace můžete používat tento token k ověření zabezpečeným prostředkům, jako jsou webové rozhraní API.                                                                                                                                                                                                                                                                                                                                      |
| token_type    | Určuje hodnotu typ tokenu. Nosiče je jediným typem, který podporuje Azure AD                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Jak dlouho přístupový token je platný (v sekundách).                                                                                                                                                                                                                                                                                                                                                                                                         |
| Obor         | Obory, které je platný pro access_token.                                                                                                                                                                                                                                                                                                                                                                                                           |
| refresh_token | Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token získat další přístupové tokeny po vypršení platnosti aktuální přístupový token.  Refresh_tokens je dlouhodobé a slouží k přístupu k prostředkům uchovávány dlouhou dobu.  Další podrobnosti najdete v části [odkaz tokenu v2.0](active-directory-v2-tokens.md). <br> **Poznámka:** pouze zadaná Pokud `offline_access` požadoval oboru.                                                 |
| id_token      | Nepodepsané JSON Web Token (JWT). Base64Url může aplikace dekódovat segmentů tento token pro vyžadování informací o uživateli, který přihlášení. Aplikace můžete hodnoty do mezipaměti a jejich zobrazení, ale na ně neměli spoléhat pro žádné hranice zabezpečení nebo autorizace.  Další informace o id_tokens najdete v tématu [odkaz tokenu koncový bod v2.0](active-directory-v2-tokens.md). <br> **Poznámka:** pouze zadaná Pokud `openid` požadoval oboru. |
#### <a name="error-response"></a>Chybové odpovědi
Chybové odpovědi bude vypadat jako:

```
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

| Parametr         | Popis                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| error             | Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým došlo a slouží k reagovat na chyby. |
| error_description | Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování.            |
| error_codes       | Seznam tokenů zabezpečení specifické chybové kódy, které vám můžou pomoct při diagnostiky.                                                  |
| časové razítko         | Čas, kdy se stala chyba.                                                                             |
| trace_id          | Jedinečný identifikátor pro požadavek, který vám můžou pomoct při diagnostiky.                                                 |
| correlation_id    | Jedinečný identifikátor pro požadavek, který vám můžou pomoct při diagnostiky mezi komponentami.                               |

#### <a name="error-codes-for-token-endpoint-errors"></a>Kódy chyb pro koncový bod tokenu chyby
| Kód chyby              | Popis                                                                                                           | Akce klienta                                                                                                                                                                                                                               |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| invalid_request         | Chyba protokolu, například chybějící povinný parametr.                                                                 | Opravte a odešlete požadavek znovu                                                                                                                                                                                                                |
| invalid_grant           | Autorizační kód nebo kód ověřovatele PKCE je neplatná nebo vypršela platnost.                                               | Zkuste nový požadavek `/authorize` koncového bodu a ověřit, že parametr code_verifier správné.                                                                                                                                     |
| unauthorized_client     | Ověřený klient nemá oprávnění používat tento typ udělení autorizace.                                      | K tomu obvykle dojde, pokud klientská aplikace není registrovaný ve službě Azure AD nebo nebyla přidána do klienta Azure AD pro uživatele. Aplikace můžete vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Azure AD. |
| invalid_client          | Ověření klienta se nezdařilo.                                                                                         | Pověření klienta nejsou platné. Pokud chcete vyřešit, Správce aplikací aktualizuje přihlašovací údaje.                                                                                                                                        |
| unsupported_grant_type  | Autorizace serveru nepodporuje typ udělení autorizace.                                               | Změňte typ udělení v požadavku. Tento typ chyby provedeno pouze během vývoje a zjistit během počáteční testování.                                                                                                       |
| invalid_resource        | Cílový prostředek je neplatný, protože neexistuje, Azure AD ji nemůže najít, nebo není správně nakonfigurována. | To znamená, že k prostředku, pokud existuje, není nakonfigurované v klientovi. Aplikace můžete vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Azure AD.                                            |
| interaction_required    | Požadavek vyžaduje interakci uživatele. Na další ověřování krok je třeba požadovaný.                    | Opakujte požadavek s stejného zdroje.                                                                                                                                                                                                   |
| temporarily_unavailable | Server je dočasně zaneprázdněn pro zpracování požadavku.                                                             | Opakujte žádost. Klientská aplikace mohou vysvětlit pro uživatele, že odpověď se zpožďuje kvůli dočasné podmínce.                                                                                                                 |

## <a name="use-the-access-token"></a>Použití tokenu přístupu
Teď, když jste byla úspěšně načtena `access_token`, můžete použít token v žádostech o k webovým rozhraním API zahrnutím v `Authorization` hlavičky:

> [!TIP]
> Provedení této žádosti v Postman! (Nahraďte `Authorization` záhlaví první) [ ![spustit v Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Aktualizovat přístupový token
Access_tokens jsou krátkou životnost a je nutné je aktualizovat po vypršení jejich platnosti chcete-li pokračovat, přístup k prostředkům.  Můžete to provést tak, že zadáte jiný `POST` žádost o `/token` koncový bod, v tuto chvíli Pokud `refresh_token` místo `code`:

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
> Zkuste provést tuto žádost v Postman! (Nezapomeňte nahradit `refresh_token`) [ ![spustit v Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Parametr     |                       | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tenant        | Požadované              | `{tenant}` Hodnotu v cestě požadavku slouží k řízení, kdo může přihlásit k aplikaci.  Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů.  Další podrobnosti naleznete v [protokolu Základy](active-directory-v2-protocols.md#endpoints).                                                                                                                                                                                   |
| client_id     | Požadované              | ID aplikace, která na portál pro registraci ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) přiřazené vaší aplikace.                                                                                                                                                                                                                                                             |
| grant_type    | Požadované              | Musí být `refresh_token` pro tuto větev toku kódu autorizace.                                                                                                                                                                                                                                                                                                                                                                                                     |
| Obor         | Požadované              | Seznam obory oddělených mezerami.  Požadovaný v této větev obory musí být ekvivalent nebo podmnožinu obory požadované v původní žádost větev authorization_code.  Pokud rozsahy určené v této žádosti o span více prostředků serveru, se vrátí koncový bod v2.0 token pro zadaný v oboru první prostředek.  Podrobnější vysvětlení oborů, najdete v části [oprávnění, souhlasu a obory](active-directory-v2-scopes.md). |
| refresh_token | Požadované              | Refresh_token, kterou jste získali v druhé větev toku.                                                                                                                                                                                                                                                                                                                                                                                                       |
| redirect_uri  | Požadované              | Stejné redirect_uri hodnota, která byla použita k získání authorization_code.                                                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | vyžaduje se pro webové aplikace | Tajný klíč aplikace vytvořené v portálu pro registraci aplikace pro vaši aplikaci.  Není vhodné jej použít v nativní aplikaci, protože client_secrets nelze uložit spolehlivě na zařízení.  Je vyžadována pro webové aplikace a webové rozhraní API, které můžete bezpečně uložit tajný klíč client_secret na straně serveru.                                                                                                                                                     |

#### <a name="successful-response"></a>Úspěšná odpověď
Úspěšné odpovědi tokenu bude vypadat jako:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametr     | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | Požadovaný přístupový token. Aplikace můžete používat tento token k ověření zabezpečeným prostředkům, jako jsou webové rozhraní API.                                                                                                                                                                                                                                                                                                                                      |
| token_type    | Určuje hodnotu typ tokenu. Nosiče je jediným typem, který podporuje Azure AD                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Jak dlouho přístupový token je platný (v sekundách).                                                                                                                                                                                                                                                                                                                                                                                                         |
| Obor         | Obory, které je platný pro access_token.                                                                                                                                                                                                                                                                                                                                                                                                           |
| refresh_token | Nové obnovovací token OAuth 2.0. Staré tokenu obnovení musí nahraďte tento token nově získal aktualizace Ujistěte se, že jsou dál platné pro stejně dlouho obnovovacích tokenů. <br> **Poznámka:** pouze zadaná Pokud `offline_access` požadoval oboru.                                                                                                                                                                                                 |
| id_token      | Nepodepsané JSON Web Token (JWT). Base64Url může aplikace dekódovat segmentů tento token pro vyžadování informací o uživateli, který přihlášení. Aplikace můžete hodnoty do mezipaměti a jejich zobrazení, ale na ně neměli spoléhat pro žádné hranice zabezpečení nebo autorizace.  Další informace o id_tokens najdete v tématu [odkaz tokenu koncový bod v2.0](active-directory-v2-tokens.md). <br> **Poznámka:** pouze zadaná Pokud `openid` požadoval oboru. |

#### <a name="error-response"></a>Chybové odpovědi
```
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

| Parametr         | Popis                                                                                                       |
|-------------------|-------------------------------------------------------------------------------------------------------------------|
| error             | Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým došlo a slouží k reagovat na chyby. |
| error_description | Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování.            |
| error_codes |Seznam tokenů zabezpečení specifické chybové kódy, které vám můžou pomoct při diagnostiky. |
| časové razítko |Čas, kdy se stala chyba. |
| trace_id |Jedinečný identifikátor pro požadavek, který vám můžou pomoct při diagnostiky. |
| correlation_id |Jedinečný identifikátor pro požadavek, který vám můžou pomoct při diagnostiky mezi komponentami. |

Popis kódy chyb a klienta doporučenou akci najdete v tématu [kódy chyb pro koncový bod tokenu chyby](#error-codes-for-token-endpoint-errors).

