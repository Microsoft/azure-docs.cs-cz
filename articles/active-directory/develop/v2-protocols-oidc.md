---
title: Protokol OpenID Connect – Microsoft Identity Platform | Azure
description: Sestavujte webové aplikace pomocí implementace ověřovacího protokolu OpenID Connect v platformě Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0ed1cb6a080a35fa81c6a859f88d987020c8504c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773327"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity Platform a OpenID Connect Protocol

OpenID Connect je ověřovací protokol založený na OAuth 2,0, který můžete použít k bezpečnému přihlášení uživatele k webové aplikaci. Když použijete implementaci OpenID Connect koncového bodu platformy Microsoft identity, můžete přidat přihlašování a přístup k rozhraní API k vašim webovým aplikacím. Tento článek ukazuje, jak to provést nezávisle na jazyku a popisuje, jak odesílat a přijímat zprávy HTTP bez použití knihoven Microsoft Open-Source Library.

> [!NOTE]
> Koncový bod platformy Microsoft Identity Platform nepodporuje všechny scénáře a funkce Azure Active Directory (Azure AD). Pokud chcete zjistit, jestli byste měli použít koncový bod platformy Microsoft identity, přečtěte si informace o [omezeních platformy Microsoft Identity](active-directory-v2-limitations.md).

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozšiřuje *autorizační* protokol OAuth 2,0, který se použije jako *ověřovací* protokol, abyste mohli provádět jednotné přihlašování pomocí OAuth. OpenID Connect zavádí koncept *tokenu ID*, což je token zabezpečení, který umožňuje klientovi ověřit identitu uživatele. Token ID také získá základní informace o profilu uživatele. Vzhledem k tomu, že OpenID Connect rozšiřuje OAuth 2,0, můžou aplikace bezpečně získat *přístupové tokeny*, které se dají použít k přístupu k prostředkům zabezpečeným [autorizačním serverem](active-directory-v2-protocols.md#the-basics). Koncový bod platformy Microsoft Identity taky umožňuje aplikacím třetích stran, které jsou zaregistrované ve službě Azure AD, vystavovat přístupové tokeny pro zabezpečené prostředky, jako jsou webová rozhraní API. Další informace o tom, jak nastavit aplikaci pro vydávání přístupových tokenů, najdete v tématu [Registrace aplikace s koncovým bodem Microsoft Identity Platform](quickstart-register-app.md). Pokud vytváříte [webovou aplikaci](v2-app-types.md#web-apps) , která je hostována na serveru a přístupná přes prohlížeč, doporučujeme použít OpenID Connect.

## <a name="protocol-diagram-sign-in"></a>Diagram protokolu: přihlášení

Nejzákladnější tok přihlášení má kroky uvedené v dalším diagramu. Jednotlivé kroky jsou podrobně popsané v tomto článku.

![Protokol OpenID Connect: přihlášení](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Načtení dokumentu metadat OpenID Connect

OpenID Connect popisuje dokument metadat, který obsahuje většinu informací potřebných k tomu, aby se aplikace mohla přihlašovat. Zahrnuje to i informace, jako jsou adresy URL, které se mají použít, a umístění veřejných podpisových klíčů služby. V případě koncového bodu Microsoft Identity Platform se jedná o dokument metadat OpenID Connect, který byste měli použít:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Vyzkoušejte si to! Kliknutím na [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) zobrazíte konfiguraci `common` klientů.

`{tenant}` může mít jednu ze čtyř hodnot:

| Hodnota | Popis |
| --- | --- |
| `common` |Uživatelé, kteří mají osobní účet Microsoft a pracovní nebo školní účet z Azure AD, se můžou k aplikaci přihlásit. |
| `organizations` |K aplikaci se můžou přihlásit jenom uživatelé s pracovními nebo školními účty ze služby Azure AD. |
| `consumers` |K aplikaci se můžou přihlásit jenom uživatelé s osobním účet Microsoft. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` | Můžou se přihlásit k aplikaci jenom uživatelům z konkrétního tenanta Azure AD (ať už jsou členy v adresáři s pracovním nebo školním účtem nebo jsou hosty v adresáři s osobním účet Microsoft). Dá se použít popisný název domény tenanta Azure AD nebo identifikátor GUID klienta. Klienta spotřebitelů `9188040d-6c67-4c5b-b112-36a304b66dad`místo `consumers`ho tenanta taky můžete použít.  |

Metadata jsou jednoduchý dokument JavaScript Object Notation (JSON). Příklad naleznete v následujícím fragmentu kódu. Obsah fragmentu je plně popsán ve [specifikaci OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

Pokud vaše aplikace obsahuje vlastní podpisové klíče v důsledku použití funkce [mapování deklarací](active-directory-claims-mapping.md) , musíte připojit parametr `appid` dotazu obsahujícího ID aplikace, aby se `jwks_uri` odkazovalo na informace o podpisovém klíči vaší aplikace. Například: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` obsahuje `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

Obvykle byste tento dokument metadat použili ke konfiguraci knihovny nebo sady SDK OpenID Connect. Knihovna by tato metadata použila k tomu, aby fungovala. Pokud ale nepoužíváte předem vytvořenou knihovnu OpenID Connect, můžete k tomu použít postup ve zbývající části tohoto článku, abyste se mohli přihlásit do webové aplikace pomocí koncového bodu Microsoft Identity Platform.

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Pokud vaše webová aplikace potřebuje ověřit uživatele, může uživatele nasměrovat na koncový bod `/authorize`. Tento požadavek je podobný prvnímu průchodu [toku autorizačního kódu OAuth 2,0](v2-oauth2-auth-code-flow.md), přičemž tyto důležité odlišnosti:

* Požadavek musí v parametru `scope` zahrnovat obor `openid`.
* Parametr `response_type` musí zahrnovat `id_token`.
* Požadavek musí zahrnovat parametr `nonce`.

> [!IMPORTANT]
> Aby bylo možné úspěšně požádat o token ID z koncového bodu/Authorization, musí registrace aplikace na [portálu pro registraci](https://portal.azure.com) mít povolený implicitní udělení id_tokens na kartě ověřování (což nastaví příznak `oauth2AllowIdTokenImplicitFlow` v [manifestu aplikace](reference-app-manifest.md) na `true`). Pokud není povoleno, bude vrácena `unsupported_response`á Chyba: "Zadaná hodnota pro vstupní parametr" response_type "není pro tohoto klienta povolena. Očekávaná hodnota je Code (kód).

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
> Kliknutím na následující odkaz spusťte tento požadavek. Po přihlášení se v prohlížeči přesměruje na `https://localhost/myapp/`s tokenem ID na adresním řádku. Všimněte si, že tato žádost používá `response_mode=fragment` (jenom pro demonstrační účely). Doporučujeme použít `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Můžete použít hodnotu `{tenant}` v cestě požadavku k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů. Další informace najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Požaduje se | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `response_type` | Požaduje se | Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Může také zahrnovat další `response_type` hodnoty, jako je například `code`. |
| `redirect_uri` | Doporučené | Identifikátor URI pro přesměrování vaší aplikace, ve kterém může vaše aplikace odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódovaný v adrese URL. Pokud tato akce není k dispozici, koncový bod vybere jednu registrovanou redirect_uri náhodně, aby bylo možné uživatele odeslat zpět do. |
| `scope` | Požaduje se | Mezerou oddělený seznam oborů. Pro OpenID Connect musí zahrnovat obor `openid`, který v uživatelském rozhraní souhlasu překládá oprávnění přihlásit se. V této žádosti můžete také zahrnout další obory pro žádost o souhlas. |
| `nonce` | Požaduje se | Hodnota obsažená v požadavku, která se vygenerovala aplikací, která se zahrne do výsledné id_token hodnoty jako deklarace. Aplikace může tuto hodnotu ověřit a zmírnit tak útoky na opakované přehrání tokenů. Hodnota je obvykle náhodný jedinečný řetězec, který lze použít k identifikaci původu žádosti. |
| `response_mode` | Doporučené | Určuje metodu, která se má použít k odeslání výsledného autorizačního kódu zpátky do vaší aplikace. Může být `form_post` nebo `fragment`. U webových aplikací doporučujeme použít `response_mode=form_post`, abyste zajistili nejbezpečnější přenos tokenů do aplikace. |
| `state` | Doporučené | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně generovaná jedinečná hodnota se obvykle používá k [zabránění útokům proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informací o stavu uživatele v aplikaci před tím, než k žádosti o ověření dojde, například na stránce nebo zobrazení, na které uživatel byl. |
| `prompt` | Volitelné | Určuje typ interakce uživatele, která je povinná. V tuto chvíli jsou k dispozici pouze platné hodnoty `login`, `none`a `consent`. Deklarace identity `prompt=login` vynutí, aby uživatel zadal přihlašovací údaje k této žádosti, což má za sebou jednotné přihlašování. `prompt=none` deklarace identity je opak. Tato deklarace identity zajišťuje, že uživatel nebude mít žádné interaktivní výzvy na adrese. Pokud se žádost nedá v tichém režimu dokončit pomocí jednotného přihlašování, vrátí koncová platforma Microsoft Identity Platform chybu. Deklarace `prompt=consent` aktivuje dialog souhlasu OAuth poté, co se uživatel přihlásí. Dialogové okno požádá uživatele o udělení oprávnění k aplikaci. |
| `login_hint` | Volitelné | Tento parametr můžete použít k předvyplnění pole uživatelské jméno a e-mailová adresa přihlašovací stránky pro uživatele, pokud znáte uživatelské jméno předem. Aplikace často používají tento parametr během opakovaného ověřování, po již extrakci uživatelského jména z dřívějšího přihlášení pomocí deklarace `preferred_username`. |
| `domain_hint` | Volitelné | Sféra uživatele ve federovaném adresáři.  Tím se přeskočí proces zjišťování na základě e-mailu, který uživatel prochází na přihlašovací stránce, aby bylo poněkud jednodušší uživatelské prostředí. Pro klienty, kteří jsou federované prostřednictvím místního adresáře, jako je AD FS, to často vede k bezproblémovému přihlášení kvůli stávající přihlašovací relaci. |

V tomto okamžiku se uživateli zobrazí výzva k zadání přihlašovacích údajů a dokončení ověřování. Koncový bod platformy Microsoft Identity ověřuje, že uživatel souhlasí s oprávněními uvedenými v parametru dotazu `scope`. Pokud uživatel nesouhlasí s některým z těchto oprávnění, koncový bod platformy Microsoft Identity Platform vyzve uživatele k vyjádření souhlasu s požadovanými oprávněními. Můžete si přečíst další informace o [oprávněních, souhlasu a víceklientské aplikace](v2-permissions-and-consent.md).

Jakmile se uživatel ověří a udělí souhlas, koncový bod platformy Microsoft Identity vrátí odpověď do vaší aplikace na určeném identifikátoru URI přesměrování pomocí metody zadané v parametru `response_mode`.

### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď při použití `response_mode=form_post` vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Popis |
| --- | --- |
| `id_token` | Token ID, který aplikace požadovala Pomocí parametru `id_token` můžete ověřit identitu uživatele a zahájit relaci s uživatelem. Další informace o tokenech ID a jejich obsahu najdete v [referenčních](id-tokens.md)informacích o`id_tokens`. |
| `state` | Pokud je v požadavku zahrnut parametr `state`, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

### <a name="error-response"></a>Chybová odezva

K identifikátoru URI přesměrování se můžou poslat taky odpovědi na chyby, aby je aplikace mohla zpracovat. Reakce na chybu vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým dochází, a k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která vám může pomáhat identifikovat hlavní příčinu chyby ověřování. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Chybové kódy pro chyby koncového bodu autorizace

Následující tabulka obsahuje popis chybových kódů, které lze vrátit v parametru `error` chybové odpovědi:

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| `invalid_request` | Chyba protokolu, například chybějící, povinný parametr. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoje, která se obvykle zachycuje při počátečním testování. |
| `unauthorized_client` | Klientská aplikace nemůže požádat o autorizační kód. |K tomu obvykle dochází, když klientská aplikace není registrovaná v Azure AD nebo není přidaná do tenanta Azure AD uživatele. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| `access_denied` | Vlastník prostředku zamítl souhlas. |Klientská aplikace může uživatele informovat, že nemůže pokračovat, dokud se uživatel nesouhlasí. |
| `unsupported_response_type` |Autorizační Server v žádosti nepodporuje typ odpovědi. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoje, která se obvykle zachycuje při počátečním testování. |
| `server_error` | Na serveru došlo k neočekávané chybě. |Opakujte požadavek. Tyto chyby mohou být způsobeny dočasnými podmínkami. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděná kvůli dočasné chybě. |
| `temporarily_unavailable` | Server je dočasně zaneprázdněný pro zpracování žádosti. |Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
| `invalid_resource` | Cílový prostředek není platný, protože neexistuje, služba Azure AD ho nemůže najít nebo není správně nakonfigurovaná. |To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |

## <a name="validate-the-id-token"></a>Ověřit token ID

Pouze příjem id_token nestačí k ověření uživatele; je nutné ověřit podpis id_token a ověřit deklarace identity v tokenu podle požadavků vaší aplikace. Koncový bod platformy Microsoft Identity využívá [webové tokeny JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografii s veřejným klíčem k podepisování tokenů a k ověření, že jsou platné.

Můžete se rozhodnout ověřit `id_token` v kódu klienta, ale běžný postup je odeslat `id_token` na back-end Server a provést ověření tam. Jakmile ověříte podpis id_token, budete muset ověřit několik deklarací identity. Další informace najdete v [referenčních](id-tokens.md) informacích k`id_token`, včetně [ověřování tokenů](id-tokens.md#validating-an-id_token) a [důležitých informací o výměně klíčů](active-directory-signing-key-rollover.md). Doporučujeme používat knihovnu pro analýzu a ověřování tokenů – pro většinu jazyků a platforem je k dispozici alespoň jeden.

V závislosti na vašem scénáři taky můžete chtít ověřit další deklarace identity. Mezi běžná ověření patří:

* Zajistěte, aby se uživatel nebo organizace zaregistrovali do aplikace.
* Zajistěte, aby měl uživatel správnou autorizaci nebo oprávnění.
* Bylo zajištěno, že došlo k určité síle ověřování, jako je například Multi-Factor Authentication.

Po ověření id_token můžete zahájit relaci s uživatelem a pomocí deklarací v id_token získat informace o uživateli ve vaší aplikaci. Tyto informace lze použít pro zobrazení, záznamy, přizpůsobení atd.

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

Pokud chcete odhlásit uživatele z vaší aplikace, nestačí vymazat soubory cookie vaší aplikace nebo jinak ukončit relaci uživatele. Musíte také přesměrovat uživatele na koncový bod platformy Microsoft identity, abyste se odhlásili. Pokud to neuděláte, uživatel se znovu ověří do vaší aplikace bez zadání přihlašovacích údajů, protože bude mít platnou relaci jednotného přihlašování s koncovým bodem Microsoft Identity Platform.

Uživatele můžete přesměrovat na `end_session_endpoint`, které jsou uvedené v dokumentu metadat OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametr | Podmínka | Popis |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Doporučené | Adresa URL, na kterou je uživatel přesměrován po úspěšném odhlášení. Pokud parametr není zahrnutý, zobrazí se uživateli obecná zpráva generovaná koncovým bodem Microsoft Identity Platform. Tato adresa URL se musí shodovat s jedním z identifikátorů URI přesměrování registrovaných pro vaši aplikaci na portálu pro registraci aplikací. |

## <a name="single-sign-out"></a>Jednotné odhlašování

Když uživatele přesměrujete na `end_session_endpoint`, koncové body Microsoft Identity Platform vymaže relaci uživatele z prohlížeče. Uživatel však může být stále přihlášený k jiným aplikacím, které používají účty Microsoft pro ověřování. Aby mohly tyto aplikace při současném podepsání uživatele podepsat, koncovým bodem platformy Microsoft Identity Platform pošle požadavek HTTP GET na registrovanou `LogoutUrl` všech aplikací, ke kterým je uživatel aktuálně přihlášený. Aplikace musí na tuto žádost reagovat vymazáním jakékoli relace, která identifikuje uživatele a vrácením `200` odpovědi. Pokud chcete v aplikaci podporovat jednotné přihlašování, musíte implementovat takové `LogoutUrl` v kódu vaší aplikace. `LogoutUrl` můžete nastavit na portálu pro registraci aplikací.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagram protokolu: získání přístupového tokenu

Mnohé webové aplikace potřebují pouze podepsat uživatele v, ale také pro přístup k webové službě jménem uživatele pomocí protokolu OAuth. Tento scénář kombinuje OpenID Connect pro ověřování uživatelů, zatímco současně načítá autorizační kód, který můžete použít k získání přístupových tokenů, pokud používáte tok autorizačního kódu OAuth.

Celý tok pro přihlášení k OpenID a získání tokenu se bude podobat následujícímu diagramu. Jednotlivé kroky podrobněji popisujeme v dalších částech tohoto článku.

![Protokol OpenID Connect: získání tokenu](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Získat přístupové tokeny
Pokud chcete získat přístupové tokeny, upravte žádost o přihlášení:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Kliknutím na následující odkaz spusťte tento požadavek. Po přihlášení se Váš prohlížeč přesměruje na `https://localhost/myapp/`s tokenem ID a kódem na adresním řádku. Všimněte si, že tato žádost používá `response_mode=fragment` jenom pro demonstrační účely. Doporučujeme použít `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Díky zahrnutí oborů oprávnění do žádosti a pomocí `response_type=id_token code`koncový bod platformy Microsoft Identity zajišťuje, že uživatel souhlasí s oprávněními uvedenými v parametru dotazu `scope`. Vrátí autorizační kód do vaší aplikace pro výměnu přístupového tokenu.

### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď z použití `response_mode=form_post` vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Popis |
| --- | --- |
| `id_token` | Token ID, který aplikace požadovala Token ID můžete použít k ověření identity uživatele a zahájení relace s uživatelem. Další podrobnosti o tokenech ID a jejich obsahu najdete v [referenčních](id-tokens.md)informacích k`id_tokens`. |
| `code` | Autorizační kód, který požadovaná aplikace požaduje. Aplikace může pomocí autorizačního kódu požádat o přístupový token pro cílový prostředek. Autorizační kód je krátkodobě nekrátkodobý. Autorizační kód obvykle vyprší za přibližně 10 minut. |
| `state` | Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

### <a name="error-response"></a>Chybová odezva

K identifikátoru URI přesměrování se můžou poslat taky odpovědi na chyby, aby se aplikace mohla vhodně zpracovat. Reakce na chybu vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým dochází, a k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která vám může pomáhat identifikovat hlavní příčinu chyby ověřování. |

Popis možných kódů chyb a doporučených odpovědí klienta najdete v tématu [kódy chyb pro chyby koncového bodu autorizace](#error-codes-for-authorization-endpoint-errors).

Pokud máte autorizační kód a token ID, můžete uživatele podepsat a získat přístupové tokeny za jeho jménem. Chcete-li uživatele podepsat v, je nutné ověřit token ID [přesně tak, jak je popsáno](id-tokens.md#validating-an-id_token). Chcete-li získat přístupové tokeny, postupujte podle kroků popsaných v [dokumentaci ke službě Flow Code OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).
