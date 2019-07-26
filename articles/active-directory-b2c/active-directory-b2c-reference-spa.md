---
title: Přihlášení na jednu stránku pomocí implicitního toku – Azure Active Directory B2C
description: Přečtěte si, jak přidat jednostránkové přihlašování pomocí implicitního toku OAuth 2,0 s Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1196f3b186abcd914c409db06b52654f82f4158b
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377318"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Přihlášení na jednu stránku pomocí implicitního toku OAuth 2,0 v Azure Active Directory B2C

Mnohé moderní aplikace mají front-end jednostránkové aplikace, který je napsán primárně v jazyce JavaScript. Často je aplikace napsaná pomocí architektury, jako je například reagují, úhlová nebo Vue. js. Jednostránkové aplikace a další aplikace JavaScriptu, které běží primárně v prohlížeči, mají nějaké další výzvy k ověření:

- Bezpečnostní charakteristiky těchto aplikací se liší od tradičních webových aplikací založených na serveru.
- Mnoho autorizačních serverů a zprostředkovatelů identity nepodporuje požadavky na sdílení prostředků mezi zdroji (CORS).
- Přesměrování prohlížeče na celé stránce od aplikace může být náročné na uživatelské prostředí.

Pro podporu těchto aplikací používá Azure Active Directory B2C (Azure AD B2C) implicitní tok OAuth 2,0. Tok udělení implicitního udělení autorizace OAuth 2,0 je popsaný v [části 4,2 specifikace OAuth 2,0](https://tools.ietf.org/html/rfc6749). V implicitním toku aplikace obdrží tokeny přímo z Azure Active Directory (Azure AD) autorizačního koncového bodu bez jakéhokoli serveru Exchange Server to-Server. Veškerá logika ověřování a zpracování relace se provádí zcela v klientovi JavaScriptu buď pomocí přesměrování stránky, nebo pomocí automaticky otevíraného okna.

Azure AD B2C rozšiřuje implicitní tok standardu OAuth 2,0 na více než jednoduché ověřování a autorizaci. Azure AD B2C zavádí [parametr zásad](active-directory-b2c-reference-policies.md). Pomocí parametru zásad můžete pomocí OAuth 2,0 přidat zásady do vaší aplikace, jako je registrace, přihlašování a uživatelské toky správy profilů. V příkladech požadavků HTTP v tomto článku se jako příklad používá **fabrikamb2c.onmicrosoft.com** . Můžete nahradit `fabrikamb2c` názvem vašeho tenanta, pokud ho máte a máte vytvořeného toku uživatele.

Implicitní tok přihlášení vypadá podobně jako na následujícím obrázku. Každý krok je podrobněji popsán dále v článku.

![Diagram stylu plavecké dráhy znázorňující implicitní tok OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Odeslat požadavky na ověření

Pokud vaše webová aplikace potřebuje ověřit uživatele a spustit tok uživatele, může uživatele nasměrovat na `/authorize` koncový bod. Uživatel provede akci v závislosti na toku uživatele.

V této žádosti klient indikuje oprávnění, která musí získat od uživatele v `scope` parametru, a tok uživatele, který se má spustit `p` v parametru. V následujících oddílech jsou uvedeny tři příklady (s přerušením řádků pro čitelnost), z nichž každá používá jiný uživatelský tok. Pokud chcete zjistit, jak každý požadavek funguje, zkuste vložit požadavek do prohlížeče a spustit ho. Můžete nahradit `fabrikamb2c` názvem vašeho tenanta, pokud ho máte a máte vytvořeného toku uživatele.

### <a name="use-a-sign-in-user-flow"></a>Použití toku uživatele přihlašování

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-user-flow"></a>Použití toku uživatele pro registraci
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-user-flow"></a>Použití uživatelského toku pro úpravy profilu
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametr | Požadováno | Popis |
| --------- | -------- | ----------- |
| client_id | Ano | ID aplikace, které [Azure Portal](https://portal.azure.com/) přiřazena k vaší aplikaci. |
| response_type | Ano | Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Může také zahrnovat typ `token`odpovědi. Pokud použijete `token`aplikaci, může ihned získat přístupový token z autorizačního koncového bodu, aniž by se musel zasílat druhý požadavek na koncový bod autorizace.  `token` Použijete`scope` -li typ odpovědi, parametr musí obsahovat obor, který určuje, který prostředek má vydávat token pro. |
| redirect_uri | Ne | Identifikátor URI pro přesměrování vaší aplikace, ve kterém může vaše aplikace odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu, s tím rozdílem, že musí být zakódovaný na adrese URL. |
| response_mode | Ne | Určuje metodu, která se má použít k odeslání výsledného tokenu zpátky do vaší aplikace.  Pro implicitní toky použijte `fragment`. |
| scope | Ano | Mezerou oddělený seznam oborů. Jedna hodnota oboru indikuje pro Azure AD obě oprávnění, která jsou požadována. `openid` Rozsah označuje oprávnění k přihlášení uživatele a získání dat o uživateli ve formě tokenů ID. `offline_access` Obor je volitelný pro webové aplikace. Indikuje, že vaše aplikace potřebuje aktualizační token pro dlouhodobě přístup k prostředkům. |
| state | Ne | Hodnota obsažená v požadavku, která se také vrací v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete použít. Obvykle se používá náhodně vygenerovaná jedinečná hodnota, která zabraňuje útokům proti padělání požadavků mezi lokalitami. Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než k žádosti o ověření dojde, například na stránce, na které se nachází. |
| nonce | Ano | Hodnota obsažená v žádosti (generovaná aplikací), která je součástí výsledného tokenu ID jako deklarace identity. Aplikace pak může tuto hodnotu ověřit a zmírnit tak útoky na opakované přehrání tokenů. Obvykle je hodnota náhodným, jedinečným řetězcem, který lze použít k identifikaci původu žádosti. |
| p | Ano | Zásada, která se má spustit. Je to název zásady (tok uživatele), která se vytvoří ve vašem tenantovi Azure AD B2C. Hodnota název zásady by měla začínat na **B2C\_1\_** . |
| výzv | Ne | Typ uživatelské interakce, která je povinná. V současné době jediná platná hodnota je `login`. Tento parametr přinutí uživatele k zadání přihlašovacích údajů k této žádosti. Jednotné přihlašování se neprojeví. |

V tomto okamžiku se uživateli zobrazí výzva k dokončení pracovního postupu zásady. Uživatel bude muset zadat své uživatelské jméno a heslo, přihlašovat se přes sociální identitu, zaregistrovat se k adresáři nebo jakýkoli jiný počet kroků. Akce uživatele závisí na způsobu definování toku uživatele.

Až uživatel tok uživatele dokončí, Azure AD vrátí odpověď vaší aplikaci na hodnotu, kterou jste použili pro `redirect_uri`. Používá metodu určenou v `response_mode` parametru. Odpověď je přesně stejná pro každý scénář akce uživatele, nezávisle na spuštěném toku uživatele.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď, která používá `response_mode=fragment` a `response_type=id_token+token` vypadá takto, se zalomením řádků pro čitelnost:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parametr | Popis |
| --------- | ----------- |
| access_token | Přístupový token, který aplikace požadovala |
| token_type | Hodnota typu tokenu. Jediným typem, který podporuje Azure AD, je nosič. |
| expires_in | Doba, po kterou je přístupový token platný (v sekundách). |
| scope | Obory, pro které je token platný. Můžete také použít obory k ukládání tokenů do mezipaměti pro pozdější použití. |
| id_token | Token ID, který aplikace požadovala Token ID můžete použít k ověření identity uživatele a zahájení relace s uživatelem. Další informace o tokenech ID a jejich obsahu najdete v referenčních informacích k [tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| state | Pokud je `state` parametr zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že `state` hodnoty v žádosti a odpovědi jsou identické. |

### <a name="error-response"></a>Chybová odezva
Odpovědi na chyby se také odesílají do identifikátoru URI přesměrování, aby je aplikace mohla vhodně zpracovat:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --------- | ----------- |
| chyba | Kód používaný ke klasifikaci typů chyb, ke kterým došlo. |
| error_description | Konkrétní chybová zpráva, která vám může pomáhat identifikovat hlavní příčinu chyby ověřování. |
| state | Pokud je `state` parametr zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že `state` hodnoty v žádosti a odpovědi jsou identické.|

## <a name="validate-the-id-token"></a>Ověřit token ID

Získání tokenu ID není dostatečné pro ověření uživatele. Ověří signaturu tokenu ID a ověří deklarace identity v tokenu podle požadavků vaší aplikace. Azure AD B2C používá [webové tokeny JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografii s veřejným klíčem k podepisování tokenů a ověření, že jsou platné.

K dispozici je řada Open-Source knihoven pro ověřování JWTs v závislosti na jazyku, který dáváte přednost při použití. Zvažte možnost prozkoumat dostupné open source knihovny místo implementace vlastní logiky ověřování. Informace v tomto článku vám pomůžou zjistit, jak tyto knihovny správně používat.

Azure AD B2C má koncový bod metadat OpenID Connect. Aplikace může pomocí koncového bodu načíst informace o Azure AD B2C za běhu. Mezi tyto informace patří koncové body, obsah tokenu a podpisové klíče tokenu. K dispozici je dokument metadat JSON pro každý tok uživatele ve vašem Azure AD B2Cm tenantovi. Například dokument metadat pro tok uživatele b2c_1_sign_in v tenantovi fabrikamb2c.onmicrosoft.com je umístěný v:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jednou z vlastností tohoto dokumentu konfigurace je `jwks_uri`. Hodnota pro stejný tok uživatele by byla:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Chcete-li zjistit, který tok uživatele byl použit k podepsání tokenu ID (a kde načíst metadata z), máte dvě možnosti. Nejdřív je název toku uživatele zahrnutý v `acr` deklaraci identity v. `id_token` Informace o tom, jak analyzovat deklarace identity z tokenu ID, najdete v tématu [Reference k tokenům Azure AD B2C](active-directory-b2c-reference-tokens.md). Druhou možností je zakódovat tok uživatele v hodnotě `state` parametru při vystavení žádosti. Pak dekódováním `state` parametru určíte, který tok uživatele byl použit. Kterákoli z metod je platná.

Po získání dokumentu metadat z koncového bodu metadat OpenID Connect můžete použít veřejné klíče RSA-256 (nacházející se v tomto koncovém bodě) a ověřit podpis tokenu ID. V tomto koncovém bodě může být v daném okamžiku k dispozici více klíčů, z nichž `kid`každá je označena jako. `id_token` Záhlaví`kid` obsahuje také deklaraci identity. Označuje, které z těchto klíčů se použily k podepsání tokenu ID. Další informace, včetně informací o [ověřování tokenů](active-directory-b2c-reference-tokens.md), najdete v tématu Referenční informace k [tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Po ověření signatury tokenu ID vyžaduje několik deklarací ověření. Příklad:

* `nonce` Ověřte deklaraci identity, aby nedocházelo k útokům na opětovné přehrání tokenu Jeho hodnota by měla být ta, kterou jste zadali v žádosti o přihlášení.
* `aud` Ověřte deklaraci identity, abyste zajistili, že pro vaši aplikaci byl vydán token ID. Jeho hodnota by měla být ID aplikace vaší aplikace.
* Ověřte deklarace identity `exp`a, abyste zajistili, že platnost tokenu ID nevypršela. `iat`

Několik dalších ověření, které byste měli provést, je podrobně popsáno ve [specifikaci OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). V závislosti na vašem scénáři možná budete chtít taky ověřit další deklarace identity. Mezi běžná ověření patří:

* Ujistěte se, že se uživatel nebo organizace zaregistrovali do aplikace.
* Ujistěte se, že má uživatel správnou autorizaci a oprávnění.
* Ujistěte se, že došlo k určité síle ověřování, například pomocí Azure Multi-Factor Authentication.

Další informace o deklaracích identity v tokenu ID najdete v tématu [Azure AD B2C reference](active-directory-b2c-reference-tokens.md)k tokenu.

Po ověření tokenu ID můžete zahájit relaci s uživatelem. V aplikaci použijte k získání informací o uživateli deklarace identity v tokenu ID. Tyto informace lze použít k zobrazení, záznamům, autorizaci a tak dále.

## <a name="get-access-tokens"></a>Získat přístupové tokeny
Pokud je nutné, aby vaše webové aplikace prováděly toky uživatelů, můžete přeskočit další části. Informace v následujících částech se vztahují jenom na webové aplikace, které potřebují ověřit volání webového rozhraní API a které jsou chráněné Azure AD B2C.

Teď, když jste uživatele podepsali do své jednostránkové aplikace, můžete získat přístupové tokeny pro volání webových rozhraní API zabezpečených službou Azure AD. I v případě, že jste už token obdrželi pomocí `token` typu odpovědi, můžete pomocí této metody získat tokeny dalších prostředků bez nutnosti přesměrovat uživatele, aby se znovu přihlásil.

V typickém toku webové aplikace byste měli požádat `/token` o koncový bod. Koncový bod ale nepodporuje žádosti CORS, takže volání jazyka AJAX k získání aktualizačního tokenu neumožňují nastavení. Místo toho můžete použít implicitní tok ve skrytém elementu HTML IFRAME a získat nové tokeny pro jiná webová rozhraní API. Tady je příklad s koncem řádku pro čitelnost:

```
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&p=b2c_1_sign_in
```

| Parametr | Požadováno? | Popis |
| --- | --- | --- |
| client_id |Požadováno |ID aplikace přiřazené vaší aplikaci v [Azure Portal](https://portal.azure.com). |
| response_type |Požadováno |Musí zahrnovat `id_token` pro přihlášení OpenID Connect.  Může také zahrnovat typ `token`odpovědi. Pokud zde použijete `token` tuto možnost, může aplikace získat přístupový token z autorizačního koncového bodu, aniž by se musela provést druhá žádost o autorizaci koncového bodu. `token` Použijete`scope` -li typ odpovědi, parametr musí obsahovat obor, který určuje, který prostředek má vydávat token pro. |
| redirect_uri |Doporučené |Identifikátor URI pro přesměrování vaší aplikace, ve kterém může vaše aplikace odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu, s tím rozdílem, že musí být zakódovaný na adrese URL. |
| scope |Požadováno |Mezerou oddělený seznam oborů.  Pro získání tokenů Zahrňte všechny rozsahy, které požadujete pro zamýšlený prostředek. |
| response_mode |Doporučené |Určuje metodu, která se používá k odeslání výsledného tokenu zpátky do vaší aplikace.  Může být `query`, `form_post`, nebo `fragment`. |
| state |Doporučené |Hodnota obsažená v požadavku, která je vrácena v odpovědi tokenu.  Může to být řetězec libovolného obsahu, který chcete použít.  Obvykle se používá náhodně vygenerovaná jedinečná hodnota, která zabraňuje útokům proti padělání požadavků mezi lokalitami.  Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než k žádosti o ověření dojde. Například stránku nebo zobrazení, na kterých byl uživatel zapnutý. |
| nonce |Požadováno |Hodnota obsažená v požadavku vygenerovaná aplikací, která je součástí výsledného tokenu ID jako deklarace identity.  Aplikace pak může tuto hodnotu ověřit a zmírnit tak útoky na opakované přehrání tokenů. Obvykle je hodnota náhodným, jedinečným řetězcem, který identifikuje původ požadavku. |
| výzv |Požadováno |Chcete-li aktualizovat a získat tokeny ve skrytém `prompt=none` prvku IFRAME, použijte k zajištění toho, že prvek IFRAME nebude na přihlašovací stránce zablokovaný, a okamžitě se vrátí. |
| login_hint |Požadováno |Chcete-li aktualizovat a získat tokeny ve skrytém prvku IFRAME, zahrňte do tohoto doporučení uživatelské jméno uživatele, které bude rozlišovat mezi více relacemi, které uživatel v daném okamžiku může mít. Můžete extrahovat uživatelské jméno z dřívějšího přihlášení pomocí `preferred_username` deklarace identity `profile` (obor je vyžadován, aby bylo možné tuto `preferred_username` deklaraci získat). |
| domain_hint |Požadováno |Může být `consumers` nebo `organizations`.  Pokud chcete aktualizovat a získat tokeny ve skrytém prvku IFRAME `domain_hint` , uveďte hodnotu v žádosti.  Extrahujte `profile` `tid` deklaraci identity z tokenu ID dřívějšího přihlášení a určete, která hodnota se má použít (obor se vyžaduje pro získání deklarace). `tid` `domain_hint=consumers`Pokud je `9188040d-6c67-4c5b-b112-36a304b66dad`hodnota deklarace identity, použijte. `tid`  V opačném `domain_hint=organizations`případě použijte. |

Nastavením `prompt=none` parametru je tato žádost buď úspěšná, nebo selže okamžitě a vrátí se do vaší aplikace.  Do vaší aplikace se pošle úspěšná odpověď v zadaném identifikátoru URI přesměrování pomocí metody zadané v `response_mode` parametru.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď pomocí `response_mode=fragment` vypadá jako v tomto příkladu:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parametr | Popis |
| --- | --- |
| access_token |Token, který aplikace požadovala |
| token_type |Typ tokenu bude vždy nosič. |
| state |Pokud je `state` parametr zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že `state` hodnoty v žádosti a odpovědi jsou identické. |
| expires_in |Jak dlouho je přístupový token platný (v sekundách). |
| scope |Obory, pro které je přístupový token platný |

### <a name="error-response"></a>Chybová odezva
Odpovědi na chyby se také odesílají do identifikátoru URI přesměrování, aby je aplikace mohla vhodně zpracovat.  V `prompt=none`případě by očekávaná chyba vypadala jako v tomto příkladu:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Popis |
| --- | --- |
| chyba |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým došlo. Řetězec můžete také použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomáhat identifikovat hlavní příčinu chyby ověřování. |

Pokud se tato chyba zobrazí v žádosti IFRAME, uživatel se musí znovu interaktivně přihlásit, aby získal nový token.

## <a name="refresh-tokens"></a>Aktualizovat tokeny
Tokeny ID a přístupové tokeny po krátké době vyprší. Vaše aplikace musí být připravená aktualizovat tyto tokeny pravidelně.  Pokud chcete aktualizovat libovolný typ tokenu, proveďte stejný skrytý požadavek IFRAME, který jsme použili v předchozím příkladu, a to `prompt=none` pomocí parametru pro řízení kroků Azure AD.  Chcete-li získat `id_token` novou hodnotu, nezapomeňte použít `response_type=id_token` a `scope=openid` `nonce` a parametr.

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení
Pokud chcete uživatele podepsat z aplikace, přesměrujte uživatele do služby Azure AD, abyste se odhlásili. Pokud uživatele neuděláte, může být možné ho znovu ověřit bez zadání přihlašovacích údajů, protože mají platnou relaci jednotného přihlašování s Azure AD.

Můžete jednoduše přesměrovat uživatele na `end_session_endpoint` , který je uvedený v dokumentu metadat OpenID Connect, který je popsaný v tématu [ověření tokenu ID](#validate-the-id-token). Příklad:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Požadováno? | Popis |
| --- | --- | --- |
| p |Požadováno |Zásada, která se má použít k podepsání uživatele z vaší aplikace |
| post_logout_redirect_uri |Doporučené |Adresa URL, na kterou má být uživatel přesměrován po úspěšném přihlášení Pokud není zahrnutý, Azure AD B2C zobrazí uživateli obecnou zprávu. |

> [!NOTE]
> Uživatel, který přesměruje `end_session_endpoint` uživatele na, vymaže některé stavy jednotného přihlašování uživatele s Azure AD B2C. Nepodepisuje ale uživatele z relace sociální identity provider uživatele. Pokud uživatel vybere stejného poskytovatele identity během následného přihlášení, uživatel se znovu ověří bez zadání přihlašovacích údajů. Pokud se uživatel chce odhlásit z vaší Azure AD B2C aplikace, nemusí nutně znamenat, že by si chtěli úplně odhlásit z účtu Facebook. U místních účtů se ale relace uživatele ukončí správně.
>

## <a name="next-steps"></a>Další postup

### <a name="code-sample-hellojs-with-azure-ad-b2c"></a>Ukázka kódu: Hello. js s Azure AD B2C

[Jednostránkové aplikace postavená na Hello. js s Azure AD B2C][github-hello-js-example] GitHubu

Tato ukázka na GitHubu vám umožní začít pracovat s Azure AD B2C v jednoduché webové aplikaci založené na souboru Hello [. js][github-hello-js] a používat ověřování pomocí překryvných stylů.

<!-- Links - EXTERNAL -->
[github-hello-js-example]: https://github.com/azure-ad-b2c/apps/tree/master/spa/javascript-hellojs-singlepageapp-popup
[github-hello-js]: https://github.com/MrSwitch/hello.js
