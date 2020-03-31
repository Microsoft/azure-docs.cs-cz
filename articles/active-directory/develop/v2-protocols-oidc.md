---
title: Protokol OpenID Connect – platforma identit Microsoftu | Azure
description: Vytvářejte webové aplikace pomocí implementace platformy microsoftu identit y ověřovacího protokolu OpenID Connect.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262292"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Platforma identit Microsoftu a protokol OpenID Connect

OpenID Connect je ověřovací protokol založený na OAuth 2.0, který můžete použít k bezpečnému přihlášení uživatele k webové aplikaci. Když používáte implementaci OpenID Connect koncového bodu platformy identit y platformy Microsoft, můžete přidat přístup k přihlášení a rozhraní API k webovým aplikacím. Tento článek ukazuje, jak to udělat nezávisle na jazyku a popisuje, jak odesílat a přijímat zprávy HTTP bez použití knihoven s otevřeným zdrojovým kódem společnosti Microsoft.

> [!NOTE]
> Koncový bod platformy identit Microsoftu nepodporuje všechny scénáře a funkce azure active directory (Azure AD). Chcete-li zjistit, zda byste měli používat koncový bod platformy identit společnosti Microsoft, přečtěte si o [omezení platformy identit společnosti Microsoft](active-directory-v2-limitations.md).

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozšiřuje *autorizační* protokol OAuth 2.0 na použití jako *ověřovací* protokol, takže můžete provést jednotné přihlášení pomocí OAuth. OpenID Connect zavádí koncept *Tokenu ID*, což je token zabezpečení, který umožňuje klientovi ověřit identitu uživatele. Token ID také získá základní informace o profilu o uživateli. Vzhledem k tomu, že OpenID Connect rozšiřuje OAuth 2.0, aplikace mohou bezpečně získat *přístupové tokeny*, které lze použít pro přístup k prostředkům, které jsou zabezpečeny [autorizačním serverem](active-directory-v2-protocols.md#the-basics). Koncový bod platformy identit Microsoftu také umožňuje aplikacím třetích stran, které jsou registrované ve službě Azure AD, vydávat přístupové tokeny pro zabezpečené prostředky, jako jsou webová rozhraní API. Další informace o tom, jak nastavit aplikaci pro vydávání přístupových tokenů, najdete v [tématu Jak zaregistrovat aplikaci s koncovým bodem platformy identit microsoftu](quickstart-register-app.md). Doporučujeme používat OpenID Connect, pokud vytváříte [webovou aplikaci,](v2-app-types.md#web-apps) která je hostovaná na serveru a přístupná prostřednictvím prohlížeče.

## <a name="protocol-diagram-sign-in"></a>Diagram protokolu: Přihlášení

Nejzákladnější tok přihlášení má kroky uvedené v následujícím diagramu. Každý krok je podrobně popsán v tomto článku.

![Protokol OpenID Connect: Přihlášení](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Načtení dokumentu metadat OpenID Connect

OpenID Connect popisuje dokument metadat, který obsahuje většinu informací potřebných pro aplikaci k přihlášení. To zahrnuje informace, jako jsou adresy URL, které chcete použít, a umístění veřejných podpisových klíčů služby. Pro koncový bod platformy identity Microsoft u tohoto dokumentu metadat OpenID Connect byste měli použít:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Vyzkoušejte si to! Kliknutím [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) zobrazíte `common` konfiguraci klientů.

Může `{tenant}` mít jednu ze čtyř hodnot:

| Hodnota | Popis |
| --- | --- |
| `common` |Uživatelé s osobním účtem Microsoft a pracovním nebo školním účtem z Azure AD se můžou přihlásit k aplikaci. |
| `organizations` |K aplikaci se můžou přihlásit jenom uživatelé s pracovními nebo školními účty z Azure AD. |
| `consumers` |K aplikaci se mohou přihlásit jenom uživatelé s osobním účtem Microsoft. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` | K aplikaci se můžou přihlásit jenom uživatelé z konkrétního klienta Azure AD (ať už jsou členy v adresáři s pracovním nebo školním účtem nebo jsou to hosté v adresáři s osobním účtem Microsoft). Lze použít popisný název domény klienta Azure AD nebo identifikátor GUID klienta. Můžete také použít klienta `9188040d-6c67-4c5b-b112-36a304b66dad`příjemce , `consumers` místo klienta.  |

Metadata jsou jednoduchý dokument zápisu objektu JavaScript (JSON). Příklad najdete v následujícím úryvku. Obsah fragmentu je plně popsán ve [specifikaci OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

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

Pokud má vaše aplikace vlastní podpisové klíče v důsledku použití funkce `appid` [mapování deklarací identity,](active-directory-claims-mapping.md) musíte připojit parametr dotazu obsahující ID aplikace, abyste `jwks_uri` získali odkaz na informace o podpisovém klíči aplikace. Například: `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` obsahuje `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

Tento dokument metadat byste obvykle použili ke konfiguraci knihovny OpenID Connect nebo sady SDK. knihovna by k práci používala metadata. Pokud však nepoužíváte předem vytvořenou knihovnu OpenID Connect, můžete podle kroků ve zbývající části tohoto článku provést přihlášení ve webové aplikaci pomocí koncového bodu platformy identity Microsoftu.

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Když vaše webová aplikace potřebuje ověřit uživatele, může `/authorize` uživatele nasměrovat do koncového bodu. Tento požadavek je podobný první části [toku autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md)s těmito důležitými rozdíly:

* Požadavek musí `openid` obsahovat `scope` obor v parametru.
* Parametr `response_type` musí `id_token`obsahovat .
* Požadavek musí `nonce` obsahovat parametr.

> [!IMPORTANT]
> Aby bylo možné úspěšně požádat o token ID z koncového bodu /autorizace, registrace aplikace na [registračním portálu](https://portal.azure.com) musí mít implicitní udělení id_tokens povolena na kartě Ověřování (která nastaví `oauth2AllowIdTokenImplicitFlow` příznak v [manifestu aplikace).](reference-app-manifest.md) `true` Pokud není povolena, `unsupported_response` bude vrácena chyba: "Zadaný parametr pro vstupní parametr response_type není pro tohoto klienta povolen. Očekávaná hodnota je "kód".

Například:

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
> Klepnutím na následující odkaz spusťte tento požadavek. Po přihlášení bude váš prohlížeč přesměrován `https://localhost/myapp/`na adresu s tokenem ID v adresním řádku. Všimněte si, `response_mode=fragment` že tento požadavek používá (pouze pro demonstrační účely). Doporučujeme používat `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Můžete použít `{tenant}` hodnotu v cestě požadavku k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty `common`jsou `organizations` `consumers`identifikátory , , a tenant. Další informace naleznete v [tématu základy protokolu](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Požaduje se | **ID aplikace (klienta),** které je k vaší aplikaci přiřazené [na portálu Azure – možnosti registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `response_type` | Požaduje se | Musí `id_token` obsahovat pro přihlášení OpenID Connect. Může také obsahovat další `response_type` `code`hodnoty, například . |
| `redirect_uri` | Doporučené | Identifikátor URI přesměrování vaší aplikace, kde mohou být odpovědi na ověřování odesílány a přijímány vaší aplikací. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódována adresou URL. Pokud není k dispozici, koncový bod vybere jednu registrovanou redirect_uri náhodně odeslat uživatele zpět. |
| `scope` | Požaduje se | Mezera-oddělený seznam oborů. Pro OpenID Connect musí obsahovat obor `openid`, který se překládá k oprávnění "Přihlásit se k" v uznané. Do této žádosti o žádost o souhlas můžete zahrnout i další obory. |
| `nonce` | Požaduje se | Hodnota zahrnutá v požadavku vygenerovaná aplikací, která bude zahrnuta do výsledné id_token hodnotu jako deklaraci. Aplikace může ověřit tuto hodnotu ke zmírnění útoků přehrání tokenu. Hodnota je obvykle randomizované, jedinečný řetězec, který lze použít k identifikaci původu požadavku. |
| `response_mode` | Doporučené | Určuje metodu, která by měla být použita k odeslání výsledného autorizačního kódu zpět do aplikace. Může být `form_post` nebo `fragment`. Pro webové aplikace doporučujeme použít `response_mode=form_post`, aby byl zajištěn nejbezpečnější přenos tokenů do vaší aplikace. |
| `state` | Doporučené | Hodnota zahrnutá v požadavku, která bude také vrácena v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně generovaná jedinečná hodnota se obvykle používá k [zabránění útokům padělání požadavků na více webů](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, jako je například stránka nebo zobrazení, na které byl uživatel. |
| `prompt` | Nepovinné | Označuje typ interakce uživatele, která je požadována. Platné hodnoty jsou `login`v tuto `none`chvíli `consent`pouze , a . Deklarace `prompt=login` vynutí uživateli zadat své přihlašovací údaje na tuto žádost, která neguje jednotné přihlašování. Tvrzení `prompt=none` je opačné. Toto tvrzení zajišťuje, že uživatel není prezentována s žádnou interaktivní výzvu na adrese. Pokud požadavek nelze dokončit bezobslužně prostřednictvím jednotného přihlášení, koncový bod platformy identity Microsoftvrátí chybu. Deklarace `prompt=consent` spustí dialog souhlasu OAuth po přihlášení uživatele. Dialogové okno požádá uživatele o udělení oprávnění k aplikaci. |
| `login_hint` | Nepovinné | Tento parametr můžete použít k předběžnému vyplnění pole uživatelského jména a e-mailové adresy přihlašovací stránky uživatele, pokud znáte uživatelské jméno předem. Aplikace často používají tento parametr při opětovném ověřování, po již extrahování `preferred_username` uživatelského jména z dřívějšípřihlášení pomocí deklarace. |
| `domain_hint` | Nepovinné | Sféra uživatele ve federovaném adresáři.  Tím přeskočíte proces zjišťování na základě e-mailu, který uživatel prochází na přihlašovací stránce, pro mírně efektivnější uživatelské prostředí. Pro klienty, kteří jsou federovány prostřednictvím místního adresáře, jako je služba AD FS, to často vede k bezproblémovému přihlášení z důvodu existující relace přihlášení. |

V tomto okamžiku je uživatel vyzván k zadání pověření a dokončení ověřování. Koncový bod platformy identity společnosti Microsoft ověří, zda uživatel souhlasil `scope` s oprávněními uvedenými v parametru dotazu. Pokud uživatel nesouhlasil s žádným z těchto oprávnění, koncový bod platformy identit microsoftu vyzve uživatele k souhlasu s požadovanými oprávněními. Další informace o [oprávněních, souhlasu a víceklientských aplikacích](v2-permissions-and-consent.md).

Poté, co uživatel ověří a udělí souhlas, koncový bod platformy identit microsoftu vrátí odpověď vaší `response_mode` aplikaci na určené přesměrování URI pomocí metody zadané v parametru.

### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď při `response_mode=form_post` použití vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametr | Popis |
| --- | --- |
| `id_token` | ID token, který aplikace požadovala. `id_token` Parametr můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. Další informace o tokenech ID a [ `id_tokens` ](id-tokens.md)jejich obsahu naleznete v odkazu . |
| `state` | Pokud `state` je parametr zahrnut v požadavku, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v požadavku a odpovědi jsou identické. |

### <a name="error-response"></a>Odpověď na chybu

Chybové odpovědi mohou být také odeslány do identifikátoru URI přesměrování, aby je aplikace mohla zpracovat. Odpověď na chybu vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým dochází, a reagovat na chyby. |
| `error_description` | Konkrétní chybová zpráva, která vám může pomoci identifikovat hlavní příčinu chyby ověřování. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Chybové kódy chyb pro chyby autorizačního koncového bodu

Následující tabulka popisuje kódy chyb, které `error` mohou být vráceny v parametru chybové odpovědi:

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| `invalid_request` | Chyba protokolu, například chybějící povinný parametr. |Opravte a znovu odešlete požadavek. Toto je chyba vývoje, která je obvykle zachycena během počátečního testování. |
| `unauthorized_client` | Klientská aplikace nemůže požádat o autorizační kód. |K tomu obvykle dochází, když klientská aplikace není registrovaná ve službě Azure AD nebo není přidána do klienta Azure AD uživatele. Aplikace může vyzvat uživatele s pokyny k instalaci aplikace a přidat ji do Služby Azure AD. |
| `access_denied` | Vlastník prostředku odmítl souhlas. |Klientská aplikace může upozornit uživatele, že nemůže pokračovat, pokud uživatel souhlasí. |
| `unsupported_response_type` |Autorizační server nepodporuje typ odpovědi v požadavku. |Opravte a znovu odešlete požadavek. Toto je chyba vývoje, která je obvykle zachycena během počátečního testování. |
| `server_error` | Na serveru došlo k neočekávané chybě. |Opakujte požadavek. Tyto chyby mohou být důsledkem dočasných podmínek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné chyby. |
| `temporarily_unavailable` | Server je dočasně příliš zaneprázdněn pro zpracování požadavku. |Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
| `invalid_resource` | Cílový prostředek je neplatný, protože buď neexistuje, Azure AD ho nemůže najít nebo není správně nakonfigurovaný. |To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Služby Azure AD. |

## <a name="validate-the-id-token"></a>Ověření tokenu ID

Pouhé přijetí id_token nestačí k ověření uživatele; musíte ověřit podpis id_token a ověřit deklarace identity v tokenu podle požadavků vaší aplikace. Koncový bod platformy identit microsoftu používá [webové tokeny JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografii veřejného klíče k podepisování tokenů a ověřování, že jsou platné.

Můžete ověřit `id_token` v kódu klienta, ale běžnou praxí `id_token` je odeslat back-endový server a provést ověření tam. Po ověření podpisu id_token, existuje několik deklarací, které budete muset ověřit. Další informace naleznete v [ `id_token` odkazu,](id-tokens.md) včetně ověřování tokenů a [důležitých informací o podepisování klíče .](active-directory-signing-key-rollover.md) [Validating Tokens](id-tokens.md#validating-an-id_token) Doporučujeme používat knihovnu pro analýzu a ověřování tokenů – pro většinu jazyků a platforem je k dispozici alespoň jedna.

Můžete také ověřit další deklarace identity v závislosti na vašem scénáři. Mezi běžná ověření patří:

* Zajištění, že se uživatel/organizace zaregistrovala k aplikaci.
* Zajištění řádné autorizace/oprávnění uživatele
* Zajištění určité síly ověřování došlo, jako je například vícefaktorové ověřování.

Po ověření id_token můžete zahájit relaci s uživatelem a použít deklarace identity v id_token k získání informací o uživateli ve vaší aplikaci. Tyto informace mohou být použity pro zobrazení, záznamy, personalizace, atd.

## <a name="send-a-sign-out-request"></a>Odeslání žádosti o odhlášení

Pokud chcete odhlásit uživatele z vaší aplikace, nestačí vymazat soubory cookie aplikace nebo jinak ukončit relaci uživatele. Musíte také přesměrovat uživatele na koncový bod platformy identity Společnosti Microsoft, abyste se odhlásit. Pokud tak neučiníte, uživatel se znovu ověří ve vaší aplikaci, aniž by znovu zadali svá pověření, protože bude mít platnou relaci jednotného přihlášení s koncovým bodem platformy identit Microsoftu.

Uživatele můžete přesměrovat na `end_session_endpoint` dokument metadat OpenID Connect:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametr | Podmínka | Popis |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Doporučené | Adresa URL, na kterou je uživatel přesměrován po úspěšném odhlášení. Pokud parametr není zahrnuta, uživatel se zobrazí obecná zpráva, která je generována koncovýbod platformy identit y Microsoft. Tato adresa URL se musí shodovat s jedním z identifikátorů URI přesměrování registrovaných pro vaši aplikaci na portálu pro registraci aplikací. |

## <a name="single-sign-out"></a>Jednotné odhlašování

Při přesměrování uživatele do `end_session_endpoint`, koncový bod platformy identit y Microsoft vymaže relaci uživatele z prohlížeče. Uživatel však může být stále přihlášen k jiným aplikacím, které používají účty Microsoft pro ověřování. Chcete-li povolit tyto aplikace odhlásit uživatele současně, koncový bod platformy identit `LogoutUrl` microsoft odešle požadavek HTTP GET na registrované všechny aplikace, které je uživatel aktuálně přihlášen. Aplikace musí reagovat na tento požadavek vymazáním libovolné `200` relace, která identifikuje uživatele a vrací odpověď. Pokud chcete podporovat jednotné odhlášení ve vaší aplikaci, `LogoutUrl` je nutné implementovat takové v kódu aplikace. Můžete nastavit `LogoutUrl` z portálu pro registraci aplikací.

## <a name="protocol-diagram-access-token-acquisition"></a>Diagram protokolu: Získání tokenu aplikace Access

Mnoho webových aplikací musí nejen přihlásit uživatele, ale také pro přístup k webové službě jménem uživatele pomocí OAuth. Tento scénář kombinuje OpenID Connect pro ověřování uživatelů a současně získává autorizační kód, který můžete použít k získání přístupových tokenů, pokud používáte tok autorizačního kódu OAuth.

Úplný přihlašovací a token ový tok OpenID Connect vypadá podobně jako v dalším diagramu. Každý krok podrobně popisujeme v dalších částech článku.

![Protokol OpenID Connect: Získání tokenu](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Získání přístupových tokenů
Chcete-li získat přístupové tokeny, upravte požadavek na přihlášení:

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
> Klepnutím na následující odkaz spusťte tento požadavek. Po přihlášení je prohlížeč přesměrován `https://localhost/myapp/`na aplikaci , na kterou je token ID a kód v adresním řádku. Všimněte si, `response_mode=fragment` že tento požadavek používá pouze pro demonstrační účely. Doporučujeme používat `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Zahrnutím oborů oprávnění do `response_type=id_token code`požadavku a pomocí zajišťuje koncový bod platformy identit společnosti Microsoft, že `scope` uživatel souhlasil s oprávněními uvedenými v parametru dotazu. Vrátí autorizační kód do vaší aplikace pro výměnu za přístupový token.

### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď `response_mode=form_post` z použití vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametr | Popis |
| --- | --- |
| `id_token` | ID token, který aplikace požadovala. Token ID můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. Další podrobnosti o tokenech ID a jejich obsahu najdete v [ `id_tokens` odkazu](id-tokens.md). |
| `code` | Autorizační kód, který aplikace požadovala. Aplikace můžete použít autorizační kód požádat o přístupový token pro cílový prostředek. Autorizační kód nemá dlouhého trvání. Platnost autorizačního kódu obvykle vyprší přibližně za 10 minut. |
| `state` | Pokud je parametr stavu zahrnut a požadavek, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v požadavku a odpovědi jsou identické. |

### <a name="error-response"></a>Odpověď na chybu

Chybové odpovědi mohou být také odeslány do identifikátoru URI přesměrování, aby je aplikace mohla správně zpracovat. Odpověď na chybu vypadá takto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým dochází, a reagovat na chyby. |
| `error_description` | Konkrétní chybová zpráva, která vám může pomoci identifikovat hlavní příčinu chyby ověřování. |

Popis možných kódů chyb a doporučených odpovědí klientů naleznete v [tématu Chybové kódy pro chyby autorizačního bodu](#error-codes-for-authorization-endpoint-errors).

Pokud máte autorizační kód a token ID, můžete přihlásit uživatele a získat přístupové tokeny jeho jménem. Chcete-li uživatele přihlásit, musíte ověřit token ID [přesně tak, jak je popsáno](id-tokens.md#validating-an-id_token). Chcete-li získat přístupové tokeny, postupujte podle kroků popsaných v [dokumentaci toku kódu OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).
