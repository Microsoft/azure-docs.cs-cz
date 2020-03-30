---
title: Tok autorizačního kódu – Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvářet webové aplikace pomocí ověřovacího protokolu Azure AD B2C a OpenID Connect.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 8248ca0abb1d633786b09b894bcd6b1089ab2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260888"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Tok autorizačního kódu OAuth 2.0 ve službě Azure Active Directory B2C

Povolení autorizačního kódu OAuth 2.0 můžete použít v aplikacích nainstalovaných v zařízení, abyste získali přístup k chráněným prostředkům, jako jsou webová rozhraní API. Pomocí implementace OAuth 2.0 služby Azure Active Directory B2C (Azure AD B2C) můžete do mobilních a desktopových aplikací přidat registraci, přihlášení a další úlohy správy identit. Tento článek je nezávislý na jazyku. V článku popisujeme, jak odesílat a přijímat http zprávy bez použití knihoven s otevřeným zdrojovým kódem.

Tok autorizačního kódu OAuth 2.0 je popsán v [bodu 4.1 specifikace OAuth 2.0](https://tools.ietf.org/html/rfc6749). Můžete jej použít pro ověřování a autorizaci ve většině [typů aplikací](application-types.md), včetně webových aplikací a nativně nainstalovaných aplikací. Tok autorizačního kódu OAuth 2.0 můžete použít k bezpečnému získání přístupových tokenů a tokenů aktualizace pro vaše aplikace, které lze použít pro přístup k prostředkům, které jsou zabezpečeny [autorizačním serverem](protocols-overview.md).  Obnovovací token umožňuje klientovi získat nové přístupové (a obnovovací) tokeny po vypršení platnosti přístupového tokenu, obvykle po jedné hodině.

Tento článek se zaměřuje na **veřejné klienty** OAuth 2.0 tok autorizačního kódu. Veřejný klient je libovolná klientská aplikace, které nelze důvěřovat, aby bezpečně udržovala integritu tajného hesla. To zahrnuje mobilní aplikace, desktopové aplikace a v podstatě všechny aplikace, které běží na zařízení a potřebuje získat přístupové tokeny.

> [!NOTE]
> Chcete-li přidat správu identit do webové aplikace pomocí Azure AD B2C, použijte [OpenID Connect](openid-connect.md) místo OAuth 2.0.

Azure AD B2C rozšiřuje standardní toky OAuth 2.0 dělat víc než jednoduché ověřování a autorizace. Zavádí [tok uživatele](user-flow-overview.md). S toky uživatelů můžete pomocí OAuth 2.0 přidat uživatelské prostředí do vaší aplikace, jako je například registrace, přihlášení a správa profilu. Mezi poskytovatele identit, kteří používají protokol OAuth 2.0, patří [Amazon](identity-provider-amazon.md), [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md)a [LinkedIn](identity-provider-linkedin.md).

Chcete-li vyzkoušet požadavky HTTP v tomto článku:

1. Nahraďte `{tenant}` se názvem vašeho klienta Azure AD B2C.
1. Nahraďte `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` ID aplikace, kterou jste dříve zaregistrovali ve svém tenantovi Azure AD B2C.
1. Nahraďte `{policy}` například `b2c_1_sign_in`název zásady, kterou jste vytvořili v tenantovi .

## <a name="1-get-an-authorization-code"></a>1. Získejte autorizační kód
Tok autorizačního kódu začíná tím, že `/authorize` klient nasměruje uživatele do koncového bodu. Toto je interaktivní část toku, kde uživatel provede akci. V tomto požadavku klient označuje `scope` v parametru oprávnění, která potřebuje získat od uživatele. Následující tři příklady (s zalomení řádků pro čitelnost) každý použít jiný tok uživatele.


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| Parametr | Povinné? | Popis |
| --- | --- | --- |
|{tenant}| Požaduje se | Název vašeho klienta Azure AD B2C|
| {zásady} | Požaduje se | Tok uživatele, který má být spuštěn. Zadejte název toku uživatele, který jste vytvořili v tenantovi Azure AD B2C. Například: `b2c_1_sign_in` `b2c_1_sign_up`, `b2c_1_edit_profile`, nebo . |
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci na [webu Azure Portal](https://portal.azure.com). |
| response_type |Požaduje se |Typ odpovědi, který `code` musí obsahovat tok autorizačního kódu. |
| redirect_uri |Požaduje se |Identifikátor URI přesměrování vaší aplikace, kde vaše aplikace odesílá a přijímá odpovědi na ověřování. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódován adresou URL. |
| scope |Požaduje se |Mezera-oddělený seznam oborů. Hodnota jednoho oboru označuje azure active directory (Azure AD) obě oprávnění, která jsou požadovány. Použití ID klienta jako oboru označuje, že vaše aplikace potřebuje přístupový token, který lze použít proti vlastní služby nebo webové rozhraní API, reprezentované stejným ID klienta.  `offline_access` Obor označuje, že vaše aplikace potřebuje obnovovací token pro dlouhodobý přístup k prostředkům. Můžete také použít `openid` obor požádat o token ID z Azure AD B2C. |
| response_mode |Doporučené |Metoda, kterou použijete k odeslání výsledného autorizačního kódu zpět do aplikace. To může `query` `form_post`být `fragment`, , nebo . |
| state |Doporučené |Hodnota zahrnutá v požadavku, která může být řetězcem libovolného obsahu, který chcete použít. Obvykle se používá náhodně generovaná jedinečná hodnota, aby se zabránilo útokům padělání žádostí mezi lokalitami. Stav se také používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření. Například stránka, na které byl uživatel, nebo tok uživatele, který byl prováděn. |
| Výzva |Nepovinné |Typ interakce uživatele, která je požadována. V současné době je `login`jedinou platnou hodnotou , která nutí uživatele zadat svá pověření na tento požadavek. Jednotné přihlašování se neprojeví. |

V tomto okamžiku je uživatel vyzván k dokončení pracovního postupu toku uživatele. To může zahrnovat uživatele, který zadává své uživatelské jméno a heslo, přihlašuje se pomocí sociální identity, přihlašuje se do adresáře nebo jakýkoli jiný počet kroků. Akce uživatele závisí na tom, jak je definován tok uživatele.

Po dokončení toku uživatele Azure AD vrátí odpověď na vaši aplikaci `redirect_uri`na hodnotu, kterou jste použili pro . Používá metodu zadanou `response_mode` v parametru. Odpověď je přesně stejná pro každý scénář akce uživatele, nezávisle na toku uživatele, který byl proveden.

Úspěšná odpověď, `response_mode=query` která používá vypadá takto:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametr | Popis |
| --- | --- |
| kód |Autorizační kód, který aplikace požadovala. Aplikace můžete použít autorizační kód požádat o přístupový token pro cílový prostředek. Autorizační kódy jsou velmi krátkodobé. Obvykle vyprší asi po 10 minutách. |
| state |Viz úplný popis v tabulce v předchozí části. Pokud `state` je parametr zahrnut v požadavku, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla `state` ověřit, že hodnoty v požadavku a odpovědi jsou identické. |

Chybové odpovědi lze také odeslat do identifikátoru URI přesměrování, aby je aplikace mohla správně zpracovat:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým dochází. Můžete také použít řetězec reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomoci identifikovat hlavní příčinu chyby ověřování. |
| state |Viz úplný popis v předchozí tabulce. Pokud `state` je parametr zahrnut v požadavku, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla `state` ověřit, že hodnoty v požadavku a odpovědi jsou identické. |

## <a name="2-get-a-token"></a>2. Získejte žeton
Teď, když jste získali autorizační kód, můžete uplatnit `code` pro token určeného prostředku `/token` odesláním požadavku POST do koncového bodu. V Azure AD B2C můžete [požádat o přístupové tokeny pro jiné rozhraní API](access-tokens.md#request-a-token) jako obvykle zadáním jejich oboru v požadavku.

Můžete také požádat o přístupový token pro vlastní webové rozhraní API back-end u vaší aplikace podle konvence použití ID klienta aplikace jako požadovaného oboru (což bude mít za následek přístupový token s tímto ID klienta jako "cílovou skupinu"):

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
|{tenant}| Požaduje se | Název vašeho klienta Azure AD B2C|
|{zásady}| Požaduje se| Tok uživatele, který byl použit k získání autorizačního kódu. V tomto požadavku nelze použít jiný tok uživatele. |
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci na [webu Azure Portal](https://portal.azure.com).|
| client_secret | Ano, ve webových aplikacích | Tajný klíč aplikace, který byl vygenerován na [webu Azure Portal](https://portal.azure.com/). Tajné klíče klientů se používají v tomto toku pro scénáře webové aplikace, kde klient může bezpečně uložit tajný klíč klienta. Pro nativní aplikace (veřejný klient) scénáře klienta nelze bezpečně uložit a proto nejsou používány v tomto volání. Pokud používáte tajný klíč klienta, změňte jej pravidelně. |
| grant_type |Požaduje se |Typ grantu. Pro tok autorizačního kódu `authorization_code`musí být typ grantu . |
| scope |Doporučené |Mezera-oddělený seznam oborů. Hodnota jednoho oboru označuje azure ad obě oprávnění, která jsou požadovány. Použití ID klienta jako oboru označuje, že vaše aplikace potřebuje přístupový token, který lze použít proti vlastní služby nebo webové rozhraní API, reprezentované stejným ID klienta.  `offline_access` Obor označuje, že vaše aplikace potřebuje obnovovací token pro dlouhodobý přístup k prostředkům.  Můžete také použít `openid` obor požádat o token ID z Azure AD B2C. |
| kód |Požaduje se |Autorizační kód, který jste získali v první části toku. |
| redirect_uri |Požaduje se |Identifikátor URI přesměrování aplikace, ve které jste obdrželi autorizační kód. |

Úspěšná odpověď tokenu vypadá takto:

```JSON
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
| not_before |Čas, kdy je token považován za platný, v epochovém čase. |
| token_type |Hodnota typu tokenu. Jediný typ, který podporuje Azure AD je Nosič. |
| access_token |Podepsaný webový token JSON (JWT), který jste požadovali. |
| scope |Obory, pro které je token platný. Obory můžete také použít k ukládání tokenů do mezipaměti pro pozdější použití. |
| expires_in |Doba platnosti tokenu (v sekundách). |
| refresh_token |Obnovovací token OAuth 2.0. Aplikace můžete použít tento token k získání dalších tokenů po vypršení platnosti aktuálního tokenu. Obnovovací tokeny jsou dlouhodobé. Můžete je použít k zachování přístupu k prostředkům po delší dobu. Další informace naleznete v [odkazu tokenu Azure AD B2C](tokens-overview.md). |

Chybové odpovědi vypadají takto:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým dochází. Můžete také použít řetězec reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomoci identifikovat hlavní příčinu chyby ověřování. |

## <a name="3-use-the-token"></a>3. Použijte token
Teď, když jste úspěšně získali přístupový token, můžete použít token v požadavcích na vaše `Authorization` webová api back-endu tím, že ho zahrajete do záhlaví:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Obnovte token
Přístupové tokeny a tokeny ID jsou krátkodobé. Po vypršení jejich platnosti je nutné je aktualizovat, abyste mohli nadále přistupovat k prostředkům. Chcete-li to provést, odešlete další požadavek POST do koncového `/token` bodu. Tentokrát zadejte `refresh_token` místo `code`:

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
|{tenant}| Požaduje se | Název vašeho klienta Azure AD B2C|
|{zásady} |Požaduje se |Tok uživatele, který byl použit k získání původního obnovovacího tokenu. V tomto požadavku nelze použít jiný tok uživatele. |
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci na [webu Azure Portal](https://portal.azure.com). |
| client_secret | Ano, ve webových aplikacích | Tajný klíč aplikace, který byl vygenerován na [webu Azure Portal](https://portal.azure.com/). Tajné klíče klientů se používají v tomto toku pro scénáře webové aplikace, kde klient může bezpečně uložit tajný klíč klienta. Pro nativní aplikace (veřejný klient) scénáře klienta nelze bezpečně uložit a proto nejsou používány v tomto volání. Pokud používáte tajný klíč klienta, změňte jej pravidelně. |
| grant_type |Požaduje se |Typ grantu. Pro tuto část toku autorizačního kódu `refresh_token`musí být typ grantu . |
| scope |Doporučené |Mezera-oddělený seznam oborů. Hodnota jednoho oboru označuje azure ad obě oprávnění, která jsou požadovány. Použití ID klienta jako oboru označuje, že vaše aplikace potřebuje přístupový token, který lze použít proti vlastní služby nebo webové rozhraní API, reprezentované stejným ID klienta.  `offline_access` Obor označuje, že vaše aplikace bude potřebovat obnovovací token pro dlouhodobý přístup k prostředkům.  Můžete také použít `openid` obor požádat o token ID z Azure AD B2C. |
| redirect_uri |Nepovinné |Identifikátor URI přesměrování aplikace, ve které jste obdrželi autorizační kód. |
| refresh_token |Požaduje se |Původní obnovovací token, který jste získali v druhé části toku. |

Úspěšná odpověď tokenu vypadá takto:

```JSON
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
| not_before |Čas, kdy je token považován za platný, v epochovém čase. |
| token_type |Hodnota typu tokenu. Jediný typ, který podporuje Azure AD je Nosič. |
| access_token |Podepsaný JWT, který jste požadovali. |
| scope |Obory, pro které je token platný. Můžete také použít obory do mezipaměti tokeny pro pozdější použití. |
| expires_in |Doba platnosti tokenu (v sekundách). |
| refresh_token |Obnovovací token OAuth 2.0. Aplikace můžete použít tento token k získání dalších tokenů po vypršení platnosti aktuálního tokenu. Obnovovací tokeny jsou dlouhodobé a lze je použít k zachování přístupu k prostředkům po delší dobu. Další informace naleznete v [odkazu tokenu Azure AD B2C](tokens-overview.md). |

Chybové odpovědi vypadají takto:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým dochází. Můžete také použít řetězec reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomoci identifikovat hlavní příčinu chyby ověřování. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Použití vlastního adresáře Azure AD B2C
Chcete-li tyto požadavky vyzkoušet sami, proveďte následující kroky. Nahraďte ukázkové hodnoty, které jsme použili v tomto článku, vlastními hodnotami.

1. [Vytvořte adresář Azure AD B2C](tutorial-create-tenant.md). V požadavcích použijte název adresáře.
2. [Vytvořte aplikaci](tutorial-register-applications.md) pro získání ID aplikace a identifikátor URI přesměrování. Do aplikace zahrňte nativního klienta.
3. [Vytvořte toky uživatelů,](user-flow-overview.md) abyste získali názvy uživatelských toků.
