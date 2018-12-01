---
title: Tok autorizačního kódu v Azure Active Directory B2C | Dokumentace Microsoftu
description: Naučte se vytvářet webové aplikace pomocí Azure AD B2C a OpenID Connect ověřovací protokol.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f39efcbc051bf57ab350357b020039eddd0f7c18
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720773"
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C: Tok autorizačního kódu OAuth 2.0
Udělení autorizačního kódu OAuth 2.0 můžete použít v aplikacích, které jsou nainstalované v zařízení získat přístup k chráněným prostředkům, jako je například webové rozhraní API. Pomocí Azure Active Directory B2C (Azure AD B2C) provádění OAuth 2.0, můžete přidat registrace, přihlašování a identity management. Další úkoly do vašich mobilních a desktopových aplikací. Tento článek je nezávislým na jazyku. V následujícím článku zjistíte, jak posílat a přijímat zprávy HTTP bez použití jakékoli knihovny open-source.

Tok autorizačního kódu OAuth 2.0 je popsaný v [části 4.1 specifikace OAuth 2.0](http://tools.ietf.org/html/rfc6749). Můžete ho použít pro ověřování a autorizace ve většině [typy aplikací](active-directory-b2c-apps.md), včetně webových aplikací a nativně nainstalovaných aplikací. Tok autorizačního kódu OAuth 2.0 můžete použít k zabezpečenému získat přístupové tokeny a obnovovacích tokenů pro vaše aplikace, které je možné použít pro přístup k prostředkům, které jsou zabezpečené pomocí [autorizační server](active-directory-b2c-reference-protocols.md).  Obnovovací token umožňuje klientovi získat přístup k nové (a aktualizace) tokeny po vypršení platnosti přístupového tokenu, obvykle po jedné hodině.

Tento článek se týká **veřejní klienti** tok autorizačního kódu OAuth 2.0. Veřejné klienta je klientská aplikace, který nemůže být důvěryhodné a zabezpečené udržovat integritu tajný klíč. To zahrnuje mobilních aplikací, aplikací klasické pracovní plochy a v podstatě jakékoli aplikace, která běží na zařízení a je potřeba získat přístupové tokeny. 

> [!NOTE]
> Chcete-li přidat správy identit do webové aplikace pomocí Azure AD B2C, použijte [OpenID Connect](active-directory-b2c-reference-oidc.md) místo OAuth 2.0.

Azure AD B2C rozšiřuje standard, kterou toky OAuth 2.0 lepší než jednoduché ověřování a autorizace. Zavádí [parametr toku uživatele](active-directory-b2c-reference-policies.md). Toky uživatelů, můžete pomocí OAuth 2.0 přidáte uživatelské prostředí pro vaši aplikaci, jako například registrace, přihlašování a správy profilů. V tomto článku ukážeme, jak používat toky OAuth 2.0 a uživatel k implementaci každého z těchto možností v nativních aplikací. Můžeme také ukazují, jak získat přístupové tokeny pro přístup k webovým rozhraním API.

V žádosti o příklad HTTP v tomto článku používáme naše ukázka adresář Azure AD B2C, **fabrikamb2c.onmicrosoft.com**. Použijeme také toky naší ukázkové aplikace a uživatele. Požadavky můžete také vyzkoušet sami s využitím těchto hodnot, nebo je můžete nahradit vlastními hodnotami.
Zjistěte, jak [získat vlastní toky adresáře, aplikace a uživatele Azure AD B2C](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Získání autorizačního kódu
Tok autorizačního kódu začíná klienta směruje uživatele `/authorize` koncového bodu. Toto je interaktivní součástí toku, kdy uživatel provede akci. V této žádosti, klient naznačuje v `scope` parametr oprávnění, která je potřeba získat od uživatele. V `p` parametru, označuje tok uživatele k provedení. Následující tři příklady (pomocí konců řádků pro lepší čitelnost) každý použít tok jiný uživatel.

### <a name="use-a-sign-in-user-flow"></a>Použijte tok přihlášení uživatele
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-user-flow"></a>Použijte tok registrace uživatele
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-user-flow"></a>Použít tok, který upravit profil uživatele
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci v [webu Azure portal](https://portal.azure.com). |
| response_type |Požaduje se |Typ odpovědi, které musí obsahovat `code` pro tok autorizačního kódu. |
| redirect_uri |Požaduje se |Identifikátor URI přesměrování vaší aplikace, kde jsou žádosti o ověření odesílat a přijímat vaší aplikace. Ho musí přesně odpovídat jedné z přesměrování identifikátory URI, které jste zaregistrovali na portálu s tím rozdílem, že musí být kódovaná adresou URL. |
| scope |Požaduje se |Seznam oborů oddělených mezerami. Hodnota jeden obor značí do Azure Active Directory (Azure AD) i oprávnění, které jsou požadovány. Pomocí ID klienta jako obor Určuje, že vaše aplikace potřebuje přístupový token, dá se použít pro vlastní služba nebo webové rozhraní API, reprezentovaný stejné ID klienta.  `offline_access` Obor Určuje, že vaše aplikace potřebuje obnovovací token pro dlouhodobé přístup k prostředkům. Můžete také použít `openid` oboru k vyžádání tokenu ID z Azure AD B2C. |
| response_mode |Doporučené |Metoda, která můžete používat k odesílání výsledný autorizační kód zpět do vaší aplikace. Může to být `query`, `form_post`, nebo `fragment`. |
| state |Doporučené |Hodnota v požadavku, který se může jednat o řetězec veškerý obsah, který chcete použít. Obvykle náhodně generované jedinečná hodnota se používá, prevenci proti útokům padělání žádosti více webů. Stav se také používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě. Například stránka, kterou uživatel byl v nebo právě spuštěný tok uživatele. |
| p |Požaduje se |Tok uživatele, který se spouští. Jde o název toku uživatele, který je vytvořen v adresáři Azure AD B2C. Hodnota názvu toku uživatele by měl začínat **b2c\_1\_**. Další informace o toky uživatelů najdete v tématu [toky uživatelů Azure AD B2C](active-directory-b2c-reference-policies.md). |
| řádek |Nepovinné |Typ interakce s uživatelem, který je požadován. V současné době je jedinou platnou hodnotou `login`, které donutí uživatele k zadání přihlašovacích údajů tohoto požadavku. Jednotné přihlašování se projeví. |

V tomto okamžiku je uživatel vyzván k dokončení pracovního postupu tok uživatele. To může zahrnovat uživatele při zadávání uživatelského jména a hesla, přihlašování přes sociální identity, k adresáři nebo jakékoli jiné číslo kroky. Uživatel akce závisí na tok uživatele bude definován takhle.

Když uživatel dokončil tok uživatele, Azure AD vrátí odpověď do vaší aplikace na hodnotu jste použili pro `redirect_uri`. Používá metodu určenou v příkazu `response_mode` parametru. Odpověď je přesně stejný pro jednotlivé uživatele akce scénáře, nezávisle na spuštěný tok uživatele.

Úspěšná odpověď, která používá `response_mode=query` vypadá přibližně takto:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametr | Popis |
| --- | --- |
| kód |Autorizační kód požadovanou aplikaci. Aplikace může používat autorizační kód k vyžádání tokenu pro cílový prostředek. Autorizační kódy jsou velmi krátkodobé. Obvykle platnost souhlasu vyprší po přibližně 10 minut. |
| state |Viz úplný popis v tabulce v předchozí části. Pokud `state` parametr je zahrnutý v požadavku, stejnou hodnotu by se měla zobrazit v odpovědi. Aplikace by měla ověřte, že `state` hodnoty v požadavku a odpovědi jsou identické. |

Chybové odpovědi lze také odešle do identifikátor URI pro přesměrování tak, aby aplikace můžete odpovídajícím způsobem zpracovat:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, můžete použít ke klasifikaci typy chyb, ke kterým dochází. Můžete také použít řetězec reagovat na chyby. |
| error_description |Určité chybové zprávě, které vám pomohou identifikovat hlavní příčinu chyby ověřování. |
| state |Viz úplný popis v předchozí tabulce. Pokud `state` parametr je zahrnutý v požadavku, stejnou hodnotu by se měla zobrazit v odpovědi. Aplikace by měla ověřte, že `state` hodnoty v požadavku a odpovědi jsou identické. |

## <a name="2-get-a-token"></a>2. Získání tokenu
Teď, když jste získali autorizační kód, uplatněte `code` pro token určený prostředek odesláním požadavkům POST odeslaných `/token` koncového bodu. V Azure AD B2C je jediný zdroj, který můžete požádat o token pro vaši vlastní back endové webové aplikace API. Konvence, která slouží k vyžádání tokenu sami sobě je použít ID klienta aplikace jako obor:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| p |Požaduje se |Tok uživatele, který byl použitý k získání autorizačního kódu. V této žádosti nelze použít tok jiný uživatel. Všimněte si, že přidáte tento parametr *řetězec dotazu*, ne v textu POST. |
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci v [webu Azure portal](https://portal.azure.com). |
| Parametr grant_type |Požaduje se |Typ udělení. Tok autorizačního kódu, musí být typ udělení `authorization_code`. |
| scope |Doporučené |Seznam oborů oddělených mezerami. Hodnota jeden obor značí do služby Azure AD i oprávnění, které jsou požadovány. Pomocí ID klienta jako obor Určuje, že vaše aplikace potřebuje přístupový token, dá se použít pro vlastní služba nebo webové rozhraní API, reprezentovaný stejné ID klienta.  `offline_access` Obor Určuje, že vaše aplikace potřebuje obnovovací token pro dlouhodobé přístup k prostředkům.  Můžete také použít `openid` oboru k vyžádání tokenu ID z Azure AD B2C. |
| kód |Požaduje se |Autorizační kód, který jste získali v první fáze toku. |
| redirect_uri |Požaduje se |Identifikátor URI pro přesměrování aplikace, kterou jste dostali autorizační kód. |

Úspěšné odpovědi tokenu vypadá takto:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametr | Popis |
| --- | --- |
| not_before |Čas, kdy token, který je považován za platný v unixovém čase. |
| token_type |Hodnota, typ tokenu. Jediný typ, který podporuje Azure AD je nosiče. |
| access_token |Podepsané JSON Web Token (JWT), kterou jste požadovali. |
| scope |Obory, které je token platný pro. Můžete také pomocí oborů tokeny v mezipaměti pro pozdější použití. |
| expires_in |Časový úsek, který je token platný (v sekundách). |
| refresh_token |Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token se po vypršení platnosti tokenu aktuální získat další tokeny. Obnovovací tokeny jsou s dlouhým poločasem rozpadu. Můžete využít k přístupu k prostředkům uchovávat po dlouhou dobu. Další informace najdete v tématu [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Chybové odpovědi vypadat nějak takto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, můžete použít ke klasifikaci typy chyb, ke kterým dochází. Můžete také použít řetězec reagovat na chyby. |
| error_description |Určité chybové zprávě, které vám pomohou identifikovat hlavní příčinu chyby ověřování. |

## <a name="3-use-the-token"></a>3. Použití tokenu
Teď, když jste úspěšně získat přístupový token, můžete token v žádosti k back endu webové rozhraní API zahrnutím v `Authorization` hlavičky:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Aktualizovat token
Jsou krátkodobé přístupové tokeny a tokeny typu ID. Po vypršení platnosti, je nutné aktualizovat, aby dál přístup k prostředkům. K tomuto účelu odeslat další požadavek POST do `/token` koncového bodu. Tentokrát, zadejte `refresh_token` místo `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&client_secret=JqQX2PNo9bpM0uEihUPzyrh&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| p |Požaduje se |Tok uživatele, který byl použitý k získání původní token obnovení. V této žádosti nelze použít tok jiný uživatel. Všimněte si, že přidáte tento parametr *řetězec dotazu*, ne v textu POST. |
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci v [webu Azure portal](https://portal.azure.com). |
| Hodnota client_secret |Požaduje se |Hodnota client_secret přidružené k vaší client_id v [webu Azure portal](https://portal.azure.com). |
| Parametr grant_type |Požaduje se |Typ udělení. Pro tuto větev tok autorizačního kódu musí být typ udělení `refresh_token`. |
| scope |Doporučené |Seznam oborů oddělených mezerami. Hodnota jeden obor značí do služby Azure AD i oprávnění, které jsou požadovány. Pomocí ID klienta jako obor Určuje, že vaše aplikace potřebuje přístupový token, dá se použít pro vlastní služba nebo webové rozhraní API, reprezentovaný stejné ID klienta.  `offline_access` Obor Určuje, že vaše aplikace bude potřebovat obnovovací token pro dlouhodobé přístup k prostředkům.  Můžete také použít `openid` oboru k vyžádání tokenu ID z Azure AD B2C. |
| redirect_uri |Nepovinné |Identifikátor URI pro přesměrování aplikace, kterou jste dostali autorizační kód. |
| refresh_token |Požaduje se |Původní token obnovení, kterou jste získali v druhé fáze toku. |

Úspěšné odpovědi tokenu vypadá takto:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametr | Popis |
| --- | --- |
| not_before |Čas, kdy token, který je považován za platný v unixovém čase. |
| token_type |Hodnota, typ tokenu. Jediný typ, který podporuje Azure AD je nosiče. |
| access_token |Podepsaný token JWT, kterou jste požadovali. |
| scope |Obory, které je token platný pro. Můžete také pomocí oborů tokeny v mezipaměti pro pozdější použití. |
| expires_in |Časový úsek, který je token platný (v sekundách). |
| refresh_token |Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token se po vypršení platnosti tokenu aktuální získat další tokeny. Aktualizace jsou dlouhodobé tokeny a slouží k přístupu k prostředkům uchovávat po dlouhou dobu. Další informace najdete v tématu [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Chybové odpovědi vypadat nějak takto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, můžete použít ke klasifikaci typy chyb, ke kterým dochází. Můžete také použít řetězec reagovat na chyby. |
| error_description |Určité chybové zprávě, které vám pomohou identifikovat hlavní příčinu chyby ověřování. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Použití služby Azure AD B2C
Pokud chcete vyzkoušet tyto požadavky, proveďte následující kroky. Nahraďte vzorové hodnoty, které jsme použili v tomto článku s vlastními hodnotami.

1. [Vytvoření adresáře Azure AD B2C](active-directory-b2c-get-started.md). Použijte název vašeho adresáře v požadavcích.
2. [Vytvoření aplikace](active-directory-b2c-app-registration.md) k získání ID aplikace a identifikátor URI přesměrování. Zahrňte nativního klienta ve vaší aplikaci.
3. [Vytvářet toky uživatelů](active-directory-b2c-reference-policies.md) získat názvy tok uživatele.

