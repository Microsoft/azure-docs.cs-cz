---
title: Autorizovat přístup k webové aplikaci pomocí OpenID Connect & Azure AD | Microsoft Docs
description: Tento článek popisuje, jak pomocí zpráv HTTP autorizovat přístup k webovým aplikacím a webovým rozhraním API ve vašem tenantovi pomocí Azure Active Directory a OpenID Connect.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 6eb71bb4fdaa602dcbce5d351c261493647f3caa
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700800"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizace přístupu k webovým aplikacím pomocí OpenID Connect a Azure Active Directory

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) je jednoduchá vrstva identity postavená nad protokolem OAuth 2,0. OAuth 2,0 definuje mechanismy pro získání a použití [**přístupových tokenů**](access-tokens.md) pro přístup k chráněným prostředkům, ale nedefinuje standardní metody pro poskytování informací o identitě. OpenID Connect implementuje ověřování jako rozšíření procesu autorizace OAuth 2,0. Poskytuje informace o koncovém uživateli ve formě [`id_token`](id-tokens.md) , který ověřuje identitu uživatele a poskytuje základní informace o profilu uživatele.

OpenID Connect je naše doporučení, pokud vytváříte webovou aplikaci, která je hostována na serveru a přístupná přes prohlížeč.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>Tok ověřování pomocí OpenID Connect

Nejzákladnější tok přihlášení obsahuje následující postup – každý z nich je podrobně popsán níže.

![Tok ověřování OpenId Connect](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Dokument metadat OpenID Connect

OpenID Connect popisuje dokument metadat, který obsahuje většinu informací požadovaných pro aplikaci k provedení přihlášení. Zahrnuje to i informace, jako jsou adresy URL, které se mají použít, a umístění veřejných podpisových klíčů služby. Dokument metadat OpenID Connect najdete na adrese:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadata jsou jednoduchý dokument JavaScript Object Notation (JSON). Příklad naleznete v následujícím fragmentu kódu. Obsah fragmentu je plně popsán ve [specifikaci OpenID Connect](https://openid.net). Upozorňujeme, že zadání ID tenanta místo `common` místo u typu {tenant} bude mít za následek to, že se v objektu JSON vrátí identifikátory URI specifické pro tenanta.

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
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

Pokud vaše aplikace obsahuje vlastní podpisové klíče v důsledku použití funkce [mapování deklarací](active-directory-claims-mapping.md) , musíte připojit parametr `appid` dotazu obsahujícího ID aplikace, aby se `jwks_uri` odkazovalo na informace o podpisovém klíči vaší aplikace. Například: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` obsahuje `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Pokud vaše webová aplikace potřebuje ověřit uživatele, musí uživatele nasměrovat na koncový bod `/authorize`. Tento požadavek je podobný prvnímu průchodu [toku autorizačního kódu OAuth 2,0](v1-protocols-oauth-code.md), a to z několika důležitých rozdílů:

* Požadavek musí zahrnovat `openid` oboru v parametru `scope`.
* Parametr `response_type` musí zahrnovat `id_token`.
* Požadavek musí zahrnovat parametr `nonce`.

Proto by ukázkový požadavek vypadal takto:

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
| tenant |required |Hodnotu `{tenant}` v cestě k žádosti lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou identifikátory klientů, například `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` nebo `common` pro tokeny nezávislé na tenantovi. |
| client_id |required |ID aplikace přiřazené vaší aplikaci, když ji zaregistrujete ve službě Azure AD. Najdete ho v Azure Portal. Klikněte na **Azure Active Directory**, klikněte na **Registrace aplikací**, vyberte aplikaci a na stránce aplikace vyhledejte ID aplikace. |
| response_type |required |Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Může také zahrnovat jiné response_types, například `code` nebo `token`. |
| scope | Doporučené | Specifikace OpenID Connect vyžaduje obor `openid`, který v uživatelském rozhraní souhlasu překládá oprávnění přihlásit se. Tento a další obory OIDC se na koncovém bodu verze 1.0 ignorují, ale pro klienty kompatibilní se standardem je stále doporučený postup. |
| nonce |required |Hodnota obsažená v požadavku vygenerovaná aplikací, která je součástí výsledného `id_token` jako deklarace identity. Aplikace pak může tuto hodnotu ověřit a zmírnit tak útoky na opakované přehrání tokenů. Hodnota je obvykle náhodný, jedinečný řetězec nebo identifikátor GUID, který lze použít k identifikaci původu žádosti. |
| redirect_uri | Doporučené |Redirect_uri vaší aplikace, ve které vaše aplikace může odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z redirect_uris, který jste zaregistrovali na portálu, s výjimkou musí být zakódovaný URL. Pokud chybí, uživatelský agent se pošle zpátky k jednomu z identifikátorů URI pro přesměrování zaregistrovaných pro aplikaci náhodně. Maximální délka je 255 bajtů. |
| response_mode |volitelné |Určuje metodu, která se má použít k odeslání výsledného authorization_code zpět do vaší aplikace. Podporované hodnoty jsou `form_post` pro *post formuláře http* a `fragment` *fragmentu adresy URL*. U webových aplikací doporučujeme použít `response_mode=form_post` k zajištění nejbezpečnějšího přenosu tokenů do aplikace. Výchozí hodnota pro jakýkoliv tok včetně id_token je `fragment`.|
| state |Doporučené |Hodnota obsažená v požadavku, která je vrácena v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně vygenerovaná jedinečná hodnota se obvykle používá k [prevenci útoků proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
| výzv |volitelné |Určuje typ interakce uživatele, která je povinná. V současné době jsou jedinými platnými hodnotami "login," none "a" souhlas ". `prompt=login` vynutí, aby uživatel zadal přihlašovací údaje k této žádosti, přičemž se pro ně provede negace jednotného přihlašování. `prompt=none` je opak, zajišťuje, že se uživateli nezobrazí žádná interaktivní výzva. Pokud požadavek nejde v tichém režimu dokončit pomocí jednotného přihlašování, vrátí koncový bod chybu. `prompt=consent` aktivuje dialog souhlasu OAuth po přihlášení uživatele a požádá uživatele, aby aplikaci udělil oprávnění. |
| login_hint |volitelné |Dá se použít k předvyplnění pole uživatelské jméno a e-mailová adresa přihlašovací stránky pro uživatele, pokud znáte své uživatelské jméno předem. Aplikace často používají tento parametr během opakovaného ověřování, kteří již extrahovali uživatelské jméno z předchozího přihlášení pomocí `preferred_username` deklarace identity. |

V tomto okamžiku se uživateli zobrazí výzva k zadání přihlašovacích údajů a dokončení ověřování.

### <a name="sample-response"></a>Ukázková odpověď

Ukázková odpověď, která se pošle do `redirect_uri` zadaného v žádosti o přihlášení po ověření uživatele, může vypadat takto:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |`id_token`, kterou aplikace požadovala Pomocí `id_token` můžete ověřit identitu uživatele a zahájit relaci s uživatelem. |
| state |Hodnota obsažená v požadavku, která je také vrácena v odpovědi tokenu. Náhodně vygenerovaná jedinečná hodnota se obvykle používá k [prevenci útoků proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |

### <a name="error-response"></a>Chybová odezva

Do `redirect_uri` lze také odeslat odpovědi na chyby, aby je aplikace mohla správně zpracovat:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Chybové kódy pro chyby koncového bodu autorizace

Následující tabulka popisuje různé chybové kódy, které lze vrátit v parametru `error` chybové odpovědi.

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, například chybějící požadovaný parametr. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoje, která se obvykle zachycuje při počátečním testování. |
| unauthorized_client |Klientská aplikace nemá oprávnění vyžadovat autorizační kód. |K tomu obvykle dochází, když klientská aplikace není registrovaná v Azure AD nebo není přidaná do tenanta Azure AD uživatele. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| access_denied |Vlastník prostředku zamítl souhlas. |Klientská aplikace může uživatele informovat, že nemůže pokračovat, dokud se uživatel nesouhlasí. |
| unsupported_response_type |Autorizační Server v žádosti nepodporuje typ odpovědi. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoje, která se obvykle zachycuje při počátečním testování. |
| server_error |Na serveru došlo k neočekávané chybě. |Opakujte požadavek. Tyto chyby mohou být způsobeny dočasnými podmínkami. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné chyby. |
| temporarily_unavailable |Server je dočasně zaneprázdněný pro zpracování žádosti. |Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
| invalid_resource |Cílový prostředek není platný, protože neexistuje, Azure AD ho nemůže najít nebo není správně nakonfigurovaný. |To znamená, že pokud tento prostředek existuje, není v tenantovi nakonfigurovaný. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |

## <a name="validate-the-id_token"></a>Ověřit id_token

Pouze příjem `id_token` nestačí k ověření uživatele; je nutné ověřit podpis a ověřit deklarace identity v `id_token` podle požadavků vaší aplikace. Koncový bod Azure AD využívá webové tokeny JSON (JWTs) a kryptografii s veřejným klíčem k podepisování tokenů a k ověření, že jsou platné.

Můžete se rozhodnout ověřit `id_token` v kódu klienta, ale běžný postup je odeslat `id_token` na back-end Server a provést ověření tam. 

V závislosti na vašem scénáři taky můžete chtít ověřit další deklarace identity. Mezi běžná ověření patří:

* Zajistěte, aby se uživatel nebo organizace zaregistrovali do aplikace.
* Zajistěte, aby měl uživatel správnou autorizaci nebo oprávnění pomocí deklarací identity `wids` nebo `roles`. 
* Bylo zajištěno, že došlo k určité síle ověřování, jako je například Multi-Factor Authentication.

Po ověření `id_token`můžete zahájit relaci s uživatelem a pomocí deklarací v `id_token` získat informace o uživateli ve vaší aplikaci. Tyto informace lze použít pro zobrazení, záznamy, přizpůsobení atd. Pokud chcete získat další informace o `id_tokens` a deklaracích, přečtěte si [Id_tokens AAD](id-tokens.md).

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

Pokud chcete uživatele podepsat z aplikace, není dostačující vymazat soubory cookie vaší aplikace nebo jinak ukončit relaci s uživatelem. Musíte také přesměrovat uživatele na `end_session_endpoint` pro odhlášení. Pokud to neuděláte, uživatel bude moct znovu ověřit vaši aplikaci, aniž by musel znovu zadávat přihlašovací údaje, protože budou mít platnou relaci jednotného přihlašování s koncovým bodem Azure AD.

Uživatele můžete jednoduše přesměrovat na `end_session_endpoint`, které jsou uvedené v dokumentu metadat OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametr |  | Popis |
| --- | --- | --- |
| post_logout_redirect_uri |Doporučené |Adresa URL, na kterou má být uživatel přesměrován po úspěšném odhlášení.  Tato adresa URL se musí shodovat s jedním z identifikátorů URI přesměrování registrovaných pro vaši aplikaci na portálu pro registraci aplikací.  Pokud není zahrnut *post_logout_redirect_uri* , zobrazí se uživateli obecná zpráva. |

## <a name="single-sign-out"></a>Jednotné odhlašování

Když uživatele přesměrujete na `end_session_endpoint`, Azure AD vymaže relaci uživatele z prohlížeče. Uživatel se ale může pořád přihlásit k jiným aplikacím, které používají Azure AD k ověřování. Aby mohly tyto aplikace při současném podepsání uživatele podepsat, služba Azure AD pošle požadavek HTTP GET do registrované `LogoutUrl` všech aplikací, ke kterým je uživatel aktuálně přihlášený. Aplikace musí na tuto žádost reagovat vymazáním jakékoli relace, která identifikuje uživatele a vrácením `200` odpovědi. Pokud chcete v aplikaci podporovat jednotné odhlašování, musíte implementovat takové `LogoutUrl` v kódu vaší aplikace. `LogoutUrl` můžete nastavit z Azure Portal:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Kliknutím na svůj účet v pravém horním rohu stránky vyberte svoji službu Active Directory.
3. V levém navigačním panelu zvolte **Azure Active Directory**, zvolte **Registrace aplikací** a vyberte svou aplikaci.
4. Klikněte na **Nastavení**a pak na **vlastnosti** a vyhledejte textové pole **odhlašovací adresa URL** . 

## <a name="token-acquisition"></a>Získání tokenu
Mnohé webové aplikace potřebují pouze podepsat uživatele v, ale také k webové službě jménem tohoto uživatele pomocí OAuth. Tento scénář kombinuje OpenID Connect pro ověřování uživatelů, zatímco současně načítá `authorization_code`, která se dá použít k získání `access_tokens` pomocí [toku autorizačního kódu OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Získat přístupové tokeny
Aby bylo možné získat přístupové tokeny, je nutné upravit žádost o přihlášení z výše uvedeného:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Díky zahrnutí rozsahů oprávnění do žádosti a pomocí `response_type=code+id_token`koncový bod `authorize` zajišťuje, že uživatel souhlasí s oprávněními uvedenými v parametru `scope`ho dotazu, a vrátí vaší aplikaci autorizační kód pro výměnu přístupového tokenu.

### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď, která se pošle `redirect_uri` pomocí `response_mode=form_post`, vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |`id_token`, kterou aplikace požadovala Pomocí `id_token` můžete ověřit identitu uživatele a zahájit relaci s uživatelem. |
| kód |Authorization_code, kterou aplikace požadovala Aplikace může pomocí autorizačního kódu požádat o přístupový token pro cílový prostředek. Authorization_codes jsou krátkodobé a obvykle po 10 minutách vyprší platnost. |
| state |Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

### <a name="error-response"></a>Chybová odezva

Do `redirect_uri` lze také odeslat odpovědi na chyby, aby je aplikace mohla správně zpracovat:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování. |

Popis možných kódů chyb a jejich doporučené akce klienta najdete v tématu [kódy chyb pro chyby koncového bodu autorizace](#error-codes-for-authorization-endpoint-errors).

Jakmile se zobrazí autorizační `code` a `id_token`, můžete uživatele podepsat a získat [přístupové tokeny](access-tokens.md) za jeho jménem. Chcete-li uživatele podepsat v, je nutné ověřit `id_token` přesně tak, jak je popsáno výše. K získání přístupových tokenů můžete postupovat podle kroků popsaných v části "použití autorizačního kódu k vyžádání přístupového tokenu" v [dokumentaci toku kódu OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [přístupových tokenech](access-tokens.md).
* Přečtěte si další informace o [`id_token` a deklaracích](id-tokens.md).
