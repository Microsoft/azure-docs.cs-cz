---
title: Autorizace přístupu k webovým aplikacím pomocí OpenID Connect & Azure AD | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak pomocí zpráv HTTP autorizovat přístup k webovým aplikacím a webovým rozhraním API ve vašem tenantovi pomocí Azure Active Directory a OpenID Connect.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eef8174056be7e6be35cea56788c0a519d02944e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154436"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizace přístupu k webovým aplikacím s použitím OpenID Connect a Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) je jednoduchá vrstva identity postavená na protokolu OAuth 2.0. OAuth 2.0 definuje mechanismy pro získání a použití [**přístupových tokenů**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) pro přístup k chráněným prostředkům, ale nedefinují standardní metody pro poskytování informací o identitě. OpenID Connect implementuje ověřování jako rozšíření autorizačního procesu OAuth 2.0. Poskytuje informace o koncovém uživateli [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) ve formě, která ověřuje identitu uživatele a poskytuje základní informace o profilu o uživateli.

OpenID Connect je naše doporučení, pokud vytváříte webovou aplikaci, která je hostována na serveru a přístupná prostřednictvím prohlížeče.

## <a name="register-your-application-with-your-ad-tenant"></a>Registrace aplikace pomocí tenanta AD
Nejprve zaregistrujte aplikaci u svého klienta Azure Active Directory (Azure AD). Pro svou aplikaci tak získáte ID a umožníte jí přijímat tokeny.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
   
1. Vyberte si klienta Azure AD výběrem účtu v pravém horním rohu stránky, následovaným výběrem navigace **Přepnout adresář** a pak výběrem příslušného klienta. 
   - Přeskočte tento krok, pokud máte jenom jednoho klienta Azure AD pod vaším účtem nebo pokud jste už vybrali příslušného klienta Azure AD.
   
1. Na webu Azure Portal vyhledejte a vyberte **Službu Azure Active Directory**.
   
1. V levé nabídce **Služby Azure Active Directory** vyberte **Registrace aplikací**a pak vyberte **Nová registrace**.
   
1. Postupujte podle zobrazených výzev a vytvořte novou aplikaci. Nezáleží na tom, jestli se jedná o webovou aplikaci nebo veřejnou klientskou (mobilní & desktop) aplikaci pro tento kurz, ale pokud chcete konkrétní příklady pro webové aplikace nebo veřejné klientské aplikace, podívejte se na naše [rychlé starty](v1-overview.md).
   
   - **Název** je název aplikace, který aplikaci popisuje koncovým uživatelům.
   - V části **Podporované typy účtů**vyberte Účty ve všech **organizačních adresářích a osobních účtech Microsoft**.
   - Zadejte **identifikátor URI přesměrování**. Pro webové aplikace se jedná o základní adresu URL vaší aplikace, kde se uživatelé mohou přihlásit.  Například, `http://localhost:12345`. Pro veřejného klienta (mobilní & desktop), Azure AD používá k vrácení odpovědi tokenu. Zadejte hodnotu specifickou pro vaši aplikaci.  Například, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Po dokončení registrace azure ad přiřadí vaší aplikaci jedinečný identifikátor klienta **(ID aplikace).** Tuto hodnotu potřebujete v následujících částech, proto ji zkopírujte ze stránky aplikace.
   
1. Pokud chcete najít aplikaci na webu Azure Portal, vyberte **Registrace aplikací**a pak vyberte Zobrazit **všechny aplikace**.

## <a name="authentication-flow-using-openid-connect"></a>Tok ověřování pomocí OpenID Connect

Nejzákladnější tok přihlášení obsahuje následující kroky - každý z nich je podrobně popsán níže.

![Tok ověřování openId connect](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Dokument metadat OpenID Connect

OpenID Connect popisuje dokument metadat, který obsahuje většinu informací potřebných k tomu, aby se aplikace přihlašovala. To zahrnuje informace, jako jsou adresy URL, které chcete použít, a umístění veřejných podpisových klíčů služby. Dokument metadat OpenID Connect naleznete na adrese:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadata jsou jednoduchý dokument zápisu objektu JavaScript (JSON). Příklad najdete v následujícím úryvku. Obsah fragmentu je plně popsán ve [specifikaci OpenID Connect](https://openid.net). Všimněte si, že poskytnutí `common` ID klienta spíše než místo {tenant} výše bude mít za následek identifikátory URI specifické pro klienta v objektu JSON vrácena.

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

Pokud má vaše aplikace vlastní podpisové klíče v důsledku použití funkce `appid` [mapování deklarací identity,](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) musíte připojit parametr dotazu obsahující ID aplikace, abyste `jwks_uri` získali odkaz na informace o podpisovém klíči aplikace. Například: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` obsahuje `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Když vaše webová aplikace potřebuje ověřit uživatele, musí `/authorize` uživatele nasměrovat na koncový bod. Tato žádost je podobná první části [Toku autorizačního kódu OAuth 2.0](v1-protocols-oauth-code.md)s několika důležitými rozdíly:

* Požadavek musí obsahovat `openid` `scope` obor v parametru.
* Parametr `response_type` musí `id_token`obsahovat .
* Požadavek musí `nonce` obsahovat parametr.

Takže ukázkový požadavek bude vypadat takto:

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
| Nájemce |Požadovaný |Hodnotu `{tenant}` v cestě požadavku lze použít k řízení, kdo se může přihlásit do aplikace. Povolené hodnoty jsou identifikátory klienta, `contoso.onmicrosoft.com` `common` například `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo tokeny nezávislé na tenantovi |
| client_id |Požadovaný |ID aplikace přiřazené vaší aplikaci, když jste ji zaregistrovali ve službě Azure AD. Najdete to na webu Azure Portal. Klikněte na **Službu Azure Active Directory**, klikněte na **Registrace aplikací**, vyberte aplikaci a vyhledejte ID aplikace na stránce aplikace. |
| response_type |Požadovaný |Musí `id_token` obsahovat pro přihlášení OpenID Connect. Může také zahrnovat další response_types, například `code` nebo `token`. |
| scope | Doporučené | Specifikace OpenID Connect vyžaduje `openid`obor , který se překládá k oprávnění "Přihlásit se k" v uzponě souhlasu. Tento a další obory OIDC jsou ignorovány na koncovém bodu v1.0, ale je stále osvědčeným postupem pro klienty kompatibilní se standardy. |
| Nonce |Požadovaný |Hodnota zahrnutá v požadavku vygenerovaná aplikací, která `id_token` je zahrnuta ve výsledném jako deklarace. Aplikace pak můžete ověřit tuto hodnotu ke zmírnění útoků přehrání tokenu. Hodnota je obvykle randomizované, jedinečný řetězec nebo GUID, které lze použít k identifikaci původu požadavku. |
| redirect_uri | Doporučené |Redirect_uri vaší aplikace, kde mohou být odpovědi na ověření odesílány a přijímány vaší aplikací. Musí přesně odpovídat jednomu z redirect_uris, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódována adresou URL. Pokud chybí, uživatelský agent bude odeslán zpět do jednoho z přesměrování identifikátorů URI registrovaných pro aplikaci, náhodně. Maximální délka je 255 bajtů |
| response_mode |optional |Určuje metodu, která by měla být použita k odeslání výsledné authorization_code zpět do aplikace. Podporované hodnoty `form_post` jsou pro `fragment` příspěvek formuláře *HTTP* a pro fragment adresy *URL*. Pro webové aplikace doporučujeme použít `response_mode=form_post` k zajištění nejbezpečnějšího přenosu tokenů do vaší aplikace. Výchozí hodnota pro všechny toky včetně id_token je `fragment`.|
| state |Doporučené |Hodnota zahrnutá v požadavku, která je vrácena v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně generovaná jedinečná hodnota se obvykle používá k [zabránění útokům padělání požadavků na příčce webu](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, jako je například stránka nebo zobrazení, na které se nachází. |
| Výzva |optional |Označuje typ interakce uživatele, která je požadována. V současné době jsou jedinými platnými hodnotami "login", "none" a "consent". `prompt=login`vynutí, aby uživatel na základě této žádosti zadá svá pověření a vypouzuje jednotné přihlašování. `prompt=none`je opak - zajišťuje, že uživatel není prezentován s žádnou interaktivní výzvu vůbec. Pokud požadavek nelze dokončit bezobslužně prostřednictvím jednotného přihlášení, koncový bod vrátí chybu. `prompt=consent`spustí dialogové okno souhlasu OAuth po přihlášení uživatele a požádá uživatele o udělení oprávnění k aplikaci. |
| login_hint |optional |Lze použít k předběžnému vyplnění pole uživatelského jména/e-mailové adresy přihlašovací stránky uživatele, pokud znáte jeho uživatelské jméno předem. Aplikace často používají tento parametr při opětovném ověřování, protože již extrahovaly uživatelské jméno z předchozího přihlášení pomocí `preferred_username` deklarace. |

V tomto okamžiku je uživatel vyzván k zadání svých přihlašovacích údajů a dokončení ověřování.

### <a name="sample-response"></a>Ukázková odpověď

Vzorová odpověď odeslaná `redirect_uri` zadanému v žádosti o přihlášení po ověření uživatele může vypadat takto:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |K `id_token` tomu, co aplikace požadovala. Můžete použít `id_token` k ověření identity uživatele a zahájit relaci s uživatelem. |
| state |Hodnota zahrnutá v požadavku, která je také vrácena v odpovědi tokenu. Náhodně generovaná jedinečná hodnota se obvykle používá k [zabránění útokům padělání požadavků na příčce webu](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, jako je například stránka nebo zobrazení, na které se nachází. |

### <a name="error-response"></a>Odpověď na chybu

Odpovědi na chyby mohou `redirect_uri` být také odeslány, aby je aplikace mohla správně zpracovat:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází, a lze jej použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která může vývojáři pomoci identifikovat hlavní příčinu chyby ověřování. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Chybové kódy chyb pro chyby autorizačního koncového bodu

Následující tabulka popisuje různé kódy chyb, které `error` mohou být vráceny v parametru chybové odpovědi.

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, například chybějící požadovaný parametr. |Opravte a znovu odešlete požadavek. Toto je chyba vývoje a je obvykle zachycena během počátečního testování. |
| unauthorized_client |Klientská aplikace není oprávněna požadovat autorizační kód. |K tomu obvykle dochází, když klientská aplikace není registrovaná ve službě Azure AD nebo není přidána do klienta Azure AD uživatele. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Služby Azure AD. |
| access_denied |Vlastník prostředku odepřel souhlas |Klientská aplikace může upozornit uživatele, že nemůže pokračovat, pokud uživatel nesouhlasí. |
| unsupported_response_type |Autorizační server nepodporuje typ odpovědi v požadavku. |Opravte a znovu odešlete požadavek. Toto je chyba vývoje a je obvykle zachycena během počátečního testování. |
| server_error |Na serveru došlo k neočekávané chybě. |Opakujte požadavek. Tyto chyby mohou být důsledkem dočasných podmínek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné chyby. |
| temporarily_unavailable |Server je dočasně příliš zaneprázdněn pro zpracování požadavku. |Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
| invalid_resource |Cílový prostředek je neplatný, protože neexistuje, Azure AD ho nemůže najít nebo není správně nakonfigurován. |To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Služby Azure AD. |

## <a name="validate-the-id_token"></a>Ověření id_token

Pouhé přijetí `id_token` není dostatečné k ověření uživatele; musíte ověřit podpis a ověřit deklarace identity `id_token` podle požadavků vaší aplikace. Koncový bod Azure AD používá JSON webové tokeny (JWTs) a kryptografie veřejného klíče k podepsání tokenů a ověření, že jsou platné.

Můžete ověřit `id_token` v kódu klienta, ale běžnou praxí `id_token` je odeslat back-endový server a provést ověření tam. 

Můžete také ověřit další deklarace identity v závislosti na vašem scénáři. Mezi běžná ověření patří:

* Zajištění, že se uživatel/organizace zaregistrovala k aplikaci.
* Zajištění, že uživatel má správnou autorizaci/oprávnění pomocí deklarací `wids` nebo. `roles` 
* Zajištění určité síly ověřování došlo, jako je například vícefaktorové ověřování.

Po ověření `id_token`, můžete zahájit relaci s uživatelem a použít `id_token` deklarace v aplikaci k získání informací o uživateli ve vaší aplikaci. Tyto informace mohou být použity pro zobrazení, záznamy, personalizace, atd. Další informace `id_tokens` o deklaracích identity naleznete [v id_tokens AAD](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="send-a-sign-out-request"></a>Odeslání žádosti o odhlášení

Pokud chcete uživatele odhlásit z aplikace, nestačí vymazat soubory cookie aplikace nebo jinak ukončit relaci s uživatelem. Je také nutné přesměrovat `end_session_endpoint` uživatele na pro odhlášení. Pokud se vám to nepodaří, uživatel bude moci znovu ověřit do vaší aplikace bez zadání jejich přihlašovací údaje znovu, protože budou mít platnou relaci jednotného přihlášení s koncovým bodem Azure AD.

Uživatele můžete jednoduše přesměrovat `end_session_endpoint` na dokument metadat OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametr |  | Popis |
| --- | --- | --- |
| post_logout_redirect_uri |Doporučené |Adresa URL, na kterou by měl být uživatel po úspěšném odhlášení přesměrován.  Tato adresa URL se musí shodovat s jedním z identifikátorů URI přesměrování registrovaných pro vaši aplikaci na portálu pro registraci aplikací.  Pokud *post_logout_redirect_uri* není zahrnuta, uživateli se zobrazí obecná zpráva. |

## <a name="single-sign-out"></a>Jednotné odhlašování

Při přesměrování uživatele na `end_session_endpoint`, Azure AD vymaže relace uživatele z prohlížeče. Uživatel však může být stále přihlášen k jiným aplikacím, které používají Azure AD pro ověřování. Chcete-li povolit tyto aplikace odhlásit uživatele současně, Azure AD `LogoutUrl` odešle požadavek HTTP GET na registrované všechny aplikace, které je uživatel aktuálně přihlášen. Aplikace musí reagovat na tento požadavek vymazáním libovolné `200` relace, která identifikuje uživatele a vrací odpověď. Pokud chcete podporovat jednotné odhlášení ve vaší aplikaci, je nutné implementovat takové `LogoutUrl` v kódu aplikace. Můžete nastavit `LogoutUrl` z portálu Azure:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyberte službu Active Directory kliknutím na účet v pravém horním rohu stránky.
3. Na levém navigačním panelu zvolte **Azure Active Directory**, pak zvolte Registrace **aplikací** a vyberte aplikaci.
4. Klikněte na **Nastavení**, potom **na Vlastnosti** a najděte textové pole **Url odhlášení.** 

## <a name="token-acquisition"></a>Získání tokenu
Mnoho webových aplikací musí nejen přihlásit uživatele, ale také přístup k webové službě jménem tohoto uživatele pomocí OAuth. Tento scénář kombinuje OpenID Connect pro ověřování uživatelů `authorization_code` a současně získává, který lze použít k získání `access_tokens` pomocí [Toku autorizačního kódu OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="get-access-tokens"></a>Získat přístupové tokeny
Chcete-li získat přístupové tokeny, je třeba upravit požadavek na přihlášení shora:

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

Zahrnutím oborů oprávnění do `response_type=code+id_token`požadavku `authorize` a používání koncový bod zajistí, že uživatel souhlasil `scope` s oprávněními uvedenými v parametru dotazu a vrátí aplikaci autorizační kód k výměně za přístupový token.

### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď odeslaná `redirect_uri` `response_mode=form_post`na using vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |K `id_token` tomu, co aplikace požadovala. Můžete použít `id_token` k ověření identity uživatele a zahájit relaci s uživatelem. |
| kód |Authorization_code, které aplikace požadovala. Aplikace můžete použít autorizační kód požádat o přístupový token pro cílový prostředek. Authorization_codes jsou krátkodobé a obvykle vyprší asi po 10 minutách. |
| state |Pokud je parametr stavu zahrnut a požadavek, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v požadavku a odpovědi jsou identické. |

### <a name="error-response"></a>Odpověď na chybu

Odpovědi na chyby mohou `redirect_uri` být také odeslány, aby je aplikace mohla správně zpracovat:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází, a lze jej použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která může vývojáři pomoci identifikovat hlavní příčinu chyby ověřování. |

Popis možných kódů chyb a jejich doporučenou akci klienta naleznete v [tématu Chybové kódy pro chyby autorizačního bodu](#error-codes-for-authorization-endpoint-errors).

Jakmile získáte autorizaci `code` a `id_token`, můžete se přihlásit k uživateli a získat [přístupové tokeny](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) jeho jménem. Chcete-li se přihlásit k `id_token` uživateli, musíte ověřit přesně tak, jak je popsáno výše. Chcete-li získat přístupové tokeny, můžete postupovat podle kroků popsaných v části "Pomocí autorizačního kódu požádat o přístupový token" v naší [dokumentaci o toku kódu OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Další kroky

* Další informace o [přístupových tokenech](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* Další informace o [ `id_token` deklaracích a](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
