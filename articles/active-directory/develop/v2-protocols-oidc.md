---
title: Platforma identit Microsoft a protokolu OpenID Connect | Azure
description: Vytvoření webové aplikace pomocí Microsoft identity platform implementaci ověřovacího protokolu OpenID Connect.
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23a8eaaf095be1d59944791bd793047886dda40c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544809"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Platforma identit Microsoft a protokolu OpenID Connect

Ověřovací protokol OAuth 2.0, který vám umožní bezpečně přihlásit uživatele k webové aplikaci využívající OpenID Connect se. Při použití koncového Microsoft identity platform implementaci OpenID Connect, můžete přidat přihlášení a přístup k rozhraní API pro vaše webové aplikace. Tento článek ukazuje, jak provést tuto bez ohledu na jazyk a popisuje, jak posílat a přijímat zprávy HTTP bez použití jakékoli Microsoft open source knihoven.

> [!NOTE]
> Koncový bod Microsoft identity platform nepodporuje všechny scénáře Azure Active Directory (Azure AD) a funkce. Pokud chcete zjistit, zda by měl použít koncový bod Microsoft identity platform, přečtěte si informace o [Microsoft identity platform omezení](active-directory-v2-limitations.md).

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozšiřuje OAuth 2.0 *autorizace* protokol se má použít jako *ověřování* protokol, tak, aby vám pomůžou jednotné přihlašování pomocí OAuth. OpenID Connect zavádí koncepci *ID token*, což je token zabezpečení, která umožňuje klientům ověřovat identitu uživatele. ID token také získá profil základní informace o uživateli. Protože OpenID Connect rozšiřuje OAuth 2.0, aplikace můžete bezpečně získat *přístupové tokeny*, které lze použít pro přístup k prostředkům, které jsou zabezpečené pomocí [autorizační server](active-directory-v2-protocols.md#the-basics). Koncový bod Microsoft identity platform také umožňuje aplikacím třetích stran, které jsou registrované v Azure AD a vystavovat tokeny přístupu k zabezpečeným prostředkům, jako je například webová rozhraní API. Další informace o tom, jak nastavit aplikaci pro vydávání tokenů přístupu najdete v části [postup registrace aplikace pomocí Microsoft identity platform endpoint](quickstart-register-app.md). Doporučujeme vám, že používáte OpenID Connect, pokud vytváříte [webovou aplikaci](v2-app-types.md#web-apps) , který je hostovaný na serveru a získat přístup z prohlížeče.

## <a name="protocol-diagram-sign-in"></a>Diagram protokolu: Přihlášení

Základní tok přihlášení má kroků je vidět na dalším obrázku. Každý krok je podrobně popsány v tomto článku.

![Protokol OpenID Connect: Přihlášení](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Načíst metadata dokumentů OpenID Connect

Dokument metadat, který obsahuje požadované informace o aplikaci pro přihlášení OpenID Connect popisuje. To zahrnuje informace, jako jsou adresy URL k použití a umístění služby veřejné podpisového klíče. Pro Microsoft identity platform koncový bod tím je dokument metadat OpenID Connect, které byste měli použít:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Vyzkoušejte si to! Klikněte na tlačítko [ https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration ](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) zobrazíte `common` konfigurace klientů.

`{tenant}` Může trvat jednu ze čtyř hodnot:

| Hodnota | Popis |
| --- | --- |
| `common` |Uživatele pomocí osobního účtu Microsoft a pracovní nebo školní účet ze služby Azure AD můžete přihlásit k aplikaci. |
| `organizations` |Pouze uživatelé s pracovní nebo školní účty z Azure AD můžete přihlásit k aplikaci. |
| `consumers` |Jenom uživatelé s osobním účtem Microsoft můžete přihlásit k aplikaci. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` | Jenom uživatelé s pracovní či školní účty z konkrétní služby Azure AD tenanta můžete přihlásit k aplikaci. Lze použít buď popisný název tenanta Azure AD nebo identifikátor GUID vašeho tenanta. Můžete také použít tenanta příjemce `9188040d-6c67-4c5b-b112-36a304b66dad`, místo `consumers` tenanta.  |

Metadata jsou jednoduché dokumentu JavaScript Object Notation (JSON). Prohlédněte si následující fragment kódu pro příklad. Jsou podrobně popsány v tomto fragmentu kódu obsah [OpenID Connect specifikace](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

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

Pokud vaše aplikace má vlastní podpisové klíče v důsledku použití [mapování deklarací](active-directory-claims-mapping.md) funkci, musíte připojit `appid` obsahující ID aplikace, pokud chcete získat parametr dotazu `jwks_uri` odkazující na vaši aplikaci prvku podpisový klíč informace. Příklad: `https://login.microsoftonline.com/{tenant}/.well-known/v2.0/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` obsahuje `jwks_uri` z `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

Obvykle použijete tento dokument metadat konfigurace OpenID Connect knihovny nebo sady SDK; knihovny by metadata pomocí provádí svou práci. Pokud nechcete použít knihovnu předem sestavených OpenID Connect, můžete však postupujte podle kroků ve zbývající části tohoto článku k registraci ve službě web app pomocí koncového bodu Microsoft identity platform.

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Pokud vaše webová aplikace potřebuje k ověření uživatele, můžete nasměrovat uživatele `/authorize` koncového bodu. Tento požadavek je podobná první větev spojeného [tok autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md), se tyto důležité rozdíly:

* Musí zahrnovat požadavek `openid` v oboru `scope` parametr.
* `response_type` Musí obsahovat parametr `id_token`.
* Musí zahrnovat požadavek `nonce` parametru.

> [!IMPORTANT]
> Aby bylo možné úspěšně požádat o ID token z /authorization koncového bodu registrace aplikace v [portál pro registraci](https://portal.azure.com) musí mít implicitního udělení id_tokens povolena na kartě ověřování (které nastaví `oauth2AllowIdTokenImplicitFlow`příznak v [manifest aplikace](reference-app-manifest.md) k `true`). Pokud není povolen, `unsupported_response` se vrátí Chyba: "Pro vstupní parametr 'typ odpovědi' zadaná hodnota není povolena pro tohoto klienta. Očekávaná hodnota je "kód" "

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
> Kliknutím na následující odkaz k provedení tohoto požadavku. Po přihlášení prohlížeči, budete přesměrováni na `https://localhost/myapp/`, k tokenu ID do adresního řádku. Všimněte si, že tento požadavek používá `response_mode=fragment` (pouze pro demonstrační účely). Doporučujeme, abyste použili `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Můžete použít `{tenant}` hodnota v cestě požadavku řídit, kdo může přihlásit k aplikaci. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátorů klienta. Další informace najdete v tématu [protokol Základy](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Požaduje se | **ID aplikace (klient)** , který [webu Azure portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené vaší aplikaci. |
| `response_type` | Požaduje se | Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Může taky obsahovat další `response_type` hodnoty, jako například `code`. |
| `redirect_uri` | Doporučené | Identifikátor URI přesměrování vaší aplikace, kde můžete odesílat a přijímat aplikací pro žádosti o ověření. Se musí přesně odpovídat jednu registraci na portálu pro identifikátory URI přesměrování s tím rozdílem, že ho musí mít kódování URL. Pokud není k dispozici, koncový bod bude jeden registrovaný redirect_uri vyskladnění náhodně na uživatele poslat na zpět. |
| `scope` | Požaduje se | Seznam oborů oddělených mezerami. Pro OpenID Connect, musí zahrnovat obor `openid`, který se přeloží na "Přihlášení" oprávnění v souhlasu uživatelského rozhraní. V této žádosti pro vyžádání souhlasu může taky obsahovat další obory. |
| `nonce` | Požaduje se | Hodnota zahrnutý v požadavku, vygenerované aplikaci, ve kterém se zahrnou výsledná hodnota id_token jako deklarace identity. Aplikace můžete ověřit tuto hodnotu a zmírnění útoků opětovného přehrání tokenu. Hodnota je obvykle náhodnou jedinečného řetězce, který můžete použít k identifikaci původcem požadavku. |
| `response_mode` | Doporučené | Určuje metodu, která se má použít k odeslání výsledný autorizační kód zpět do aplikace. Může být `form_post` nebo `fragment`. Pro webové aplikace, doporučujeme používat `response_mode=form_post`, aby bylo zajištěno nejbezpečnější přenos tokeny do vaší aplikace. |
| `state` | Doporučené | Hodnota v požadavku, která se také vrátit v odpovědi tokenu. Může být řetězec s žádný obsah, který chcete. Náhodně generované jedinečná hodnota se obvykle používá k [útokům padělání žádosti více webů](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které uživatel byl v. |
| `prompt` | Nepovinné | Určuje typ interakce s uživatelem, který je požadován. V tuto chvíli pouze platné hodnoty jsou `login`, `none`, a `consent`. `prompt=login` Deklarace identity donutí uživatele k zadání přihlašovacích údajů tohoto požadavku, které negují jednotného přihlašování. `prompt=none` Deklarace identity je opak. Tento požadavek zajistí, že uživatel se nezobrazí se žádné interaktivní výzvu v. Pokud žádost nejde dokončit tiše prostřednictvím jednotného přihlašování, koncový bod Microsoft identity platform vrátí chybu. `prompt=consent` Deklarace identity se aktivuje dialogové okno souhlasu OAuth po přihlášení uživatele. Dialogové okno požádá uživatele a udělit oprávnění k aplikaci. |
| `login_hint` | Nepovinné | Tento parametr můžete předběžně vyplnit pole uživatelské jméno a e-mailová adresa stránky přihlášení pro uživatele, pokud víte, uživatelské jméno předem. Aplikace často, použijte tento parametr během opětovné ověření po již extrahování uživatelské jméno ze starší přihlášení s použitím `preferred_username` deklarací identity. |
| `domain_hint` | Nepovinné | Sféra uživatele v adresáři federované.  To přeskočí proces zjišťování na základě e-mailu, který uživatel prochází na přihlašovací stránku, o něco jednodušší uživatelské prostředí. U klientů, které jsou federované pomocí v místním adresáři, jako je služba AD FS často výsledkem bezproblémové přihlašování z důvodu existující relaci přihlášení. |

V tomto okamžiku bude uživatel vyzván k zadání přihlašovacích údajů a bylo možné ověření dokončit. Koncový bod platforma identit Microsoft ověřuje, že uživatel vyjádřil souhlas se oprávnění uvedená v `scope` parametr dotazu. Pokud uživatele. nevyjádřil souhlas některý z těchto oprávnění, koncový bod Microsoft identity platform zobrazí výzvu k požadovaná oprávnění vyjádřit souhlas. Další informace o [aplikace s více tenanty, oprávnění a souhlas](v2-permissions-and-consent.md).

Poté, co uživatel ověří a uděluje souhlas, koncového bodu Microsoft identity platform vrátí odpověď na vaši aplikaci na na zadaný identifikátor URI přesměrování pomocí metody popsané v `response_mode` parametru.

### <a name="successful-response"></a>Úspěšné odpovědi

Úspěšná odpověď, když použijete `response_mode=form_post` vypadá přibližně takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Popis |
| --- | --- |
| `id_token` | ID tokenu požadovanou aplikaci. Můžete použít `id_token` parametr pro ověření identity uživatele a zahájit relaci s uživatelem. Další informace o tokeny typu ID a jejich obsah, najdete v článku [ `id_tokens` odkaz](id-tokens.md). |
| `state` | Pokud `state` parametr je zahrnutý v požadavku, stejnou hodnotu by se měla zobrazit v odpovědi. Aplikace by měl ověřit, že jsou identické hodnoty stavu v požadavku a odpovědi. |

### <a name="error-response"></a>Odpověď na chybu

Chyba posílají žádosti o může také být identifikátor URI přesměrování tak, že aplikace zvládne. Chybová odpověď vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, můžete použít ke klasifikaci typy chyb, ke kterým dochází a reagovat na chyby. |
| `error_description` | Určité chybové zprávě, které vám pomohou identifikovat hlavní příčinu chyby ověřování. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Kódy chyb pro chyby koncový bod autorizace

Následující tabulka popisuje chybové kódy, které mohou být vráceny v `error` parametr odpovědi na chybu:

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| `invalid_request` | Chyba protokolu, jako je například chybějící, povinný parametr. |Opravte a odešlete požadavek znovu. Toto je chyba vývoj, která je zachycena obvykle během počátečního testování. |
| `unauthorized_client` | Klientská aplikace nemůže požádat o autorizační kód. |K tomu obvykle dojde, když klientská aplikace není zaregistrovaný ve službě Azure AD nebo se nepřidal do tenanta služby Azure AD daného uživatele. Aplikace může vyzvat uživatele s pokyny k instalaci aplikace a přidejte ji do služby Azure AD. |
| `access_denied` | Vlastník prostředku odepřen souhlas. |Klientská aplikace může upozornit uživatele, který nemůže pokračovat, dokud uživatel souhlasí. |
| `unsupported_response_type` |Autorizační server nepodporuje typ odpovědi v požadavku. |Opravte a odešlete požadavek znovu. Toto je chyba vývoj, která je zachycena obvykle během počátečního testování. |
| `server_error` | Na serveru došlo k neočekávané chybě. |Zkuste požadavek. Tyto chyby můžou být výsledkem dočasné situace. Klientská aplikace může vysvětlit uživatelům, že odpověď se zpožďuje kvůli dočasné chybě. |
| `temporarily_unavailable` | Server je dočasně příliš zaneprázdněn a nemůže žádost zpracovat. |Zkuste požadavek. Klientská aplikace může vysvětlit uživateli, odpověď se zpožďuje kvůli dočasné podmínce. |
| `invalid_resource` | Cílový prostředek je neplatný, protože buď neexistuje, Azure AD nelze najít nebo není správně nakonfigurovaný. |To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a jeho přidání do služby Azure AD. |

## <a name="validate-the-id-token"></a>Ověřit ID token

Pouhého získání tokentu id_token není dostatečná k ověření uživatele. je nutné ověřit podpis požadavku id_token a ověřte, deklarace identity v tokenu podle požadavků vaší aplikace. Koncový bod Microsoft identity platform používá [webové tokeny JSON (Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografii využívající veřejného klíče pro podepisování tokenů a ověřte, že jsou platné.

Můžete ověřit `id_token` v klientovi kód, ale běžnou praxí je odeslat `id_token` do back-endovému serveru a proveďte ověření existuje. Jakmile ověříte podpisu požadavku id_token, existují několik deklarací identity, které bude nutné ověřit. Najdete v článku [ `id_token` odkaz](id-tokens.md) Další informace, včetně [ověřování tokenů](id-tokens.md#validating-an-id_token) a [důležité informace o podpisový klíč výměny](active-directory-signing-key-rollover.md). Doporučujeme, abyste využívající knihovnu k analýze a ověřování tokenů: k dispozici aspoň jeden k dispozici pro většinu jazyky a platformy.

Také můžete chtít ověřit další deklarace identity v závislosti na vašem scénáři. Některé běžné ověření patří:

* Zajištění uživatele nebo organizaci zaregistroval k aplikaci.
* Zajistit, že uživatel má správnou autorizaci/oprávnění
* Zajištění sílu ověřování došlo, jako je ověřování službou Multi-Factor Authentication.

Jakmile ověříte požadavku id_token, můžete zahájit relaci s uživatelem a používat deklarace identity v požadavku id_token k získání informací o uživateli ve vaší aplikaci. Tyto informace můžete použít pro zobrazení záznamů, přizpůsobení, atd.

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

Pokud chcete odhlásit uživatele z vaší aplikace, není dostatečná k vyčištění souborů cookie vaší aplikace nebo jinak ukončení uživatelské relace. Musíte také přesměruje uživatele na Microsoft identity platform koncový bod se odhlásit. Pokud to neuděláte, uživatel rozdílnými do vaší aplikace bez nutnosti zadávat své přihlašovací údaje znovu, protože mají platný jedné přihlašovací relaci s koncovým bodem Microsoft identity platform.

Můžete přesměrovat uživatele `end_session_endpoint` uvedené v tomto dokumentu metadat OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametr | Podmínka | Popis |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Doporučené | Adresa URL, která bude uživatel přesměrován po úspěšném odhlášení. Pokud parametr není zahrnut, uživateli se zobrazí obecná zpráva, který je generován koncového bodu Microsoft identity platform. Tato adresa URL musí odpovídat jedné z přesměrování, na které registrovaný kód URIs pro vaši aplikaci v portálu pro registraci aplikace. |

## <a name="single-sign-out"></a>Jednotné odhlašování

Pokud má uživatel přesměrovat `end_session_endpoint`, koncový bod Microsoft identity platform vymaže relace uživatele z prohlížeče. Však uživatel stále být přihlášeni k ostatním aplikacím, které používají účty Microsoft pro ověřování. Povolení aplikací pro přihlášení uživatele out současně, Microsoft identity platform endpoint odešle požadavek HTTP GET zaregistrovanou `LogoutUrl` všech aplikací, které uživatel je aktuálně přihlášený k. Aplikace musí odpovědět na tuto žádost zrušíte všechny relace, který identifikuje uživatele a vrácení `200` odpovědi. Pokud chcete podporovat odhlašování ve vaší aplikaci jedním, je nutné implementovat takové `LogoutUrl` v kódu vaší aplikace. Můžete nastavit `LogoutUrl` z portálu pro registraci aplikace.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagram protokolu: Získání tokenu přístupu

Mnoho webových aplikací muset nejen přihlásit uživatele v, ale také pro přístup k webové službě jménem uživatele pomocí OAuth. Tento scénář spojuje OpenID Connect pro ověřování uživatelů při získávání současně autorizační kód, který můžete použít k získání přístupových tokenů, pokud používáte toku kódu autorizace OAuth.

Úplné přihlášení a získání tokenu tok OpenID Connect vypadá podobně jako následující diagram. Popisujeme každý krok podrobně v další části tohoto článku.

![Protokol OpenID Connect: Získání tokenu](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Získání přístupových tokenů
Chcete-li získat přístupové tokeny, upravte žádost o přihlášení:

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
> Kliknutím na následující odkaz k provedení tohoto požadavku. Po přihlášení prohlížeči přesměrován `https://localhost/myapp/`, ID token a kód do adresního řádku. Všimněte si, že tento požadavek používá `response_mode=fragment` pouze pro demonstrační účely. Doporučujeme, abyste použili `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Včetně obory oprávnění v požadavku a použitím `response_type=id_token code`, koncový bod Microsoft identity platform zajistí, že uživatel vyjádřil souhlas se oprávnění uvedená v `scope` parametr dotazu. Vrátí autorizační kód do vaší aplikace k výměně za přístupový token.

### <a name="successful-response"></a>Úspěšné odpovědi

Úspěšné odpovědi pomocí `response_mode=form_post` vypadá přibližně takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Popis |
| --- | --- |
| `id_token` | ID tokenu požadovanou aplikaci. ID token můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. Najdete další podrobnosti o tokeny typu ID a jejich obsah [ `id_tokens` odkaz](id-tokens.md). |
| `code` | Autorizační kód požadovanou aplikaci. Aplikace může používat autorizační kód k vyžádání tokenu pro cílový prostředek. Je autorizační kód. Obvykle autorizačního kódu vyprší během 10 minut. |
| `state` | Pokud parametr stavu je zahrnutý v požadavku, by se zobrazit stejnou hodnotu v odpovědi. Aplikace by měl ověřit, že jsou identické hodnoty stavu v požadavku a odpovědi. |

### <a name="error-response"></a>Odpověď na chybu

Chybové odpovědi může být také odesílána identifikátor URI přesměrování, aby aplikace můžete odpovídajícím způsobem zpracovat. Chybová odpověď vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, můžete použít ke klasifikaci typy chyb, ke kterým dochází a reagovat na chyby. |
| `error_description` | Určité chybové zprávě, které vám pomohou identifikovat hlavní příčinu chyby ověřování. |

Popis možné kódy chyb a odpovědí doporučených klienta najdete v tématu [kódy chyb pro chyby koncový bod autorizace](#error-codes-for-authorization-endpoint-errors).

Až budete mít autorizační kód a tokenu ID, můžete uživatele přihlásit a získat přístupové tokeny jejich jménem. Pro přihlášení uživatele, musíte ověřit ID token [přesně tak, jak je popsáno](id-tokens.md#validating-an-id_token). Pokud chcete získat přístupové tokeny, postupujte podle kroků popsaných v [dokumentace ke službě flow OAuth kód](v2-oauth2-auth-code-flow.md#request-an-access-token).
