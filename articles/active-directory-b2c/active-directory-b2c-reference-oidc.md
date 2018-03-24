---
title: Webové přihlášení s OpenID Connect - Azure AD B2C | Microsoft Docs
description: Vytváření webových aplikací pomocí služby Azure Active Directory implementace ověřovacího protokolu OpenID Connect
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
ms.openlocfilehash: e787ea36ab5099705f151504385dd5dc97029e37
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C: Přihlášení Web s OpenID Connect
OpenID Connect je ověřovací protokol, nástavbou OAuth 2.0, který slouží k bezpečně přihlášení uživatele k webovým aplikacím. Pomocí Azure Active Directory B2C implementaci OpenID Connect (Azure AD B2C), můžete externí registrace, přihlášení a prostředí pro další správu identit ve vaší webové aplikace do Azure Active Directory (Azure AD). Tento průvodce vám ukáže, jak postupovat způsobem nezávislé na jazyku. Popisuje, jak odesílat a přijímat zprávy HTTP bez použití některé z našich knihovny open-source.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) rozšiřuje OAuth 2.0 *autorizace* protokol pro použití jako *ověřování* protokolu. To umožňuje provádět jednotné přihlašování pomocí OAuth. Zavádí koncepci *ID token*, což je token zabezpečení, která umožňuje klientovi k ověření identity uživatele a získat základní profil informace o uživateli.

Vzhledem k tomu, že ji rozšiřuje OAuth 2.0, taky umožňuje aplikace bezpečně získat *přístup tokeny*. Můžete použít access_tokens pro přístup k prostředkům, které jsou zabezpečeny [serveru ověřování](active-directory-b2c-reference-protocols.md#the-basics). Doporučujeme OpenID Connect, pokud vytváříte webovou aplikaci, která je hostovaná na serveru a přístupných prostřednictvím prohlížeče. Pokud chcete přidat Správa identit pro mobilní nebo desktopové aplikace pomocí Azure AD B2C, měli byste použít [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) místo OpenID Connect.

Azure AD B2C rozšiřuje standardní protokol OpenID Connect více než jednoduché ověřování a autorizace. Zavádí [zásad parametr](active-directory-b2c-reference-policies.md), který můžete použít OpenID Connect přidání uživatelského prostředí – například registrace, přihlašování a správy profilů--do vaší aplikace. Zde jsme ukazují, jak používat OpenID Connect a zásady implementace každý z těchto prostředí na vaše webové aplikace. Jsme budete také ukazují, jak získat přístupové tokeny pro přístup k webové rozhraní API.

Příklad protokolu HTTP žádosti v další části použijte naše ukázka adresáři B2C, fabrikamb2c.onmicrosoft.com, jakož i naše ukázková aplikace https://aadb2cplayground.azurewebsites.neta zásady. Jste volné můžete vyzkoušet na žádosti sami pomocí těchto hodnot, nebo je můžete nahradit vlastními.
Zjistěte, jak [získat klienta B2C, aplikace a zásady](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Odeslání žádosti o ověření
Když webové aplikace potřebuje k ověření uživatele a spustit zásadu, se přímá uživateli `/authorize` koncový bod. Toto je interaktivní část toku, kde uživatel provede akci, v závislosti na zásady.

V této žádosti o, klient naznačuje, oprávnění, která je nutné získat od uživatele v `scope` parametr a zásadu na provedení v `p` parametr. Tři příklady jsou uvedeny v následujících částech (pomocí konců řádků čitelnější), každý využívající jiné zásady. Chcete-li se podívat, jak funguje každý požadavek, zkuste vkládání požadavek do prohlížeče a spustit ho.

#### <a name="use-a-sign-in-policy"></a>Použít zásadu přihlášení
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

#### <a name="use-a-sign-up-policy"></a>Použít zásadu registrace
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

#### <a name="use-an-edit-profile-policy"></a>Pomocí zásady úpravy profilu
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
| client_id |Požaduje se |ID aplikace, která [portál Azure](https://portal.azure.com/) přiřazené vaší aplikaci. |
| response_type |Požaduje se |Typ odpovědi musí obsahovat ID token pro OpenID Connect. Pokud vaše webová aplikace také musí tokeny pro volání webového rozhraní API, můžete použít `code+id_token`, jak jsme jste na jednom místě. |
| redirect_uri |Doporučené |`redirect_uri` Parametr vaší aplikace, kde můžete odesílat a přijímat aplikace odpovědi ověřování. Se musí přesně shodovat s jedním z `redirect_uri` parametry, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódovaná adresou URL. |
| Obor |Požaduje se |Seznam obory oddělených mezerami. Hodnota jeden obor označuje do služby Azure AD obě oprávnění, které jsou požadovány. `openid` Oboru označuje oprávnění přihlásit uživatele a získat data o uživateli ve formě tokeny typu ID (více přijít na to později v článku). `offline_access` Obor je volitelné pro webové aplikace. Označuje, že vaše aplikace bude potřebovat *obnovovací token* pro dlouhodobé přístup k prostředkům. |
| response_mode |Doporučené |Metoda, která se má použít k odeslání výsledné autorizační kód zpět do aplikace. Může být buď `query`, `form_post`, nebo `fragment`.  `form_post` Režim odpovědi se doporučuje pro zvýšení zabezpečení. |
| state |Doporučené |Hodnota součástí požadavek, který je také vrácený v odpovědi tokenu. Může být řetězec o délce veškerý obsah, který chcete. Náhodně generované jedinečné hodnoty se obvykle používá pro prevence útoků padělání požadavku posílaného mezi weby. Stav se také používá ke kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky, které byly na. |
| hodnotu Nonce |Požaduje se |Hodnota součástí požadavek (generované aplikace), který bude součástí výsledný token ID jako deklarace identity. Aplikace pak může ověřit tuto hodnotu zmírnit útoky opětovného přehrání tokenu. Hodnota je obvykle náhodnou jedinečného řetězce, který slouží k určení původu požadavku. |
| p |Požaduje se |Zásady, které budou spuštěny. Je název zásady, který je vytvořen v svého klienta B2C. Hodnota název zásad by měl začínat obráceným `b2c\_1\_`. Další informace o zásadách a [rozšiřitelném rozhraní zásad](active-directory-b2c-reference-policies.md). |
| řádku |Nepovinné |Typ interakci s uživatelem, který je vyžadován. Jedinou platnou hodnotou v tuto chvíli je `login`, který vynutí uživatele k zadání přihlašovacích údajů tohoto požadavku. Jednotné přihlašování se neprojeví. |

V tomto okamžiku uživatel požádán o dokončení pracovního postupu nastavení zásad. To může zahrnovat uživatele zadáním uživatelského jména a hesla, přihlášení pomocí sociálních identitu, přihlašování k adresáři, nebo jakékoli jiné číslo kroků, v závislosti na tom, jak je definované zásady.

Poté, co uživatel vykoná zásadu, Azure AD vrátí odpověď do vaší aplikace na uvedené `redirect_uri` parametr, a to pomocí metody, která je zadána v `response_mode` parametr. Odpověď je stejný pro všechny předchozí případy, nezávisle na zásadu, která se spustí.

Úspěšná odpověď pomocí `response_mode=fragment` bude vypadat:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --- | --- |
| id_token |ID token, který požadované aplikace. ID token můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. Další informace o ID tokeny a jejich obsah jsou součástí [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| kód |Autorizační kód, který požadované aplikace, pokud jste použili `response_type=code+id_token`. Aplikace můžete autorizační kód vyžádat token přístupu pro cílový prostředek. Kódy ověřování jsou velmi krátkodobou. Obvykle se jejich platnost vyprší po přibližně 10 minut. |
| state |Pokud `state` parametr je zahrnuta v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda `state` hodnoty v požadavku a odpovědi jsou identické. |

Chybové odpovědi lze je také odeslat do `redirect_uri` parametr tak, aby aplikace můžete správně zpracovat:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --- | --- |
| error |Kód chyby řetězec, který slouží ke klasifikaci typů chyb, které nastat a který slouží k reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování. |
| state |Viz úplný popis v první tabulce v této části. Pokud `state` parametr je zahrnuta v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda `state` hodnoty v požadavku a odpovědi jsou identické. |

## <a name="validate-the-id-token"></a>Ověřit ID token
Právě přijetí tokenu ID není dostatečně k ověření uživatele. Musíte ověřit ID token podpisu a ověřit deklarace identity v tokenu podle požadavků vaší aplikace. Používá Azure AD B2C [webové tokeny JSON (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografie využívající veřejného klíče pro podepisování tokenů a ověřte, zda je platný.

Existuje mnoho knihovny open-source, které jsou k dispozici pro ověření tokeny Jwt, v závislosti na vaší jazykové předvolby. Doporučujeme, abyste zkoumat těchto možností než implementace vlastní logiky ověřování. Zde uvedené informace budou užitečné v nad tím, jak tyto knihovny odpovídajícím způsobem používat.

Azure AD B2C má OpenID Connect metadata koncový bod, který umožňuje aplikaci načíst informace o Azure AD B2C za běhu. Tyto informace zahrnují koncových bodů, obsah tokenu a token podpisových klíčů. Je dokument metadat JSON pro každé zásady v svého klienta B2C. Například dokument metadat pro `b2c_1_sign_in` zásad v `fabrikamb2c.onmicrosoft.com` se nachází na:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jedna z vlastností tohoto dokumentu konfigurace je `jwks_uri`, jehož hodnota by stejné zásady:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Chcete-li zjistit, jaké zásady byla použita v ID podepisování tokenů (a odkud se načíst metadata), máte dvě možnosti. Nejprve je součástí název zásady `acr` deklarací identity v ID tokenu. Informace o tom, jak analyzovat deklarací z tokenu ID najdete v tématu [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). Vaše další možností je určený ke kódování zásad v hodnotě `state` parametr při vydání požadavku a potom ji k určení, jaké zásady byl použit dekódovat. Buď metoda je platná.

Poté, co jste získali dokument metadat z koncového bodu metadat OpenID Connect, můžete použít veřejné klíče RSA 256 (které jsou umístěné na tento koncový bod) k ověření podpisu tokenu ID. Může být více klíčů uvedený na tento koncový bod v libovolném bodě v čase, každý se identifikovanou pomocí `kid` deklarací identity. Záhlaví ID tokenu také obsahuje `kid` deklarace identity, která určuje, které tyto klíče se použil k podepsání ID token. Další informace najdete v tématu [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md) (části na [ověřování tokenů](active-directory-b2c-reference-tokens.md#token-validation), zejména).
<!--TODO: Improve the information on this-->

Po jste ověření podpisu tokenu ID, existuje několik deklarace identity, které je třeba ověřit. Například:

* Měli byste ověřit, `nonce` deklarace identity, aby se zabránilo útokům opětovného přehrání tokenu. Její hodnota musí být zadaná v žádosti o přihlášení.
* Měli byste ověřit, `aud` deklarace identity k zajištění, že ID token vydán pro vaši aplikaci. Její hodnota musí být ID aplikace vaší aplikace.
* Měli byste ověřit, `iat` a `exp` Ujistěte se, zda nevypršela platnost tokenu ID deklarace identity.

Existují také několik další ověření, které byste měli provést. Tyto možnosti jsou popsány v části [OpenID Connect základní specifikace](http://openid.net/specs/openid-connect-core-1_0.html).  Můžete také ověřit další deklarace identity, v závislosti na vašem scénáři. Některé běžné ověření patří:

* Zajištění, že uživatele či organizace má zápis pro aplikaci.
* Zajištění, že uživatel má správná oprávnění autorizace.
* Zajištění, že došlo k sílu ověřování, jako je například ověřování Azure Multi-Factor Authentication.

Další informace o ID tokenu deklarací identity, najdete v článku [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md).

Po ověření tokenu ID můžete začít relaci s uživatelem. Deklarace identity v tokenu ID vám pomůže získat informace o uživateli ve vaší aplikaci. Používá tyto informace zahrnují zobrazení, záznamy a autorizace.

## <a name="get-a-token"></a>Získání tokenu
Pokud potřebujete vaší webové aplikace na pouze spouštění zásady, můžete přeskočit několik dalších částech. Tyto části platí pouze pro webové aplikace, které je třeba, aby ověření volání webového rozhraní API a také jsou chráněné službou Azure AD B2C.

Lze uplatnit autorizační kód, který jste získali (pomocí `response_type=code+id_token`) pro token pro požadovaný prostředek odesláním `POST` žádost o `/token` koncový bod. V současné době je pouze prostředek, který můžete požádat o token pro vaše aplikace vlastní back endové webové rozhraní API. Konvence pro požadování tokenu sami sobě je použít ID klienta aplikace jako obor:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| p |Požaduje se |Zásada, která byla použita k získání autorizační kód. V této žádosti nelze použít jinou zásadu. Poznámka: Přidat tento parametr do řetězce dotazu, nikoli k `POST` textu. |
| client_id |Požaduje se |ID aplikace, která [portál Azure](https://portal.azure.com/) přiřazené vaší aplikaci. |
| grant_type |Požaduje se |Typ udělení, který musí být `authorization_code` pro tok autorizačního kódu. |
| Obor |Doporučené |Seznam obory oddělených mezerami. Hodnota jeden obor označuje do služby Azure AD obě oprávnění, které jsou požadovány. `openid` Oboru označuje oprávnění přihlásit uživatele a získat data o uživateli ve formě požadavku id_token parametry. Slouží k získání tokenů pro vaše aplikace vlastní back endové webové rozhraní API, která je reprezentována stejným ID aplikace jako klient. `offline_access` Oboru označuje, že vaše aplikace budete potřebovat obnovovací token pro dlouhodobé přístup k prostředkům. |
| kód |Požaduje se |Autorizační kód, který jste získali v prvním větev toku. |
| redirect_uri |Požaduje se |`redirect_uri` Parametr aplikace, kterou jste dostali autorizační kód. |
| client_secret |Požaduje se |Tajný klíč aplikace, který jste vygenerovali v [portál Azure](https://portal.azure.com/). Tento tajný klíč aplikace je důležité zabezpečení artefakt. Měli byste uložit ji bezpečně na vašem serveru. Musí také otočit tento tajný klíč klienta v pravidelných intervalech. |

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
| token_type |Typ tokenu hodnota. Pouze typ, který podporuje Azure AD je `Bearer`. |
| access_token |Podepsaný token JWT, které jste žádali. |
| Obor |Oborů, pro které token je platný. Ty lze použít pro ukládání do mezipaměti tokeny pro pozdější použití. |
| expires_in |Časový interval, který přístupový token je platný (v sekundách). |
| refresh_token |Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token získat další tokeny po vypršení platnosti aktuálního tokenu. Aktualizujte je dlouhodobé tokeny a slouží k přístupu k prostředkům uchovávány dlouhou dobu. Další podrobnosti najdete [odkaz tokenu B2C](active-directory-b2c-reference-tokens.md). Všimněte si, že je třeba pomocí oboru `offline_access` v autorizace a žádosti o tokeny, aby bylo možné přijímat token obnovení. |

Chybové odpovědi vypadat podobně jako:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Kód chyby řetězec, který slouží ke klasifikaci typů chyb, které nastat a který slouží k reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování. |

## <a name="use-the-token"></a>Použití tokenu
Teď, když jste úspěšně získat přístupový token, můžete použít token v požadavcích na back endové webové rozhraní API zahrnutím v `Authorization` hlavičky:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Aktualizovat token
ID tokeny jsou krátkodobou. Je nutné aktualizovat po vypršení jejich platnosti chcete-li pokračovat, nebudete mít přístup k prostředkům. Můžete to provést tak, že zadáte jiný `POST` žádost o `/token` koncový bod. Zadejte této doby `refresh_token` parametr místo `code` parametr:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametr | Požaduje se | Popis |
| --- | --- | --- |
| p |Požaduje se |Zásada, která byla použita k získání původní token obnovení. V této žádosti nelze použít jinou zásadu. Všimněte si, že přidáte tento parametr řetězce dotazu, nikoli při textu POST. |
| client_id |Požaduje se |ID aplikace, která [portál Azure](https://portal.azure.com/) přiřazené vaší aplikaci. |
| grant_type |Požaduje se |Typ udělení, který musí být token obnovení pro tuto větev toku kódu autorizace. |
| Obor |Doporučené |Seznam obory oddělených mezerami. Hodnota jeden obor označuje do služby Azure AD obě oprávnění, které jsou požadovány. `openid` Oboru označuje oprávnění přihlásit uživatele a získat data o uživateli ve formě ID tokeny. Slouží k získání tokenů pro vaše aplikace vlastní back endové webové rozhraní API, která je reprezentována stejným ID aplikace jako klient. `offline_access` Oboru označuje, že vaše aplikace budete potřebovat obnovovací token pro dlouhodobé přístup k prostředkům. |
| redirect_uri |Doporučené |`redirect_uri` Parametr aplikace, kterou jste dostali autorizační kód. |
| refresh_token |Požaduje se |Původní obnovovací token, který jste získali v druhé větev toku. Všimněte si, že je třeba pomocí oboru `offline_access` v autorizace a žádosti o tokeny, aby bylo možné přijímat token obnovení. |
| client_secret |Požaduje se |Tajný klíč aplikace, který jste vygenerovali v [portál Azure](https://portal.azure.com/). Tento tajný klíč aplikace je důležité zabezpečení artefakt. Měli byste uložit ji bezpečně na vašem serveru. Musí také otočit tento tajný klíč klienta v pravidelných intervalech. |

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
| token_type |Typ tokenu hodnota. Pouze typ, který podporuje Azure AD je `Bearer`. |
| access_token |Podepsaný token JWT, které jste žádali. |
| Obor |Oboru, který token je platný, který můžete použít pro ukládání do mezipaměti tokeny pro pozdější použití. |
| expires_in |Časový interval, který přístupový token je platný (v sekundách). |
| refresh_token |Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token získat další tokeny po vypršení platnosti aktuálního tokenu.  Aktualizujte je dlouhodobé tokeny a slouží k přístupu k prostředkům uchovávány dlouhou dobu. Další podrobnosti najdete v části [odkaz tokenu B2C](active-directory-b2c-reference-tokens.md). |

Chybové odpovědi vypadat podobně jako:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Popis |
| --- | --- |
| error |Kód chyby řetězec, který slouží ke klasifikaci typů chyb, které nastat a který slouží k reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování. |

## <a name="send-a-sign-out-request"></a>Poslat žádost o odhlášení
Pokud se chcete přihlásit uživatele mimo aplikaci, není dostatek vymažete soubory cookie vaší aplikace nebo jinak end relace s uživatelem. Musíte také přesměruje uživatele na Azure AD se odhlásíte. Pokud tak učinit, uživatel může být schopný k novému ověření do vaší aplikace bez opětovného zadávání svých přihlašovacích údajů. Je to proto, že budou mít platný jedné přihlášení relace s Azure AD.

Můžete jednoduše přesměruje uživatele na `end_session` koncový bod, který je uveden v dokumentu metadat OpenID Connect popsané dříve v "ověřit ID token" části:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| p |Požaduje se |Zásada, kterou chcete použít k podepsání uživateli mimo vaší aplikace. |
| post_logout_redirect_uri |Doporučené |Adresu URL, kterou uživatel by měl být přesměrován na po úspěšné odhlášení. Pokud není součástí, Azure AD B2C zobrazí obecná zpráva uživatele. |

> [!NOTE]
> I když odkazovat uživatele `end_session` koncový bod vymaže některé z jedné přihlašování stavu uživatele v Azure AD B2C, se nebude přihlášení uživatele z jejich relace sociálních identity zprostředkovatele (IDP). Pokud uživatel vybere stejné rozšíření IDP při následné přihlášení, se bude mít k novému ověření, bez nutnosti zadávat své přihlašovací údaje. Pokud chce uživatel z vaší aplikace B2C odhlásit, je však nemusí znamenat, že se chcete odhlásit z účtu sítě Facebook. Ale v případě místní účty relace uživatele bude ukončen správně.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Použití vlastního klienta B2C
Pokud budete chtít zkusit tyto požadavky pro sebe, musíte nejprve provést tyto tři kroky a pak nahraďte ukázkové hodnoty vlastními popsané výše:

1. [Vytvoření klienta B2C](active-directory-b2c-get-started.md)a používat název vašeho klienta v žádosti.
2. [Vytvoření aplikace](active-directory-b2c-app-registration.md) získat identifikátor aplikace. Zahrňte webové aplikace nebo webové rozhraní API ve vaší aplikaci. Volitelně můžete vytvořte tajný klíč aplikace.
3. [Vytvoření vlastních zásad](active-directory-b2c-reference-policies.md) získat názvy zásad.

