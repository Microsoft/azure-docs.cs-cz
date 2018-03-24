---
title: Tok autorizačního kódu – Azure AD B2C | Microsoft Docs
description: Naučte se vytvářet webové aplikace pomocí Azure AD B2C a OpenID Connect ověřovací protokol.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: d49a1c97a578726c26f8533476042646b0b302d3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C: Toku kódu autorizace OAuth 2.0
Udělení autorizačního kódu OAuth 2.0 v aplikace nainstalované v zařízení můžete použít k získání přístupu k chráněným prostředkům, jako jsou webová rozhraní API. Pomocí Azure Active Directory B2C (Azure AD B2C) provádění OAuth 2.0, můžete přidat registrace, přihlašování a jiných správu identity úkolů mobilních a desktopových aplikací. Tento článek je nezávislé na jazyku. V následujícím článku jsme popisují, jak odesílat a přijímat zprávy HTTP bez použití žádné knihovny open-source.

<!-- TODO: Need link to libraries -->

Toku kódu autorizace OAuth 2.0 je popsaná v [části 4.1 specifikace OAuth 2.0](http://tools.ietf.org/html/rfc6749). Můžete ji použít k ověřování a autorizaci většinu typů aplikací, včetně [webové aplikace](active-directory-b2c-apps.md#web-apps) a [nativně nainstalované aplikace](active-directory-b2c-apps.md#mobile-and-native-apps). Můžete použít toku kódu autorizace OAuth 2.0 bezpečně získat *přístup tokeny* pro vaše aplikace, které lze použít pro přístup k prostředkům, které jsou zabezpečeny [serveru ověřování](active-directory-b2c-reference-protocols.md#the-basics).

Tento článek se týká **veřejné klienti** toku kódu autorizace OAuth 2.0. Veřejné klienta je klientská aplikace, která nemůže být důvěryhodný bezpečně zachovat integritu tajný hesla. To zahrnuje mobilní aplikace, aplikace klasické pracovní plochy a v podstatě jakékoli aplikace, která běží na zařízení a získat tokeny přístupu je potřeba. 

> [!NOTE]
> Chcete-li přidat Správa identit pro webové aplikace pomocí Azure AD B2C, použijte [OpenID Connect](active-directory-b2c-reference-oidc.md) místo OAuth 2.0.

Azure AD B2C rozšiřuje standardní, které OAuth 2.0 toků více než jednoduché ověřování a autorizace. Zavádí [zásad parametr](active-directory-b2c-reference-policies.md). Pomocí předdefinovaných zásad vám pomůže OAuth 2.0 přidejte koncových uživatelů na aplikaci, například registrace, přihlašování a správy profilů. V tomto článku jsme ukazují, jak každý z těchto prostředí implementovat v nativních aplikací pomocí OAuth 2.0 a zásady. Můžeme také ukazují, jak získat tokeny přístupu pro přístup k webové rozhraní API.

V žádosti o příklad HTTP v tomto článku používáme naše ukázka adresáře Azure AD B2C, **fabrikamb2c.onmicrosoft.com**. Používáme se naše ukázková aplikace a zásady. Požadavky můžete také zkusit sami pomocí těchto hodnot, nebo je můžete nahradit vlastními hodnotami.
Zjistěte, jak [získat vlastní adresář Azure AD B2C, aplikace a zásady](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Získat autorizační kód
Tok autorizačního kódu začíná klienta odkazovat uživatele `/authorize` koncový bod. Toto je část interaktivní tok, kde uživatel provede akci. V této žádosti o označuje klienta v `scope` parametr oprávnění, která je nutné získat od uživatele. V `p` parametr, znamená to zásady provést. Následující tři příklady (pomocí konců řádků čitelnější) každou pomocí různých zásad.

### <a name="use-a-sign-in-policy"></a>Použít zásadu přihlášení
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Použít zásadu registrace
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Pomocí zásady úpravy profilu
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
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
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci v [portál Azure](https://portal.azure.com). |
| response_type |Požaduje se |Typ odpovědi, která musí zahrnovat `code` pro tok autorizačního kódu. |
| redirect_uri |Požaduje se |Identifikátor URI přesměrování vaší aplikace, kde odesílání a přijímání aplikace odpovědi ověřování. Ho musí přesně shodovat s jedním přesměrování identifikátory URI, který je zaregistrovaný na portálu, s tím rozdílem, že musí být kódovaná adresou URL. |
| Obor |Požaduje se |Seznam obory oddělených mezerami. Hodnota jeden obor označuje do Azure Active Directory (Azure AD) i oprávnění, která se vyžadují. Pomocí ID klienta podle oboru ukazuje, že vaše aplikace vyžaduje přístupový token, který můžete použít u službou nebo webové rozhraní API, reprezentována stejné ID klienta.  `offline_access` Oboru ukazuje, že vaše aplikace vyžaduje obnovovací token pro dlouhodobé přístup k prostředkům. Můžete taky použít `openid` oboru požadavku na ID token z Azure AD B2C. |
| response_mode |Doporučené |Metoda, která můžete pomocí odesílat výsledné autorizační kód zpět do vaší aplikace. Může být `query`, `form_post`, nebo `fragment`. |
| state |Doporučené |Hodnota součástí požadavek, který je vrácený v odpovědi tokenu. Může být řetězec o délce veškerý obsah, který chcete použít. Obvykle náhodně generované jedinečné hodnoty se používá, aby se zabránilo útokům padělání požadavku posílaného mezi weby. Stav se také používá ke kódování informace o stavu uživatele v aplikaci, než požadavek na ověření došlo k chybě. Například stránka, kterou uživatel byl na nebo zásad, který byl vykonáván. |
| p |Požaduje se |Zásady, které se spustí. Je název zásady, který je vytvořen v adresáři Azure AD B2C. Hodnota název zásad by měl začínat obráceným **b2c\_1\_**. Další informace o zásadách najdete v tématu [integrovaných zásad Azure AD B2C](active-directory-b2c-reference-policies.md). |
| řádku |Nepovinné |Typ interakci s uživatelem, který je vyžadován. V současné době je jedinou platnou hodnotou `login`, který vynutí uživatele k zadání přihlašovacích údajů tohoto požadavku. Jednotné přihlašování se neprojeví. |

V tomto okamžiku uživatel požádán o dokončení pracovního postupu nastavení zásad. To může zahrnovat uživatele zadáním uživatelského jména a hesla, přihlášení pomocí sociálních identitu, přihlašování k adresáři, nebo jakékoli jiné číslo kroky. Uživatel akce závisí na tom, jak je definované zásady.

Poté, co uživatel vykoná zásadu, Azure AD vrátí odpověď do vaší aplikace na hodnotu jste použili pro `redirect_uri`. Používá metody popsané v `response_mode` parametr. Odpověď je přesně stejný pro všechny uživatele akce scénářů, nezávisle na zásadu, která byla spuštěna.

Úspěšná odpověď, který používá `response_mode=query` vypadá podobně jako tento:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametr | Popis |
| --- | --- |
| kód |Autorizační kód, který požadované aplikace. Aplikace můžete autorizační kód vyžádat token přístupu pro cílový prostředek. Kódy ověřování jsou velmi krátkodobou. Obvykle se jejich platnost vyprší po přibližně 10 minut. |
| state |Viz úplný popis v tabulce v předchozí části. Pokud `state` parametr je zahrnuta v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda `state` hodnoty v požadavku a odpovědi jsou identické. |

Chybové odpovědi můžete také odešle do identifikátor URI přesměrování tak, aby aplikace můžete správně zpracovat:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, můžete použít ke klasifikaci typy chyb, ke kterým dochází. Můžete také použít řetězec reagování na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomoct určit hlavní příčinu chyby ověřování. |
| state |Viz úplný popis v předchozí tabulce. Pokud `state` parametr je zahrnuta v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda `state` hodnoty v požadavku a odpovědi jsou identické. |

## <a name="2-get-a-token"></a>2. Získání tokenu
Teď, když jste získali autorizační kód, uplatněte `code` pro token pro určený prostředek odesláním požadavku POST k `/token` koncový bod. V Azure AD B2C je pouze prostředek, který můžete požádat o token pro vaše aplikace vlastní back endové webové rozhraní API. Je použít ID klienta aplikace jako obor názvů, který se používá pro požadování tokenu sami sobě:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| p |Požaduje se |Zásada, která byla použita k získání autorizační kód. V této žádosti nelze použít jinou zásadu. Všimněte si, že přidáte tento parametr *řetězec dotazu*, není v textu POST. |
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci v [portál Azure](https://portal.azure.com). |
| grant_type |Požaduje se |Typ udělení. Tok autorizačního kódu, musí být typ udělení `authorization_code`. |
| Obor |Doporučené |Seznam obory oddělených mezerami. Hodnota jeden obor označuje do služby Azure AD i oprávnění, která se vyžadují. Pomocí ID klienta podle oboru ukazuje, že vaše aplikace vyžaduje přístupový token, který můžete použít u službou nebo webové rozhraní API, reprezentována stejné ID klienta.  `offline_access` Oboru ukazuje, že vaše aplikace vyžaduje obnovovací token pro dlouhodobé přístup k prostředkům.  Můžete taky použít `openid` oboru požadavku na ID token z Azure AD B2C. |
| kód |Požaduje se |Autorizační kód, který jste získali v prvním větev toku. |
| redirect_uri |Požaduje se |Identifikátor URI přesměrování aplikace, kterou jste dostali autorizační kód. |

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
| not_before |Čas, kdy je token považován za platný v čase epoch. |
| token_type |Typ tokenu hodnota. Pouze typ, který podporuje Azure AD je nosiče. |
| access_token |Podepsaný JSON Web Token (JWT) požadovaná. |
| Obor |Obory, které token je platný pro. Také můžete pomocí oborů tokeny mezipaměti pro pozdější použití. |
| expires_in |Časový interval, který je token platný (v sekundách). |
| refresh_token |Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token získat další tokeny po vypršení platnosti aktuálního tokenu. Tokeny obnovení je dlouhodobé. Můžete je používat k přístupu k prostředkům uchovávány dlouhou dobu. Další informace najdete v tématu [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Chybové odpovědi bude vypadat takto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, můžete použít ke klasifikaci typy chyb, ke kterým dochází. Můžete také použít řetězec reagování na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomoct určit hlavní příčinu chyby ověřování. |

## <a name="3-use-the-token"></a>3. Použití tokenu
Teď, když jste úspěšně získat přístupový token, můžete použít token v požadavcích na back endové webové rozhraní API zahrnutím v `Authorization` hlavičky:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Aktualizovat token
Přístupové tokeny a ID tokeny jsou krátkodobou. Po vypršení jejich platnosti, je nutné aktualizovat, aby dál přístup k prostředkům. K tomuto účelu odeslat další požadavek POST do `/token` koncový bod. Zadejte této doby `refresh_token` místo `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| p |Požaduje se |Zásada, která byla použita k získání původní token obnovení. V této žádosti nelze použít jinou zásadu. Všimněte si, že přidáte tento parametr *řetězec dotazu*, není v textu POST. |
| client_id |Doporučené |ID aplikace přiřazené vaší aplikaci v [portál Azure](https://portal.azure.com). |
| grant_type |Požaduje se |Typ udělení. Pro tuto větev tok autorizačního kódu, musí být typ udělení `refresh_token`. |
| Obor |Doporučené |Seznam obory oddělených mezerami. Hodnota jeden obor označuje do služby Azure AD i oprávnění, která se vyžadují. Pomocí ID klienta podle oboru ukazuje, že vaše aplikace vyžaduje přístupový token, který můžete použít u službou nebo webové rozhraní API, reprezentována stejné ID klienta.  `offline_access` Oboru označuje, že vaše aplikace budete potřebovat obnovovací token pro dlouhodobé přístup k prostředkům.  Můžete taky použít `openid` oboru požadavku na ID token z Azure AD B2C. |
| redirect_uri |Nepovinné |Identifikátor URI přesměrování aplikace, kterou jste dostali autorizační kód. |
| refresh_token |Požaduje se |Původní obnovovací token, který jste získali v druhé větev toku. |

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
| not_before |Čas, kdy je token považován za platný v čase epoch. |
| token_type |Typ tokenu hodnota. Pouze typ, který podporuje Azure AD je nosiče. |
| access_token |JWT podepsaný držitelem, které jste žádali. |
| Obor |Obory, které token je platný pro. Také můžete obory tokeny mezipaměti pro pozdější použití. |
| expires_in |Časový interval, který je token platný (v sekundách). |
| refresh_token |Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token získat další tokeny po vypršení platnosti aktuálního tokenu. Aktualizujte je dlouhodobé tokeny a slouží k přístupu k prostředkům uchovávány dlouhou dobu. Další informace najdete v tématu [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). |

Chybové odpovědi bude vypadat takto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, můžete použít ke klasifikaci typů chyb, ke kterým dochází. Můžete také použít řetězec reagování na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomoct určit hlavní příčinu chyby ověřování. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Použít vlastní adresáře Azure AD B2C
Chcete-li vyzkoušet tyto požadavky, proveďte následující kroky. Nahraďte ukázkové hodnoty, které jsme použili v tomto článku vlastními hodnotami.

1. [Vytvořte adresář služby Azure AD B2C](active-directory-b2c-get-started.md). Použijte název adresáře v žádosti.
2. [Vytvoření aplikace](active-directory-b2c-app-registration.md) k získání ID aplikací a identifikátor URI pro přesměrování. Zahrňte nativního klienta ve vaší aplikaci.
3. [Vytvoření vlastních zásad](active-directory-b2c-reference-policies.md) získat názvy zásad.

