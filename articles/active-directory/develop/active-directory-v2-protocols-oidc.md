---
title: Azure Active Directory v2.0 a protokolu OpenID Connect | Microsoft Docs
description: Vytvoření webové aplikace pomocí Azure AD v2.0 implementace ověřovacího protokolu OpenID Connect.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a0cd077b1c6530c5794c92f131dffb814f5b341d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157713"
---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>Azure Active Directory v2.0 a protokolu OpenID Connect
OpenID Connect je ověřovací protokol založený na OAuth 2.0, který můžete použít k bezpečně přihlášení uživatele k webové aplikaci. Použijete-li koncového bodu v2.0 implementaci OpenID Connect, můžete přidat přihlášení a rozhraní API pro přístup k vaší webové aplikace. V tomto článku jsme ukazují, jak chcete tento bez ohledu na jazyk. Jsme popisují, jak odesílat a přijímat zprávy HTTP bez použití žádné knihovny Microsoft open-source.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny scénáře Azure Active Directory a funkce. Pokud chcete zjistit, zda byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
> 
> 

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) rozšiřuje OAuth 2.0 *autorizace* protokol bude použit jako *ověřování* protokolu, takže můžete provést jeden přihlašování pomocí metody OAuth. OpenID Connect zavádí koncepci *ID token*, což je token zabezpečení, která umožňuje klientovi k ověření identity uživatele. ID token také získá profil základní informace o uživateli. Protože OpenID Connect rozšiřuje OAuth 2.0, můžete bezpečně získat aplikace *přístup tokeny*, které lze použít pro přístup k prostředkům, které jsou zabezpečeny [serveru ověřování](active-directory-v2-protocols.md#the-basics). Koncový bod v2.0 také umožňuje aplikacím jiných výrobců, které jsou registrovány k vydání tokenů přístupu k zabezpečeným prostředkům, například webovým rozhraním API Azure AD. Další informace o tom, jak nastavit aplikace k vydání tokenů přístupu najdete v tématu [postup registrace aplikace s koncovým bodem v2.0](active-directory-v2-app-registration.md). Doporučujeme vám, že používáte OpenID Connect, pokud vytváříte [webové aplikace](active-directory-v2-flows.md#web-apps) která je hostovaná na serveru a získat přístup prostřednictvím prohlížeče.

## <a name="protocol-diagram-sign-in"></a>Diagram protokolu: přihlášení
Nejzákladnější toku přihlášení je znázorněno na obrázku další kroky. Jsme popisují každý krok podrobně v tomto článku.

![Protokolu OpenID Connect: přihlášení](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Načtení dokumentu metadat OpenID Connect
Popisuje dokumentu metadat, který obsahuje většinu požadované informace o aplikaci k provedení přihlášení, OpenID Connect. To zahrnuje informace, jako jsou adresy URL používat a umístění služby veřejného podpisového klíče. Pro koncový bod v2.0 to je dokument metadat OpenID Connect, který byste měli použít:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP] 
> Vyzkoušet Klikněte na tlačítko [ https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration ](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) zobrazíte `common` konfigurace klientů. 
>

`{tenant}` Může trvat jednu ze čtyř hodnot:

| Hodnota | Popis |
| --- | --- |
| `common` |Uživatelé s účtem Microsoft osobní i pracovní nebo školní účet ze služby Azure Active Directory (Azure AD) můžete přihlásit k aplikaci. |
| `organizations` |Pouze uživatelé s pracovní nebo školní účty z Azure AD se můžete přihlásit k aplikaci. |
| `consumers` |Pouze uživatelé s osobní účet Microsoft se můžete přihlásit k aplikaci. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` |Jenom uživatelé s pracovní nebo školní účet z konkrétní Azure AD můžete klienta přihlásit k aplikaci. Můžete použít buď domény popisný název klienta Azure AD, nebo identifikátor GUID klienta. |

Metadata jsou jednoduché dokumentu JavaScript Object Notation (JSON). Viz následující fragment kódu pro příklad. Jsou podrobně popsány v tomto fragmentu kódu obsah [OpenID Connect specifikace](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

Obvykle byste tento dokument metadat použijte ke konfiguraci OpenID Connect knihovny nebo SDK; knihovny využije metadata ke své práci. Pokud nepoužíváte knihovnu před sestavením OpenID Connect, můžete však postupujte podle kroků v zbývající část tohoto článku provést přihlášení ve webové aplikaci pomocí koncového bodu v2.0.

## <a name="send-the-sign-in-request"></a>Odeslání žádosti o přihlášení
Když webové aplikace potřebuje k ověření uživatele, se přímá uživateli `/authorize` koncový bod. Tento požadavek je podobná první rameno [toku kódu autorizace OAuth 2.0](active-directory-v2-protocols-oauth-code.md), se tyto důležité rozdíly:

* Musí zahrnovat požadavek `openid` oboru v `scope` parametr.
* `response_type` Musí obsahovat parametr `id_token`.
* Musí zahrnovat požadavek `nonce` parametr.

> [!IMPORTANT]
> V pořadí, které se úspěšně žádosti o token ID registrace aplikací v [portálu pro registraci](https://apps.dev.microsoft.com) musí mít **[implicitní grant](active-directory-v2-protocols-implicit.md)** povolené pro webového klienta. Pokud není povolený, `unsupported_response` bude vrácena chyba: "pro vstupní parametr 'response_type' zadaná hodnota není povolena pro tohoto klienta. Očekávaná hodnota je 'kód."

Příklad:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Kliknutím na následující odkaz k provedení této žádosti. Po přihlášení, prohlížeč bude přesměrován na https://localhost/myapp/, k tokenu ID na panelu Adresa. Všimněte si, že tento požadavek používá `response_mode=fragment` (pouze pro demonstrační účely). Doporučujeme vám, že používáte `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| tenant |Požaduje se |Můžete použít `{tenant}` hodnotu v cestě požadavku určovat, kdo může přihlásit k aplikaci. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů. Další informace najdete v tématu [protokolu Základy](active-directory-v2-protocols.md#endpoints). |
| client_id |Požaduje se |ID aplikace, která [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) přiřazené vaší aplikaci. |
| response_type |Požaduje se |Musí zahrnovat `id_token` pro OpenID Connect přihlášení. Může také obsahovat jiné `response_types` hodnoty, jako například `code`. |
| redirect_uri |Doporučené |Identifikátor URI přesměrování vaší aplikace, kde můžete odesílat a přijímat aplikace odpovědi ověřování. Ho musí přesně shodovat s jedním přesměrování identifikátory URI, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódovaná adresou URL. |
| scope |Požaduje se |Seznam obory oddělených mezerami. Pro OpenID Connect, musí obsahovat rozsah `openid`, což znamená, že je na oprávnění "Přihlásit" v souhlasu uživatelského rozhraní. Také mohou zahrnovat jiné obory v této žádosti o žádosti o souhlas. |
| hodnotu Nonce |Požaduje se |Hodnota, zahrnuté v požadavku, vygenerovaný aplikací, který bude zahrnut v výsledná hodnota požadavku id_token jako deklarace identity. Aplikace můžete ověřit tuto hodnotu s snížit riziko útoků opětovného přehrání tokenu. Hodnota je obvykle náhodnou jedinečného řetězce, který můžete použít k identifikaci původcem požadavku. |
| response_mode |Doporučené |Určuje metodu, která se používá k odeslání výsledné autorizační kód zpět do vaší aplikace. Může být `form_post` nebo `fragment`. U webových aplikací, doporučujeme použít `response_mode=form_post`, aby bylo zajištěno nejbezpečnější přenos tokeny do vaší aplikace. |
| state |Doporučené |Hodnota, zahrnuté v požadavku, který bude také vrácen v odpovědi tokenu. Může být řetězec o délce požadovaný obsah. Náhodně generované jedinečné hodnoty se obvykle používá ke [zabránit útokům padělání požadavku posílaného mezi weby](http://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky nebo zobrazení, které uživatel byl na. |
| řádku |Nepovinné |Označuje typ interakce s uživatelem, který je vyžadován. V tuto chvíli pouze platné hodnoty jsou `login`, `none`, a `consent`. `prompt=login` Vynutí deklarace identity uživatele k zadání přihlašovacích údajů tohoto požadavku, která Neguje jednotné přihlašování. `prompt=none` Deklarace identity je jako opak. Tento požadavek zajistí, že uživatel není uveden s žádné interaktivní výzvu jakkoli. Pokud požadavek nemůže být dokončena bez upozornění pomocí jednotného přihlašování, koncový bod v2.0 vrátí chybu. `prompt=consent` Deklarace identity se aktivuje dialogové okno souhlas OAuth po přihlášení uživatele. Dialogové okno požádá uživatele a udělit oprávnění k aplikaci. |
| login_hint |Nepovinné |Tento parametr můžete předem vyplnit pole uživatelské jméno a e-mailové adresy na stránce přihlášení pro uživatele, pokud víte, uživatelské jméno předem. Často aplikace tento parametr použijte během opětovné ověření po již extrahování uživatelské jméno ze starší sign-in pomocí `preferred_username` deklarací identity. |
| domain_hint |Nepovinné |Tato hodnota může být `consumers` nebo `organizations`. Pokud zahrnuty, přeskočí proces zjišťování na základě e-mailu, který uživatel prochází na v2.0 přihlašovací stránce pro mírně zefektivnění činnost koncového uživatele. Často aplikace použijte tento parametr během opětovné ověření extrahováním `tid` deklarací z tokenu ID. Pokud `tid` deklarace, hodnota je `9188040d-6c67-4c5b-b112-36a304b66dad` (Account Microsoft klienta příjemce), použijte `domain_hint=consumers`. Jinak použijte `domain_hint=organizations`. |

V tomto okamžiku bude uživatel vyzván k zadání přihlašovacích údajů a dokončení ověření. Koncový bod v2.0 ověřuje, že uživatel souhlasí s tím oprávnění uvedené v `scope` parametr dotazu. Pokud uživatel nebyla přijata některé z těchto oprávnění, koncového bodu v2.0 vyzve uživatele o souhlas pro požadovaná oprávnění. Další informace o [oprávnění, souhlasu a víceklientské aplikace](active-directory-v2-scopes.md).

Poté, co uživatel ověří a uděluje souhlas, koncový bod v2.0 vrátí odpověď do vaší aplikace na uvedené identifikátor URI přesměrování pomocí metody popsané v `response_mode` parametr.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď při použití `response_mode=form_post` vypadá podobně jako tento:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |ID token, který požadované aplikace. Můžete použít `id_token` parametr pro ověření identity uživatele a zahájit relaci s uživatelem. Další informace o ID tokeny a jejich obsah, najdete v článku [koncového bodu v2.0 tokeny odkaz](active-directory-v2-tokens.md). |
| state |Pokud `state` parametr je zahrnuta v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda jsou identické hodnoty stavu v požadavku a odpovědi. |

### <a name="error-response"></a>Chybové odpovědi
Chybové odpovědi mohou být také odeslány identifikátor URI přesměrování, aby aplikace dokáže zpracovat je. Chybnou odpověď vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, které můžete použít ke klasifikaci typů chyb, ke kterým došlo a reagování na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomoct určit hlavní příčinu chyby ověřování. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Kódy chyb pro chyb koncový bod autorizace
Následující tabulka popisuje chybové kódy, které mohou být vráceny v `error` parametr odpovědi na chybu:

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, například chybějící, vyžaduje parametr. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoj, který je obvykle zachycena během počáteční testování. |
| unauthorized_client |Klientská aplikace nemůže požádat o autorizační kód. |K tomu obvykle dojde, pokud klientská aplikace není registrovaný ve službě Azure AD nebo nebyla přidána do klienta Azure AD pro uživatele. Aplikace můžete vyzvat uživatele s pokyny k instalaci aplikace a přidejte ji do služby Azure AD. |
| ACCESS_DENIED |Vlastník prostředku odepřen souhlasu. |Klientská aplikace může upozornit uživatele, který nemůže pokračovat, pokud uživatel souhlasí. |
| unsupported_response_type |Autorizace serveru nepodporuje typ odpovědi v požadavku. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoj, který je obvykle zachycena během počáteční testování. |
| server_error |Serveru došlo k neočekávané chybě. |Opakujte žádost. Tyto chyby může být důsledkem dočasné stavy. Klientská aplikace mohou vysvětlit pro uživatele, že odpověď se zpožďuje kvůli dočasné chybě. |
| temporarily_unavailable |Server je dočasně zaneprázdněn pro zpracování požadavku. |Opakujte žádost. Klientská aplikace mohou vysvětlit pro uživatele, že odpověď se zpožďuje kvůli dočasné podmínce. |
| invalid_resource |Cílový prostředek je neplatný, protože buď neexistuje, Azure AD ji nemůže najít, nebo není správně nakonfigurována. |To znamená, že k prostředku, pokud existuje, nebyl nakonfigurován v klientovi. Aplikace můžete vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Azure AD. |

## <a name="validate-the-id-token"></a>Ověřit ID token
Přijetí tokenu ID není dostatečná k ověření uživatele. Musíte také ověřit ID token podpisu a ověřit deklarace identity v tokenu podle požadavků vaší aplikace. Koncový bod v2.0 používá [webové tokeny JSON (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografie využívající veřejného klíče pro podepisování tokenů a ověřte, zda je platný.

Můžete se ověřit ID token v kódu klienta, ale běžnou praxí je odeslat ID token back-end serverů a došlo k ověření. Po jste ověření podpisu tokenu ID, budete muset ověřit pár deklarací identity. Další informace, včetně informace o [ověřování tokenů](active-directory-v2-tokens.md#validating-tokens) a [důležité informace o podepisování výměna klíče](active-directory-v2-tokens.md#validating-tokens), najdete v článku [v2.0 tokeny odkaz](active-directory-v2-tokens.md). Doporučujeme používat knihovnu k analýze a ověření tokenů. Nejméně jedna z těchto knihoven k dispozici je pro většinu jazyky a platformy.
<!--TODO: Improve the information on this-->

Můžete také ověřit další deklarace identity, v závislosti na vašem scénáři. Některé běžné ověření patří:

* Zkontrolujte, zda uživatel nebo organizace má přihlášeni pro aplikaci.
* Zkontrolujte, zda má uživatel požadované oprávnění nebo autorizace.
* Ujistěte se, že došlo k sílu ověřování, jako je vícefaktorové ověřování.

Další informace o deklaracích identity v tokenu ID najdete v tématu [koncového bodu v2.0 tokeny odkaz](active-directory-v2-tokens.md).

Po zcela ověření tokenu ID můžete začít relaci s uživatelem. Chcete-li získat informace o uživateli ve vaší aplikaci použijte deklarace identity v ID tokenu. Tyto informace můžete použít pro zobrazení, záznamy, oprávnění a tak dále.

## <a name="send-a-sign-out-request"></a>Poslat žádost o odhlášení
Když chcete se odhlásit uživatele z vaší aplikace, není dostatečná k vymazání souborů cookie vaší aplikace nebo v opačném případě ukončení relace uživatele. Musíte také přesměruje uživatele na koncový bod v2.0 se odhlásíte. Pokud to neuděláte, znovu ověří se uživatel do aplikace bez nutnosti zadávat své přihlašovací údaje znovu, protože budou mít platný jedné přihlašovací relace s koncovým bodem v2.0.

Můžete přesměrovat uživatele `end_session_endpoint` uvedené v dokumentu metadat OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametr | Podmínka | Popis |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Doporučené | Adresu URL, kterou je uživatel přesměrován na po úspěšně odhlášení. Pokud parametr neuvedete, uživateli se zobrazí obecná zpráva, že je generován koncového bodu v2.0. Tato adresa URL se musí shodovat s jedním přesměrování identifikátory URI zaregistrované pro svoji aplikaci v portálu pro registraci aplikace. |

## <a name="single-sign-out"></a>Jednotné odhlašování
Když přesměruje uživatele na `end_session_endpoint`, koncový bod v2.0 vymaže relace uživatele z prohlížeče. Ale může se uživatel přihlášen stále k ostatním aplikacím, které používají účty Microsoft pro ověřování. Chcete-li povolit těchto aplikací na přihlášení uživatele se současně, v2.0 koncový bod odešle požadavek HTTP GET na zaregistrovanou `LogoutUrl` všech aplikací, které uživatel je aktuálně přihlášený k. Aplikace musí reagovat na tento požadavek zrušením jakékoli relace, který identifikuje uživatele a vrácení `200` odpovědi. Pokud chcete limit podporují jednotné přihlašování v aplikaci, je nutné implementovat, `LogoutUrl` v kódu aplikace. Můžete nastavit `LogoutUrl` z portálu pro registraci aplikace.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagram protokolu: přístup k získání tokenu
Mnoho webových aplikací je nutné nejen přihlásit uživatele v, ale také přístup k webové službě jménem uživatele pomocí OAuth. Tento scénář kombinuje OpenID Connect pro ověřování uživatelů při získávání současně autorizační kód, který vám pomůže získat přístupové tokeny, pokud používáte toku kódu autorizace OAuth.

Úplné přihlášení a tokenu pořízení tok OpenID Connect bude vypadat podobně jako další diagramu. Jsme popisují každý krok podrobně v další části tohoto článku.

![Protokolu OpenID Connect: získání tokenu](../../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>Získat přístupové tokeny
Získat přístupové tokeny, upravte požádat o přihlášení:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Kliknutím na následující odkaz k provedení této žádosti. Po přihlášení, váš prohlížeč je přesměrován na https://localhost/myapp/, s ID tokenu a kódu v panelu Adresa. Všimněte si, že tento požadavek používá `response_mode=fragment` (pouze pro demonstrační účely). Doporučujeme vám, že používáte `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

Zahrnutím obory oprávnění v požadavku a pomocí `response_type=id_token code`, koncový bod v2.0 zajistí, že uživatel souhlasí s tím oprávnění uvedené v `scope` parametr dotazu. Vrátí autorizační kód do vaší aplikace do systému exchange pro přístupový token.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď z pomocí `response_mode=form_post` vypadá podobně jako tento:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |ID token, který požadované aplikace. ID token můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. Zjistíte další podrobnosti o ID tokeny a jejich obsah v [koncového bodu v2.0 tokeny odkaz](active-directory-v2-tokens.md). |
| Kód |Autorizační kód, který požadované aplikace. Aplikace můžete autorizační kód vyžádat token přístupu pro cílový prostředek. Je velmi krátkodobou autorizační kód. Autorizační kód se obvykle vyprší během přibližně 10 minut. |
| state |Pokud parametr stavu je obsažena v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda jsou identické hodnoty stavu v požadavku a odpovědi. |

### <a name="error-response"></a>Chybové odpovědi
Chybové odpovědi mohou být také odeslány identifikátor URI přesměrování, aby aplikace můžete správně zpracovat. Chybnou odpověď vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, které můžete použít ke klasifikaci typů chyb, ke kterým došlo a reagování na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomoct určit hlavní příčinu chyby ověřování. |

Popis možných kódů chyb a klienta doporučenou odpovědí najdete v tématu [kódy chyb pro chyb koncový bod autorizace](#error-codes-for-authorization-endpoint-errors).

Pokud máte autorizační kód a ID token, můžete uživatele přihlásit a získat tokeny přístupu jejich jménem. Chcete-li přihlásit uživatele v, je třeba ověřit ID token [přesně tak, jak je popsáno](#validate-the-id-token). Chcete-li získat přístupové tokeny, postupujte podle kroků popsaných v našich [dokumentace k protokolu OAuth](active-directory-v2-protocols-oauth-code.md#request-an-access-token).
