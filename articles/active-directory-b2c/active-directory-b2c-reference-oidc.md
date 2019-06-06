---
title: Webové přihlášení s OpenID Connect – Azure Active Directory B2C | Dokumentace Microsoftu
description: Sestavení webové aplikace pomocí ověřovacího protokolu OpenID Connect v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 85639e2648131f9475ad2ae77f31d43e64bf82e7
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509205"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webové přihlášení s OpenID Connect v Azure Active Directory B2C

OpenID Connect je ověřovací protokol, postavený na OAuth 2.0, který umožňuje bezpečně přihlásit uživatele k webovým aplikacím. Pomocí Azure Active Directory B2C (Azure AD B2C) implementaci OpenID Connect, externí pomocí registrace, přihlašování a prostředí pro další správu identit ve vašich webových aplikacích do služby Azure Active Directory (Azure AD). Tato příručka ukazuje, jak postupovat způsobem nezávislým na jazyku. Popisuje jak odesílat a přijímat zprávy HTTP bez použití našich knihoven open source.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozšiřuje OAuth 2.0 *autorizace* protokol pro použití jako *ověřování* protokolu. Tento protokol ověřování umožňuje provádět jednotné přihlašování. Zavádí koncepci *ID token*, která umožňuje klientům ověřovat identitu uživatele a získat základní profilové informace o uživateli.

Vzhledem k tomu, že rozšiřuje OAuth 2.0, také umožňuje aplikacím bezpečně získat *přístupové tokeny*. Přístupové tokeny můžete použít pro přístup k prostředkům, které jsou zabezpečené pomocí [autorizační server](active-directory-b2c-reference-protocols.md). OpenID Connect se doporučuje Pokud vytváříte webovou aplikaci, která má hostovaný na serveru a přístupných prostřednictvím prohlížeče. Pokud chcete přidat Správa identit pro mobilní nebo desktopové aplikace pomocí služby Azure AD B2C, měli byste použít [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) místo OpenID Connect. Další informace o tokeny, najdete v článku [přehled tokenů v Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

Azure AD B2C rozšiřuje standardní protokol OpenID Connect lepší než jednoduché ověřování a autorizace. Zavádí [parametr toku uživatele](active-directory-b2c-reference-policies.md), což vám umožní přidávat uživatele pomocí OpenID Connect prostředí pro vaši aplikaci, jako například registrace, přihlášení a Správa profilů.

## <a name="send-authentication-requests"></a>Odesílat požadavky na ověřování

Pokud vaše webová aplikace potřebuje ověřit uživatele a spustit tok uživatele, můžete nasměrovat uživatele `/authorize` koncového bodu. Uživatel provede akci v závislosti na toku uživatele.

V této žádosti klienta určuje oprávnění, která je potřeba získat od uživatele v `scope` parametr a tok uživatele ke spuštění v `p` parametru. Tři příklady jsou k dispozici v následujících částech (pomocí konců řádků pro lepší čitelnost), každý pomocí tok jiný uživatel. Chcete-li získat představu pro každý požadavek jak funguje, zkuste žádost vložením do prohlížeče a jeho spuštění. Můžete nahradit `fabrikamb2c` s názvem vašeho tenanta, pokud máte jeden a vytvořili tok uživatele.

#### <a name="use-a-sign-in-user-flow"></a>Použijte tok přihlášení uživatele
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>Použijte tok registrace uživatele
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>Použít tok, který upravit profil uživatele
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametr | Požaduje se | Popis |
| --------- | -------- | ----------- |
| client_id | Ano | ID aplikace, které [webu Azure portal](https://portal.azure.com/) přiřazené pro vaši aplikaci. |
| response_type | Ano | Musí obsahovat ID token pro OpenID Connect. Pokud vaše webová aplikace také potřebuje tokeny pro volání rozhraní web API, můžete použít `code+id_token`. |
| redirect_uri | Ne | `redirect_uri` Parametr vaší aplikace, kde můžete odesílat a přijímat aplikace žádosti o ověření. Musí odpovídat přesně jeden z `redirect_uri` parametry, které jste zaregistrovali na webu Azure Portal, s tím rozdílem, že ho musí mít kódování URL. |
| scope | Ano | Seznam oborů oddělených mezerami. `openid` Rozsah Určuje oprávnění pro uživatele a získat data o uživateli v podobě ID tokenů. `offline_access` Obor je nepovinné pro webové aplikace. Znamená to, že aplikace bude vyžadovat *obnovovací token* pro rozšířené přístup k prostředkům. |
| response_mode | Ne | Metoda, která se používá k odesílání výsledný autorizační kód zpět do aplikace. Může být buď `query`, `form_post`, nebo `fragment`.  `form_post` Režim odpovědi se doporučuje pro zvýšení zabezpečení. |
| stav | Ne | Hodnota v požadavku, který je také vrácen v odpovědi tokenu. Může být řetězec s žádný obsah, který chcete. Náhodně generované jedinečná hodnota se obvykle používá pro prevenci útoků proti padělání žádosti více webů. Stav se také používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, jako jsou stránky, které byly na. |
| nonce | Ano | Hodnota zahrnutý v požadavku (generovaný aplikací), který je součástí výsledný token ID jako deklarace identity. Aplikace pak můžete ověřit tuto hodnotu a zmírnění útoků opětovného přehrání tokenu. Hodnota je obvykle náhodnou jedinečný řetězec, který slouží k určení původu žádosti. |
| p | Ano | Tok uživatele, který se spouští. Jde o název toku uživatele, který je vytvořen ve vašem tenantovi Azure AD B2C. Název toku uživatele by měl začínat `b2c\_1\_`. |
| řádek | Ne | Typ interakce s uživatelem, který je potřeba. Jediná platná hodnota v tuto chvíli je `login`, které donutí uživatele k zadání přihlašovacích údajů tohoto požadavku. |

V tomto okamžiku je uživatel vyzván k dokončení pracovního postupu. Uživatel může mít pro zadání uživatelského jména a hesla, přihlaste se pomocí identity v sociálních sítích nebo přihlašování pro adresář. Může existovat libovolný počet kroků v závislosti na tom, jak je definované tok uživatele.

Poté, co uživatel vykoná tok uživatele, odpověď se vrátí do vaší aplikace v označený `redirect_uri` parametr pomocí metody ve stanoveném `response_mode` parametru. Odpověď je stejný pro všechny předchozí případy, nezávisle na tok uživatele.

Úspěšné odpovědi pomocí `response_mode=fragment` vypadat nějak takto:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --------- | ----------- |
| id_token | Token ID, která aplikace požaduje. ID token můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. |
| code | Autorizační kód, který aplikace požaduje, pokud jste použili `response_type=code+id_token`. Aplikace můžete požádat o přístupový token pro cílový prostředek autorizační kód. Autorizační kódy se obvykle vyprší po přibližně 10 minut. |
| stav | Pokud `state` parametr je zahrnutý v požadavku, stejnou hodnotu by se měla zobrazit v odpovědi. Aplikace by měla ověřte, že `state` hodnoty v požadavku a odpovědi jsou identické. |

Chybové odpovědi je také možné odeslat `redirect_uri` parametru tak, aby aplikace můžete odpovídajícím způsobem zpracovat:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --------- | ----------- |
| error | Kód, který slouží ke klasifikaci typy chyb, ke kterým dochází. |
| error_description | Určité chybové zprávě, který vám pomůže identifikovat hlavní příčinu chyby ověřování. |
| stav | Pokud `state` parametr je zahrnutý v požadavku, stejnou hodnotu by se měla zobrazit v odpovědi. Aplikace by měla ověřte, že `state` hodnoty v požadavku a odpovědi jsou identické. |

## <a name="validate-the-id-token"></a>Ověřit ID token

Pouhého získání tokenu ID není dostatečná k ověření uživatele. Ověření podpisu tokenu ID a ověřte, deklarace identity v tokenu podle požadavků vaší aplikace. Azure AD B2C používá [webové tokeny JSON (Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografii využívající veřejného klíče pro podepisování tokenů a ověřte, že jsou platné. Existuje mnoho open source knihoven, které jsou k dispozici pro ověřování tokeny Jwt, v závislosti na jazyku předvoleb. Doporučujeme, abyste namísto implementace logiky ověření zkoumání těchto možností. 

Azure AD B2C má OpenID Connect metadat koncový bod, který umožňuje aplikaci získat informace o Azure AD B2C v době běhu. Tyto informace zahrnují koncových bodů, obsah tokenu a tokenu podpisových klíčů. Existuje dokument metadat JSON pro každý tok uživatele ve vašem tenantovi B2C. Například dokument metadat pro `b2c_1_sign_in` tok uživatele v `fabrikamb2c.onmicrosoft.com` se nachází na:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jedna z vlastností dokumentu tato konfigurace je `jwks_uri`, jehož hodnota bude stejný tok uživatele:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Chcete-li zjistit, které tok uživatele byl použit v ID podpisu tokenu (a ze které se mají získat metadata), máte dvě možnosti. Nejprve je součástí názvu toku uživatele `acr` deklarací identity v tokenu ID. Další možností je určený ke kódování tok uživatele v hodnotě `state` parametr vydávat žádosti, a pak ji k určení, které tok uživatele. byl použit dekódovat. Některé z metod je platný.

Po dokumentu metadat z koncových bodů metadat OpenID Connect, které jste získali, můžete se ověřit podpis tokenu ID veřejné klíče RSA 256. Může být více klíčů uvedené v tomto koncovém bodu, nichž každá je označena `kid` deklarací identity. Také obsahuje záhlaví ID token `kid` deklarace identity, která označuje, který tyto klíče se použil k podepsání ID token.

Po jste ověření podpisu tokenu ID, existuje několik deklarací identity, které je třeba ověřit. Příklad:

- Ověření `nonce` deklaraci identity pro zabránění útokům opětovného přehrání tokenu. Jeho hodnota musí být zadaná v žádosti o přihlášení.
- Ověření `aud` deklaraci identity pro zajištění, že byl vydán ID token pro vaši aplikaci. ID aplikace pro vaše aplikace by mít hodnotu.
- Ověření `iat` a `exp` deklarací identity, abyste měli jistotu, že nevypršela platnost tokenu ID.

Existuje také několik další ověření, které byste měli provést. Ověření jsou popsány podrobně [OpenID Connect základní specifikace](https://openid.net/specs/openid-connect-core-1_0.html). Můžete také ověřit další deklarace identity, v závislosti na vašem scénáři. Některé běžné ověření patří:

- Zajištění, že uživatel a organizace zaregistrovala pro aplikaci.
- Zajištění, že uživatel má správnou autorizaci oprávnění.
- Zajištění, že sílu ověřování došlo, jako je Azure Multi-Factor Authentication.

Po ověření tokenu ID, můžete začít relaci s uživatelem. Deklarace identity v tokenu ID můžete použít k získání informací o uživateli ve vaší aplikaci. Používá tyto informace zahrnují zobrazení, záznamů a autorizace.

## <a name="get-a-token"></a>Získání tokenu

Pokud potřebujete spustit jenom toky uživatelů pro webové aplikace, můžete přeskočit několik částí. Tyto části platí pouze pro webové aplikace, které je potřeba provést ověřená volání do webového rozhraní API a jsou také chráněné službou Azure AD B2C.

Uplatníte autorizační kód, který jste získali (s použitím `response_type=code+id_token`) o token u požadovaného prostředku odesláním `POST` požádat o `/token` koncového bodu. V současné době je jediný zdroj, který můžete požádat o token pro vaše aplikace vlastní back endové webové rozhraní API. Konvence pro požadování tokenu sami sobě je použít ID klienta aplikace jako obor:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametr | Požaduje se | Popis |
| --------- | -------- | ----------- |
| p | Ano | Tok uživatele, který byl použitý k získání autorizačního kódu. V této žádosti nelze použít tok jiný uživatel. Přidejte tento parametr řetězce dotazu, nikoli na text příspěvku. |
| client_id | Ano | ID aplikace, které [webu Azure portal](https://portal.azure.com/) přiřazené pro vaši aplikaci. |
| grant_type | Ano | Typ udělení, který musí být `authorization_code` pro tok autorizačního kódu. |
| scope | Ne | Seznam oborů oddělených mezerami. `openid` Rozsah Určuje oprávnění pro uživatele a získat data o uživateli ve formě id_token parametry. Slouží k získání tokenů pro vaše aplikace vlastní back endové webové rozhraní API, která je reprezentována stejným ID aplikace jako klient. `offline_access` Obor Určuje, že vaše aplikace potřebuje obnovovací token pro rozšířené přístup k prostředkům. |
| code | Ano | Autorizační kód, který jste získali na začátku tok uživatele. |
| redirect_uri | Ano | `redirect_uri` Parametrů aplikace, kterou jste dostali autorizační kód. |
| client_secret | Ano | Tajný klíč aplikace, který byl vygenerován v [webu Azure portal](https://portal.azure.com/). Tento tajný kód aplikace je důležitým bezpečnostním artefakt. Měli byste uložit je bezpečně na vašem serveru. Změňte tento tajný kód klienta a v pravidelných intervalech. |

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
| --------- | ----------- |
| not_before | Čas, kdy token, který je považován za platný v unixovém čase. |
| token_type | Hodnota, typ tokenu. `Bearer` je jediným typem, který je podporovaný. |
| access_token | Podepsaný token JWT, kterou jste si vyžádali. |
| scope | Obory, pro které je token platný. |
| expires_in | Časový úsek, který je přístupový token platný (v sekundách). |
| refresh_token | Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token se po vypršení platnosti tokenu aktuální získat další tokeny. Aktualizovat tokeny je možné zachovat přístup k prostředkům pro dlouhou dobu. Rozsah `offline_access` je třeba použít v autorizace a žádostí o token pro získání tokenu obnovení. |

Chybové odpovědi vypadat takto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --------- | ----------- |
| error | Kód, který slouží ke klasifikaci typy chyb, ke kterým dochází. |
| error_description | Zpráva, která může pomoci identifikovat hlavní příčinu chyby ověřování. |

## <a name="use-the-token"></a>Použití tokenu

Teď, když jste úspěšně získat přístupový token, můžete token v žádosti k back endu webové rozhraní API zahrnutím v `Authorization` hlavičky:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Aktualizovat token

V krátké době vyprší platnost těchto tokenů ID. Aktualizujte tokeny po vypršení platnosti pokračovat, nebudou moct přistupovat k prostředkům. Můžete aktualizovat token odesláním jiného `POST` požádat o `/token` koncového bodu. Tentokrát, zadejte `refresh_token` parametr namísto `code` parametr:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametr | Požaduje se | Popis |
| --------- | -------- | ----------- |
| p | Ano | Tok uživatele, který byl použitý k získání původní token obnovení. V této žádosti nelze použít tok jiný uživatel. Přidejte tento parametr řetězce dotazu, nikoli na text příspěvku. |
| client_id | Ano | ID aplikace, které [webu Azure portal](https://portal.azure.com/) přiřazené pro vaši aplikaci. |
| grant_type | Ano | Typ udělení, který musí být token obnovení pro tuto část tok autorizačního kódu. |
| scope | Ne | Seznam oborů oddělených mezerami. `openid` Rozsah Určuje oprávnění pro uživatele a získat data o uživateli v podobě ID tokenů. Slouží k odesílání tokeny do vaší aplikace vlastní back endové webové rozhraní API, která je reprezentována stejným ID aplikace jako klient. `offline_access` Obor Určuje, že vaše aplikace potřebuje obnovovací token pro rozšířené přístup k prostředkům. |
| redirect_uri | Ne | `redirect_uri` Parametrů aplikace, kterou jste dostali autorizační kód. |
| refresh_token | Ano | Původní obnovovací token, který se získal v druhé části toku. `offline_access` Oboru je potřeba použít ve autorizaci a žádostí o token pro získání tokenu obnovení. |
| client_secret | Ano | Tajný klíč aplikace, který byl vygenerován v [webu Azure portal](https://portal.azure.com/). Tento tajný kód aplikace je důležitým bezpečnostním artefakt. Měli byste uložit je bezpečně na vašem serveru. Změňte tento tajný kód klienta a v pravidelných intervalech. |

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
| --------- | ----------- |
| not_before | Čas, kdy token, který je považován za platný v unixovém čase. |
| token_type | Hodnota, typ tokenu. `Bearer` je jediným typem, který je podporovaný. |
| access_token | Podepsaný token JWT, který byl vyžádán. |
| scope | Obor, u kterého je token platný. |
| expires_in | Časový úsek, který je přístupový token platný (v sekundách). |
| refresh_token | Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token se po vypršení platnosti tokenu aktuální získat další tokeny. Aktualizovat tokeny je možné zachovat přístup k prostředkům pro dlouhou dobu. |

Chybové odpovědi vypadat takto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --------- | ----------- |
| error | Kód, který slouží ke klasifikaci typy chyb, ke kterým dochází. |
| error_description | Zpráva, která může pomoci identifikovat hlavní příčinu chyby ověřování. |

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

Pokud chcete přihlášení uživatele z aplikace, není dost informací k vyčištění souborů cookie aplikace nebo jinak ukončit relaci s uživatelem. Přesměruje uživatele na Azure AD B2C se odhlásit. Pokud k tomu, uživatel může být schopni donutit k vaší aplikaci bez nutnosti zadávat své přihlašovací údaje znovu.

Můžete jednoduše přesměrovat uživatele `end_session` koncový bod, který je uveden v OpenID Connect dokument metadat bylo popsáno dříve:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Požaduje se | Popis |
| --------- | -------- | ----------- |
| p | Ano | Tok uživatele, který chcete použít pro přihlášení uživatele z vaší aplikace. |
| post_logout_redirect_uri | Ne | Adresa URL, která uživatel by měl být přesměrován na po úspěšném odhlášení. Pokud není součástí, Azure AD B2C zobrazí obecná zpráva uživatele. |

Směruje uživatele `end_session` koncový bod vymaže část jednotné přihlašování – stav uživatele v Azure AD B2C, ale odhlášeni odhlásit jejich sociálních identit zprostředkovatele (IDP) relace uživatele. Pokud si uživatel vybere stejného zprostředkovatele identity při následné přihlašování, že jsou znovu neověřuje, bez nutnosti zadávat své přihlašovací údaje. Pokud chce uživatel se odhlásit z aplikace, ji neznamená nutně, že se chcete odhlásit z jejich účtu na Facebooku. Nicméně pokud místní účty se používají, uživatelské relace se správně skončí.

