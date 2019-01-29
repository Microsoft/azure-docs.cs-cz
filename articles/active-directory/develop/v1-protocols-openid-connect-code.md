---
title: Pochopení toku kódu ověřování OpenID Connect ve službě Azure AD | Dokumentace Microsoftu
description: Tento článek popisuje, jak používat zprávy HTTP k autorizaci přístupu k webovým aplikacím a webovým rozhraním API ve vašem tenantovi pomocí služby Azure Active Directory a OpenID Connect.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b78d8a5bd5ebb0d7b2f3c2012ec2021e4f999c1f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100797"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizace přístupu k webovým aplikacím pomocí OpenID Connect a službou Azure Active Directory

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) je jednoduchý identity vrstva postavené na protokol OAuth 2.0. OAuth 2.0 definuje mechanismy získání a použití [ **přístupové tokeny** ](access-tokens.md) pro přístup k chráněným prostředkům, ale nemá definován standardní metody, které poskytují informace o identitě. Ověřování OpenID Connect implementuje jako rozšíření proces autorizace OAuth 2.0. Poskytuje informace o koncový uživatel ve formě [ `id_token` ](id-tokens.md) , který ověřuje identitu uživatele a poskytuje základní profil informace o uživateli.

Naše doporučení OpenID Connect se, pokud vytváříte webovou aplikaci, která je hostována na serveru a získat přístup z prohlížeče.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Tok ověření pomocí OpenID Connect

Základní tok přihlášení obsahuje následující kroky – každý z nich je podrobně popsaná níže.

![OpenId Connect tok ověřování](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Dokument metadat připojení OpenID

Dokument metadat, který obsahuje požadované informace o aplikaci provést přihlášení OpenID Connect popisuje. To zahrnuje informace, jako jsou adresy URL k použití a umístění služby veřejné podpisového klíče. Dokument metadat OpenID Connect najdete v:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadata jsou jednoduché dokumentu JavaScript Object Notation (JSON). Prohlédněte si následující fragment kódu pro příklad. Jsou podrobně popsány v tomto fragmentu kódu obsah [OpenID Connect specifikace](https://openid.net). Mějte na paměti tohoto tenanta poskytují spíše než `common` v místě {klient} výše způsobí specifickým pro tenanta identifikátory URI v vrátil objekt ve formátu JSON.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/common/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/common/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Pokud vaše webová aplikace potřebuje ověřit uživatele, musí směrovat uživateli `/authorize` koncového bodu. Tento požadavek je podobná první větev spojeného [tok OAuth 2.0 autorizační kód](v1-protocols-oauth-code.md), s několika důležité rozdíly:

* Žádost musí obsahovat obor `openid` v `scope` parametru.
* `response_type` Musí obsahovat parametr `id_token`.
* Musí zahrnovat požadavek `nonce` parametru.

Ukázka požadavku by tak vypadat nějak takto:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parametr |  | Popis |
| --- | --- | --- |
| tenant |povinné |`{tenant}` Hodnota v cestě požadavku je možné řídit, kdo se můžete přihlásit do aplikace. Povolené hodnoty jsou identifikátory klientů, třeba `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` nebo `common` tokeny nezávislé na tenanta |
| client_id |povinné |Id aplikace přiřazené vaší aplikaci, když je zaregistrován ve službě Azure AD. To můžete najít na webu Azure Portal. Klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikací**, zvolte aplikaci a vyhledejte číslo Id aplikace na stránce aplikace. |
| response_type |povinné |Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Může také zahrnovat další response_types, jako například `code` nebo `token`. |
| scope |povinné |Seznam oborů oddělených mezerami. Pro OpenID Connect, musí zahrnovat obor `openid`, který se přeloží na "Přihlášení" oprávnění v souhlasu uživatelského rozhraní. V této žádosti pro vyžádání souhlasu mohou zahrnovat také další obory. |
| Hodnota Nonce |povinné |Hodnota zahrnutý v požadavku, vygenerované aplikaci, která je zahrnutá ve výsledné `id_token` jako deklarace identity. Aplikace pak můžete ověřit tuto hodnotu a zmírnění útoků opětovného přehrání tokenu. Hodnota je obvykle náhodného, jedinečný řetězec nebo identifikátor GUID, který slouží k určení původu žádosti. |
| redirect_uri |Doporučené |Redirect_uri vaší aplikace, kde můžete odesílat a přijímat aplikací pro žádosti o ověření. Musí odpovídat přesně jeden z redirect_uris, které jste zaregistrovali na portálu, s tím rozdílem, musí být kódování url. |
| response_mode |nepovinné |Určuje metodu, která se má použít k odeslání výsledný authorization_code zpět do aplikace. Podporované hodnoty jsou `form_post` pro *HTTP post formuláře* a `fragment` pro *fragment adresy URL*. Pro webové aplikace, doporučujeme používat `response_mode=form_post` k zajištění nejvyšší zabezpečení přenosu tokeny do vaší aplikace. Ve výchozím nastavení všech toků, včetně tokentu id_token `fragment`.|
| state |Doporučené |Hodnota v požadavku, který je vrácený v odpovědi tokenu. Může být řetězec jakéhokoli obsahu, který chcete. Náhodně generované jedinečná hodnota se obvykle používá pro [prevence útoků proti padělání žádosti více webů](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které byly na. |
| řádek |nepovinné |Určuje typ interakce s uživatelem, který je požadován. V současné době pouze platné hodnoty jsou "přihlášení", "žádný" a "souhlas". `prompt=login` donutí uživatele k zadání přihlašovacích údajů tohoto požadavku negace jednotného přihlašování. `prompt=none` je opakem – zajišťuje, že uživatel se nezobrazí se žádné interaktivní výzvu jakýmkoli způsobem. Pokud žádost nejde dokončit tiše prostřednictvím jednotného přihlašování, koncový bod vrátí chybu. `prompt=consent` aktivační události OAuth souhlas dialogové okno po přihlášení uživatele, s výzvou uživateli udělit oprávnění k aplikaci. |
| login_hint |nepovinné |Umožňuje předem vyplnit pole uživatelské jméno nebo e-mailová adresa stránky přihlášení pro uživatele, pokud znáte svoje uživatelské jméno předem. Tento parametr použijte, často aplikace během opětovné ověření, uživatelské jméno s již extrahovat z předchozí přihlášení pomocí `preferred_username` deklarací identity. |

V tomto okamžiku je uživatel vyzván k zadání přihlašovacích údajů a bylo možné ověření dokončit.

### <a name="sample-response"></a>Ukázková odpověď

Ukázkové odpovědi, až uživatel byl ověřen, může vypadat například takto:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |`id_token` Požadovanou aplikaci. Můžete použít `id_token` ověřit identitu uživatele a zahájit relaci s uživatelem. |
| state |Hodnota v požadavku, který je také vrácen v odpovědi tokenu. Náhodně generované jedinečná hodnota se obvykle používá pro [prevence útoků proti padělání žádosti více webů](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které byly na. |

### <a name="error-response"></a>Odpověď na chybu

Chybové odpovědi může také být odeslán `redirect_uri` tak aplikaci můžete odpovídajícím způsobem zpracovat:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, která slouží ke klasifikaci typy chyb, ke kterým dochází a je možné reagovat na chyby. |
| error_description |Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kódy chyb pro chyby koncový bod autorizace

Následující tabulka popisuje různé kódy chyb, které mohou být vráceny v `error` parametr odpovědi na chybu.

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, jako je například chybí povinný parametr. |Opravte a odešlete požadavek znovu. Toto je vývoj chyba a obvykle zachycuje se během počáteční testování. |
| unauthorized_client |Klientská aplikace není povoleno požádat o autorizační kód. |K tomu obvykle dojde, když klientská aplikace není zaregistrovaný ve službě Azure AD nebo se nepřidal do tenanta služby Azure AD daného uživatele. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a jeho přidání do služby Azure AD. |
| access_denied |Odepřen souhlas vlastníka prostředku |Klientská aplikace může upozornit uživatele, který nemůže pokračovat, dokud uživatel souhlasí. |
| unsupported_response_type |Autorizační server nepodporuje typ odpovědi v požadavku. |Opravte a odešlete požadavek znovu. Toto je vývoj chyba a obvykle zachycuje se během počáteční testování. |
| server_error |Na serveru došlo k neočekávané chybě. |Zkuste požadavek. Tyto chyby můžou být výsledkem dočasné situace. Klientská aplikace může vysvětlit uživatelům, že odpověď se zpožďuje kvůli dočasné chybě. |
| temporarily_unavailable |Server je dočasně příliš zaneprázdněn a nemůže žádost zpracovat. |Zkuste požadavek. Klientská aplikace může vysvětlit uživateli, odpověď se zpožďuje kvůli dočasnému chybovému stavu. |
| invalid_resource |Cílový prostředek je neplatný, protože buď neexistuje, Azure AD nelze najít nebo není správně nakonfigurovaný. |To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a jeho přidání do služby Azure AD. |

## <a name="validate-the-idtoken"></a>Ověření požadavku id_token

Pouhého získání `id_token` není dostatečná k ověření uživatele; je nutné ověřit podpis a ověřit deklarace identity v `id_token` podle požadavků vaší aplikace. Koncový bod Azure AD používá k podepisování tokenů a ověřte, že jsou platné webové tokeny JSON (Jwt) a kryptografie využívající veřejného klíče.

Můžete také ověřit `id_token` v klientovi kód, ale běžnou praxí je odeslat `id_token` back-end server a provést ověření existuje. Jakmile ověříte podpis `id_token`, existuje několik deklarací identity, které jsou nutné k ověření.

Také můžete chtít ověřit další deklarace identity v závislosti na vašem scénáři. Některé běžné ověření patří:

* Zajištění uživatele nebo organizaci zaregistroval k aplikaci.
* Zajistit, že uživatel má správnou autorizaci/oprávnění
* Zajištění sílu ověřování došlo, jako je ověřování službou Multi-Factor Authentication.

Jakmile ověříte `id_token`, můžete zahájit relaci s uživatelem a používat deklarace identity v `id_token` k získání informací o uživateli ve vaší aplikaci. Tyto informace můžete použít pro zobrazení záznamů, přizpůsobení, atd. Další informace o `id_tokens` a deklarace identity, přečtěte si [AAD id_tokens](id-tokens.md).

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

Když budete chtít přihlásit uživatele z aplikace, není dostatečná k Vymazat soubory cookie vaší aplikace nebo jinak end relace s uživatelem. Také je třeba přesměrovat uživatele `end_session_endpoint` pro odhlášení. Pokud k tomu, bude uživatel moct donutit k vaší aplikaci bez nutnosti zadávat své přihlašovací údaje znovu, protože budou mít platnou jednotné přihlašování relaci ke koncovému bodu Azure AD.

Můžete jednoduše přesměrovat uživatele `end_session_endpoint` uvedené v tomto dokumentu metadat OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametr |  | Popis |
| --- | --- | --- |
| post_logout_redirect_uri |Doporučené |Adresa URL, která uživatel by měl být přesměrován na po úspěšném odhlášení. Pokud nejsou zahrnuty, uživateli se zobrazí obecná zpráva. |

## <a name="single-sign-out"></a>Jednotné odhlašování

Pokud má uživatel přesměrovat `end_session_endpoint`, Azure AD vymaže relace uživatele z prohlížeče. Však uživatel stále být přihlášeni k ostatním aplikacím, které používají Azure AD pro ověřování. Pokud chcete povolit tyto aplikace se zároveň Odhlásit uživatele, Azure AD odešle požadavek HTTP GET na zaregistrovanou `LogoutUrl` všech aplikací, které uživatel je aktuálně přihlášený k. Aplikace musí odpovědět na tuto žádost zrušíte všechny relace, který identifikuje uživatele a vrácení `200` odpovědi. Pokud chcete podporu jednotného přihlašování, odhlašování ve vaší aplikaci, je nutné implementovat takové `LogoutUrl` v kódu vaší aplikace. Můžete nastavit `LogoutUrl` z portálu Azure portal:

1. Přejděte [webu Azure Portal](https://portal.azure.com).
2. Kliknutím na váš účet v pravém horním rohu stránky zvolte služby Active Directory.
3. V levém navigačním panelu zvolte **Azure Active Directory**, klikněte na tlačítko **registrace aplikací** a vyberte svou aplikaci.
4. Klikněte na **nastavení**, pak **vlastnosti** a najít **odhlašovací adresa URL** textového pole. 

## <a name="token-acquisition"></a>Získání tokenu
Mnoho webových aplikací je potřeba jenom přihlášení uživatele, ale také přístup k webové službě jménem uživatele pomocí metody OAuth. Tento scénář spojuje OpenID Connect pro ověřování uživatelů při získávání současně `authorization_code` , který můžete použít k získání `access_tokens` pomocí [toku kódu autorizace OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Získání přístupových tokenů
Získání přístupových tokenů, budete muset upravit žádost o přihlášení z výše:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Včetně obory oprávnění v požadavku a použitím `response_type=code+id_token`, `authorize` koncový bod se zajistí, že uživatel vyjádřil souhlas se oprávnění uvedená v `scope` parametr dotazu a vrátí autorizační kód k výměně pro vaši aplikaci přístupový token.

### <a name="successful-response"></a>Úspěšné odpovědi

Úspěšné odpovědi pomocí `response_mode=form_post` vypadá jako:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |`id_token` Požadovanou aplikaci. Můžete použít `id_token` ověřit identitu uživatele a zahájit relaci s uživatelem. |
| kód |Authorization_code požadovanou aplikaci. Aplikace může používat autorizační kód k vyžádání tokenu pro cílový prostředek. Authorization_codes jsou krátké žít a obvykle vyprší po přibližně 10 minut. |
| state |Pokud parametr stavu je zahrnutý v požadavku, by se zobrazit stejnou hodnotu v odpovědi. Aplikace by měl ověřit, že jsou identické hodnoty stavu v požadavku a odpovědi. |

### <a name="error-response"></a>Odpověď na chybu

Chybové odpovědi může také být odeslán `redirect_uri` tak aplikaci můžete odpovídajícím způsobem zpracovat:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, která slouží ke klasifikaci typy chyb, ke kterým dochází a je možné reagovat na chyby. |
| error_description |Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování. |

Popis kódů chyb a jejich akci doporučenou klientskou najdete v tématu [kódy chyb pro chyby koncový bod autorizace](#error-codes-for-authorization-endpoint-errors).

Jakmile se vám zobrazila povolení `code` a `id_token`, můžete uživatele přihlásit a získat [přístupové tokeny](access-tokens.md) jejich jménem. Chcete-li přihlásit uživatele, je třeba ověřit `id_token` přesně tak, jak je popsáno výše. K získání přístupových tokenů, můžete postupovat podle kroků popsaných v části "Použití autorizační kód k vyžádání tokenu přístupu" náš [dokumentace ke službě flow OAuth kód](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Další postup

* Další informace o [přístupové tokeny](access-tokens.md).
* Další informace o [ `id_token` a deklarace identity](id-tokens.md).
