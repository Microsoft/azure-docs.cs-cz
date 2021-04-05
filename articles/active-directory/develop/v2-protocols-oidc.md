---
title: Microsoft Identity Platform a OpenID Connect Protocol | Azure
titleSuffix: Microsoft identity platform
description: Sestavujte webové aplikace pomocí implementace ověřovacího protokolu OpenID Connect v platformě Microsoft Identity Platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de1fcdc259de3f72e35feb411bcc836354352eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752594"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity Platform a OpenID Connect Protocol

OpenID Connect (OIDC) je ověřovací protokol založený na OAuth 2,0, který můžete použít k bezpečnému přihlášení uživatele do aplikace. Když použijete implementaci OpenID Connect platformy Microsoft identity, můžete do svých aplikací přidat přihlašování a přístup k rozhraní API. Tento článek ukazuje, jak to provést nezávisle na jazyku a popisuje, jak odesílat a přijímat zprávy HTTP bez použití [knihoven Microsoft Open-Source](reference-v2-libraries.md)Library.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozšiřuje *autorizační* protokol OAuth 2,0 pro použití jako *ověřovací* protokol, aby bylo možné provádět jednotné přihlašování pomocí protokolu OAuth. OpenID Connect zavádí koncept *tokenu ID*, což je token zabezpečení, který umožňuje klientovi ověřit identitu uživatele. Token ID také získá základní informace o profilu uživatele. Zavádí taky [koncový bod UserInfo](userinfo.md), rozhraní API, které vrací informace o uživateli. 


## <a name="protocol-diagram-sign-in"></a>Diagram protokolu: přihlášení

Nejzákladnější tok přihlášení má kroky uvedené v dalším diagramu. Jednotlivé kroky jsou podrobně popsané v tomto článku.

![Protokol OpenID Connect: přihlášení](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Načtení dokumentu metadat OpenID Connect

OpenID Connect popisuje dokument metadat [(RFC)](https://openid.net/specs/openid-connect-discovery-1_0.html) , který obsahuje většinu informací potřebných k tomu, aby se aplikace mohla přihlásit. Zahrnuje to i informace, jako jsou adresy URL, které se mají použít, a umístění veřejných podpisových klíčů služby. Tento dokument můžete najít tak, že připojíte cestu k dokumentu zjišťování k adrese URL autority:

Cesta k dokumentu zjišťování: `/.well-known/openid-configuration`

Dohled `https://login.microsoftonline.com/{tenant}/v2.0`

`{tenant}`Může mít jednu ze čtyř hodnot:

| Hodnota | Popis |
| --- | --- |
| `common` |Uživatelé, kteří mají osobní účet Microsoft a pracovní nebo školní účet z Azure AD, se můžou k aplikaci přihlásit. |
| `organizations` |K aplikaci se můžou přihlásit jenom uživatelé s pracovními nebo školními účty ze služby Azure AD. |
| `consumers` |K aplikaci se můžou přihlásit jenom uživatelé s osobním účet Microsoft. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` | Můžou se přihlásit k aplikaci jenom uživatelům z konkrétního tenanta Azure AD (ať už jsou členy v adresáři s pracovním nebo školním účtem nebo jsou hosty v adresáři s osobním účet Microsoft). Dá se použít popisný název domény tenanta Azure AD nebo identifikátor GUID klienta. Můžete také použít tenanta spotřebitele `9188040d-6c67-4c5b-b112-36a304b66dad` místo `consumers` tenanta.  |

Autorita se liší v národních cloudech, například v případě `https://login.microsoftonline.de` instance Azure AD Německo. Pokud veřejný cloud nepoužíváte, přečtěte si prosím v [národním koncovém bodu cloudu](authentication-national-cloud.md#azure-ad-authentication-endpoints) , kde najdete vhodné informace pro vás. Zajistěte, aby byl tenant `/v2.0/` ve vaší žádosti přítomen, abyste mohli použít verzi tohoto koncového bodu v 2.0.

> [!TIP]
> Vyzkoušejte si to! Kliknutím [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) zobrazíte `common` konfiguraci.

### <a name="sample-request"></a>Ukázková žádost

Pokud chcete zavolat koncový bod UserInfo pro společnou autoritu ve veřejném cloudu, použijte následující:

```http
GET /common/v2.0/.well-known/openid-configuration
Host: login.microsoftonline.com
```

### <a name="sample-response"></a>Ukázková odpověď

Metadata jsou jednoduchý dokument JavaScript Object Notation (JSON). Příklad naleznete v následujícím fragmentu kódu. Obsah je plně popsán ve [specifikaci OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```json
{
  "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize",
  "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys",
  "userinfo_endpoint": "https://graph.microsoft.com/oidc/userinfo",
  "subject_types_supported": [
      "pairwise"
  ],
  ...

}
```

Pokud vaše aplikace obsahuje vlastní podpisové klíče v důsledku použití funkce [mapování deklarací](active-directory-claims-mapping.md) , musíte připojit `appid` parametr dotazu obsahující ID aplikace, aby bylo možné přejít `jwks_uri` k informacím o podpisovém klíči vaší aplikace. Příklad: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` obsahuje a `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

Obvykle byste tento dokument metadat použili ke konfiguraci knihovny nebo sady SDK OpenID Connect. Knihovna by tato metadata použila k tomu, aby fungovala. Pokud ale nepoužíváte předem vytvořenou knihovnu OpenID Connect, můžete k tomu použít postup ve zbývající části tohoto článku, abyste se přihlásili do webové aplikace pomocí platformy Microsoft identity.

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Pokud vaše webová aplikace potřebuje ověřit uživatele, může uživatele nasměrovat na `/authorize` koncový bod. Tento požadavek je podobný prvnímu průchodu [toku autorizačního kódu OAuth 2,0](v2-oauth2-auth-code-flow.md), přičemž tyto důležité odlišnosti:

* Požadavek musí zahrnovat `openid` obor v `scope` parametru.
* `response_type`Parametr musí obsahovat `id_token` .
* Požadavek musí obsahovat `nonce` parametr.

> [!IMPORTANT]
> Aby bylo možné úspěšně požádat o token ID z koncového bodu/Authorization, musí mít registrace aplikace na [portálu pro registraci](https://portal.azure.com) implicitní udělení id_tokens povoleny na kartě ověřování (která nastaví `oauth2AllowIdTokenImplicitFlow` příznak v [manifestu aplikace](reference-app-manifest.md) na `true` ). Pokud není povoleno, `unsupported_response` bude vrácena chyba: "Zadaná hodnota pro vstupní parametr ' response_type ' není pro tohoto klienta povolena. Očekávaná hodnota je Code (kód).

Například:

```HTTP
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

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Povinné | `{tenant}`Hodnotu v cestě k požadavku můžete použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common` `organizations` `consumers` identifikátory klientů,, a. Další informace najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Vyžadováno | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `response_type` | Vyžadováno | Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Může také obsahovat další `response_type` hodnoty, například `code` . |
| `redirect_uri` | Doporučeno | Identifikátor URI pro přesměrování vaší aplikace, ve kterém může vaše aplikace odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódovaný v adrese URL. Pokud tato akce není k dispozici, koncový bod vybere jednu registrovanou redirect_uri náhodně, aby bylo možné uživatele odeslat zpět do. |
| `scope` | Vyžadováno | Mezerou oddělený seznam oborů. Pro OpenID Connect musí zahrnovat obor `openid` , který se v uživatelském rozhraní souhlasu překládá na oprávnění přihlásit se. V této žádosti můžete také zahrnout další obory pro žádost o souhlas. |
| `nonce` | Vyžadováno | Hodnota obsažená v požadavku, která se vygenerovala aplikací, která se zahrne do výsledné id_token hodnoty jako deklarace. Aplikace může tuto hodnotu ověřit a zmírnit tak útoky na opakované přehrání tokenů. Hodnota je obvykle náhodný jedinečný řetězec, který lze použít k identifikaci původu žádosti. |
| `response_mode` | Doporučeno | Určuje metodu, která se má použít k odeslání výsledného autorizačního kódu zpátky do vaší aplikace. Může být `form_post` nebo `fragment`. Pro webové aplikace doporučujeme použít `response_mode=form_post` , abyste zajistili nejbezpečnější přenos tokenů do aplikace. |
| `state` | Doporučeno | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně generovaná jedinečná hodnota se obvykle používá k [zabránění útokům proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informací o stavu uživatele v aplikaci před tím, než k žádosti o ověření dojde, například na stránce nebo zobrazení, na které uživatel byl. |
| `prompt` | Volitelné | Určuje typ interakce uživatele, která je povinná. Jediné platné hodnoty jsou v tomto okamžiku `login` , `none` a `consent` . `prompt=login`Deklarace identity vynutí, aby uživatel zadal své přihlašovací údaje k této žádosti, který má na starosti jednotné přihlašování. `prompt=none`Deklarace identity je opak. Tato deklarace identity zajišťuje, že uživatel nebude mít žádné interaktivní výzvy na adrese. Pokud se žádost nedá v tichém režimu dokončit pomocí jednotného přihlašování, vrátí platforma Microsoft Identity Platform chybu. `prompt=consent`Deklarace identity aktivuje dialogové okno pro vyjádření souhlasu OAuth, až se uživatel přihlásí. Dialogové okno požádá uživatele o udělení oprávnění k aplikaci. |
| `login_hint` | Volitelné | Tento parametr můžete použít k předvyplnění pole uživatelské jméno a e-mailová adresa přihlašovací stránky pro uživatele, pokud znáte uživatelské jméno předem. Aplikace často používají tento parametr během opakovaného ověřování, po již extrakci uživatelského jména z dřívějšího přihlášení pomocí `preferred_username` deklarace identity. |
| `domain_hint` | Volitelné | Sféra uživatele ve federovaném adresáři.  Tím se přeskočí proces zjišťování na základě e-mailu, který uživatel prochází na přihlašovací stránce, aby bylo poněkud jednodušší uživatelské prostředí. Pro klienty, kteří jsou federované prostřednictvím místního adresáře, jako je AD FS, to často vede k bezproblémovému přihlášení kvůli stávající přihlašovací relaci. |

V tomto okamžiku se uživateli zobrazí výzva k zadání přihlašovacích údajů a dokončení ověřování. Platforma Microsoft Identity ověřuje, že uživatel souhlasí s oprávněními uvedenými v `scope` parametru dotazu. Pokud uživatel nesouhlasí s některým z těchto oprávnění, platforma Microsoft Identity Platform vyzve uživatele k vyjádření souhlasu s požadovanými oprávněními. Můžete si přečíst další informace o [oprávněních, souhlasu a víceklientské aplikace](v2-permissions-and-consent.md).

Jakmile se uživatel ověří a udělí souhlas, platforma Microsoftu identity vrátí odpověď do vaší aplikace na určeném identifikátoru URI přesměrování pomocí metody zadané v `response_mode` parametru.

### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď, když použijete, `response_mode=form_post` vypadá takto:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Popis |
| --- | --- |
| `id_token` | Token ID, který aplikace požadovala Pomocí `id_token` parametru můžete ověřit identitu uživatele a zahájit relaci s uživatelem. Další informace o tokenech ID a jejich obsahu najdete v [ `id_tokens` referenčních](id-tokens.md)informacích. |
| `state` | Pokud `state` je parametr zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

### <a name="error-response"></a>Chybová odezva

K identifikátoru URI přesměrování se můžou poslat taky odpovědi na chyby, aby je aplikace mohla zpracovat. Reakce na chybu vypadá takto:

```HTTP
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

V následující tabulce jsou popsány chybové kódy, které lze vrátit v `error` parametru chybové odpovědi:

| Kód chyby | Description | Akce klienta |
| --- | --- | --- |
| `invalid_request` | Chyba protokolu, například chybějící, povinný parametr. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoje, která se obvykle zachycuje při počátečním testování. |
| `unauthorized_client` | Klientská aplikace nemůže požádat o autorizační kód. |K tomu obvykle dochází, když klientská aplikace není registrovaná v Azure AD nebo není přidaná do tenanta Azure AD uživatele. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| `access_denied` | Vlastník prostředku zamítl souhlas. |Klientská aplikace může uživatele informovat, že nemůže pokračovat, dokud se uživatel nesouhlasí. |
| `unsupported_response_type` |Autorizační Server v žádosti nepodporuje typ odpovědi. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoje, která se obvykle zachycuje při počátečním testování. |
| `server_error` | Na serveru došlo k neočekávané chybě. |Opakujte požadavek. Tyto chyby mohou být způsobeny dočasnými podmínkami. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděná kvůli dočasné chybě. |
| `temporarily_unavailable` | Server je dočasně zaneprázdněný pro zpracování žádosti. |Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
| `invalid_resource` | Cílový prostředek není platný, protože neexistuje, služba Azure AD ho nemůže najít nebo není správně nakonfigurovaná. |To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |

## <a name="validate-the-id-token"></a>Ověřit token ID

Pouze příjem id_token není vždy dostačující k ověření uživatele; je také možné, že budete muset ověřit signaturu id_token a ověřit deklarace identity v tokenu podle požadavků vaší aplikace. Stejně jako všechny OIDC platformy Microsoft Identity Platform využívá [webové tokeny JSON (JWTs)](https://tools.ietf.org/html/rfc7519) a kryptografii s veřejným klíčem k podepisování tokenů ID a ověření, že jsou platné.

Ne všechny aplikace mají výhodu při ověřování tokenu ID – nativní aplikace a jednostránkové aplikace, například zřídka se z ověřování tokenu ID nevyužívá.  Někdo s fyzickým přístupem k zařízení (nebo prohlížeči) může obejít ověření mnoha způsoby – od úpravy webového provozu na zařízení, aby poskytovali falešné tokeny a klíče pro jednoduché ladění aplikace, aby bylo možné přeskočit logiku ověřování.  Na druhé straně webové aplikace a rozhraní API, které používají token ID k autorizaci, musí pečlivě ověřit token ID, protože mají přístup k datům.

Jakmile ověříte podpis id_token, budete muset ověřit několik deklarací identity. Další informace najdete v [ `id_token` referenčních](id-tokens.md) informacích, včetně [ověřování tokenů](id-tokens.md#validating-an-id_token) a [důležitých informací o výměně klíčů](active-directory-signing-key-rollover.md). Doporučujeme používat knihovnu pro analýzu a ověřování tokenů – pro většinu jazyků a platforem je k dispozici alespoň jeden.

V závislosti na vašem scénáři taky můžete chtít ověřit další deklarace identity. Mezi běžná ověření patří:

* Zajistěte, aby se uživatel nebo organizace zaregistrovali do aplikace.
* Zajistěte, aby měl uživatel správnou autorizaci nebo oprávnění.
* Bylo zajištěno, že došlo k určité síle ověřování, jako je například [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

Po ověření id_token můžete zahájit relaci s uživatelem a pomocí deklarací v id_token získat informace o uživateli ve vaší aplikaci. Tyto informace lze použít pro zobrazení, záznamy, přizpůsobení atd.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagram protokolu: získání přístupového tokenu

Mnohé webové aplikace potřebují pouze podepsat uživatele v, ale také pro přístup k webové službě jménem uživatele pomocí protokolu OAuth. Tento scénář kombinuje OpenID Connect pro ověřování uživatelů, zatímco současně načítá autorizační kód, který můžete použít k získání přístupových tokenů, pokud používáte tok autorizačního kódu OAuth.

Celý tok pro přihlášení k OpenID a získání tokenu se bude podobat následujícímu diagramu. Jednotlivé kroky podrobněji popisujeme v dalších částech tohoto článku.

![Protokol OpenID Connect: získání tokenu](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-an-access-token-to-call-userinfo"></a>Získání přístupového tokenu pro volání informací o uživateli

Pokud chcete získat token pro koncový bod OIDC UserInfo, upravte žádost o přihlášení:

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20token                       // this will return both an id_token and an access token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid+profile+email                           // `openid` is required.  `profile` and `email` provide additional information in the UserInfo endpoint the same way they do in an ID token. 
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

Můžete také použít [tok autorizačního kódu](v2-oauth2-auth-code-flow.md), [tok kódu zařízení](v2-oauth2-device-code.md)nebo [obnovovací token](v2-oauth2-auth-code-flow.md#refresh-the-access-token) místo `response_type=token` k získání tokenu pro vaši aplikaci.

> [!TIP]
> Kliknutím na následující odkaz spusťte tento požadavek. Po přihlášení se Váš prohlížeč přesměruje na `https://localhost/myapp/` , s tokenem ID a tokenem na adresním řádku. Všimněte si, že tento požadavek používá `response_mode=fragment` jenom pro demonstrační účely – pro WebApp doporučujeme použít `form_post` pro další zabezpečení, pokud je to možné. 
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid+profile+email&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

### <a name="successful-token-response"></a>Úspěšná odpověď tokenu

Úspěšná odpověď z použití `response_mode=form_post` vypadá takto:

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
 access_token=eyJ0eXAiOiJKV1QiLCJub25jZSI6I....
 &token_type=Bearer
 &expires_in=3598
 &scope=email+openid+profile
 &id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI....
 &state=12345
```

Parametry odpovědi znamenají stejnou věc bez ohledu na tok, který je používá k jejich získání.

| Parametr | Popis |
| --- | --- |
| `access_token` | Token, který bude použit pro volání koncového bodu UserInfo.|
| `token_type` | Vždy "nosič" |
| `expires_in`| Doba do vypršení platnosti přístupového tokenu (v sekundách) |
| `scope` | Oprávnění udělená u přístupového tokenu.  Vzhledem k tomu, že koncový bod UserInfo je hostovaný v MS graphu, můžou zde uvedené další obory grafu (třeba User. Read), pokud se jim dřív udělila aplikace.  Důvodem je skutečnost, že token pro daný prostředek vždy zahrnuje každé oprávnění, které je aktuálně uděleno klientovi.  |
| `id_token` | Token ID, který aplikace požadovala Token ID můžete použít k ověření identity uživatele a zahájení relace s uživatelem. Další podrobnosti o tokenech ID a jejich obsahu najdete v [ `id_tokens` referenčních](id-tokens.md)informacích. |
| `state` | Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

### <a name="error-response"></a>Chybová odezva

K identifikátoru URI přesměrování se můžou poslat taky odpovědi na chyby, aby se aplikace mohla vhodně zpracovat. Reakce na chybu vypadá takto:

```HTTP
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

### <a name="calling-the-userinfo-endpoint"></a>Volání koncového bodu UserInfo

Přečtěte si [dokumentaci k UserInfo](userinfo.md#calling-the-api) a podívejte se, jak zavolat koncovému bodu UserInfo s tímto tokenem.

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

Pokud chcete odhlásit uživatele z vaší aplikace, nestačí vymazat soubory cookie vaší aplikace nebo jinak ukončit relaci uživatele. Musíte také přesměrovat uživatele na Microsoft Identity Platform, abyste se odhlásili. Pokud to neuděláte, uživatel se znovu ověří do vaší aplikace bez zadání přihlašovacích údajů, protože bude mít platnou relaci jednotného přihlašování s platformou Microsoft identity.

Uživatele můžete přesměrovat na `end_session_endpoint` uvedené v dokumentu metadat OpenID Connect:

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametr | Podmínka | Description |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Doporučeno | Adresa URL, na kterou je uživatel přesměrován po úspěšném odhlášení. Pokud parametr není zahrnutý, zobrazí se uživateli obecná zpráva generovaná platformou Microsoft identity. Tato adresa URL se musí shodovat s jedním z identifikátorů URI přesměrování registrovaných pro vaši aplikaci na portálu pro registraci aplikací. |

## <a name="single-sign-out"></a>Jednotné odhlašování

Když uživatele přesměrujete na `end_session_endpoint` , Microsoft Identity Platform vymaže relaci uživatele z prohlížeče. Uživatel však může být stále přihlášený k jiným aplikacím, které používají účty Microsoft pro ověřování. Aby mohly tyto aplikace při současném podepsání uživatele podepsat, platforma identity Microsoftu pošle požadavek HTTP GET na zaregistrované `LogoutUrl` všechny aplikace, ke kterým je uživatel aktuálně přihlášený. Aplikace musí na tuto žádost reagovat vymazáním jakékoli relace, která uživatele identifikuje a vrátí `200` odpověď. Pokud chcete v aplikaci podporovat jednotné přihlašování, musíte implementovat takový `LogoutUrl` kód v kódu vaší aplikace. Můžete nastavit na `LogoutUrl` portálu pro registraci aplikací.

## <a name="next-steps"></a>Další kroky

* Přečtěte si [dokumentaci k uživateli](userinfo.md)
* Naučte se, jak [přizpůsobit hodnoty v tokenu](active-directory-claims-mapping.md) pomocí dat z vašich místních systémů. 
* Naučte se [zahrnout do tokenů další standardní deklarace identity](active-directory-optional-claims.md).  
