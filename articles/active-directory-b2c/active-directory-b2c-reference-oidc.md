---
title: Webové přihlášení s OpenID Connect v Azure Active Directory B2C | Dokumentace Microsoftu
description: Vytváření webových aplikací pomocí Azure Active Directory implementaci ověřovacího protokolu OpenID Connect.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4ad7a6fb032c805072fd9608fb8058a70aa12914
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441827"
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C: Webové přihlášení s OpenID Connect
OpenID Connect je ověřovací protokol, postavený na OAuth 2.0, který umožňuje bezpečně přihlásit uživatele k webovým aplikacím. Pomocí Azure Active Directory B2C (Azure AD B2C) implementaci OpenID Connect, externí pomocí registrace, přihlašování a prostředí pro další správu identit ve vašich webových aplikacích do služby Azure Active Directory (Azure AD). Tato příručka ukazuje, jak postupovat způsobem nezávislým na jazyku. Popisuje jak odesílat a přijímat zprávy HTTP bez použití našich knihoven open source.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) rozšiřuje OAuth 2.0 *autorizace* protokol pro použití jako *ověřování* protokolu. To umožňuje provádět jednotné přihlašování pomocí OAuth. Zavádí koncepci *ID token*, což je token zabezpečení, která umožňuje klientům ověřovat identitu uživatele a získat základní profilové informace o uživateli.

Vzhledem k tomu, že rozšiřuje OAuth 2.0, také umožňuje aplikacím bezpečně získat *přístupové tokeny*. Můžete použít pro přístup k prostředkům zabezpečeným službou access_tokens [autorizační server](active-directory-b2c-reference-protocols.md#the-basics). Doporučujeme OpenID Connect, pokud vytváříte webovou aplikaci, která je hostována na serveru a přístupných prostřednictvím prohlížeče. Pokud chcete přidat správy identit do mobilních a desktopových aplikací pomocí Azure AD B2C, měli byste použít [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) místo OpenID Connect.

Azure AD B2C rozšiřuje standardní protokol OpenID Connect lepší než jednoduché ověřování a autorizace. Zavádí [parametr zásad](active-directory-b2c-reference-policies.md), což vám umožní přidat uživatelské prostředí – například pomocí OpenID Connect registrace, přihlašování a správy profilů – do vaší aplikace. Tady jsme ukazují, jak každá z těchto možností implementace ve vašich webových aplikacích pomocí OpenID Connect a zásady. Vám také ukážeme, jak získat přístupové tokeny pro přístup k webovým rozhraním API.

Příklad HTTP požadavky v další části využívat naše ukázka adresář B2C, fabrikamb2c.onmicrosoft.com, jakož i naší ukázkovou aplikací https://aadb2cplayground.azurewebsites.neta zásady. Můžete zdarma vyzkoušet požadavky sami a použít tyto hodnoty, nebo je můžete nahradit vlastními.
Zjistěte, jak [získání tenanta B2C, aplikace a zásady](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Odesílat požadavky na ověřování
Když webové aplikace potřebuje ověřit uživatele a spuštění zásady, můžete nasměrovat uživatele `/authorize` koncového bodu. Toto je interaktivní spotřebovanou část toku, kdy uživatel provede akci, v závislosti na zásady.

V této žádosti klienta určuje oprávnění, která je potřeba získat od uživatele v `scope` parametr a zásady pro spouštění v `p` parametru. Tři příklady jsou k dispozici v následujících částech (pomocí konců řádků pro lepší čitelnost), každý pomocí jiné zásady. Chcete-li získat představu pro každý požadavek jak funguje, zkuste žádost vložením do prohlížeče a jeho spuštění.

#### <a name="use-a-sign-in-policy"></a>Použití zásad přihlášení
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Použijte zásady registrace
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Pomocí zásady úprav profilu
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| client_id |Požaduje se |ID aplikace, které [webu Azure portal](https://portal.azure.com/) přiřazené vaší aplikaci. |
| response_type |Požaduje se |Typ odpovědi musí obsahovat ID token pro OpenID Connect. Pokud vaše webová aplikace také musí tokeny pro volání webového rozhraní API, můžete použít `code+id_token`, jak jsme provedli tady. |
| redirect_uri |Doporučené |`redirect_uri` Parametr vaší aplikace, kde můžete odesílat a přijímat aplikací pro žádosti o ověření. Musí odpovídat přesně jeden z `redirect_uri` parametry, které jste zaregistrovali na portálu, s tím rozdílem, že ho musí mít kódování URL. |
| scope |Požaduje se |Seznam oborů oddělených mezerami. Hodnota jeden obor značí do služby Azure AD i oprávnění, které jsou požadovány. `openid` Rozsah Určuje oprávnění pro uživatele a získat data o uživateli ve formě tokeny typu ID (více přijít na to později v tomto článku). `offline_access` Obor je nepovinné pro webové aplikace. Znamená to, že vaše aplikace bude potřebovat *obnovovací token* pro dlouhodobé přístup k prostředkům. |
| response_mode |Doporučené |Metoda, která se má použít k odeslání výsledný autorizační kód zpět do aplikace. Může být buď `query`, `form_post`, nebo `fragment`.  `form_post` Režim odpovědi se doporučuje pro zvýšení zabezpečení. |
| state |Doporučené |Hodnota v požadavku, který je také vrácen v odpovědi tokenu. Může být řetězec s žádný obsah, který chcete. Náhodně generované jedinečná hodnota se obvykle používá pro prevenci útoků proti padělání žádosti více webů. Stav se také používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, jako jsou stránky, které byly na. |
| Hodnota Nonce |Požaduje se |Hodnota zahrnutý v požadavku (generovaný aplikací), který bude obsahovat výsledný token ID jako deklarace identity. Aplikace pak můžete ověřit tuto hodnotu a zmírnění útoků opětovného přehrání tokenu. Hodnota je obvykle náhodnou jedinečný řetězec, který slouží k určení původu žádosti. |
| p |Požaduje se |Zásady, které se spustí. Je název zásady, které se vytvoří ve vašem tenantovi B2C. Hodnota názvu zásad by měl začínat `b2c\_1\_`. Další informace o zásadách a [rozhraní rozšiřitelných zásad](active-directory-b2c-reference-policies.md). |
| řádek |Nepovinné |Typ interakce s uživatelem, který je požadován. Jediná platná hodnota v tuto chvíli je `login`, které donutí uživatele k zadání přihlašovacích údajů tohoto požadavku. Jednotné přihlašování se projeví. |

V tomto okamžiku se uživateli výzva k dokončení pracovního postupu zásady. To může zahrnovat uživatele při zadávání uživatelského jména a hesla, přihlašování přes sociální identity, k adresáři nebo jakékoli jiné číslo kroky v závislosti na tom, jak je definované zásady.

Když uživatel vykoná zásadu, Azure AD vrátí odpověď na vaši aplikaci na označený `redirect_uri` parametr pomocí metody ve stanoveném `response_mode` parametru. Odpověď je stejný pro všechny předchozí případy, nezávisle na zásadu, která se spustí.

Úspěšné odpovědi pomocí `response_mode=fragment` vypadat nějak takto:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --- | --- |
| id_token |ID tokenu požadovanou aplikaci. ID token můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. Další podrobnosti o tokeny typu ID a jejich obsah jsou součástí [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| kód |Autorizační kód, který požadované aplikace, pokud jste použili `response_type=code+id_token`. Aplikace může používat autorizační kód k vyžádání tokenu pro cílový prostředek. Autorizační kódy jsou velmi krátkodobé. Obvykle platnost souhlasu vyprší po přibližně 10 minut. |
| state |Pokud `state` parametr je zahrnutý v požadavku, stejnou hodnotu by se měla zobrazit v odpovědi. Aplikace by měla ověřte, že `state` hodnoty v požadavku a odpovědi jsou identické. |

Chybové odpovědi je také možné odeslat `redirect_uri` parametru tak, aby aplikace můžete odpovídajícím způsobem zpracovat:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --- | --- |
| error |Kód chyby: řetězec, který slouží ke klasifikaci typy chyb, které nastat a, který umožňuje reagovat na chyby. |
| error_description |Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování. |
| state |Viz úplný popis v první tabulce v této části. Pokud `state` parametr je zahrnutý v požadavku, stejnou hodnotu by se měla zobrazit v odpovědi. Aplikace by měla ověřte, že `state` hodnoty v požadavku a odpovědi jsou identické. |

## <a name="validate-the-id-token"></a>Ověřit ID token
Pouhého získání tokenu ID není dostatečná k ověření uživatele. Je nutné ověřit ID token podpisu a ověřte, deklarace identity v tokenu podle požadavků vaší aplikace. Azure AD B2C používá [webové tokeny JSON (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografii využívající veřejného klíče pro podepisování tokenů a ověřte, že jsou platné.

Existuje mnoho open source knihoven, které jsou k dispozici pro ověřování tokeny Jwt, v závislosti na jazyku předvoleb. Doporučujeme, abyste namísto implementace logiky ověření zkoumání těchto možností. Zde uvedené informace budou užitečné při tím, jak správně používat tyto knihovny.

Azure AD B2C má OpenID Connect koncový bod metadat, což umožňuje aplikacím načítat informace o Azure AD B2C v době běhu. Tyto informace zahrnují koncových bodů, obsah tokenu a tokenu podpisových klíčů. Existuje dokument metadat JSON pro jednotlivé zásady ve vašem tenantovi B2C. Například dokument metadat pro `b2c_1_sign_in` zásad v `fabrikamb2c.onmicrosoft.com` se nachází na:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jedna z vlastností dokumentu tato konfigurace je `jwks_uri`, jehož hodnota bude stejná zásada:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Chcete-li zjistit, jaké zásady se používané při podepisování ID tokenu (a ze které se mají načíst metadata), máte dvě možnosti. Nejprve je součástí názvu zásady `acr` deklarací identity v tokenu ID. Informace o tom, jak analyzovat deklarací z tokenu ID najdete v tématu [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). Další možností je určený ke kódování zásady v hodnotě `state` parametr vydávat žádosti, a pak ji k určení, jaké zásady se použil dekódovat. Některé z metod je platný.

Po dokumentu metadat z koncových bodů metadat OpenID Connect, které jste získali, veřejné klíče RSA 256 (která se nachází v tomto koncovém bodu) můžete použít k ověření podpisu tokenu ID. Může být více klíčů uvedené v tomto koncovém bodu v libovolném časovém okamžiku v čase, nichž každá je označena `kid` deklarací identity. Také obsahuje záhlaví ID token `kid` deklarace identity, která označuje, který tyto klíče se použil k podepsání ID token. Další informace najdete v tématu [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md) (část o [ověřování tokenů](active-directory-b2c-reference-tokens.md#token-validation), zejména).
<!--TODO: Improve the information on this-->

Po jste ověření podpisu tokenu ID, existuje několik deklarací identity, které je třeba ověřit. Příklad:

* Měli byste ověřit, `nonce` deklaraci identity pro zabránění útokům opětovného přehrání tokenu. Jeho hodnota musí být zadaná v žádosti o přihlášení.
* Měli byste ověřit, `aud` deklaraci identity pro zajištění, že byl vydán ID token pro vaši aplikaci. Jeho hodnota by měla být ID aplikace pro vaši aplikaci.
* Měli byste ověřit, `iat` a `exp` tvrdí, ujistěte se, že nevypršela platnost tokenu ID.

Existuje také několik další ověření, které byste měli provést. Tyto možnosti jsou popsány podrobně [OpenID Connect základní specifikace](http://openid.net/specs/openid-connect-core-1_0.html).  Můžete také ověřit další deklarace identity, v závislosti na vašem scénáři. Některé běžné ověření patří:

* Zajištění, že uživatel a organizace zaregistrovala pro aplikaci.
* Zajištění, že uživatel má správnou autorizaci oprávnění.
* Zajištění, že sílu ověřování došlo, jako je Azure Multi-Factor Authentication.

Další informace o deklarace identity v tokenu ID najdete v článku [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md).

Po ověření tokenu ID můžete začít relaci s uživatelem. Deklarace identity v tokenu ID můžete použít k získání informací o uživateli ve vaší aplikaci. Používá tyto informace zahrnují zobrazení, záznamů a autorizace.

## <a name="get-a-token"></a>Získání tokenu
Pokud potřebujete webové aplikace spustí, jenom zásady, můžete přeskočit několik částí. Tyto části platí pouze pro webové aplikace, které je potřeba provést ověřená volání do webového rozhraní API a jsou také chráněné službou Azure AD B2C.

Uplatníte autorizační kód, který jste získali (s použitím `response_type=code+id_token`) o token u požadovaného prostředku odesláním `POST` požádat o `/token` koncového bodu. V současné době je jediný zdroj, který můžete požádat o token pro vaši vlastní back endové webové aplikace API. Konvence pro požadování tokenu sami sobě je použít ID klienta aplikace jako obor:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| p |Požaduje se |Zásada, která byla použita k získání autorizačního kódu. V této žádosti nelze použít jiné zásady. Všimněte si, že přidáte tento parametr řetězce dotazu, nikoli k `POST` textu. |
| client_id |Požaduje se |ID aplikace, které [webu Azure portal](https://portal.azure.com/) přiřazené vaší aplikaci. |
| Parametr grant_type |Požaduje se |Typ udělení, který musí být `authorization_code` pro tok autorizačního kódu. |
| scope |Doporučené |Seznam oborů oddělených mezerami. Hodnota jeden obor značí do služby Azure AD i oprávnění, které jsou požadovány. `openid` Rozsah Určuje oprávnění pro uživatele a získat data o uživateli ve formě id_token parametry. Slouží k získání tokenů pro vaše aplikace vlastní back endové webové rozhraní API, která je reprezentována stejným ID aplikace jako klient. `offline_access` Obor Určuje, že vaše aplikace bude potřebovat obnovovací token pro dlouhodobé přístup k prostředkům. |
| kód |Požaduje se |Autorizační kód, který jste získali v první fáze toku. |
| redirect_uri |Požaduje se |`redirect_uri` Parametrů aplikace, kterou jste dostali autorizační kód. |
| Hodnota client_secret |Požaduje se |Tajný klíč aplikace, kterou jste vygenerovali v [webu Azure portal](https://portal.azure.com/). Tento tajný kód aplikace je důležitým bezpečnostním artefakt. Měli byste uložit je bezpečně na vašem serveru. By měl také otočit tento tajný kód klienta a v pravidelných intervalech. |

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
| token_type |Hodnota, typ tokenu. Jediný typ, který podporuje Azure AD je `Bearer`. |
| access_token |Podepsaný token JWT, kterou jste si vyžádali. |
| scope |Obory, pro které je token platný. Ty je použít pro ukládání do mezipaměti pro pozdější použití. tokeny. |
| expires_in |Časový úsek, který je přístupový token platný (v sekundách). |
| refresh_token |Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token se po vypršení platnosti tokenu aktuální získat další tokeny. Aktualizace jsou dlouhodobé tokeny a slouží k přístupu k prostředkům uchovávat po dlouhou dobu. Další podrobnosti najdete [odkaz tokenu B2C](active-directory-b2c-reference-tokens.md). Všimněte si, že je třeba pomocí oboru `offline_access` v autorizace a žádostí o token pro získání tokenu obnovení. |

Chybové odpovědi vypadat takto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Kód chyby: řetězec, který slouží ke klasifikaci typy chyb, které nastat a, který umožňuje reagovat na chyby. |
| error_description |Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování. |

## <a name="use-the-token"></a>Použití tokenu
Teď, když jste úspěšně získat přístupový token, můžete token v žádosti k back endu webové rozhraní API zahrnutím v `Authorization` hlavičky:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Aktualizovat token
ID tokeny jsou krátkodobé a jednorázové. Je nutné aktualizovat po vypršení platnosti pokračovat, nebudou moct přistupovat k prostředkům. Můžete tak učinit odesláním jiného `POST` žádat o `/token` koncový bod. Tentokrát, zadejte `refresh_token` parametr namísto `code` parametr:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametr | Požaduje se | Popis |
| --- | --- | --- |
| p |Požaduje se |Zásada, která byla použita k získání původní token obnovení. V této žádosti nelze použít jiné zásady. Všimněte si, že přidáte tento parametr řetězce dotazu, nikoli do textu POST. |
| client_id |Požaduje se |ID aplikace, které [webu Azure portal](https://portal.azure.com/) přiřazené vaší aplikaci. |
| Parametr grant_type |Požaduje se |Typ udělení, který musí být token obnovení pro tuto větev tok autorizačního kódu. |
| scope |Doporučené |Seznam oborů oddělených mezerami. Hodnota jeden obor značí do služby Azure AD i oprávnění, které jsou požadovány. `openid` Rozsah Určuje oprávnění pro uživatele a získat data o uživateli v podobě ID tokenů. Slouží k získání tokenů pro vaše aplikace vlastní back endové webové rozhraní API, která je reprezentována stejným ID aplikace jako klient. `offline_access` Obor Určuje, že vaše aplikace bude potřebovat obnovovací token pro dlouhodobé přístup k prostředkům. |
| redirect_uri |Doporučené |`redirect_uri` Parametrů aplikace, kterou jste dostali autorizační kód. |
| refresh_token |Požaduje se |Původní token obnovení, kterou jste získali v druhé fáze toku. Všimněte si, že je třeba pomocí oboru `offline_access` v autorizace a žádostí o token pro získání tokenu obnovení. |
| Hodnota client_secret |Požaduje se |Tajný klíč aplikace, kterou jste vygenerovali v [webu Azure portal](https://portal.azure.com/). Tento tajný kód aplikace je důležitým bezpečnostním artefakt. Měli byste uložit je bezpečně na vašem serveru. By měl také otočit tento tajný kód klienta a v pravidelných intervalech. |

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
| token_type |Hodnota, typ tokenu. Jediný typ, který podporuje Azure AD je `Bearer`. |
| access_token |Podepsaný token JWT, kterou jste si vyžádali. |
| scope |Rozsah, který je token platný, který slouží k ukládání do mezipaměti pro pozdější použití. tokeny. |
| expires_in |Časový úsek, který je přístupový token platný (v sekundách). |
| refresh_token |Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token se po vypršení platnosti tokenu aktuální získat další tokeny.  Aktualizace jsou dlouhodobé tokeny a slouží k přístupu k prostředkům uchovávat po dlouhou dobu. Další podrobnosti najdete [odkaz tokenu B2C](active-directory-b2c-reference-tokens.md). |

Chybové odpovědi vypadat takto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Kód chyby: řetězec, který slouží ke klasifikaci typy chyb, které nastat a, který umožňuje reagovat na chyby. |
| error_description |Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování. |

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení
Pokud chcete přihlášení uživatele z aplikace, není dostatek Vymazat soubory cookie vaší aplikace nebo jinak end relace s uživatelem. Musíte také přesměruje uživatele na Azure AD a odhlásit se. Pokud k tomu, uživatel může být schopni donutit k vaší aplikaci bez nutnosti zadávat své přihlašovací údaje znovu. Je to proto, že mají platný jednotné přihlašování relaci s Azure AD.

Můžete jednoduše přesměrovat uživatele `end_session` koncový bod, který je uvedené v tomto dokumentu metadat OpenID Connect popsané dříve v "ověřit ID token" části:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| p |Požaduje se |Zásady, které chcete použít pro přihlášení uživatele z vaší aplikace. |
| post_logout_redirect_uri |Doporučené |Adresu URL, kterou uživatel by měl být přesměrován na po úspěšném odhlášení. Pokud neuvedete, Azure AD B2C zobrazí obecná zpráva uživateli. |

> [!NOTE]
> I když směruje uživatele `end_session` koncový bod se vymažou některé jednotné přihlašování – stav uživatele v Azure AD B2C, ho nebude přihlášení uživatele z jejich relace zprostředkovatele (IDP) identity v sociálních sítích. Pokud si uživatel vybere během následné přihlášení stejného zprostředkovatele identity, se bude znovu neověřuje, bez nutnosti zadávat své přihlašovací údaje. Pokud chce uživatel se odhlásit z aplikace B2C, neznamená nutně, že se chcete odhlásit z jejich účtu na Facebooku. Ale v případě místních účtů uživatelské relace bude ukončena správně.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Použití vašeho vlastního tenanta B2C
Pokud chcete vyzkoušet tyto požadavky, musíte nejprve provést tyto tři kroky a potom nahraďte vzorové hodnoty uvedli vlastními:

1. [Vytvoření tenanta B2C](active-directory-b2c-get-started.md)a použijte název vašeho tenanta v požadavcích.
2. [Vytvoření aplikace](active-directory-b2c-app-registration.md) získat identifikátor aplikace. Zahrňte webové aplikace/webové rozhraní API ve vaší aplikaci. Volitelně můžete vytvořte tajný klíč aplikace.
3. [Vytvořte svoje zásady](active-directory-b2c-reference-policies.md) získat názvy vašich zásad.

