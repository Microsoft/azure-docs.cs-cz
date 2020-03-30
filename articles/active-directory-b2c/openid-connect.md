---
title: Webové přihlášení pomocí OpenID Connect – Azure Active Directory B2C
description: Vytvářejte webové aplikace pomocí ověřovacího protokolu OpenID Connect ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6640ab1660e6499a97a8c990a0001d5fbae4e997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264385"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webové přihlášení pomocí OpenID Connect ve službě Azure Active Directory B2C

OpenID Connect je ověřovací protokol, postavený na OAuth 2.0, který lze použít k bezpečnému přihlášení uživatelů k webovým aplikacím. Pomocí implementace OpenID Connect (Azure Active Directory B2C) služby Azure Active Directory můžete ve svých webových aplikacích do Služby Azure Active Directory (Azure AD) zadávat registraci, přihlášení a další prostředí správy identit do služby Azure Active Directory (Azure AD). Tato příručka ukazuje, jak to udělat způsobem nezávislým na jazyce. Popisuje, jak odesílat a přijímat http zprávy bez použití některéz našich open-source knihoven.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozšiřuje *autorizační* protokol OAuth 2.0 pro použití jako *ověřovací* protokol. Tento ověřovací protokol umožňuje provádět jednotné přihlašování. Zavádí koncept *tokenu ID*, který umožňuje klientovi ověřit identitu uživatele a získat základní informace o profilu o uživateli.

Vzhledem k tomu, že rozšiřuje OAuth 2.0, umožňuje také aplikacím bezpečně získat *přístupové tokeny*. Přístupové tokeny můžete použít pro přístup k prostředkům, které jsou zabezpečeny [autorizačním serverem](protocols-overview.md). OpenID Connect se doporučuje, pokud vytváříte webovou aplikaci, která je hostovaná na serveru a přístupná prostřednictvím prohlížeče. Další informace o tokenech najdete v [tématu Přehled tokenů ve službě Azure Active Directory B2C](tokens-overview.md)

Azure AD B2C rozšiřuje standardní protokol OpenID Connect na práci více než jen jednoduché ověřování a autorizace. Zavádí parametr [toku uživatele](user-flow-overview.md), který umožňuje používat OpenID Connect k přidání uživatelských prostředí do vaší aplikace, jako je registrace, přihlášení a správa profilů.

## <a name="send-authentication-requests"></a>Odeslání žádostí o ověření

Když vaše webová aplikace potřebuje ověřit uživatele a spustit tok `/authorize` uživatele, může uživatele nasměrovat do koncového bodu. Uživatel provede akci v závislosti na toku uživatele.

V tomto požadavku klient označuje oprávnění, která potřebuje získat `scope` od uživatele v parametru a určuje tok uživatele ke spuštění. Chcete-li získat informace o tom, jak žádost funguje, zkuste požadavek vložit do prohlížeče a spustit jej. Nahraďte `{tenant}` se jménem svého tenanta. Nahraďte `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` ID aplikace aplikace, kterou jste dříve zaregistrovali ve svém tenantovi. Změňte také název`{policy}`zásady ( ) na název zásady, který máte v tenantovi, například `b2c_1_sign_in`.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametr | Požaduje se | Popis |
| --------- | -------- | ----------- |
| {tenant} | Ano | Název vašeho klienta Azure AD B2C |
| {zásady} | Ano | Tok uživatele, který má být spuštěn. Zadejte název toku uživatele, který jste vytvořili v tenantovi Azure AD B2C. Například: `b2c_1_sign_in` `b2c_1_sign_up`, `b2c_1_edit_profile`, nebo . |
| client_id | Ano | ID aplikace, které [portál Azure](https://portal.azure.com/) přiřazenka vaší aplikaci. |
| Nonce | Ano | Hodnota zahrnutá v požadavku (vygenerovaná aplikací), která je zahrnuta ve výsledném tokenu ID jako deklarace. Aplikace pak můžete ověřit tuto hodnotu ke zmírnění útoků přehrání tokenu. Hodnota je obvykle randomizované jedinečný řetězec, který lze použít k identifikaci původu požadavku. |
| response_type | Ano | Musí obsahovat id token pro OpenID Connect. Pokud vaše webová aplikace také potřebuje tokeny pro `code+id_token`volání webového rozhraní API, můžete použít . |
| scope | Ano | Mezera-oddělený seznam oborů. Obor `openid` označuje oprávnění k přihlášení uživatele a získání dat o uživateli ve formě tokenů ID. Obor `offline_access` je volitelný pro webové aplikace. Označuje, že vaše aplikace bude potřebovat *obnovovací token* pro rozšířený přístup k prostředkům. |
| Výzva | Ne | Typ interakce uživatele, která je požadována. Jedinou platnou hodnotou `login`v tomto okamžiku je , která nutí uživatele zadat svá pověření na tento požadavek. |
| redirect_uri | Ne | Parametr `redirect_uri` aplikace, kde odpovědi na ověření mohou být odesílány a přijímány vaší aplikací. Musí přesně odpovídat `redirect_uri` jednomu z parametrů, které jste zaregistrovali na webu Azure Portal, s tím rozdílem, že musí být zakódována adresa URL. |
| response_mode | Ne | Metoda, která se používá k odeslání výsledného autorizačního kódu zpět do aplikace. Může to `query`být `form_post`buď `fragment`, , nebo .  Režim `form_post` odezvy je doporučen pro nejlepší zabezpečení. |
| state | Ne | Hodnota zahrnutá v požadavku, která je také vrácena v odpovědi tokenu. Může se jedná o řetězec libovolného obsahu, který chcete. Náhodně generovaná jedinečná hodnota se obvykle používá k zabránění útokům padělání požadavků na více webů. Stav se také používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, například na stránce, na které se používali. |

V tomto okamžiku je uživatel vyzván k dokončení pracovního postupu. Uživatel může mít zadejte své uživatelské jméno a heslo, přihlásit se pomocí sociální identity nebo se zaregistrovat do adresáře. Může existovat libovolný jiný počet kroků v závislosti na tom, jak je definován tok uživatele.

Poté, co uživatel dokončí tok uživatele, je odpověď vrácena do aplikace na uvedený `redirect_uri` parametr `response_mode` pomocí metody, která je zadána v parametru. Odpověď je stejná pro každý z předchozích případů, nezávisle na toku uživatele.

Úspěšná odpověď `response_mode=fragment` pomocí bude vypadat takto:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --------- | ----------- |
| id_token | Token ID, který aplikace požadovala. Token ID můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. |
| kód | Autorizační kód, který žádost požadovala, pokud jste použili `response_type=code+id_token`. Aplikace může použít autorizační kód k vyžádání přístupového tokenu pro cílový prostředek. Autorizační kódy obvykle vyprší přibližně po 10 minutách. |
| state | Pokud `state` je parametr zahrnut v požadavku, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla `state` ověřit, zda jsou hodnoty v požadavku a odpovědi identické. |

Chybové odpovědi lze také `redirect_uri` odeslat do parametru tak, aby aplikace může zpracovat odpovídajícím způsobem:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --------- | ----------- |
| error | Kód, který lze použít ke klasifikaci typů chyb, ke kterým dochází. |
| error_description | Konkrétní chybová zpráva, která může pomoci identifikovat hlavní příčinu chyby ověřování. |
| state | Pokud `state` je parametr zahrnut v požadavku, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla `state` ověřit, zda jsou hodnoty v požadavku a odpovědi identické. |

## <a name="validate-the-id-token"></a>Ověření tokenu ID

Pouhé přijetí tokenu ID nestačí k ověření uživatele. Ověřte podpis tokenu ID a ověřte deklarace identity v tokenu podle požadavků vaší aplikace. Azure AD B2C používá [JSON webové tokeny (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografie veřejného klíče k podepsání tokenů a ověření, že jsou platné. Existuje mnoho knihoven s otevřeným zdrojovým kódem, které jsou k dispozici pro ověření JWTs, v závislosti na jazyce předvoleb. Doporučujeme prozkoumat tyto možnosti spíše než implementovat vlastní logiku ověřování.

Azure AD B2C má koncový bod metadat OpenID Connect, který umožňuje aplikaci získat informace o Azure AD B2C za běhu. Tyto informace zahrnují koncové body, obsah tokenu a podpisové klíče tokenu. Existuje dokument metadat JSON pro každý tok uživatele ve vašem tenantovi B2C. Například dokument metadat pro `b2c_1_sign_in` tok `fabrikamb2c.onmicrosoft.com` uživatele v je umístěn na adrese:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Jednou z vlastností tohoto `jwks_uri`konfiguračního dokumentu je , jehož hodnota pro stejný tok uživatele by byla:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Chcete-li zjistit, který tok uživatele byl použit při podepisování tokenu ID (a odkud získat metadata), máte dvě možnosti. Nejprve je název toku `acr` uživatele zahrnut do deklarace v tokenu ID. Další možností je zakódovat tok uživatele `state` v hodnotě parametru při vystavení požadavku a potom jej dekódovat, abyste zjistili, který tok uživatele byl použit. Obě metody jsou platné.

Po získání dokumentu metadat z koncového bodu metadat OpenID Connect můžete použít veřejné klíče RSA 256 k ověření podpisu tokenu ID. V tomto koncovém bodě může být uvedeno `kid` více klíčů, z nichž každý je identifikován deklarací. Záhlaví tokenu ID také `kid` obsahuje deklaraci, která označuje, který z těchto klíčů byl použit k podepsání tokenu ID.

Chcete-li ověřit tokeny z Azure AD B2C, je třeba generovat veřejný klíč pomocí exponent(e) a modulus(n). Je třeba určit, jak to provést v příslušném programovacím jazyce odpovídajícím způsobem. Oficiální dokumentaci o generování veřejného klíče s protokolem RSA naleznete zde:https://tools.ietf.org/html/rfc3447#section-3.1

Po ověření podpisu tokenu ID existuje několik deklarací, které je třeba ověřit. Například:

- Ověřte deklaraci, `nonce` abyste zabránili útokům opětovného přehrání tokenu. Jeho hodnota by měla být to, co jste zadali v žádosti o přihlášení.
- Ověřte deklaraci `aud` a ujistěte se, že token ID byl vydán pro vaši aplikaci. Jeho hodnota by měla být ID aplikace vaší aplikace.
- `iat` Ověřte `exp` a deklarace identity a ujistěte se, že platnost tokenu ID nevypršela.

Existuje také několik dalších ověření, které byste měli provést. Ověření jsou podrobně popsána v [specifikaci Jádra OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). Můžete také chtít ověřit další deklarace identity, v závislosti na scénáři. Mezi běžná ověření patří:

- Zajištění, že se uživatel nebo organizace zaregistrovali k aplikaci.
- Zajištění, že uživatel má správné oprávnění/oprávnění.
- Zajištění, že došlo k určité síle ověřování, jako je například Azure Multi-Factor Authentication.

Po ověření tokenu ID můžete zahájit relaci s uživatelem. Deklarace identity v tokenu ID můžete použít k získání informací o uživateli ve vaší aplikaci. Použití těchto informací zahrnuje zobrazení, záznamy a autorizaci.

## <a name="get-a-token"></a>Získání žetonu

Pokud potřebujete, aby vaše webová aplikace spouštěla pouze toky uživatelů, můžete přeskočit několik dalších částí. Tyto oddíly se vztahují jenom pro webové aplikace, které potřebují provádět ověřená volání webového rozhraní API a jsou také chráněny azure ad B2C.

Autorizační kód, který jste získali `response_type=code+id_token`(pomocí) pro token k `POST` požadovanému `/token` prostředku, můžete uplatnit odesláním požadavku do koncového bodu. V Azure AD B2C můžete [požádat o přístupové tokeny pro jiná api](access-tokens.md#request-a-token) jako obvykle zadáním jejich oboru v požadavku.

Můžete také požádat o přístupový token pro vlastní webové rozhraní API back-end u vaší aplikace podle konvence použití ID klienta aplikace jako požadovaného oboru (což bude mít za následek přístupový token s tímto ID klienta jako "cílovou skupinu"):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Požaduje se | Popis |
| --------- | -------- | ----------- |
| {tenant} | Ano | Název vašeho klienta Azure AD B2C |
| {zásady} | Ano | Tok uživatele, který byl použit k získání autorizačního kódu. V tomto požadavku nelze použít jiný tok uživatele. Přidejte tento parametr do řetězce dotazu, nikoli do těla POST. |
| client_id | Ano | ID aplikace, které [portál Azure](https://portal.azure.com/) přiřazenka vaší aplikaci. |
| client_secret | Ano, ve webových aplikacích | Tajný klíč aplikace, který byl vygenerován na [webu Azure Portal](https://portal.azure.com/). Tajné klíče klientů se používají v tomto toku pro scénáře webové aplikace, kde klient může bezpečně uložit tajný klíč klienta. Pro nativní aplikace (veřejný klient) scénáře, tajné klíče klienta nelze bezpečně uložit, threfore není použit v tomto toku. Pokud používáte tajný klíč klienta, změňte jej v pravidelných intervalech. |
| kód | Ano | Autorizační kód, který jste získali na začátku toku uživatele. |
| grant_type | Ano | Typ grantu, který `authorization_code` musí být pro tok autorizačního kódu. |
| redirect_uri | Ano | Parametr `redirect_uri` aplikace, kde jste obdrželi autorizační kód. |
| scope | Ne | Mezera-oddělený seznam oborů. Obor `openid` označuje oprávnění k přihlášení uživatele a získání dat o uživateli ve formě id_token parametrů. Slouží k získání tokenů do vlastního webového rozhraní API back-endu vaší aplikace, které je reprezentováno stejným ID aplikace jako klient. Obor `offline_access` označuje, že vaše aplikace potřebuje obnovovací token pro rozšířený přístup k prostředkům. |

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
| --------- | ----------- |
| not_before | Čas, kdy je token považován za platný, v epochovém čase. |
| token_type | Hodnota typu tokenu. `Bearer`je jediný typ, který je podporován. |
| access_token | Podepsaný token JWT, který jste požadovali. |
| scope | Obory, pro které je token platný. |
| expires_in | Doba platnosti přístupového tokenu (v sekundách). |
| refresh_token | Obnovovací token OAuth 2.0. Aplikace můžete použít tento token k získání dalších tokenů po vypršení platnosti aktuálního tokenu. Obnovovací tokeny lze zachovat přístup k prostředkům po delší dobu. Obor `offline_access` musí být použit v požadavcích na autorizaci i tokeny, aby bylo možné získat obnovovací token. |

Odpovědi na chyby vypadají takto:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --------- | ----------- |
| error | Kód, který lze použít ke klasifikaci typů chyb, ke kterým dochází. |
| error_description | Zpráva, která může pomoci identifikovat hlavní příčinu chyby ověřování. |

## <a name="use-the-token"></a>Použití tokenu

Teď, když jste úspěšně získali přístupový token, můžete použít token v požadavcích na vaše `Authorization` webová api back-endu tím, že ho zahrajete do záhlaví:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Aktualizace tokenu

Platnost tokenů ID vyprší v krátkém časovém období. Aktualizujte tokeny po vypršení jejich platnosti, abyste mohli nadále přistupovat k prostředkům. Token můžete aktualizovat odesláním jiného `POST` požadavku `/token` do koncového bodu. Tentokrát zadejte `refresh_token` parametr namísto `code` parametru:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Požaduje se | Popis |
| --------- | -------- | ----------- |
| {tenant} | Ano | Název vašeho klienta Azure AD B2C |
| {zásady} | Ano | Tok uživatele, který byl použit k získání původního obnovovacího tokenu. V tomto požadavku nelze použít jiný tok uživatele. Přidejte tento parametr do řetězce dotazu, nikoli do těla POST. |
| client_id | Ano | ID aplikace, které [portál Azure](https://portal.azure.com/) přiřazenka vaší aplikaci. |
| client_secret | Ano, ve webových aplikacích | Tajný klíč aplikace, který byl vygenerován na [webu Azure Portal](https://portal.azure.com/). Tajné klíče klientů se používají v tomto toku pro scénáře webové aplikace, kde klient může bezpečně uložit tajný klíč klienta. Pro nativní aplikace (veřejný klient) scénáře, tajné klíče klienta nelze bezpečně uložit, threform nepoužívá na toto volání. Pokud používáte tajný klíč klienta, změňte jej v pravidelných intervalech. |
| grant_type | Ano | Typ grantu, který musí být obnovovacím tokenem pro tuto část toku autorizačního kódu. |
| refresh_token | Ano | Původní obnovovací token, který byl získán v druhé části toku. Obor `offline_access` musí být použit v požadavcích na autorizaci i tokeny, aby bylo možné získat obnovovací token. |
| redirect_uri | Ne | Parametr `redirect_uri` aplikace, kde jste obdrželi autorizační kód. |
| scope | Ne | Mezera-oddělený seznam oborů. Obor `openid` označuje oprávnění k přihlášení uživatele a získání dat o uživateli ve formě tokenů ID. Lze jej použít k odeslání tokenů do vlastního webového rozhraní API back-endvaší aplikace, které je reprezentováno stejným ID aplikace jako klient. Obor `offline_access` označuje, že vaše aplikace potřebuje obnovovací token pro rozšířený přístup k prostředkům. |

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
| --------- | ----------- |
| not_before | Čas, kdy je token považován za platný, v epochovém čase. |
| token_type | Hodnota typu tokenu. `Bearer`je jediný typ, který je podporován. |
| access_token | Podepsaný token JWT, který byl požadován. |
| scope | Obor, pro který je token platný. |
| expires_in | Doba platnosti přístupového tokenu (v sekundách). |
| refresh_token | Obnovovací token OAuth 2.0. Aplikace můžete použít tento token k získání dalších tokenů po vypršení platnosti aktuálního tokenu. Obnovovací tokeny lze zachovat přístup k prostředkům po delší dobu. |

Odpovědi na chyby vypadají takto:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --------- | ----------- |
| error | Kód, který lze použít ke klasifikaci typů chyb, ke kterým dochází. |
| error_description | Zpráva, která může pomoci identifikovat hlavní příčinu chyby ověřování. |

## <a name="send-a-sign-out-request"></a>Odeslání žádosti o odhlášení

Pokud chcete odhlásit uživatele z aplikace, nestačí vymazat soubory cookie aplikace nebo jinak ukončit relaci s uživatelem. Přesměrovat uživatele na Azure AD B2C odhlásit. Pokud tak neučiníte, uživatel může být schopen znovu ověřit do aplikace bez zadání jejich pověření znovu.

Chcete-li uživatele odhlásit, přesměrujte uživatele na `end_session` koncový bod, který je uveden v dokumentu metadat OpenID Connect popsaném výše:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parametr | Požaduje se | Popis |
| --------- | -------- | ----------- |
| {tenant} | Ano | Název vašeho klienta Azure AD B2C |
| {zásady} | Ano | Tok uživatele, který chcete použít k odhlášení uživatele z vaší aplikace. |
| id_token_hint| Ne | Dříve vydaný token ID pro předání koncovému bodu odhlášení jako nápověda o aktuální ověřené relaci koncového uživatele s klientem. Zajišťuje, `id_token_hint` že `post_logout_redirect_uri` je registrovaná adresa URL odpovědi v nastavení aplikace Azure AD B2C. |
| client_id | Ne* | ID aplikace, které [portál Azure](https://portal.azure.com/) přiřazenka vaší aplikaci.<br><br>\**To je vyžadováno při použití `Application` izolace konfigurace s přihlašování a vyžadovat _ID token_ v odhlášení požadavku je nastavena na `No`.* |
| post_logout_redirect_uri | Ne | Adresa URL, na kterou by měl být uživatel po úspěšném odhlášení přesměrován. Pokud není zahrnuta, Azure AD B2C zobrazí uživateli obecnou zprávu. Pokud nezadáte `id_token_hint`, neměli byste zaregistrovat tuto adresu URL jako adresu URL odpovědi v nastavení aplikace Azure AD B2C. |
| state | Ne | Pokud `state` je parametr zahrnut v požadavku, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla `state` ověřit, zda jsou hodnoty v požadavku a odpovědi identické. |

### <a name="secure-your-logout-redirect"></a>Zabezpečte přesměrování odhlášení

Po odhlášení je uživatel přesměrován na identifikátor `post_logout_redirect_uri` URI zadaný v parametru bez ohledu na adresy URL odpovědí, které byly určeny pro aplikaci. Pokud je však předán platný, `id_token_hint` Azure AD B2C `post_logout_redirect_uri` ověří, že hodnota odpovídá jedné z nakonfigurovaných identifikátorů URI přesměrování aplikace před provedením přesměrování. Pokud pro aplikaci nebyla nakonfigurována žádná odpovídající adresa URL odpovědi, zobrazí se chybová zpráva a uživatel nebude přesměrován.

### <a name="external-identity-provider-sign-out"></a>Odhlášení poskytovatele externí identity

Přesměrování uživatele na `end_session` koncový bod vymaže některé stavu jednotného přihlašování uživatele pomocí Azure AD B2C, ale neodhlásí uživatele z relace zprostředkovatele sociální identity (IDP). Pokud uživatel vybere stejné IDP během následného přihlášení, jsou znovu ověřeny bez zadání jejich pověření. Pokud se uživatel chce z aplikace odhlásit, nemusí to nutně znamenat, že se chce odhlásit ze svého účtu na Facebooku. Pokud jsou však použity místní účty, relace uživatele skončí správně.
