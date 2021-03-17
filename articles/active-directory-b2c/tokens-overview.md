---
title: Přehled tokenů – Azure Active Directory B2C
description: Přečtěte si o tokenech používaných v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b4e268d35a2e31db0ce92ff61e66fd23bce68e38
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516362"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Přehled tokenů v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) emituje několik typů tokenů zabezpečení při zpracovávání každého [toku ověřování](application-types.md). Tento dokument popisuje formát, charakteristiky zabezpečení a obsah každého typu tokenu.

## <a name="token-types"></a>Typy tokenů

Azure AD B2C podporuje [protokoly OAuth 2,0 a OpenID Connect](protocols-overview.md), které využívají tokeny pro ověřování a zabezpečený přístup k prostředkům. Všechny tokeny používané v Azure AD B2C jsou [webové tokeny JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) , které obsahují kontrolní výrazy informací o nosiči a předmětu tokenu.

Při komunikaci s Azure AD B2C se používají následující tokeny:

- *ID tokenu – token* JWT, který obsahuje deklarace identity, které můžete použít k identifikaci uživatelů v aplikaci. Tento token se bezpečně pošle v požadavcích HTTP na komunikaci mezi dvěma komponentami stejné aplikace nebo služby. Deklarace identity v tokenu ID můžete použít podle svých potřeb. Obvykle se používají k zobrazení informací o účtu nebo k rozhodování o řízení přístupu v aplikaci. Tokeny ID jsou podepsané, ale nejsou zašifrované. Když vaše aplikace nebo rozhraní API obdrží token ID, musí ověřit signaturu a prokázat, že token je pravý. Vaše aplikace nebo rozhraní API musí také ověřit pár deklarací identity v tokenu, aby bylo možné prokázat, že je platná. V závislosti na požadavcích na scénář se může deklarace identity ověřené aplikací lišit, ale aplikace musí v každém scénáři provádět některé běžné validace deklarací identity.
- *Přístupový token – token* JWT obsahující deklarace identity, které můžete použít k identifikaci udělených oprávnění pro vaše rozhraní API. Přístupové tokeny jsou podepsané, ale nejsou zašifrované. Přístupové tokeny slouží k poskytnutí přístupu k rozhraním API a serverům prostředků.  Když rozhraní API obdrží přístupový token, musí ověřit signaturu a prokázat tak, že token je pravý. Vaše rozhraní API musí také ověřit, jestli je v tokenu několik deklarací identity, aby bylo možné prokázat, že je platný. V závislosti na požadavcích na scénář se může deklarace identity ověřené aplikací lišit, ale aplikace musí v každém scénáři provádět některé běžné validace deklarací identity.
- *Aktualizovat token* – aktualizace tokenů se používají k získání nových tokenů ID a přístupových tokenů v toku OAuth 2,0. Poskytují aplikaci dlouhodobě přístup k prostředkům jménem uživatelů bez nutnosti interakce s těmito uživateli. Aktualizační tokeny jsou neprůhledné pro vaši aplikaci. Jsou vydávány Azure AD B2C a lze je prozkoumat a interpretovat pouze pomocí Azure AD B2C. Jsou dlouhotrvající, ale aplikace by neměla být zapsána očekávaným způsobem, že obnovovací token bude po určitou dobu trvat. Platnost tokenů aktualizace lze kdykoli zrušit z nejrůznějších důvodů. Jediným způsobem, jak aplikace zjistit, jestli je obnovovací token platný, je pokus o uplatnění žádosti o token na Azure AD B2C. Při uplatnění aktualizačního tokenu pro nový token obdržíte v odpovědi na token nový token aktualizace. Uložte nový obnovovací token. Nahrazuje obnovovací token, který jste předtím použili v žádosti. Tato akce pomáhá zaručit, že vaše obnovovací tokeny zůstanou platné po dobu co možná. Mějte na paměti, že jednostránkové aplikace používající tok autorizačního kódu s PKCE vždy mají dobu životnosti obnovovacího tokenu 24 hodin. [Přečtěte si další informace o dopadech aktualizace tokenů v prohlížeči na zabezpečení](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="endpoints"></a>Koncové body

[Registrovaná aplikace](tutorial-register-applications.md) přijímá tokeny a komunikuje s Azure AD B2C odesláním požadavků do těchto koncových bodů:

- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize`
- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token`

Tokeny zabezpečení, které vaše aplikace přijímá z Azure AD B2C můžou pocházet z `/authorize` `/token` koncových bodů nebo. Pokud jsou tokeny ID získány z `/authorize` koncového bodu, je provedeno pomocí [implicitního toku](implicit-flow-single-page-application.md), který se často používá pro uživatele přihlašování k webovým aplikacím založeným na jazyce JavaScript. Když se tokeny ID získávají z `/token` koncového bodu, provádí se pomocí [toku autorizačního kódu](openid-connect.md#get-a-token), který tento token udržuje v prohlížeči skrytý.

## <a name="claims"></a>Deklarace identit

Při použití Azure AD B2C máte jemně odstupňovanou kontrolu nad obsahem vašich tokenů. Můžete nakonfigurovat [toky uživatelů](user-flow-overview.md) a [vlastní zásady](custom-policy-overview.md) , které odesílají určité sady uživatelských dat v deklaracích identity, které jsou nutné pro vaši aplikaci. Tyto deklarace můžou zahrnovat standardní vlastnosti, jako jsou **DisplayName** a **EmailAddress**. Vaše aplikace můžou tyto deklarace identity použít k bezpečnému ověření uživatelů a požadavků.

Deklarace identity v tokenech ID se nevrací v žádném konkrétním pořadí. Nové deklarace identity je možné kdykoli zavést v tokenech ID. Vaše aplikace by se neměla porušit při zavedení nových deklarací. V deklaracích můžete také zahrnout [vlastní atributy uživatele](user-flow-custom-attributes.md) .

V následující tabulce jsou uvedeny deklarace identity, které můžete očekávat v tokenech ID a přístupových tokenech vydaných Azure AD B2C.

| Name | Deklarovat | Příklad hodnoty | Popis |
| ---- | ----- | ------------- | ----------- |
| Cílová skupina | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifikuje zamýšleného příjemce tokenu. Pro Azure AD B2C je cílovou skupinou ID aplikace. Vaše aplikace by měla tuto hodnotu ověřit a zamítnout token, pokud se neshoduje. Cílová skupina je synonymum s prostředkem. |
| Vystavitel | `iss` |`https://<tenant-name>.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifikuje službu tokenů zabezpečení (STS), která vytvoří a vrátí token. Identifikuje taky adresář, ve kterém se uživatel ověřil. Vaše aplikace by měla ověřit deklaraci vystavitele, aby se zajistilo, že token pochází z příslušného koncového bodu. |
| Vydáno v | `iat` | `1438535543` | Čas vydání tokenu, který je reprezentován v epocha času. |
| Čas vypršení platnosti | `exp` | `1438539443` | Čas, kdy se token stal neplatným, reprezentovaný v epocha času. Vaše aplikace by měla tuto deklaraci identity použít k ověření platnosti životnosti tokenu. |
| Ne před | `nbf` | `1438535543` | Čas, kdy bude token platný, reprezentovaný v epocha času. Tato doba je obvykle stejná jako čas, kdy byl token vydán. Vaše aplikace by měla tuto deklaraci identity použít k ověření platnosti životnosti tokenu. |
| Verze | `ver` | `1.0` | Verze tokenu ID definovaného Azure AD B2C. |
| Hodnota hash kódu | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Hodnota hash kódu je obsažena v tokenu ID pouze v případě, že je token vydán společně s autorizačním kódem OAuth 2,0. Hodnota hash kódu se dá použít k ověření pravosti autorizačního kódu. Další informace o tom, jak provést toto ověření, najdete v tématu [specifikace OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hodnota hash tokenu přístupu | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Hodnota hash přístupového tokenu, která je obsažená v tokenu ID jenom v případě, že je token vydaný společně s přístupovým tokenem OAuth 2,0. K ověření pravosti přístupového tokenu se dá použít hodnota hash přístupového tokenu. Další informace o tom, jak provést toto ověření, najdete v tématu [specifikace OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) .  |
| Generované | `nonce` | `12345` | Hodnota nonce je strategie používaná ke zmírnění útoků opakovaného přehrání tokenu. Vaše aplikace může zadat hodnotu NONCE v žádosti o autorizaci pomocí `nonce` parametru dotazu. Hodnota, kterou zadáte v požadavku, je vygenerována beze změny v `nonce` deklaraci identity tokenu ID. Tato deklarace umožňuje vaší aplikaci ověřit hodnotu oproti hodnotě zadané v požadavku. Vaše aplikace by měla provést toto ověření během procesu ověřování tokenu ID. |
| Předmět | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Objekt zabezpečení, o kterém token vyhodnotí informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze ji znovu přiřadit ani použít znovu. Dá se použít k bezpečnému provádění kontrol autorizace, například když se token používá pro přístup k prostředku. Ve výchozím nastavení se deklarace identity subjektu naplní s ID objektu uživatele v adresáři. |
| Referenční dokumentace třídy kontextu ověřování | `acr` | Není | Používá se jenom se staršími zásadami. |
| Zásada pro pravidlo důvěryhodnosti | `tfp` | `b2c_1_signupsignin1` | Název zásady, která byla použita k získání tokenu ID. |
| Čas ověřování | `auth_time` | `1438535543` | Čas, kdy uživatel naposledy zadal pověření, reprezentovaný v epocha čase. Neexistuje žádná diskriminace mezi tímto ověřováním, jedná se o nové přihlášení, relaci jednotného přihlašování (SSO) nebo jiný typ přihlášení. `auth_time`Je poslední čas, kdy aplikace (nebo uživatel) iniciovala pokus o ověření u Azure AD B2C. Metoda použitá k ověření není odlišná. |
| Obor | `scp` | `Read`| Oprávnění udělená prostředku pro přístupový token. Vícenásobná udělená oprávnění jsou oddělená mezerou. |
| Autorizovaná strana | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | **ID aplikace** klientské aplikace, která iniciovala požadavek. |

## <a name="configuration"></a>Konfigurace

Následující vlastnosti se používají ke [správě životností tokenů zabezpečení](configure-tokens.md) emitovaných Azure AD B2C:

- **Přístup k životnosti tokenu ID & (minuty)** – doba životnosti nosného tokenu OAuth 2,0, který se používá k získání přístupu k chráněnému prostředku. Výchozí hodnota je 60 minut. Minimální (včetně) je 5 minut. Maximální (včetně) je 1440 minut.

- **Doba platnosti tokenu aktualizace (dny)** – maximální doba, po jejímž uplynutí může být obnovovací token použit k získání nového přístupu nebo tokenu ID. Toto časové období také pokrývá získání nového obnovovacího tokenu, pokud je vaší aplikaci udělen `offline_access` obor. Výchozí hodnota je 14 dní. Minimální (včetně) je jeden den. Maximální (včetně) je 90 dní.

- **Doba platnosti posuvných oken (dnů) aktualizačního tokenu (dny)** – po uplynutí tohoto časového období se uživatel nuceně znovu neověřuje bez ohledu na dobu platnosti nejnovějšího obnovovacího tokenu, který aplikace získala. Dá se zadat jenom v případě, že je přepínač nastavený na **Bounded**. Musí být větší než nebo rovno hodnotě **životnosti obnovovacího tokenu (dny)** . Pokud je přepínač nastavený na **neohraničený**, nemůžete zadat konkrétní hodnotu. Výchozí hodnota je 90 dní. Minimální (včetně) je jeden den. Maximální (včetně) je 365 dní.

Následující případy použití jsou povolené pomocí těchto vlastností:

- Umožní uživateli zůstat přihlášení k mobilní aplikaci po neomezenou dobu, pokud je uživatel v aplikaci neustále aktivní. Můžete nastavit **dobu trvání posuvných oken aktualizačního tokenu (dny)** na **neohraničený** uživatelským tokem přihlášení.
- Nastavením příslušných životností přístupového tokenu vyhovět požadavkům na zabezpečení a dodržování předpisů v oboru.

Tato nastavení nejsou k dispozici pro uživatelské toky resetování hesla.

## <a name="compatibility"></a>Kompatibilita

Ke [správě kompatibility tokenů](configure-tokens.md)se používají následující vlastnosti:

- **Vystavitelský nárok** – Tato vlastnost identifikuje tenanta Azure AD B2C, který token vystavil. Výchozí hodnota je `https://<domain>/{B2C tenant GUID}/v2.0/`. Hodnota `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` zahrnuje ID pro klienta Azure AD B2C i tok uživatele, který byl použit v žádosti o token. Pokud vaše aplikace nebo knihovna potřebuje, aby Azure AD B2C kompatibilní se [specifikací OpenID Connect Discovery 1,0](https://openid.net/specs/openid-connect-discovery-1_0.html), použijte tuto hodnotu.

- **Deklarace subjektu (sub)** – Tato vlastnost identifikuje entitu, pro kterou token uplatňuje informace. Výchozí hodnota je **objectID**, která vyplní `sub` deklaraci identity v tokenu s ID objektu uživatele. Hodnota **není podporována** je poskytnuta pouze pro zpětnou kompatibilitu. Doporučuje se přepnout na **objectID** , jakmile budete moct.

- **Deklarace identity představující ID zásady** – Tato vlastnost identifikuje typ deklarace identity, do kterého se vyplní název zásady použitý v žádosti o tokenu. Výchozí hodnota je `tfp`. Hodnota `acr` je poskytnuta pouze pro zpětnou kompatibilitu.

## <a name="pass-through"></a>Průchod

Po zahájení cesty uživatele Azure AD B2C obdrží přístupový token od poskytovatele identity. Azure AD B2C používá tento token k načtení informací o uživateli. V toku uživatele povolíte deklaraci identity, abyste mohli [token předat](idp-pass-through-user-flow.md) do aplikací, které zaregistrujete v Azure AD B2C. Aby bylo možné využít výhod předávání tokenu jako deklarace identity, musí vaše aplikace používat [doporučený tok uživatele](user-flow-versions.md) .

Azure AD B2C aktuálně podporuje pouze předávání přístupového tokenu zprostředkovatelů identity OAuth 2,0, které zahrnují Facebook a Google. U všech ostatních zprostředkovatelů identity se deklarace identity vrátí jako prázdná.

## <a name="validation"></a>Ověřování

Chcete-li ověřit token, aplikace by měla ověřit podpis i deklarace identity tokenu. Mnoho Open-Source knihoven je k dispozici pro ověřování JWTs v závislosti na preferovaném jazyce. Doporučuje se tyto možnosti prozkoumat místo implementace vlastní logiky ověřování.

### <a name="validate-signature"></a>Ověřit podpis

Token JWT obsahuje tři segmenty, *hlavičku*, *tělo* a *podpis*. Segment podpisu lze použít k ověření pravosti tokenu, aby mohl být vaší aplikací důvěryhodný. Tokeny Azure AD B2C jsou podepsané pomocí standardních asymetrických šifrovacích algoritmů, jako je například RSA 256.

Záhlaví tokenu obsahuje informace o klíči a metodě šifrování použité k podepsání tokenu:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Hodnota deklarace identity **ALG** je algoritmus, který se použil k podepsání tokenu. Hodnota deklarace **Kid** je veřejný klíč, který se použil k podepsání tokenu. V jednom okamžiku Azure AD B2C může podepsat token pomocí kterékoli ze sad dvojic klíčů veřejných a privátních klíčů. Azure AD B2C může pravidelně otáčet možné sady klíčů. Vaše aplikace by měla být zapsána, aby se tyto klíčové změny automaticky zpracovaly. Přiměřenou frekvencí pro kontrolu aktualizací veřejných klíčů, které používá Azure AD B2C, je každých 24 hodin. Aby bylo možné zpracovat neočekávané změny klíčů, měla by být aplikace vytvořena pro opětovné načtení veřejných klíčů, pokud obdrží neočekávanou hodnotu **Kid** .

Azure AD B2C má koncový bod metadat OpenID Connect. Pomocí tohoto koncového bodu můžou aplikace požadovat informace o Azure AD B2C za běhu. Mezi tyto informace patří koncové body, obsah tokenu a podpisové klíče tokenu. Váš tenant Azure AD B2C obsahuje dokument metadat JSON pro každou zásadu. Dokument metadat je objekt JSON, který obsahuje několik užitečných informací. Metadata obsahují **jwks_uri**, která poskytuje umístění sady veřejných klíčů, které se používají k podepisování tokenů. Toto umístění je zde k dispozici, ale je nejvhodnější načíst umístění dynamicky pomocí dokumentu metadat a analýzy **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/discovery/v2.0/keys
```
Dokument JSON umístěný na této adrese URL obsahuje všechny informace o veřejných klíčích, které se v daném okamžiku používají. Vaše aplikace může použít `kid` deklaraci identity v HLAVIČCE JWT k výběru veřejného klíče v dokumentu JSON, který se používá k podepsání konkrétního tokenu. Potom může provést ověření podpisu pomocí správného veřejného klíče a uvedeného algoritmu.

Dokument metadat pro `B2C_1_signupsignin1` zásadu v `contoso.onmicrosoft.com` tenantovi se nachází v těchto umístěních:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/v2.0/.well-known/openid-configuration
```

Chcete-li zjistit, které zásady byly použity k podepsání tokenu (a kde přejít na požadavky na metadata), máte dvě možnosti. Nejdřív je název zásady zahrnutý v `tfp` (výchozí) nebo v `acr` tokenu (jak je nakonfigurované) v tokenu. Můžete analyzovat deklarace identity mimo tělo tokenu JWT základní-64 dekódováním těla a deserializací řetězce JSON, který je výsledkem. `tfp` `acr` Deklarace identity nebo je název zásady, která se použila k vystavení tokenu. Druhou možností je zakódovat zásadu v hodnotě `state` parametru při vystavení žádosti a potom dekódovat, abyste zjistili, které zásady byly použity. Kterákoli z metod je platná.

Popis, jak provést ověření podpisu, je mimo rozsah tohoto dokumentu. K dispozici je řada Open Source knihoven, které vám pomůžou ověřit token.

### <a name="validate-claims"></a>Ověřit deklarace identity

Když vaše aplikace nebo rozhraní API obdrží token ID, měl by také provést několik kontrol v deklaracích identity v tokenu ID. Měli byste zkontrolovat následující deklarace identity:

- **cílová skupina** – ověřuje, že token ID má být poskytnut vaší aplikaci.
- doba **před** a **vypršení platnosti** – ověřuje, že platnost tokenu ID nevypršela.
- **Vystavitel** – ověřuje, že se token vystavil do vaší aplikace Azure AD B2C.
- **hodnota nonce** – strategie pro zmírnění útoku na opakované přehrání tokenu.

Úplný seznam ověření, které by měla vaše aplikace provádět, najdete v tématu [specifikace OpenID Connect](https://openid.net).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [použití přístupových tokenů](access-tokens.md).

