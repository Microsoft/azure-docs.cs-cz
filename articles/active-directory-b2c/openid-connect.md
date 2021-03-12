---
title: Webové přihlašování pomocí OpenID Connect – Azure Active Directory B2C
description: Sestavujte webové aplikace pomocí ověřovacího protokolu OpenID Connect v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5095e077ad1f2259c227c37f789dbcaf1f6d1cd7
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611857"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webové přihlašování pomocí OpenID Connect v Azure Active Directory B2C

OpenID Connect je ověřovací protokol, který je postaven na OAuth 2,0, který se dá použít k bezpečnému podepisování uživatelů v webových aplikacích. Pomocí Azure Active Directory B2C (Azure AD B2C) implementace OpenID Connect můžete v rámci svých webových aplikací k Azure Active Directory (Azure AD) přihlašovat, přihlašovat a další prostředí pro správu identit. V tomto průvodci se dozvíte, jak tak učinit způsobem nezávislým na jazyce. Popisuje, jak odesílat a přijímat zprávy HTTP bez použití žádné z našich open source knihoven.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozšiřuje *autorizační* protokol OAuth 2,0 pro použití jako *ověřovací* protokol. Tento ověřovací protokol vám umožní provést jednotné přihlašování. Zavádí koncept *tokenu ID*, který umožňuje klientovi ověřit identitu uživatele a získat základní informace o profilu uživatele.

Vzhledem k tomu, že rozšiřuje OAuth 2,0, umožňuje aplikacím zabezpečeně získat *přístupové tokeny*. Přístupové tokeny můžete použít pro přístup k prostředkům, které jsou zabezpečené [autorizačním serverem](protocols-overview.md). OpenID Connect se doporučuje, pokud vytváříte webovou aplikaci, která je hostována na serveru a přístupná prostřednictvím prohlížeče. Další informace o tokenech najdete v tématu [Přehled tokenů v Azure Active Directory B2C](tokens-overview.md)

Azure AD B2C rozšiřuje standardní protokol OpenID Connect a provede více než jednoduché ověřování a autorizaci. Zavádí [parametr toku uživatele](user-flow-overview.md), který umožňuje používat OpenID Connect k přidání uživatelského prostředí do aplikace, jako je například registrace, přihlášení a Správa profilů.

## <a name="send-authentication-requests"></a>Odeslat požadavky na ověření

Pokud vaše webová aplikace potřebuje ověřit uživatele a spustit tok uživatele, může uživatele nasměrovat na `/authorize` koncový bod. Uživatel provede akci v závislosti na toku uživatele.

V této žádosti klient indikuje oprávnění, která potřebuje získat od uživatele v `scope` parametru, a určuje tok uživatele, který se má spustit. Pokud chcete zjistit, jak požadavek funguje, zkuste vložit požadavek do prohlížeče a spustit ho. Nahraďte `{tenant}` názvem vašeho tenanta. Nahraďte ID aplikace, `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` kterou jste předtím zaregistrovali ve vašem tenantovi. Změňte také název zásady ( `{policy}` ) na název zásady, který máte ve vašem tenantovi, například `b2c_1_sign_in` .

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametr | Povinné | Popis |
| --------- | -------- | ----------- |
| tenant | Yes | Název vašeho tenanta Azure AD B2C |
| politických | Yes | Tok uživatele, který má být spuštěn. Zadejte název uživatelského toku, který jste vytvořili ve vašem tenantovi Azure AD B2C. Například: `b2c_1_sign_in` , `b2c_1_sign_up` , nebo `b2c_1_edit_profile` . |
| client_id | Yes | ID aplikace, které [Azure Portal](https://portal.azure.com/) přiřazena k vaší aplikaci. |
| generované | Yes | Hodnota obsažená v žádosti (generovaná aplikací), která je součástí výsledného tokenu ID jako deklarace identity. Aplikace pak může tuto hodnotu ověřit a zmírnit tak útoky prostřednictvím opakovaného přehrání tokenu. Hodnota je obvykle náhodný jedinečný řetězec, který lze použít k identifikaci původu požadavku. |
| response_type | Yes | Musí zahrnovat token ID pro OpenID Connect. Pokud vaše webová aplikace také potřebuje tokeny pro volání webového rozhraní API, můžete použít `code+id_token` . |
| scope | Yes | Mezerou oddělený seznam oborů. `openid`Rozsah označuje oprávnění k přihlášení uživatele a získání dat o uživateli ve formě tokenů ID. `offline_access`Obor je volitelný pro webové aplikace. Indikuje, že vaše aplikace bude potřebovat *aktualizační token* pro rozšířený přístup k prostředkům. |
| výzv | No | Typ uživatelské interakce, která je povinná. Jediná platná hodnota je v tomto okamžiku `login` , která uživateli vynutí zadání přihlašovacích údajů k této žádosti. |
| redirect_uri | No | `redirect_uri`Parametr vaší aplikace, kde lze odesílat a přijímat odpovědi na ověřování vaší aplikací. Musí přesně odpovídat jednomu z `redirect_uri` parametrů, které jste zaregistrovali v Azure Portal, s tím rozdílem, že musí být kódovaný v adrese URL. |
| response_mode | No | Metoda, která slouží k odeslání výsledného autorizačního kódu zpátky do vaší aplikace. Může to být buď `query` , `form_post` nebo `fragment` .  `form_post`Pro zajištění nejlepšího zabezpečení se doporučuje režim odezvy. |
| state | No | Hodnota obsažená v požadavku, která se také vrátila v odpovědi na token. Může to být řetězec libovolného obsahu, který chcete. Náhodně vygenerovaná jedinečná hodnota se obvykle používá k prevenci útoků proti padělání požadavků mezi lokalitami. Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než došlo k žádosti o ověření, jako je například stránka, na které se nachází. |
| login_hint | No| Dá se použít k předvyplnění pole přihlašovacího jména na přihlašovací stránce. Další informace najdete v tématu [přednastavení přihlašovací jméno](direct-signin.md#prepopulate-the-sign-in-name).  |
| domain_hint | No| Poskytuje nápovědu pro Azure AD B2C o poskytovateli sociální identity, který by se měl použít pro přihlášení. Pokud je zahrnutá platná hodnota, přejde uživatel přímo na přihlašovací stránku zprostředkovatele identity.  Další informace najdete v tématu [přesměrování přihlášení k poskytovateli sociálních sítí](direct-signin.md#redirect-sign-in-to-a-social-provider). |
| Vlastní parametry | No| Vlastní parametry, které je možné použít s [vlastními zásadami](custom-policy-overview.md). Například [dynamický identifikátor URI obsahu vlastní stránky](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)nebo [překladače deklarací klíč-hodnota](claim-resolver-overview.md#oauth2-key-value-parameters). |

V tomto okamžiku se uživateli zobrazí výzva k dokončení pracovního postupu. Uživatel bude muset zadat své uživatelské jméno a heslo, přihlašovat se přes sociální identitu nebo si zaregistrovat adresář. V závislosti na tom, jakým způsobem je tok uživatele definovaný, se může jednat o libovolný jiný počet kroků.

Poté, co uživatel dokončí tok uživatele, je vrácena odpověď do vaší aplikace v označeném `redirect_uri` parametru pomocí metody, která je zadána v `response_mode` parametru. Odpověď je stejná pro všechny předchozí případy, nezávisle na toku uživatele.

Úspěšná odpověď `response_mode=fragment` by vypadala takto:

```http
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --------- | ----------- |
| id_token | Token ID, který požadovaná aplikace požaduje. Token ID můžete použít k ověření identity uživatele a zahájení relace s uživatelem. |
| kód | Autorizační kód, který aplikace požádala, pokud jste použili `response_type=code+id_token` . Aplikace může použít autorizační kód k vyžádání přístupového tokenu pro cílový prostředek. Autorizační kódy obvykle vyprší po asi 10 minutách. |
| state | Pokud `state` je parametr zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, zda `state` jsou hodnoty v žádosti a odpovědi stejné. |

Do parametru lze také odeslat chybové odpovědi `redirect_uri` , aby je aplikace mohla správně zpracovat:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --------- | ----------- |
| error | Kód, který lze použít ke klasifikaci typů chyb, ke kterým dojde. |
| error_description | Konkrétní chybová zpráva, která může přispět k identifikaci hlavní příčiny chyby ověřování. |
| state | Pokud `state` je parametr zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, zda `state` jsou hodnoty v žádosti a odpovědi stejné. |

## <a name="validate-the-id-token"></a>Ověřit token ID

Pouze příjem tokenu ID nestačí k ověření uživatele. Ověří signaturu tokenu ID a ověří deklarace identity v tokenu podle požadavků vaší aplikace. Azure AD B2C používá [webové tokeny JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografii s veřejným klíčem k podepisování tokenů a ověření, že jsou platné. Existuje mnoho Open-Source knihoven, které jsou k dispozici pro ověřování JWTs v závislosti na jazyce předvolby. Doporučujeme tyto možnosti prozkoumat místo implementace vlastní logiky ověřování.

Azure AD B2C má koncový bod metadat OpenID Connect, který umožňuje aplikaci získat informace o Azure AD B2C za běhu. Mezi tyto informace patří koncové body, obsah tokenu a podpisové klíče tokenu. Pro každého toku uživatelů v tenantovi B2C existuje dokument metadat JSON. Například dokument metadat pro `b2c_1_sign_in` tok uživatele v nástroji `fabrikamb2c.onmicrosoft.com` je umístěný v:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Jedna z vlastností tohoto dokumentu konfigurace je `jwks_uri` , jehož hodnota pro stejný tok uživatele by byla:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Chcete-li zjistit, který tok uživatele byl použit při podepisování tokenu ID (a z toho, kde získat metadata), máte dvě možnosti. Nejdřív je název toku uživatele zahrnutý v `acr` deklaraci identity v tokenu ID. Další možností je zakódovat tok uživatele v hodnotě `state` parametru při vystavení požadavku a pak ho dekódovat, abyste zjistili, který tok uživatele byl použit. Kterákoli z metod je platná.

Po získání dokumentu metadat z koncového bodu metadat OpenID Connect můžete použít veřejné klíče RSA 256 k ověření podpisu tokenu ID. V tomto koncovém bodu může být uvedeno více klíčů, z nichž každá je označena `kid` deklarací. Záhlaví tokenu ID obsahuje také `kid` deklaraci identity, která označuje, které z těchto klíčů byly použity k podepsání tokenu ID.

Chcete-li ověřit tokeny z Azure AD B2C, je nutné vygenerovat veřejný klíč pomocí exponentu (e) a zbytku (n). Musíte určit, jak to provést v příslušném programovacím jazyce. Oficiální dokumentaci k generaci veřejných klíčů s protokolem RSA najdete tady: https://tools.ietf.org/html/rfc3447#section-3.1

Po ověření signatury tokenu ID máte k dispozici několik deklarací identity, které je třeba ověřit. Například:

- Ověřte `nonce` deklaraci identity, aby nedocházelo k útokům na opětovné přehrání tokenu Jeho hodnota by měla být ta, kterou jste zadali v žádosti o přihlášení.
- Ověřte `aud` deklaraci identity, aby se zajistilo, že se token ID vystavil pro vaši aplikaci. Jeho hodnota by měla být ID aplikace vaší aplikace.
- Ověřte `iat` deklarace identity a a ujistěte se `exp` , že platnost TOKENu ID nevypršela.

K dispozici je také několik ověření, která byste měli provést. Ověřování jsou podrobně popsány ve [specifikaci OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). V závislosti na vašem scénáři možná budete chtít taky ověřit další deklarace identity. Mezi běžná ověření patří:

- Ujistěte se, že se uživatel nebo organizace zaregistrovali do aplikace.
- Zajistěte, aby měl uživatel správnou autorizaci nebo oprávnění.
- Ujistěte se, že došlo k určité síle ověřování, jako je například Azure AD Multi-Factor Authentication.

Po ověření tokenu ID můžete zahájit relaci s uživatelem. Deklarace identity v tokenu ID můžete použít k získání informací o uživateli ve vaší aplikaci. Použití pro tyto informace zahrnuje zobrazení, záznamy a autorizaci.

## <a name="get-a-token"></a>Získání tokenu

Pokud potřebujete, aby webová aplikace spouštěla toky uživatelů, můžete přeskočit několik dalších částí. Tyto části platí jenom pro webové aplikace, které potřebují ověřená volání do webového rozhraní API a jsou taky chráněná Azure AD B2C.

Můžete uplatnit autorizační kód, který jste získali (pomocí), `response_type=code+id_token` pro token k požadovanému prostředku odesláním `POST` žádosti do `/token` koncového bodu. V Azure AD B2C můžete [požadovat přístupové tokeny pro jiná rozhraní API](access-tokens.md#request-a-token) obvyklým zadáním jejich oboru (y) v žádosti.

Můžete také požádat o přístupový token pro vlastní back-endové webové rozhraní API vaší aplikace podle konvence použití ID klienta aplikace jako požadovaného oboru (což bude mít za následek přístupový token s tímto ID klienta jako "cílová"):

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Povinné | Popis |
| --------- | -------- | ----------- |
| tenant | Yes | Název vašeho tenanta Azure AD B2C |
| politických | Yes | Tok uživatele, který se použil k získání autorizačního kódu. V této žádosti nemůžete použít jiný tok uživatele. Přidejte tento parametr do řetězce dotazu, nikoli do těla příspěvku. |
| client_id | Yes | ID aplikace, které [Azure Portal](https://portal.azure.com/) přiřazena k vaší aplikaci. |
| client_secret | Ano, v Web Apps | Tajný klíč aplikace, který byl vygenerován v [Azure Portal](https://portal.azure.com/). Tajné klíče klienta se v tomto toku používají pro scénáře webové aplikace, kde může klient bezpečně uložit tajný klíč klienta. V případě scénářů nativních aplikací (veřejného klienta) nelze tajné klíče klienta bezpečně uložit, a proto se v tomto toku nepoužívají. Pokud používáte tajný klíč klienta, pravidelně ho prosím změňte. |
| kód | Yes | Autorizační kód, který jste získali na začátku toku uživatele. |
| grant_type | Yes | Typ grantu, který musí být `authorization_code` pro tok autorizačního kódu. |
| redirect_uri | Yes | `redirect_uri`Parametr aplikace, ve které jste obdrželi autorizační kód. |
| scope | No | Mezerou oddělený seznam oborů. `openid`Rozsah označuje oprávnění k přihlášení uživatele a získání dat o uživateli ve formě parametrů id_token. Dá se použít k získání tokenů do vlastního webového rozhraní API back-endu vaší aplikace, které je reprezentováno stejným ID aplikace jako klient. `offline_access`Rozsah označuje, že vaše aplikace potřebuje aktualizační token pro rozšířený přístup k prostředkům. |

Úspěšná odpověď tokenu vypadá nějak takto:

```json
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
| not_before | Čas, kdy se token považuje za platný, v epocha čase. |
| token_type | Hodnota typu tokenu. `Bearer` je jediný typ, který je podporován. |
| access_token | Podepsaný token JWT, který jste požádali. |
| scope | Obory, pro které je token platný |
| expires_in | Doba, po kterou je přístupový token platný (v sekundách). |
| refresh_token | Obnovovací token OAuth 2,0. Aplikace může tento token použít k získání dalších tokenů po vypršení platnosti tokenu. Aktualizační tokeny se dají použít k uchování přístupu k prostředkům po delší dobu. Aby `offline_access` bylo možné získat obnovovací token, musí být obor použit v žádosti o autorizaci i token. |

Chybové odpovědi vypadají jako:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --------- | ----------- |
| error | Kód, který lze použít ke klasifikaci typů chyb, ke kterým dojde. |
| error_description | Zpráva, která může pomáhat identifikovat hlavní příčinu chyby ověřování. |

## <a name="use-the-token"></a>Použití tokenu

Teď, když jste úspěšně získali přístupový token, můžete token v žádostech na vaše back-endové webové rozhraní API použít, když ho zahrnete do `Authorization` hlavičky:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Aktualizace tokenu

Tokeny ID vyprší v krátké době. Obnovte tokeny po vypršení platnosti, abyste měli dál přístup k prostředkům. Můžete aktualizovat token odesláním další `POST` žádosti do `/token` koncového bodu. Tentokrát zadejte `refresh_token` parametr namísto `code` parametru:

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Povinné | Popis |
| --------- | -------- | ----------- |
| tenant | Yes | Název vašeho tenanta Azure AD B2C |
| politických | Yes | Tok uživatele, který se použil k získání původního obnovovacího tokenu. V této žádosti nemůžete použít jiný tok uživatele. Přidejte tento parametr do řetězce dotazu, nikoli do těla příspěvku. |
| client_id | Yes | ID aplikace, které [Azure Portal](https://portal.azure.com/) přiřazena k vaší aplikaci. |
| client_secret | Ano, v Web Apps | Tajný klíč aplikace, který byl vygenerován v [Azure Portal](https://portal.azure.com/). Tajné klíče klienta se v tomto toku používají pro scénáře webové aplikace, kde může klient bezpečně uložit tajný klíč klienta. V případě scénářů nativních aplikací (veřejného klienta) nelze tajné klíče klienta bezpečně uložit, a proto se na toto volání nepoužívají. Pokud používáte tajný klíč klienta, pravidelně ho prosím změňte. |
| grant_type | Yes | Typ grantu, který musí být `refresh_token` pro tuto část toku autorizačního kódu. |
| refresh_token | Yes | Původní obnovovací token, který byl získán v druhé části toku. `offline_access`Obor musí být použit jak v žádosti o autorizaci, tak na tokeny, aby mohl získat obnovovací token. |
| redirect_uri | No | `redirect_uri`Parametr aplikace, ve které jste obdrželi autorizační kód. |
| scope | No | Mezerou oddělený seznam oborů. `openid`Rozsah označuje oprávnění k přihlášení uživatele a získání dat o uživateli ve formě tokenů ID. Dá se použít k posílání tokenů do vlastního webového rozhraní API back-endu vaší aplikace, které je reprezentováno stejným ID aplikace jako klient. `offline_access`Rozsah označuje, že vaše aplikace potřebuje aktualizační token pro rozšířený přístup k prostředkům. |

Úspěšná odpověď tokenu vypadá nějak takto:

```json
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
| not_before | Čas, kdy se token považuje za platný, v epocha čase. |
| token_type | Hodnota typu tokenu. `Bearer` je jediný typ, který je podporován. |
| access_token | Podepsaný token JWT, který byl vyžádán. |
| scope | Obor, pro který je token platný |
| expires_in | Doba, po kterou je přístupový token platný (v sekundách). |
| refresh_token | Obnovovací token OAuth 2,0. Aplikace může tento token použít k získání dalších tokenů po vypršení platnosti tokenu. Aktualizační tokeny se dají použít k uchování přístupu k prostředkům po delší dobu. |

Chybové odpovědi vypadají jako:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --------- | ----------- |
| error | Kód, který lze použít ke klasifikaci typů chyb, ke kterým dojde. |
| error_description | Zpráva, která může pomáhat identifikovat hlavní příčinu chyby ověřování. |

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

Pokud chcete uživatele podepsat z aplikace, nestačí vymazat soubory cookie aplikace nebo jinak ukončit relaci s uživatelem. Přesměrujte uživatele na Azure AD B2C, abyste se odhlásili. Pokud to neuděláte, uživatel může být schopný znovu ověřit vaši aplikaci, aniž by musel zadávat přihlašovací údaje znovu. Další informace najdete v tématu [Azure AD B2C relace](session-behavior.md).

Pokud chcete uživatele odhlásit, přesměrujte uživatele na `end_session` koncový bod, který je uvedený v dokumentu metadat OpenID Connect popsaného výše:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parametr | Povinné | Popis |
| --------- | -------- | ----------- |
| tenant | Yes | Název vašeho tenanta Azure AD B2C |
| politických | Yes | Tok uživatele, který chcete použít k podepsání uživatele z vaší aplikace. |
| id_token_hint| No | Dřív vydaný token ID, který se má předat koncovému bodu pro odhlášení, jako pomocný parametr pro aktuální ověřenou relaci koncového uživatele s klientem. Tím `id_token_hint` zajistíte, že `post_logout_redirect_uri` je registrovaná adresa URL odpovědi v nastavení aplikace Azure AD B2C. Další informace najdete v tématu [zabezpečení přesměrování odhlášení](#secure-your-logout-redirect). |
| client_id | Žádné | ID aplikace, které [Azure Portal](https://portal.azure.com/) přiřazena k vaší aplikaci.<br><br>\**To je potřeba při použití `Application` Konfigurace izolace jednotného přihlašování a _vyžádání tokenu ID_ v žádosti o odhlášení je nastavené na `No` .* |
| post_logout_redirect_uri | No | Adresa URL, na kterou má být uživatel přesměrován po úspěšném odhlášení. Pokud není zahrnutý, Azure AD B2C zobrazuje uživatele obecnou zprávu. Pokud neposkytnete `id_token_hint` , neměli byste tuto adresu URL v nastavení aplikace Azure AD B2C zaregistrovat jako adresu URL odpovědi. |
| state | No | Pokud `state` je parametr zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, zda `state` jsou hodnoty v žádosti a odpovědi stejné. |

### <a name="secure-your-logout-redirect"></a>Zabezpečení přesměrování odhlášení

Po odhlášení se uživatel přesměruje na identifikátor URI zadaný v `post_logout_redirect_uri` parametru, a to bez ohledu na adresy URL odpovědi, které byly pro aplikaci zadané. Pokud se ale `id_token_hint` předává platnost a **token vyžadovat ID v žádostech o odhlášení** je zapnutý, Azure AD B2C ověří, jestli hodnota `post_logout_redirect_uri` odpovídá jednomu z konfigurovaných identifikátorů URI pro přesměrování aplikace před provedením přesměrování. Pokud pro aplikaci nebyla nakonfigurovaná žádná odpovídající adresa URL odpovědi, zobrazí se chybová zpráva a uživatel nebude přesměrován.

Pokud chcete nastavit požadovaný token ID v žádostech o odhlášení, přečtěte si téma [Konfigurace chování relace v Azure Active Directory B2C](session-behavior.md#secure-your-logout-redirect).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure AD B2C relaci](session-behavior.md).
