---
title: Autorizovat přístup k webové aplikaci pomocí OpenID Connect & Azure AD | Microsoft Docs
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
ms.openlocfilehash: b719e866852d2e865c16c62fddd8c549ae505b7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85551547"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizace přístupu k webovým aplikacím s použitím OpenID Connect a Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) je jednoduchá vrstva identity postavená nad protokolem OAuth 2,0. OAuth 2,0 definuje mechanismy pro získání a použití [**přístupových tokenů**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) pro přístup k chráněným prostředkům, ale nedefinuje standardní metody pro poskytování informací o identitě. OpenID Connect implementuje ověřování jako rozšíření procesu autorizace OAuth 2,0. Poskytuje informace o koncovém uživateli ve formě [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) , která ověřuje identitu uživatele a poskytuje základní informace o profilu uživatele.

OpenID Connect je naše doporučení, pokud vytváříte webovou aplikaci, která je hostována na serveru a přístupná přes prohlížeč.

## <a name="register-your-application-with-your-ad-tenant"></a>Registrace aplikace pomocí tenanta AD
Nejdřív svou aplikaci zaregistrujete u svého tenanta Azure Active Directory (Azure AD). Pro svou aplikaci tak získáte ID a umožníte jí přijímat tokeny.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
   
1. Zvolte svého tenanta Azure AD tak, že vyberete svůj účet v pravém horním rohu stránky a potom vyberete možnost navigace v **adresáři přepínače** a pak vyberete příslušného tenanta. 
   - Tento krok přeskočte, pokud máte ve svém účtu jenom jednoho tenanta Azure AD, nebo pokud jste už vybrali příslušného tenanta Azure AD.
   
1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.
   
1. V nabídce **Azure Active Directory** vlevo vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
   
1. Postupujte podle zobrazených pokynů a vytvořte novou aplikaci. Nezáleží na tom, jestli se jedná o aplikaci webové aplikace nebo veřejného klienta (mobilní & Desktop) pro tento kurz, ale pokud byste chtěli mít konkrétní příklady pro webové aplikace nebo veřejné klientské aplikace, podívejte se na naše [rychlé starty](v1-overview.md).
   
   - **Název** je název aplikace, který aplikaci popisuje koncovým uživatelům.
   - V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
   - Zadejte **identifikátor URI pro přesměrování**. U webových aplikací se jedná o základní adresu URL vaší aplikace, kde se uživatelé můžou přihlásit.  Například, `http://localhost:12345`. U veřejného klienta (Mobile & Desktop) Azure AD používá k vracení odpovědí na tokeny. Zadejte hodnotu specifickou pro vaši aplikaci.  Například, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Po dokončení registrace vám Azure AD přiřadí aplikaci jedinečný identifikátor klienta ( **ID aplikace**). Tuto hodnotu budete potřebovat v dalších částech, proto je zkopírujte ze stránky aplikace.
   
1. Chcete-li najít aplikaci v Azure Portal, vyberte možnost **Registrace aplikací** a pak vyberte možnost **Zobrazit všechny aplikace**.

## <a name="authentication-flow-using-openid-connect"></a>Tok ověřování pomocí OpenID Connect

Nejzákladnější tok přihlášení obsahuje následující postup – každý z nich je podrobně popsán níže.

![Tok ověřování OpenId Connect](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>Dokument metadat OpenID Connect

OpenID Connect popisuje dokument metadat, který obsahuje většinu informací požadovaných pro aplikaci k provedení přihlášení. Zahrnuje to i informace, jako jsou adresy URL, které se mají použít, a umístění veřejných podpisových klíčů služby. Dokument metadat OpenID Connect najdete na adrese:

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
Metadata jsou jednoduchý dokument JavaScript Object Notation (JSON). Příklad naleznete v následujícím fragmentu kódu. Obsah fragmentu je plně popsán ve [specifikaci OpenID Connect](https://openid.net). Pokud nechcete, aby místo typu {tenant} poskytovala ID tenanta, je třeba, aby se v `common` objektu JSON vrátily identifikátory URI specifické pro tenanta.

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

Pokud vaše aplikace obsahuje vlastní podpisové klíče v důsledku použití funkce [mapování deklarací](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) , musíte připojit `appid` parametr dotazu obsahující ID aplikace, aby bylo možné přejít `jwks_uri` k informacím o podpisovém klíči vaší aplikace. Příklad: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` obsahuje a `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Pokud vaše webová aplikace potřebuje ověřit uživatele, musí uživatele nasměrovat na `/authorize` koncový bod. Tento požadavek je podobný prvnímu průchodu [toku autorizačního kódu OAuth 2,0](v1-protocols-oauth-code.md), a to z několika důležitých rozdílů:

* Požadavek musí zahrnovat obor `openid` v `scope` parametru.
* `response_type`Parametr musí obsahovat `id_token` .
* Požadavek musí obsahovat `nonce` parametr.

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

| Parametr | Typ | Description |
| --- | --- | --- |
| tenant |vyžadováno |`{tenant}`Hodnotu v cestě k požadavku lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou identifikátory klientů, například `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` nebo `common` pro tokeny nezávislé na tenantovi. |
| client_id |vyžadováno |ID aplikace přiřazené vaší aplikaci, když ji zaregistrujete ve službě Azure AD. Najdete ho v Azure Portal. Klikněte na **Azure Active Directory**, klikněte na **Registrace aplikací**, vyberte aplikaci a na stránce aplikace vyhledejte ID aplikace. |
| response_type |vyžadováno |Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Může také zahrnovat jiné response_types, například `code` nebo `token` . |
| scope | doporučil | Specifikace OpenID Connect vyžaduje obor `openid` , který se v uživatelském rozhraní souhlasu převede na oprávnění přihlásit se. Tento a další obory OIDC se na koncovém bodu verze 1.0 ignorují, ale pro klienty kompatibilní se standardem je stále doporučený postup. |
| generované |vyžadováno |Hodnota obsažená v požadavku, která je vygenerovaná aplikací, která je zahrnutá ve výsledné `id_token` formě deklarace identity. Aplikace pak může tuto hodnotu ověřit a zmírnit tak útoky na opakované přehrání tokenů. Hodnota je obvykle náhodný, jedinečný řetězec nebo identifikátor GUID, který lze použít k identifikaci původu žádosti. |
| redirect_uri | doporučil |Redirect_uri vaší aplikace, ve které vaše aplikace může odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z redirect_uris, který jste zaregistrovali na portálu, s výjimkou musí být zakódovaný URL. Pokud chybí, uživatelský agent se pošle zpátky k jednomu z identifikátorů URI pro přesměrování zaregistrovaných pro aplikaci náhodně. Maximální délka je 255 bajtů. |
| response_mode |optional |Určuje metodu, která se má použít k odeslání výsledného authorization_code zpět do vaší aplikace. Podporované hodnoty jsou `form_post` pro *post formuláře http* a `fragment` pro *fragment adresy URL*. U webových aplikací doporučujeme použít `response_mode=form_post` k zajištění nejbezpečnějšího přenosu tokenů do aplikace. Výchozí hodnota pro libovolný tok včetně id_token `fragment` .|
| state |doporučil |Hodnota obsažená v požadavku, která je vrácena v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně vygenerovaná jedinečná hodnota se obvykle používá k [prevenci útoků proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
| výzv |optional |Určuje typ interakce uživatele, která je povinná. V současné době jsou jedinými platnými hodnotami "login," none "a" souhlas ". `prompt=login` vynutí, aby uživatel zadal přihlašovací údaje k danému požadavku a současně se jedná o operátor jednotného přihlašování. `prompt=none` je opakem, zajišťuje, že uživatel není zobrazen s žádnou interaktivní výzvou. Pokud požadavek nejde v tichém režimu dokončit pomocí jednotného přihlašování, vrátí koncový bod chybu. `prompt=consent` spustí dialog souhlasu OAuth po přihlášení uživatele a požádá uživatele, aby aplikaci udělil oprávnění. |
| login_hint |optional |Dá se použít k předvyplnění pole uživatelské jméno a e-mailová adresa přihlašovací stránky pro uživatele, pokud znáte své uživatelské jméno předem. Aplikace často používají tento parametr během opakovaného ověřování, který už z předchozího přihlášení extrahuje uživatelské jméno, a to pomocí `preferred_username` deklarace identity. |

V tomto okamžiku se uživateli zobrazí výzva k zadání přihlašovacích údajů a dokončení ověřování.

### <a name="sample-response"></a>Ukázková odpověď

Ukázková odpověď, která se pošle do `redirect_uri` zadané v žádosti o přihlášení po ověření uživatele, může vypadat takto:

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |`id_token`Požadovaná aplikace Můžete použít `id_token` k ověření identity uživatele a zahájení relace s uživatelem. |
| state |Hodnota obsažená v požadavku, která je také vrácena v odpovědi tokenu. Náhodně vygenerovaná jedinečná hodnota se obvykle používá k [prevenci útoků proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |

### <a name="error-response"></a>Chybová odezva

Odpovědi na chyby mohou být také odesílány do, `redirect_uri` aby je aplikace mohla správně zpracovat:

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

Následující tabulka popisuje různé chybové kódy, které mohou být vráceny v `error` parametru chybové odpovědi.

| Kód chyby | Description | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, například chybějící požadovaný parametr. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoje, která se obvykle zachycuje při počátečním testování. |
| unauthorized_client |Klientská aplikace nemá oprávnění vyžadovat autorizační kód. |K tomu obvykle dochází, když klientská aplikace není registrovaná v Azure AD nebo není přidaná do tenanta Azure AD uživatele. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| access_denied |Vlastník prostředku zamítl souhlas. |Klientská aplikace může uživatele informovat, že nemůže pokračovat, dokud se uživatel nesouhlasí. |
| unsupported_response_type |Autorizační Server v žádosti nepodporuje typ odpovědi. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoje, která se obvykle zachycuje při počátečním testování. |
| server_error |Na serveru došlo k neočekávané chybě. |Opakujte požadavek. Tyto chyby mohou být způsobeny dočasnými podmínkami. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné chyby. |
| temporarily_unavailable |Server je dočasně zaneprázdněný pro zpracování žádosti. |Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
| invalid_resource |Cílový prostředek není platný, protože neexistuje, Azure AD ho nemůže najít nebo není správně nakonfigurovaný. |To znamená, že pokud tento prostředek existuje, není v tenantovi nakonfigurovaný. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |

## <a name="validate-the-id_token"></a>Ověřit id_token

Pouze příjem `id_token` nestačí k ověření uživatele; je nutné ověřit podpis a ověřit deklarace identity podle `id_token` požadavků na aplikaci. Koncový bod Azure AD využívá webové tokeny JSON (JWTs) a kryptografii s veřejným klíčem k podepisování tokenů a k ověření, že jsou platné.

Můžete si vybrat, že se má `id_token` v kódu klienta ověřit, ale běžný postup je odeslat `id_token` na back-end Server a provést ověření tam. 

V závislosti na vašem scénáři taky můžete chtít ověřit další deklarace identity. Mezi běžná ověření patří:

* Zajistěte, aby se uživatel nebo organizace zaregistrovali do aplikace.
* Zajistěte, aby měl uživatel správnou autorizaci `wids` nebo oprávnění pomocí `roles` deklarací identity nebo. 
* Bylo zajištěno, že došlo k určité síle ověřování, jako je například Multi-Factor Authentication.

Po ověření `id_token` můžete spustit relaci s uživatelem a pomocí deklarací v `id_token` nástroji získat informace o uživateli v aplikaci. Tyto informace lze použít pro zobrazení, záznamy, přizpůsobení atd. Pokud chcete získat další informace o `id_tokens` deklaracích a, přečtěte si [id_tokens AAD](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

Pokud chcete uživatele podepsat z aplikace, není dostačující vymazat soubory cookie vaší aplikace nebo jinak ukončit relaci s uživatelem. Také je nutné přesměrovat uživatele na `end_session_endpoint` účet pro odhlášení. Pokud to neuděláte, uživatel bude moct znovu ověřit vaši aplikaci, aniž by musel znovu zadávat přihlašovací údaje, protože budou mít platnou relaci jednotného přihlašování s koncovým bodem Azure AD.

Můžete jednoduše přesměrovat uživatele na `end_session_endpoint` uvedené v dokumentu metadat OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametr | Typ | Description |
| --- | --- | --- |
| post_logout_redirect_uri |doporučil |Adresa URL, na kterou má být uživatel přesměrován po úspěšném odhlášení.  Tato adresa URL se musí shodovat s jedním z identifikátorů URI přesměrování registrovaných pro vaši aplikaci na portálu pro registraci aplikací.  Pokud není zahrnut *post_logout_redirect_uri* , zobrazí se uživateli obecná zpráva. |

## <a name="single-sign-out"></a>Jednotné odhlašování

Když uživatele přesměrujete na `end_session_endpoint` , Azure AD vymaže relaci uživatele z prohlížeče. Uživatel se ale může pořád přihlásit k jiným aplikacím, které používají Azure AD k ověřování. Aby mohly tyto aplikace při současném podepsání uživatele podepsat, služba Azure AD pošle požadavek HTTP GET na zaregistrované `LogoutUrl` všechny aplikace, ke kterým je uživatel aktuálně přihlášený. Aplikace musí na tuto žádost reagovat vymazáním jakékoli relace, která uživatele identifikuje a vrátí `200` odpověď. Pokud chcete v aplikaci podporovat jednotné odhlašování, musíte implementovat takový `LogoutUrl` kód v kódu vaší aplikace. Můžete nastavit `LogoutUrl` z Azure Portal:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Kliknutím na svůj účet v pravém horním rohu stránky vyberte svoji službu Active Directory.
3. V levém navigačním panelu zvolte **Azure Active Directory**, zvolte **Registrace aplikací** a vyberte svou aplikaci.
4. Klikněte na **Nastavení** a pak na **vlastnosti** a vyhledejte textové pole **odhlašovací adresa URL** . 

## <a name="token-acquisition"></a>Získání tokenu
Mnohé webové aplikace potřebují pouze podepsat uživatele v, ale také k webové službě jménem tohoto uživatele pomocí OAuth. Tento scénář kombinuje OpenID Connect pro ověřování uživatelů, zatímco současně `authorization_code` načítá, který je možné použít k získání `access_tokens` používání [toku autorizačního kódu OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

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

Když do žádosti a pomocí použijete obory oprávnění `response_type=code+id_token` , `authorize` koncový bod zajistí, že uživatel souhlasí s oprávněními uvedenými v `scope` parametru dotazu, a vrátí vaší aplikaci autorizační kód pro výměnu přístupového tokenu.

### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď, která se pošle na `redirect_uri` použití `response_mode=form_post` , vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Popis |
| --- | --- |
| id_token |`id_token`Požadovaná aplikace Můžete použít `id_token` k ověření identity uživatele a zahájení relace s uživatelem. |
| kód |Authorization_code, kterou aplikace požadovala Aplikace může pomocí autorizačního kódu požádat o přístupový token pro cílový prostředek. Authorization_codes jsou krátkodobé a obvykle po 10 minutách vyprší platnost. |
| state |Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

### <a name="error-response"></a>Chybová odezva

Odpovědi na chyby mohou být také odesílány do, `redirect_uri` aby je aplikace mohla správně zpracovat:

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

Jakmile se dostanete k autorizaci `code` a `id_token` , můžete uživatele podepsat a získat [přístupové tokeny](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) jménem. Chcete-li uživatele podepsat v, je nutné ověřit `id_token` přesně výše popsaným způsobem. K získání přístupových tokenů můžete postupovat podle kroků popsaných v části "použití autorizačního kódu k vyžádání přístupového tokenu" v [dokumentaci toku kódu OAuth](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [přístupových tokenech](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* Přečtěte si další informace o [ `id_token` deklaracích identity a](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
