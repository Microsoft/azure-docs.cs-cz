---
title: Tok autorizačního kódu – Azure Active Directory B2C | Microsoft Docs
description: Naučte se vytvářet webové aplikace pomocí Azure AD B2C a ověřovacího protokolu OpenID Connect.
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
ms.openlocfilehash: a6a993fdf4fd266afb9459fedd13412d8796e0a5
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611500"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Tok autorizačního kódu OAuth 2,0 v Azure Active Directory B2C

Pokud chcete získat přístup k chráněným prostředkům, jako jsou například webová rozhraní API, můžete použít udělení autorizačního kódu OAuth 2,0 v aplikacích nainstalovaných na zařízení. Pomocí implementace OAuth 2,0 pro Azure Active Directory B2C (Azure AD B2C) můžete přidat úlohy registrace, přihlašování a dalších úloh správy identit do aplikací pro samoobslužnou, mobilní a desktopovou aplikaci. Tento článek je nezávislý na jazyce. V článku popisujeme, jak odesílat a přijímat zprávy HTTP bez použití Open Source knihoven. Pokud je to možné, doporučujeme, abyste používali podporované knihovny Microsoft Authentication Library (MSAL). Podívejte se na [ukázkové aplikace, které používají MSAL](code-samples.md).

Tok autorizačního kódu OAuth 2,0 je popsaný v [části 4,1 specifikace oauth 2,0](https://tools.ietf.org/html/rfc6749). Můžete ji použít k ověřování a autorizaci ve většině [typů aplikací](application-types.md), včetně webových aplikací, jednostránkovéch aplikací a nativně nainstalovaných aplikací. Tok autorizačního kódu OAuth 2,0 můžete použít k bezpečnému získání přístupových tokenů a k aktualizaci tokenů pro vaše aplikace, které je možné použít pro přístup k prostředkům zabezpečeným [autorizačním serverem](protocols-overview.md).  Aktualizační token umožňuje klientovi získat nové tokeny přístupu (a aktualizovat) po vypršení platnosti přístupového tokenu, obvykle po jedné hodině.

Tento článek se zaměřuje na tok autorizačního kódu OAuth 2,0 **veřejných klientů** . Veřejný klient je libovolná klientská aplikace, která nemůže být důvěryhodná pro bezpečné udržování integrity tajného hesla. To zahrnuje jednostránkové aplikace, mobilní aplikace, desktopové aplikace a v podstatě všechny aplikace, které neběží na serveru.

> [!NOTE]
> Pokud chcete do webové aplikace přidat správu identit pomocí Azure AD B2C, použijte místo OAuth 2,0 [OpenID Connect](openid-connect.md) .

Azure AD B2C rozšiřuje standardní toky protokolu OAuth 2,0, aby bylo možné provádět více než jednoduché ověřování a autorizaci. Zavádí [tok uživatele](user-flow-overview.md). Pomocí uživatelských toků můžete pomocí OAuth 2,0 přidat uživatelské prostředí do aplikace, jako je například registrace, přihlášení a Správa profilů. Zprostředkovatelé identity, kteří používají protokol OAuth 2,0, zahrnují [Amazon](identity-provider-amazon.md), [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md)a [LinkedIn](identity-provider-linkedin.md).

Postup vyzkoušení požadavků HTTP v tomto článku:

1. Nahraďte `{tenant}` názvem vašeho tenanta Azure AD B2C.
1. Nahraďte `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` ID aplikace, kterou jste předtím zaregistrovali ve vašem tenantovi Azure AD B2C.
1. Nahraďte `{policy}` názvem zásady, kterou jste vytvořili ve vašem tenantovi, například `b2c_1_sign_in` .

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>Pro jednostránkové aplikace se vyžaduje nastavení identifikátoru URI přesměrování.

Tok autorizačního kódu pro aplikace s jednou stránkou vyžaduje další instalaci.  Postupujte podle pokynů pro [Vytvoření jednostránkové aplikace](tutorial-register-spa.md) pro správné označení identifikátoru URI přesměrování, který je povolený pro CORS. Pokud chcete aktualizovat existující identifikátor URI pro přesměrování a povolit CORS, můžete kliknout na dotaz migrace v části Web na kartě **ověřování** **Registrace aplikace**. Případně můžete otevřít **editor manifestu registrace aplikací** a `type` v části nastavit pole pro identifikátor URI přesměrování `spa` `replyUrlsWithType` .

`spa`Typ přesměrování je zpětně kompatibilní s implicitním tokem. Aplikace, které aktuálně používají implicitní tok k získání tokenů, se můžou přesunout na `spa` typ URI přesměrování bez problémů a dál používat implicitní tok.

## <a name="1-get-an-authorization-code"></a>1. získání autorizačního kódu
Tok autorizačního kódu začíná klientem, který uživatele přesměruje na `/authorize` koncový bod. Jedná se o interaktivní část toku, kde uživatel provede akci. V této žádosti klient indikuje v `scope` parametru oprávnění, která potřebuje získat od uživatele. Následující tři příklady (s oddělovači řádků pro čitelnost) každý využívají jiný tok uživatele.


```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

| Parametr | Povinné? | Description |
| --- | --- | --- |
|tenant| Vyžadováno | Název vašeho tenanta Azure AD B2C|
| politických | Vyžadováno | Tok uživatele, který má být spuštěn. Zadejte název uživatelského toku, který jste vytvořili ve vašem tenantovi Azure AD B2C. Například: `b2c_1_sign_in` , `b2c_1_sign_up` , nebo `b2c_1_edit_profile` . |
| client_id |Vyžadováno |ID aplikace přiřazené vaší aplikaci v [Azure Portal](https://portal.azure.com). |
| response_type |Vyžadováno |Typ odpovědi, který musí být součástí `code` toku autorizačního kódu. |
| redirect_uri |Vyžadováno |Identifikátor URI pro přesměrování vaší aplikace, ve kterém se vaše aplikace odesílá a přijímá ověřovací odpovědi. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu, s tím rozdílem, že musí být zakódovaný na adrese URL. |
| scope |Vyžadováno |Mezerou oddělený seznam oborů. Jedna hodnota oboru označuje Azure Active Directory (Azure AD) obě oprávnění, která jsou požadována. Použití ID klienta jako oboru znamená, že vaše aplikace potřebuje přístupový token, který se dá použít pro vlastní službu nebo webové rozhraní API reprezentované stejným ID klienta.  `offline_access`Rozsah označuje, že vaše aplikace potřebuje aktualizační token pro dlouhodobě přístup k prostředkům. Obor můžete použít také `openid` k vyžádání tokenu ID od Azure AD B2C. |
| response_mode |Doporučeno |Metoda, kterou použijete k odeslání výsledného autorizačního kódu zpátky do vaší aplikace. Může to být `query` , `form_post` , nebo `fragment` . |
| state |Doporučeno |Hodnota obsažená v požadavku může být řetězec libovolného obsahu, který chcete použít. Obvykle se používá náhodně vygenerovaná jedinečná hodnota, která zabraňuje útokům proti padělání požadavků mezi lokalitami. Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než k žádosti o ověření dojde. Například stránka, na které uživatel byl, nebo uživatelský tok, který se spustil. |
| výzv |Volitelné |Typ interakce uživatele, která je povinná. V současné době je jedinou platnou hodnotou `login` , která uživateli přinutí zadat přihlašovací údaje k této žádosti. Jednotné přihlašování se neprojeví. |
| code_challenge  | Doporučené/povinné | Slouží k zabezpečení autorizačního kódu prostřednictvím ověřovacího klíče pro výměnu kódu (PKCE). Požadováno `code_challenge_method` , pokud je zahrnuto. Další informace najdete v [dokumentu RFC PKCE](https://tools.ietf.org/html/rfc7636). Teď se doporučuje pro všechny typy aplikací – nativní aplikace, jednostránkové a důvěrné klienty, jako jsou webové aplikace. | 
| `code_challenge_method` | Doporučené/povinné | Metoda použitá k zakódování `code_verifier` pro `code_challenge` parametr. To *by mělo* být `S256` , ale specifikace umožňuje použití z `plain` nějakého důvodu, když klient nepodporuje SHA256. <br/><br/>Pokud je vyloučený, předpokládá se, že je v `code_challenge` případě zahrnutí prostý text `code_challenge` . Platforma Microsoft identity podporuje i `plain` `S256` . Další informace najdete v [dokumentu RFC PKCE](https://tools.ietf.org/html/rfc7636). To je nutné pro [jednostránkové aplikace pomocí toku autorizačního kódu](tutorial-register-spa.md).|
| login_hint | No| Dá se použít k předvyplnění pole přihlašovacího jména na přihlašovací stránce. Další informace najdete v tématu [přednastavení přihlašovací jméno](direct-signin.md#prepopulate-the-sign-in-name).  |
| domain_hint | No| Poskytuje nápovědu pro Azure AD B2C o poskytovateli sociální identity, který by se měl použít pro přihlášení. Pokud je zahrnutá platná hodnota, přejde uživatel přímo na přihlašovací stránku zprostředkovatele identity.  Další informace najdete v tématu [přesměrování přihlášení k poskytovateli sociálních sítí](direct-signin.md#redirect-sign-in-to-a-social-provider). |
| Vlastní parametry | No| Vlastní parametry, které je možné použít s [vlastními zásadami](custom-policy-overview.md). Například [dynamický identifikátor URI obsahu vlastní stránky](customize-ui-with-html.md?pivots=b2c-custom-policy#configure-dynamic-custom-page-content-uri)nebo [překladače deklarací klíč-hodnota](claim-resolver-overview.md#oauth2-key-value-parameters). |

V tomto okamžiku se uživateli zobrazí výzva k dokončení pracovního postupu toku uživatele. Může to znamenat, že uživatel zadá své uživatelské jméno a heslo, přihlásí se přes sociální identitu, zaregistruje se do adresáře nebo jakýkoli jiný počet kroků. Akce uživatele závisí na způsobu definování toku uživatele.

Až uživatel tok uživatele dokončí, Azure AD vrátí odpověď vaší aplikaci na hodnotu, kterou jste použili pro `redirect_uri` . Používá metodu určenou v `response_mode` parametru. Odpověď je přesně stejná pro každý scénář akce uživatele, nezávisle na spuštěném toku uživatele.

Úspěšná odpověď, která používá, `response_mode=query` vypadá takto:

```http
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametr | Popis |
| --- | --- |
| kód |Autorizační kód, který požadovaná aplikace požaduje. Aplikace může použít autorizační kód k vyžádání přístupového tokenu pro cílový prostředek. Autorizační kódy jsou velmi krátkodobé. Obvykle vyprší po asi 10 minutách. |
| state |Úplný popis najdete v tabulce v předchozí části. Pokud `state` je parametr zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že `state` hodnoty v žádosti a odpovědi jsou identické. |

Odpovědi na chyby se také odesílají do identifikátoru URI přesměrování, aby je aplikace mohla vhodně zpracovat:

```http
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým dojde. Řetězec můžete také použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomáhat identifikovat hlavní příčinu chyby ověřování. |
| state |Zobrazí úplný popis v předchozí tabulce. Pokud `state` je parametr zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že `state` hodnoty v žádosti a odpovědi jsou identické. |

## <a name="2-get-an-access-token"></a>2. získání přístupového tokenu
Teď, když jste získali autorizační kód, můžete uplatnit `code` pro token u zamýšleného prostředku odesláním požadavku post do `/token` koncového bodu. V Azure AD B2C můžete [vyžádat přístupové tokeny pro jiné rozhraní API](access-tokens.md#request-a-token) obvyklým způsobem zadáním jejich oboru (y) v žádosti.

Můžete také požádat o přístupový token pro vlastní back-endové webové rozhraní API vaší aplikace podle konvence použití ID klienta aplikace jako požadovaného oboru (což bude mít za následek přístupový token s tímto ID klienta jako "cílová"):

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
```

| Parametr | Povinné? | Description |
| --- | --- | --- |
|tenant| Vyžadováno | Název vašeho tenanta Azure AD B2C|
|politických| Vyžadováno| Tok uživatele, který se použil k získání autorizačního kódu. V této žádosti nemůžete použít jiný tok uživatele. |
| client_id |Vyžadováno |ID aplikace přiřazené vaší aplikaci v [Azure Portal](https://portal.azure.com).|
| client_secret | Ano, v Web Apps | Tajný klíč aplikace, který byl vygenerován v [Azure Portal](https://portal.azure.com/). Tajné klíče klienta se v tomto toku používají pro scénáře webové aplikace, kde může klient bezpečně uložit tajný klíč klienta. V případě scénářů nativních aplikací (veřejného klienta) nelze tajné klíče klienta bezpečně uložit, a proto nejsou použity v tomto volání. Pokud použijete tajný klíč klienta, pravidelně ho prosím změňte. |
| grant_type |Vyžadováno |Typ udělení. Pro tok autorizačního kódu musí být typ udělení `authorization_code` . |
| scope |Doporučeno |Mezerou oddělený seznam oborů. Jedna hodnota oboru indikuje pro Azure AD obě oprávnění, která jsou požadována. Použití ID klienta jako oboru znamená, že vaše aplikace potřebuje přístupový token, který se dá použít pro vlastní službu nebo webové rozhraní API reprezentované stejným ID klienta.  `offline_access`Rozsah označuje, že vaše aplikace potřebuje aktualizační token pro dlouhodobě přístup k prostředkům.  Obor můžete použít také `openid` k vyžádání tokenu ID od Azure AD B2C. |
| kód |Vyžadováno |Autorizační kód, který jste získali v první nožkě toku. |
| redirect_uri |Vyžadováno |Identifikátor URI přesměrování aplikace, kde jste dostali autorizační kód. |
| code_verifier | doporučil | Stejný code_verifier, který byl použit k získání authorization_code. Vyžaduje se, pokud se v žádosti o udělení autorizačního kódu použil PKCE. Další informace najdete v [dokumentu RFC PKCE](https://tools.ietf.org/html/rfc7636). |

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
| --- | --- |
| not_before |Čas, kdy se token považuje za platný, v epocha čase. |
| token_type |Hodnota typu tokenu. Jediným typem, který podporuje Azure AD, je nosič. |
| access_token |Podepsaný JSON Web Token (JWT), který jste požádali. |
| scope |Obory, pro které je token platný. Můžete také použít obory k ukládání tokenů do mezipaměti pro pozdější použití. |
| expires_in |Doba, po kterou je token platný (v sekundách). |
| refresh_token |Obnovovací token OAuth 2,0. Aplikace může tento token použít k získání dalších tokenů po vypršení platnosti tokenu. Aktualizační tokeny jsou dlouhodobě dlouhodobé. Můžete je použít k uchování přístupu k prostředkům po delší dobu. Další informace najdete v referenčních informacích k [tokenu Azure AD B2C](tokens-overview.md). |

Chybové odpovědi vypadají takto:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým dojde. Řetězec můžete také použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomáhat identifikovat hlavní příčinu chyby ověřování. |

## <a name="3-use-the-token"></a>3. použijte token.
Teď, když jste úspěšně získali přístupový token, můžete token v žádostech na vaše back-endové webové rozhraní API použít, když ho zahrnete do `Authorization` hlavičky:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. aktualizace tokenu
Přístupové tokeny a tokeny ID jsou krátkodobé. Po vypršení platnosti je musíte aktualizovat, abyste mohli dál přistupovat k prostředkům. Provedete to tak, že do koncového bodu odešlete další požadavek POST `/token` . Tentokrát zadejte `refresh_token` místo `code` :

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Povinné? | Description |
| --- | --- | --- |
|tenant| Vyžadováno | Název vašeho tenanta Azure AD B2C|
|politických |Vyžadováno |Tok uživatele, který se použil k získání původního obnovovacího tokenu. V této žádosti nemůžete použít jiný tok uživatele. |
| client_id |Vyžadováno |ID aplikace přiřazené vaší aplikaci v [Azure Portal](https://portal.azure.com). |
| client_secret | Ano, v Web Apps | Tajný klíč aplikace, který byl vygenerován v [Azure Portal](https://portal.azure.com/). Tajné klíče klienta se v tomto toku používají pro scénáře webové aplikace, kde může klient bezpečně uložit tajný klíč klienta. V případě scénářů nativních aplikací (veřejného klienta) nelze tajné klíče klienta bezpečně uložit, a proto nejsou použity v tomto volání. Pokud použijete tajný klíč klienta, pravidelně ho prosím změňte. |
| grant_type |Vyžadováno |Typ udělení. Pro tuto nožku toku autorizačního kódu musí být typ udělení `refresh_token` . |
| scope |Doporučeno |Mezerou oddělený seznam oborů. Jedna hodnota oboru indikuje pro Azure AD obě oprávnění, která jsou požadována. Použití ID klienta jako oboru znamená, že vaše aplikace potřebuje přístupový token, který se dá použít pro vlastní službu nebo webové rozhraní API reprezentované stejným ID klienta.  `offline_access`Rozsah označuje, že vaše aplikace bude potřebovat aktualizační token pro dlouhodobě přístup k prostředkům.  Obor můžete použít také `openid` k vyžádání tokenu ID od Azure AD B2C. |
| redirect_uri |Volitelné |Identifikátor URI přesměrování aplikace, kde jste dostali autorizační kód. |
| refresh_token |Vyžadováno |Původní obnovovací token, který jste získali v druhé nožkě toku. |

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
| --- | --- |
| not_before |Čas, kdy se token považuje za platný, v epocha čase. |
| token_type |Hodnota typu tokenu. Jediným typem, který podporuje Azure AD, je nosič. |
| access_token |Podepsaný token JWT, který jste požádali. |
| scope |Obory, pro které je token platný. Můžete také použít obory k ukládání tokenů do mezipaměti pro pozdější použití. |
| expires_in |Doba, po kterou je token platný (v sekundách). |
| refresh_token |Obnovovací token OAuth 2,0. Aplikace může tento token použít k získání dalších tokenů po vypršení platnosti tokenu. Aktualizační tokeny jsou dlouhodobé a dají se použít k uchování přístupu k prostředkům po delší dobu. Další informace najdete v referenčních informacích k [tokenu Azure AD B2C](tokens-overview.md). |

Chybové odpovědi vypadají takto:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde. Řetězec můžete také použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomáhat identifikovat hlavní příčinu chyby ověřování. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Použití vlastního adresáře Azure AD B2C
Pokud si chcete tyto požadavky vyzkoušet sami, proveďte následující kroky. Nahraďte ukázkové hodnoty, které jsme použili v tomto článku, s vašimi vlastními hodnotami.

1. [Vytvořte adresář Azure AD B2C](tutorial-create-tenant.md). V žádostech použijte název vašeho adresáře.
2. [Vytvořte aplikaci](tutorial-register-applications.md) pro získání ID aplikace a identifikátoru URI přesměrování. Zahrňte do aplikace nativního klienta.
3. [Vytvořte uživatelské toky](user-flow-overview.md) , abyste získali své názvy uživatelských toků.
