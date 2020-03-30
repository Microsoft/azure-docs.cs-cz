---
title: Přehled tokenů – Azure Active Directory B2C
description: Přečtěte si o tokenech používaných ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbbd083a6b62733d71c316af95dffaa188b28955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186484"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Přehled tokenů ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) vydává několik typů tokenů zabezpečení při procesu každého [toku ověřování](application-types.md). Tento dokument popisuje formát, charakteristiky zabezpečení a obsah každého typu tokenu.

## <a name="token-types"></a>Typy tokenů

Azure AD B2C podporuje [protokoly OAuth 2.0 a OpenID Connect](protocols-overview.md), které využívají tokeny pro ověřování a zabezpečený přístup k prostředkům. Všechny tokeny používané v Azure AD B2C jsou [JSON webové tokeny (JWTs),](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) které obsahují kontrolní výrazy informace o nositele a předmět tokenu.

Následující tokeny se používají při komunikaci s Azure AD B2C:

- *ID token* - JWT, který obsahuje deklarace identity, které můžete použít k identifikaci uživatelů ve vaší aplikaci. Tento token je bezpečně odeslán v požadavcích HTTP pro komunikaci mezi dvěma součástmi stejné aplikace nebo služby. Deklarace identity můžete použít v tokenu ID, jak uznáte za vhodné. Běžně se používají k zobrazení informací o účtu nebo k rozhodování o řízení přístupu v aplikaci. ID tokeny jsou podepsány, ale nejsou šifrovány. Když vaše aplikace nebo rozhraní API obdrží token ID, musí ověřit podpis, aby prokázal, že token je autentický. Vaše aplikace nebo rozhraní API musí také ověřit několik deklarací identity v tokenu prokázat, že je platný. V závislosti na požadavcích na scénář se deklarace identity ověřené aplikací mohou lišit, ale vaše aplikace musí provést některé běžné ověření deklarací identity v každém scénáři.
- *Přístupový token* – JWT, který obsahuje deklarace identity, které můžete použít k identifikaci udělených oprávnění k vašim apim. Přístupové tokeny jsou podepsané, ale nejsou šifrované. Přístupové tokeny se používají k poskytování přístupu k api a serverům prostředků.  Když vaše rozhraní API obdrží přístupový token, musí ověřit podpis, aby prokázal, že token je autentický. Vaše rozhraní API musí také ověřit několik deklarací identity v tokenu prokázat, že je platný. V závislosti na požadavcích na scénář se deklarace identity ověřené aplikací mohou lišit, ale vaše aplikace musí provést některé běžné ověření deklarací identity v každém scénáři.
- *Aktualizovat tokeny* – obnovovací tokeny se používají k získání nových tokenů ID a přístupových tokenů v toku OAuth 2.0. Poskytují vaší aplikaci dlouhodobý přístup k prostředkům jménem uživatelů, aniž by bylo nutné s těmito uživateli spolupracovat. Obnovovací tokeny jsou pro vaši aplikaci neprůhledné. Jsou vydávány Azure AD B2C a můžete zkontrolovat a interpretovat jenom Azure AD B2C. Jsou dlouhodobé, ale vaše aplikace by neměla být zapsána s očekáváním, že obnovovací token bude trvat určitou dobu. Obnovovat tokeny může být zrušena kdykoli z různých důvodů. Jediný způsob, jak pro vaši aplikaci vědět, pokud je platný token aktualizace je pokusit se jej uplatnit tím, že žádost o token azure AD B2C. Když uplatníte obnovovací token pro nový token, obdržíte nový obnovovací token v odpovědi tokenu. Uložte nový obnovovací token. Nahradí obnovovací token, který jste dříve použili v požadavku. Tato akce pomáhá zaručit, že vaše obnovovací tokeny zůstanou platné tak dlouho, jak je to možné.

## <a name="endpoints"></a>Koncové body

[Registrovaná aplikace](tutorial-register-applications.md) přijímá tokeny a komunikuje s Azure AD B2C odesláním požadavků na tyto koncové body:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Tokeny zabezpečení, které vaše aplikace obdrží z Azure AD B2C může pocházet z koncové body `/authorize` nebo. `/token` Když jsou tokeny ID `/authorize` získány z koncového bodu, provádí se pomocí [implicitního toku](implicit-flow-single-page-application.md), který se často používá pro uživatele, kteří se přihlašují k webovým aplikacím založeným na JavaScriptu. Když id tokeny jsou `/token` získány z koncového bodu, je provedeno pomocí [toku autorizačního kódu](openid-connect.md#get-a-token), který udržuje token skrytý z prohlížeče.

## <a name="claims"></a>Deklarace identity

Při použití Azure AD B2C, máte jemně odstupňovanou kontrolu nad obsahem tokenů. Můžete nakonfigurovat [toky uživatelů](user-flow-overview.md) a [vlastní zásady](custom-policy-overview.md) pro odesílání určitých sad uživatelských dat v deklaracích identity, které jsou požadovány pro vaši aplikaci. Tyto deklarace identity mohou zahrnovat standardní vlastnosti, jako je **displayName** a **emailAddress**. Vaše aplikace mohou tyto deklarace identity používat k bezpečnému ověření uživatelů a požadavků.

Deklarace identity v tokenech ID nejsou vráceny v určitém pořadí. Nové deklarace identity lze zavést v tokenech ID kdykoli. Vaše aplikace by neměla přerušit jako nové nároky jsou zavedeny. Do deklarací identity můžete také zahrnout [vlastní uživatelské atributy.](user-flow-custom-attributes.md)

V následující tabulce jsou uvedeny deklarace identity, které můžete očekávat v tokenech ID a přístupových tokenech vydaných Azure AD B2C.

| Name (Název) | Deklarovat | Příklad hodnoty | Popis |
| ---- | ----- | ------------- | ----------- |
| Cílová skupina | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifikuje zamýšleného příjemce tokenu. Pro Azure AD B2C je cílová skupina ID aplikace. Vaše aplikace by měla ověřit tuto hodnotu a odmítnout token, pokud se neshoduje. Cílová skupina uživatelů je synonymem pro zdroj. |
| Vystavitel | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifikuje službu tokenů zabezpečení (STS), která vytvoří a vrátí token. Také identifikuje adresář, ve kterém byl uživatel ověřen. Vaše aplikace by měla ověřit deklaraci vystavittele a ujistěte se, že token pochází z příslušného koncového bodu. |
| Vydáno na adrese | `iat` | `1438535543` | Čas, kdy byl vydán token, reprezentovaný v epochovém čase. |
| Doba vypršení platnosti | `exp` | `1438539443` | Čas, kdy se token stane neplatným, reprezentované v epochovém čase. Vaše aplikace by měla použít toto tvrzení k ověření platnosti životnosti tokenu. |
| Ne dříve | `nbf` | `1438535543` | Čas, kdy se token stane platným, reprezentované v epochovém čase. Tento čas je obvykle stejný jako čas tokenbyl vydán. Vaše aplikace by měla použít toto tvrzení k ověření platnosti životnosti tokenu. |
| Version | `ver` | `1.0` | Verze tokenu ID, jak je definováno Azure AD B2C. |
| Hash kódu | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Hash kódu zahrnutý v tokenu ID pouze v případě, že je token vydán společně s autorizačním kódem OAuth 2.0. Hodnotu hash kódu lze použít k ověření pravosti autorizačního kódu. Další informace o tom, jak provést toto ověření, naleznete ve [specifikaci OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash přístupového tokenu | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Hash přístupového tokenu zahrnutý v tokenu ID pouze v případě, že je token vydán společně s přístupovým tokenem OAuth 2.0. Hodnotu hash přístupového tokenu lze použít k ověření pravosti přístupového tokenu. Další informace o tom, jak provést toto ověření, naleznete ve [specifikaci OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Nonce je strategie používaná ke zmírnění útoků opětovného přehrání tokenů. Aplikace můžete zadat nonce v žádosti o `nonce` autorizaci pomocí parametru dotazu. Hodnota, kterou zadáte v požadavku je emitována nezměněné v `nonce` deklaraci tokenu ID pouze. Toto tvrzení umožňuje aplikaci ověřit hodnotu oproti hodnotě zadané v požadavku. Vaše aplikace by měla provést toto ověření během procesu ověření tokenu ID. |
| Subjekt | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Objekt zabezpečení, o kterém token uplatňuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze ji znovu přiřadit ani znovu použít. Lze jej použít k bezpečnému provádění kontrol autorizace, například při použití tokenu pro přístup k prostředku. Ve výchozím nastavení je deklarace předmětu naplněna ID objektu uživatele v adresáři. |
| Odkaz na třídu kontextu ověřování | `acr` | Neuvedeno | Používá se pouze se staršími zásadami. |
| Zásady rámce důvěryhodnosti | `tfp` | `b2c_1_signupsignin1` | Název zásady, která byla použita k získání tokenu ID. |
| Čas ověření | `auth_time` | `1438535543` | Čas, kdy uživatel naposledy zadal pověření, reprezentované v epochovém čase. Neexistuje žádná diskriminace mezi tímto ověřováním je nové přihlášení, relace jednotného přihlašování (SSO) nebo jiného typu přihlášení. Je `auth_time` to poslední, kdy aplikace (nebo uživatel) inicioval pokus o ověření proti Azure AD B2C. Metoda použitá k ověření není rozlišena. |
| Rozsah | `scp` | `Read`| Oprávnění udělená prostředku pro přístupový token. Více udělených oprávnění je odděleno mezerou. |
| Autorizovaná strana | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | **ID aplikace** klientské aplikace, která iniciovala požadavek. |

## <a name="configuration"></a>Konfigurace

Následující vlastnosti se používají ke [správě životnosti tokenů zabezpečení](configure-tokens.md) vyzařovaných Azure AD B2C:

- **& životnost id tokenu (minuty)** – životnost tokenu nosiče OAuth 2.0, který se používá k získání přístupu k chráněnému prostředku. Výchozí hodnota je 60 minut. Minimální (včetně) je 5 minut. Maximální (včetně) je 1440 minut.

- **Životnost obnovovacího tokenu (dny)** – maximální doba, po kterou lze aktualizovat token k získání nového přístupu nebo tokenu ID. Časové období také zahrnuje získání nového obnovovacího tokenu, pokud byl vaší aplikaci udělen `offline_access` obor. Výchozí hodnota je 14 dní. Minimální (včetně) je jeden den. Maximální (včetně) je 90 dní.

- **Obnovovat token posuvné okno životnost (dny)** - Po uplynutí této doby je uživatel nucen znovu ověřit, bez ohledu na dobu platnosti poslední obnovovací token získané aplikací. To může být k dispozici pouze v případě, že přepínač je nastaven na **ohraničené**. Musí být větší nebo rovna hodnotě **životnosti tokenu aktualizace (dny).** Pokud je přepínač nastaven na **hodnotu Bez navázané**hodnoty , nelze zadat určitou hodnotu. Výchozí hodnota je 90 dní. Minimální (včetně) je jeden den. Maximální (včetně) je 365 dní.

Následující případy použití jsou povoleny pomocí těchto vlastností:

- Umožněte uživateli zůstat přihlášeni k mobilní aplikaci neomezeně dlouho, pokud je uživatel v aplikaci neustále aktivní. Můžete nastavit **obnovit token posuvné okno životnost (dny)** na **bezohraničení** v toku přihlášení uživatele.
- Splňte požadavky na zabezpečení a dodržování předpisů ve vašem oboru nastavením příslušné životnosti přístupových tokenů.

Tato nastavení nejsou k dispozici pro toky uživatelů resetování hesla.

## <a name="compatibility"></a>Kompatibilita

Ke [správě kompatibility tokenů](configure-tokens.md)se používají následující vlastnosti :

- **Deklarace (iss)** – Tato vlastnost identifikuje klienta Azure AD B2C, který vydal token. Výchozí hodnota je `https://<domain>/{B2C tenant GUID}/v2.0/`. Hodnota `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` zahrnuje ID pro klienta Azure AD B2C a tok uživatele, který byl použit v žádosti o token. Pokud vaše aplikace nebo knihovna potřebuje Azure AD B2C, aby byla kompatibilní se [specifikací OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html), použijte tuto hodnotu.

- **Předmět (dílčí) deklarace –** Tato vlastnost identifikuje entitu, pro kterou token uplatňuje informace. Výchozí hodnota je **ObjectID**, `sub` která naplní deklaraci v tokenu ID objektu uživatele. Hodnota **Není podporována** je k dispozici pouze pro zpětnou kompatibilitu. Doporučujeme přepnout na **ObjectID,** jakmile budete moci.

- **Deklarace představující ID zásad** – Tato vlastnost identifikuje typ deklarace, do kterého je naplněn název zásady použitý v požadavku na token. Výchozí hodnota je `tfp`. Hodnota `acr` je k dispozici pouze pro zpětnou kompatibilitu.

## <a name="pass-through"></a>Průchod

Když se spustí cesta uživatele, Azure AD B2C obdrží přístupový token od poskytovatele identity. Azure AD B2C používá tento token k načtení informací o uživateli. [Povolíte deklaraci v toku uživatelů](idp-pass-through-user-flow.md) nebo [definujete deklaraci ve vlastních zásadách,](idp-pass-through-custom.md) abyste předali token aplikacím, které se zaregistrujete v Azure AD B2C. Vaše aplikace musí používat [tok uživatele v2](user-flow-versions.md) využít předávání tokenu jako deklarace.

Azure AD B2C v současné době podporuje pouze předávání přístupový token oAuth 2.0 zprostředkovatelů identit, které zahrnují Facebook a Google. Pro všechny ostatní zprostředkovatele identity je deklarace identity vrácena prázdná.

## <a name="validation"></a>Ověřování

Chcete-li ověřit token, aplikace by měla zkontrolovat podpis i deklarace identity tokenu. Mnoho open source knihovny jsou k dispozici pro ověření JWTs, v závislosti na preferovaném jazyce. Doporučujeme prozkoumat tyto možnosti spíše než implementovat vlastní logiku ověřování.

### <a name="validate-signature"></a>Ověřit podpis

JWT obsahuje tři segmenty, *záhlaví*, *tělo*a *podpis*. Segment podpisu lze použít k ověření pravosti tokenu tak, aby mohl být důvěryhodný vaší aplikací. Tokeny Azure AD B2C jsou podepsané pomocí standardních algoritmů asymetrického šifrování, jako je například RSA 256.

Záhlaví tokenu obsahuje informace o klíč a šifrování metoda použitá k podepsání tokenu:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Hodnota deklarace **identity alg** je algoritmus, který byl použit k podepsání tokenu. Hodnota deklarace **příjemce** je veřejný klíč, který byl použit k podepsání tokenu. V každém okamžiku Azure AD B2C můžete podepsat token pomocí některou ze sady párů veřejných a soukromých klíčů. Azure AD B2C pravidelně otáčí možnou sadu klíčů. Vaše aplikace by měla být zapsána pro automatické zpracování těchto klíčových změn. Přiměřená frekvence pro kontrolu aktualizací veřejných klíčů používaných Azure AD B2C je každých 24 hodin.

Azure AD B2C má koncový bod metadat OpenID Connect. Pomocí tohoto koncového bodu aplikace můžete požadovat informace o Azure AD B2C za běhu. Tyto informace zahrnují koncové body, obsah tokenu a podpisové klíče tokenu. Váš klient Azure AD B2C obsahuje dokument metadat JSON pro každou zásadu. Dokument metadat je objekt JSON, který obsahuje několik užitečných informací. Metadata obsahuje **jwks_uri**, který poskytuje umístění sadu veřejných klíčů, které se používají k podepisování tokenů. Toto umístění je k dispozici zde, ale je nejlepší načíst umístění dynamicky pomocí dokumentu metadat a analýzy **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
Dokument JSON umístěný na této adrese URL obsahuje všechny informace o veřejném klíči, které se používají v určitém okamžiku. Vaše aplikace můžete `kid` použít deklaraci v záhlaví JWT vybrat veřejný klíč v dokumentu JSON, který se používá k podepsání konkrétního tokenu. Potom může provést ověření podpisu pomocí správného veřejného klíče a uvedeného algoritmu.

Dokument metadat pro `B2C_1_signupsignin1` zásady `contoso.onmicrosoft.com` v tenantovi se nachází na adrese:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Chcete-li zjistit, která zásada byla použita k podepsání tokenu (a kam přejít na vyžádání metadat), máte dvě možnosti. Nejprve název zásady je `acr` součástí deklarace v tokenu. Můžete analyzovat deklarace identity z těla JWT base-64 dekódování těla a rekonstruovat řetězec JSON, který výsledky. Deklarace `acr` je název zásady, která byla použita k vystavení tokenu. Druhou možností je zakódovat zásadu v `state` hodnotě parametru při vydání požadavku a potom jej dekódovat, abyste zjistili, která zásada byla použita. Obě metody jsou platné.

Popis způsobu ověření podpisu je mimo rozsah tohoto dokumentu. K dispozici je mnoho knihoven s otevřeným zdrojovým kódem, které vám pomohou ověřit token.

### <a name="validate-claims"></a>Ověřit deklarace identity

Když vaše aplikace nebo rozhraní API obdrží token ID, měl by také provést několik kontrol proti deklaracím identity v tokenu ID. Je třeba zkontrolovat následující nároky:

- **publikum** - Ověří, že token ID byl určen k dané vaší žádosti.
- **není dříve** a **doba vypršení platnosti** – ověří, že platnost tokenu ID nevypršela.
- **vystavitel** – ověří, že token byl vydán do vaší aplikace Azure AD B2C.
- **nonce** - Strategie pro zmírnění útoku znovu přehrát token.

Úplný seznam ověření, která by měla vaše aplikace provést, naleznete ve [specifikaci OpenID Connect](https://openid.net).

## <a name="next-steps"></a>Další kroky

Další informace o [používání přístupových tokenů](access-tokens.md).

