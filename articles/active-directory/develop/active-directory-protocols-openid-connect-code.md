---
title: Lépe pochopit tok OpenID Connect kód ověřování ve službě Azure AD | Microsoft Docs
description: Tento článek popisuje, jak používat zprávy protokolu HTTP k autorizaci přístupu k webové aplikace a webové rozhraní API ve vašem klientovi pomocí Azure Active Directory a OpenID Connect.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a5383776aa787a087fffe1ab06bb62c2b1df073d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizace přístupu k webovým aplikacím pomocí OpenID Connect a službou Azure Active Directory
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) je vrstva jednoduché identity postavená na protokol OAuth 2.0. OAuth 2.0 definuje mechanismy pro získání a používání **přístup tokeny** pro přístup k chráněným prostředkům, ale nejsou definovány standardní metody, které poskytují informace o identitě. Jako rozšíření pro proces autorizace OAuth 2.0, OpenID Connect implementuje ověřování. Poskytuje informace o koncového uživatele ve formě `id_token` který ověřuje identitu uživatele a poskytuje základní profil informace o uživateli.

Naše doporučení OpenID Connect je, pokud vytváříte webovou aplikaci, která je hostovaná na serveru a k němu přistupovat prostřednictvím prohlížeče.


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Tok ověřování pomocí OpenID Connect
Nejzákladnější toku přihlášení obsahuje následující kroky – každý z nich je podrobně popsaná níže.

![OpenId Connect tok ověřování](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Dokument metadat OpenID Connect

Popisuje dokumentu metadat, který obsahuje většinu požadované informace o aplikaci k provedení přihlášení, OpenID Connect. To zahrnuje informace, jako jsou adresy URL používat a umístění služby veřejného podpisového klíče. Dokument metadat OpenID Connect najdete tady:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadata jsou jednoduché dokumentu JavaScript Object Notation (JSON). Viz následující fragment kódu pro příklad. Jsou podrobně popsány v tomto fragmentu kódu obsah [OpenID Connect specifikace](https://openid.net).

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/common/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/common/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    
    ...
}
```

## <a name="send-the-sign-in-request"></a>Odeslání žádosti o přihlášení
Když webové aplikace potřebuje k ověření uživatele, musí přesměrování uživatele `/authorize` koncový bod. Tento požadavek je podobná první rameno [toku OAuth 2.0 autorizační kód](active-directory-protocols-oauth-code.md), s několika důležité rozdíly:

* Žádost musí obsahovat rozsah `openid` v `scope` parametr.
* `response_type` Musí obsahovat parametr `id_token`.
* Musí zahrnovat požadavek `nonce` parametr.

Takže ukázková žádost bude vypadat takto:

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
| tenant |povinné |`{tenant}` Hodnotu v cestě požadavku slouží k řízení, kdo může přihlásit k aplikaci. Povolené hodnoty jsou identifikátory klienta, například `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` nebo `common` pro tokeny nezávislé na klienta |
| client_id |povinné |Id aplikace přiřazené vaší aplikaci při registraci s Azure AD. Tento nástroj naleznete na webu Azure Portal. Klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikace**, zvolte aplikaci a vyhledejte číslo Id aplikace na stránce aplikace. |
| response_type |povinné |Musí zahrnovat `id_token` pro OpenID Connect přihlášení. Může také obsahovat další response_types, jako například `code`. |
| scope |povinné |Seznam obory oddělených mezerami. Pro OpenID Connect, musí obsahovat rozsah `openid`, což znamená, že je na oprávnění "Přihlásit" v souhlasu uživatelského rozhraní. Může také zahrnovat další obory v této žádosti o žádosti o souhlas. |
| hodnotu Nonce |povinné |Součástí požadavku, vygenerovaný aplikací, který je součástí výsledná hodnota `id_token` jako deklarace identity. Aplikace pak může ověřit tuto hodnotu zmírnit útoky opětovného přehrání tokenu. Hodnota je obvykle náhodnou jedinečného řetězce nebo identifikátor GUID, který slouží k určení původu požadavku. |
| redirect_uri |Doporučená |O položku redirect_uri vaší aplikace, kde můžete odesílat a přijímat aplikace odpovědi ověřování. Se musí přesně shodovat s jedním redirect_uris, které jste zaregistrovali na portálu, s výjimkou musí být kódovaná adresou url. |
| response_mode |Doporučená |Určuje metodu, která se má použít k odeslání výsledné authorization_code zpět do vaší aplikace. Podporované hodnoty jsou `form_post` pro *HTTP post formuláře* a `fragment` pro *fragment adresy URL*. U webových aplikací, doporučujeme použít `response_mode=form_post` zajistit nejbezpečnější přenos tokeny do vaší aplikace. Výchozí, pokud `response_mode` neuvedete, je `fragment`.|
| state |Doporučená |Hodnota součástí požadavek, který je vrácený v odpovědi tokenu. Může být řetězec o délce veškerý obsah, který chcete. Náhodně generované jedinečné hodnoty se obvykle používá u [prevence útoků padělání požadavku posílaného mezi weby](http://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky nebo zobrazení, které byly na. |
| řádku |nepovinné |Označuje typ interakce s uživatelem, který je vyžadován. V současné době pouze platné hodnoty jsou "přihlášení", 'none' a 'souhlas'. `prompt=login` Vynutí se tak, aby uživatel zadal své přihlašovací údaje tohoto požadavku negace jednotného přihlašování. `prompt=none` je opak - zajišťuje, že uživatel není uveden s žádné interaktivní výzvu jakkoli. Pokud požadavek nemůže být dokončena bez upozornění pomocí jednotného přihlašování, koncový bod vrátí chybu. `prompt=consent` aktivační události OAuth souhlas dialogové okno po přihlášení uživatele, požádá uživatele a udělit oprávnění k aplikaci. |
| login_hint |nepovinné |Slouží k předem vyplnit pole uživatelské jméno nebo e-mailové adresy na stránce přihlášení pro uživatele, pokud znáte svoje uživatelské jméno předem. Tento parametr použijte často aplikace během opětovné ověření, uživatelské jméno s již extrahovat z předchozí přihlášení pomocí `preferred_username` deklarací identity. |

V tomto okamžiku je uživatel vyzván k zadání přihlašovacích údajů a dokončení ověření.

### <a name="sample-response"></a>Ukázková odpověď
Ukázková odpověď po byl uživatel ověřen, může vypadat například takto:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |`id_token` Požadovanou aplikaci. Můžete použít `id_token` ověřit identitu uživatele a spustíte relaci s uživatelem. |
| state |Hodnota součástí požadavek, který je také vrácený v odpovědi tokenu. Náhodně generované jedinečné hodnoty se obvykle používá u [prevence útoků padělání požadavku posílaného mezi weby](http://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky nebo zobrazení, které byly na. |

### <a name="error-response"></a>Chybové odpovědi
Chybové odpovědi se taky může odeslat do `redirect_uri` tak aplikace můžete správně zpracovat:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým došlo a slouží k reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kódy chyb pro chyb koncový bod autorizace
Následující tabulka popisuje různé kódy chyb, které mohou být vráceny v `error` parametr odpovědi na chybu.

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, například chybějící povinný parametr. |Opravte a odešlete požadavek znovu. To je chyba vývoj a je obvykle zachycena během počáteční testování. |
| unauthorized_client |Klientská aplikace nemá oprávnění k vyžádání autorizační kód. |K tomu obvykle dojde, pokud klientská aplikace není registrovaný ve službě Azure AD nebo nebyla přidána do klienta Azure AD pro uživatele. Aplikace můžete vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Azure AD. |
| ACCESS_DENIED |Vlastník prostředku odepřen souhlasu |Klientská aplikace může upozornit uživatele, který nemůže pokračovat, pokud uživatel souhlasí. |
| unsupported_response_type |Autorizace serveru nepodporuje typ odpovědi v požadavku. |Opravte a odešlete požadavek znovu. To je chyba vývoj a je obvykle zachycena během počáteční testování. |
| server_error |Serveru došlo k neočekávané chybě. |Opakujte žádost. Tyto chyby může být důsledkem dočasné stavy. Klientská aplikace mohou vysvětlit pro uživatele, že odpověď se zpožďuje kvůli dočasné chybě. |
| temporarily_unavailable |Server je dočasně zaneprázdněn pro zpracování požadavku. |Opakujte žádost. Klientská aplikace mohou vysvětlit pro uživatele, že odpověď se zpožďuje kvůli dočasné podmínce. |
| invalid_resource |Cílový prostředek je neplatný, protože neexistuje, Azure AD ji nemůže najít, nebo není správně nakonfigurována. |To znamená, že k prostředku, pokud existuje, není nakonfigurované v klientovi. Aplikace můžete vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Azure AD. |

## <a name="validate-the-idtoken"></a>Ověření požadavku id_token
Právě přijetí `id_token` není dostatečná k ověření uživatele; je nutné ověřit podpis a ověřte deklarací identity ve `id_token` podle požadavků vaší aplikace. Koncový bod Azure AD používá k podepisování tokenů a ověřte, zda jsou platná webové tokeny JSON (Jwt) a kryptografie využívající veřejného klíče.

Můžete ověřit `id_token` v klientovi kód, ale běžnou praxí je odeslat `id_token` back-end server a že k ověření. Jakmile jste ověřit podpis `id_token`, existuje několik deklarace identity, které jsou potřebné k ověření.

Můžete také ověřit další deklarace identity v závislosti na vašem scénáři. Některé běžné ověření patří:

* Zajištění uživatele či organizace má registraci pro aplikaci.
* Zajištění uživatel má správná oprávnění autorizace
* Zajištění sílu ověřování došlo k, jako je vícefaktorové ověřování.

Jakmile ověříte `id_token`, můžete začít relaci s uživatelem a používat deklarace identity v `id_token` získat informace o uživateli ve vaší aplikaci. Tyto informace můžete použít pro zobrazení, záznamy, oprávnění atd. Další informace o typech tokenů a deklarací identity, najdete v tématu [podporované tokenu a typy deklarací identity](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Poslat žádost o odhlášení
Pokud se chcete přihlásit uživatele mimo aplikaci, není dostatečná k vymazání souborů cookie vaší aplikace nebo jinak end relace s uživatelem. Také je nutné přesměrovat uživatele `end_session_endpoint` pro odhlášení. Pokud uděláte to tak, bude uživatel moci novému ověření do vaší aplikace bez nutnosti zadávat své přihlašovací údaje znovu, protože budou mít platný jedné přihlášení relace s koncovým bodem Azure AD.

Můžete jednoduše přesměruje uživatele na `end_session_endpoint` uvedené v dokumentu metadat OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametr |  | Popis |
| --- | --- | --- |
| post_logout_redirect_uri |Doporučená |Adresa URL, která má být uživatel přesměrován na po úspěšném odhlášení. Pokud nejsou, zobrazí se uživateli obecná zpráva. |

## <a name="single-sign-out"></a>Jednotné odhlašování
Když přesměruje uživatele na `end_session_endpoint`, Azure AD vymaže relace uživatele z prohlížeče. Ale může se uživatel přihlášen stále k ostatním aplikacím, které používají Azure AD pro ověřování. Povolit těchto aplikací na současně odhlášení uživatele, Azure AD, odešle požadavek HTTP GET na zaregistrovanou `LogoutUrl` všech aplikací, které uživatel je aktuálně přihlášený k. Aplikace musí reagovat na tento požadavek zrušením jakékoli relace, který identifikuje uživatele a vrácení `200` odpovědi. Pokud chcete limit podporují jednotné přihlašování v aplikaci, je nutné implementovat, `LogoutUrl` v kódu aplikace. Můžete nastavit `LogoutUrl` z portálu Azure:

1. Přejděte na [portál Azure](https://portal.azure.com).
2. Kliknutím na vašem účtu v pravém horním rohu stránky zvolte vaší služby Active Directory.
3. V levém navigačním panelu, vyberte příkaz **Azure Active Directory**, zvolte **registrace aplikace** a vyberte svou aplikaci.
4. Klikněte na **nastavení**, pak **vlastnosti** a najděte **adresy URL odhlašovací** textové pole. 

## <a name="token-acquisition"></a>Získání tokenu
Mnoho webových aplikací je nutné pouze přihlášení uživatele v, ale také přístup k webové službě jménem tohoto uživatele pomocí metody OAuth. Tento scénář kombinuje OpenID Connect pro ověřování uživatelů při získávání současně `authorization_code` , můžete použít k získání `access_tokens` pomocí [toku kódu autorizace OAuth](active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Získat přístupové tokeny
Získat přístupové tokeny, musíte upravit žádost přihlášení z výše:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                     
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Včetně obory oprávnění v požadavku a použitím `response_type=code+id_token`, `authorize` koncový bod zajistí, že uživatel souhlasí s tím oprávnění uvedené v `scope` parametr dotazu a vracet autorizační kód k systému exchange pro přístupový token aplikace.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď pomocí `response_mode=form_post` vypadá jako:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |`id_token` Požadovanou aplikaci. Můžete použít `id_token` ověřit identitu uživatele a spustíte relaci s uživatelem. |
| Kód |Authorization_code, který požadované aplikace. Aplikace můžete autorizační kód vyžádat token přístupu pro cílový prostředek. Authorization_codes jsou krátkou životnost a obvykle vyprší po přibližně 10 minut. |
| state |Pokud parametr stavu je obsažena v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda jsou identické hodnoty stavu v požadavku a odpovědi. |

### <a name="error-response"></a>Chybové odpovědi
Chybové odpovědi se taky může odeslat do `redirect_uri` tak aplikace můžete správně zpracovat:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým došlo a slouží k reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování. |

Popis možná chyba kódů a jejich klienta doporučenou akci najdete v tématu [kódy chyb pro chyb koncový bod autorizace](#error-codes-for-authorization-endpoint-errors).

Jakmile jste, že jste podmínky povolení `code` a `id_token`, můžete uživatele přihlásit a získat přístupové tokeny jejich jménem. Chcete-li přihlásit uživatele v, je třeba ověřit `id_token` přesně tak, jak je popsáno výše. Chcete-li získat přístupové tokeny, můžete podle kroků popsaných v části "Použití autorizační kód požádat o token přístupu" naše [dokumentace k protokolu OAuth](active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
