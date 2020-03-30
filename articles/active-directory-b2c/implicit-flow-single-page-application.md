---
title: Jednostránkové přihlášení pomocí implicitního toku
titleSuffix: Azure AD B2C
description: Zjistěte, jak přidat jednostránkové přihlášení pomocí implicitního toku OAuth 2.0 pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37b59c2a23a8f00e8376be2ac4a7b35a6d58aa28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399003"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Jednostránkové přihlášení pomocí implicitního toku OAuth 2.0 ve službě Azure Active Directory B2C

Mnoho moderních aplikací má jednostránkový front-end aplikace, který je napsán především v JavaScriptu. Aplikace se často píše pomocí rozhraní, jako je React, Angular nebo Vue.js. Jednostránkové aplikace a další javascriptové aplikace, které běží primárně v prohlížeči, mají některé další problémy s ověřováním:

- Charakteristiky zabezpečení těchto aplikací se liší od tradičních webových aplikací založených na serveru.
- Mnoho autorizačních serverů a poskytovatelů identit nepodporuje požadavky na sdílení prostředků napříč zdroji (CORS).
- Celostránkový prohlížeč přesměrování od aplikace může být invazivní pro uživatelské prostředí.

Pro podporu těchto aplikací azure active directory B2C (Azure AD B2C) používá implicitní tok OAuth 2.0. Implicitní grantový tok autorizace OAuth 2.0 je popsán v [části 4.2 specifikace OAuth 2.0](https://tools.ietf.org/html/rfc6749). V implicitním toku aplikace přijímá tokeny přímo z azure active directory (Azure AD) autorizovat koncový bod, bez jakékoli výměny mezi servery. Veškerá logika ověřování a zpracování relací se provádí výhradně v klientovi JavaScriptu pomocí přesměrování stránky nebo automaticky otevíraného pole.

Azure AD B2C rozšiřuje standardní Implicitní tok OAuth 2.0 na více než jednoduché ověřování a autorizace. Azure AD B2C zavádí [parametr zásady](user-flow-overview.md). Pomocí parametru zásad můžete pomocí OAuth 2.0 přidat do aplikace zásady, jako je registrace, přihlášení a toky uživatelů správy profilů. V příkladu požadavků HTTP v tomto článku **{tenant}.onmicrosoft.com** se používá jako příklad. Nahraďte `{tenant}` se jménem vašeho tenanta, pokud ho máte a také jste vytvořili tok uživatele.

Implicitní přihlašování vypadá podobně jako na následujícím obrázku. Každý krok je podrobně popsán dále v článku.

![Diagram ve stylu plavecké dráhy znázorňující implicitní tok OpenID Connect](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Odeslání žádostí o ověření

Když vaše webová aplikace potřebuje ověřit uživatele a spustit tok `/authorize` uživatele, může uživatele nasměrovat do koncového bodu. Uživatel provede akci v závislosti na toku uživatele.

V tomto požadavku klient označuje oprávnění, která potřebuje získat `scope` od uživatele v parametru a tok uživatele ke spuštění. Chcete-li získat informace o tom, jak žádost funguje, zkuste požadavek vložit do prohlížeče a spustit jej. Nahraďte `{tenant}` se názvem vašeho klienta Azure AD B2C. Nahraďte `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` ID aplikace aplikace, kterou jste dříve zaregistrovali ve svém tenantovi. Nahraďte `{policy}` například `b2c_1_sign_in`název zásady, kterou jste vytvořili v tenantovi .

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametr | Požaduje se | Popis |
| --------- | -------- | ----------- |
|{tenant}| Ano | Název vašeho klienta Azure AD B2C|
|{zásady}| Ano| Tok uživatele, který má být spuštěn. Zadejte název toku uživatele, který jste vytvořili v tenantovi Azure AD B2C. Například: `b2c_1_sign_in` `b2c_1_sign_up`, `b2c_1_edit_profile`, nebo . |
| client_id | Ano | ID aplikace, které [portál Azure](https://portal.azure.com/) přiřazenka vaší aplikaci. |
| response_type | Ano | Musí `id_token` obsahovat pro přihlášení OpenID Connect. Může také obsahovat `token`typ odpovědi . Pokud používáte `token`, vaše aplikace můžete okamžitě získat přístupový token z autorizovat koncového bodu, bez podání druhého požadavku na autorizovat koncový bod.  Pokud použijete `token` typ odpovědi, `scope` parametr musí obsahovat obor, který označuje, pro který prostředek má být token vydán. |
| redirect_uri | Ne | Identifikátor URI přesměrování vaší aplikace, kde mohou být odpovědi na ověřování odesílány a přijímány vaší aplikací. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódován adresou URL. |
| response_mode | Ne | Určuje metodu, která se má použít k odeslání výsledného tokenu zpět do aplikace.  Pro implicitní `fragment`toky použijte . |
| scope | Ano | Mezera-oddělený seznam oborů. Hodnota jednoho oboru označuje azure ad obě oprávnění, která jsou požadovány. Obor `openid` označuje oprávnění k přihlášení uživatele a získání dat o uživateli ve formě tokenů ID. Obor `offline_access` je volitelný pro webové aplikace. Označuje, že vaše aplikace potřebuje obnovovací token pro dlouhodobý přístup k prostředkům. |
| state | Ne | Hodnota zahrnutá v požadavku, která je také vrácena v odpovědi tokenu. Může se jedná o řetězec libovolného obsahu, který chcete použít. Obvykle se používá náhodně generovaná jedinečná hodnota, aby se zabránilo útokům padělání žádostí mezi lokalitami. Stav se také používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, stejně jako na stránce, na které byli. |
| Nonce | Ano | Hodnota zahrnutá v požadavku (vygenerovaná aplikací), která je zahrnuta ve výsledném tokenu ID jako deklarace. Aplikace pak můžete ověřit tuto hodnotu ke zmírnění útoků přehrání tokenu. Hodnota je obvykle randomizovaný, jedinečný řetězec, který lze použít k identifikaci původu požadavku. |
| Výzva | Ne | Typ interakce uživatele, která je požadována. V současné době je `login`jedinou platnou hodnotou . Tento parametr vynutí uživateli zadat svá pověření na tento požadavek. Jednotné přihlašování se neprojeví. |

V tomto okamžiku je uživatel vyzván k dokončení pracovního postupu zásady. Uživatel může mít zadejte své uživatelské jméno a heslo, přihlásit se pomocí sociální identity, zaregistrovat se do adresáře nebo jakýkoli jiný počet kroků. Akce uživatele závisí na tom, jak je definován tok uživatele.

Po dokončení toku uživatele Azure AD vrátí odpověď na vaši aplikaci `redirect_uri`na hodnotu, kterou jste použili pro . Používá metodu zadanou `response_mode` v parametru. Odpověď je přesně stejná pro každý scénář akce uživatele, nezávisle na toku uživatele, který byl proveden.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď, `response_mode=fragment` `response_type=id_token+token` která používá a vypadá takto, s zalomením řádků pro čitelnost:

```HTTP
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
| access_token | Přístupový token, který aplikace požadovala. |
| token_type | Hodnota typu tokenu. Jediný typ, který podporuje Azure AD je Nosič. |
| expires_in | Doba platnosti přístupového tokenu (v sekundách). |
| scope | Obory, pro které je token platný. Obory můžete také použít k ukládání tokenů do mezipaměti pro pozdější použití. |
| id_token | ID token, který aplikace požadovala. Token ID můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. Další informace o tokenech ID a jejich obsahu najdete v [tématu odkaz na token Azure AD B2C](tokens-overview.md). |
| state | Pokud `state` je parametr zahrnut v požadavku, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla `state` ověřit, že hodnoty v požadavku a odpovědi jsou identické. |

### <a name="error-response"></a>Odpověď na chybu
Chybové odpovědi lze také odeslat do identifikátoru URI přesměrování, aby je aplikace mohla správně zpracovat:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --------- | ----------- |
| error | Kód používaný ke klasifikaci typů chyb, ke kterým dochází. |
| error_description | Konkrétní chybová zpráva, která vám může pomoci identifikovat hlavní příčinu chyby ověřování. |
| state | Pokud `state` je parametr zahrnut v požadavku, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla `state` ověřit, že hodnoty v požadavku a odpovědi jsou identické.|

## <a name="validate-the-id-token"></a>Ověření tokenu ID

Přijetí tokenu ID nestačí k ověření uživatele. Ověřte podpis tokenu ID a ověřte deklarace identity v tokenu podle požadavků vaší aplikace. Azure AD B2C používá [JSON webové tokeny (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografie veřejného klíče k podepsání tokenů a ověření, že jsou platné.

Mnoho open source knihovny jsou k dispozici pro ověření JWTs, v závislosti na jazyku, který chcete použít. Zvažte zkoumání dostupných knihoven s otevřeným zdrojovým kódem spíše než implementaci vlastní logiky ověřování. Informace v tomto článku vám pomohou naučit se správně používat tyto knihovny.

Azure AD B2C má koncový bod metadat OpenID Connect. Aplikace můžete použít koncový bod k načtení informací o Azure AD B2C za běhu. Tyto informace zahrnují koncové body, obsah tokenu a podpisové klíče tokenu. Existuje dokument metadat JSON pro každý tok uživatelů v tenantovi Azure AD B2C. Například dokument metadat pro tok b2c_1_sign_in uživatele v fabrikamb2c.onmicrosoft.com tenanta se nachází na adrese:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Jednou z vlastností tohoto konfiguračního `jwks_uri`dokumentu je . Hodnota pro stejný tok uživatele by byla:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Chcete-li zjistit, který tok uživatele byl použit k podepsání tokenu ID (a odkud lze načíst metadata), máte dvě možnosti. Nejprve je název toku `acr` uživatele `id_token`zahrnut do deklarace v . Informace o tom, jak analyzovat deklarace identity z tokenu ID, najdete v tématu [odkaz na token Azure AD B2C](tokens-overview.md). Další možností je zakódovat tok uživatele `state` v hodnotě parametru při vystavení požadavku. Potom dekódovat `state` parametr k určení, který tok uživatele byl použit. Obě metody jsou platné.

Po získání dokumentu metadat z koncového bodu metadat OpenID Connect můžete použít veřejné klíče RSA-256 (umístěné v tomto koncovém bodě) k ověření podpisu tokenu ID. V tomto koncovém bodě může být v daném okamžiku `kid`uvedeno více klíčů, z nichž každý je identifikován . Záhlaví také `id_token` obsahuje `kid` deklaraci. Označuje, který z těchto klíčů byl použit k podepsání tokenu ID. Další informace, včetně informací o [ověřování tokenů](tokens-overview.md), najdete v tématu [odkaz na token Azure AD B2C](tokens-overview.md).
<!--TODO: Improve the information on this-->

Po ověření podpisu tokenu ID vyžaduje ověření několik deklarací identity. Například:

* Ověřte deklaraci, `nonce` abyste zabránili útokům opětovného přehrání tokenu. Jeho hodnota by měla být to, co jste zadali v žádosti o přihlášení.
* Ověřte `aud` deklaraci, abyste zajistili, že byl pro vaši aplikaci vydán token ID. Jeho hodnota by měla být ID aplikace vaší aplikace.
* `iat` Ověřte `exp` a deklarace, které zajistí, že platnost tokenu ID nevypršela.

Několik dalších ověření, které byste měli provést, je podrobně popsáno v [specifikaci Jádra OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). Můžete také chtít ověřit další deklarace identity, v závislosti na scénáři. Mezi běžná ověření patří:

* Zajištění, že se uživatel nebo organizace zaregistrovali k aplikaci.
* Zajištění, že uživatel má správné oprávnění a oprávnění.
* Zajištění, že došlo k určité síle ověřování, například pomocí Azure Multi-Factor Authentication.

Další informace o deklaracích identity v tokenu ID najdete v [tématu odkaz na token Azure AD B2C](tokens-overview.md).

Po ověření tokenu ID můžete zahájit relaci s uživatelem. Ve vaší aplikaci použijte deklarace identity v tokenu ID k získání informací o uživateli. Tyto informace lze použít pro zobrazení, záznamy, autorizaci a tak dále.

## <a name="get-access-tokens"></a>Získání přístupových tokenů
Pokud jediné, co vaše webové aplikace musí udělat, je spustit toky uživatelů, můžete přeskočit několik dalších částí. Informace v následujících částech se vztahuje pouze na webové aplikace, které je potřeba provést ověřená volání webového rozhraní API a které jsou chráněny Azure AD B2C.

Teď, když jste uživatele přihlásili do jednostránkové aplikace, můžete získat přístupové tokeny pro volání webových rozhraní API, která jsou zabezpečená službou Azure AD. I v případě, že jste `token` již obdrželi token pomocí typu odpovědi, můžete tuto metodu získat tokeny pro další prostředky bez přesměrování uživatele znovu přihlásit.

V typické toku webové aplikace byste provést `/token` požadavek na koncový bod. Koncový bod však nepodporuje požadavky CORS, takže volání AJAX získat token aktualizace není možnost. Místo toho můžete použít implicitní tok ve skrytém elementu iframe HTML k získání nových tokenů pro jiná webová rozhraní API. Zde je příklad s zalomením řádků pro čitelnost:

```HTTP
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
|{tenant}| Požaduje se | Název vašeho klienta Azure AD B2C|
{zásady}| Požaduje se| Tok uživatele, který má být spuštěn. Zadejte název toku uživatele, který jste vytvořili v tenantovi Azure AD B2C. Například: `b2c_1_sign_in` `b2c_1_sign_up`, `b2c_1_edit_profile`, nebo . |
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci na [webu Azure Portal](https://portal.azure.com). |
| response_type |Požaduje se |Musí `id_token` obsahovat pro přihlášení OpenID Connect.  Může také obsahovat `token`typ odpovědi . Pokud používáte `token` zde, vaše aplikace můžete okamžitě získat přístupový token z autorizovat koncového bodu, bez provedení druhý požadavek na autorizovat koncový bod. Pokud použijete `token` typ odpovědi, `scope` parametr musí obsahovat obor, který označuje, pro který prostředek má být token vydán. |
| redirect_uri |Doporučené |Identifikátor URI přesměrování vaší aplikace, kde mohou být odpovědi na ověřování odesílány a přijímány vaší aplikací. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódován adresou URL. |
| scope |Požaduje se |Mezera-oddělený seznam oborů.  Pro získání tokenů, zahrnout všechny obory, které potřebujete pro zamýšlený prostředek. |
| response_mode |Doporučené |Určuje metodu, která se používá k odeslání výsledného tokenu zpět do vaší aplikace. Pro implicitní `fragment`tok použijte . Dva další režimy `query` lze `form_post`zadat a , ale nefungují v implicitní toku. |
| state |Doporučené |Hodnota zahrnutá v požadavku, která je vrácena v odpovědi tokenu.  Může se jedná o řetězec libovolného obsahu, který chcete použít.  Obvykle se používá náhodně generovaná jedinečná hodnota, aby se zabránilo útokům padělání žádostí mezi lokalitami.  Stav se také používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření. Například stránka nebo zobrazení, na kterých byl uživatel. |
| Nonce |Požaduje se |Hodnota zahrnutá v požadavku vygenerovaná aplikací, která je zahrnuta ve výsledném tokenu ID jako deklarace.  Aplikace pak můžete ověřit tuto hodnotu ke zmírnění útoků přehrání tokenu. Hodnota je obvykle randomizovaný, jedinečný řetězec, který identifikuje původ požadavku. |
| Výzva |Požaduje se |Chcete-li aktualizovat a získat tokeny `prompt=none` ve skrytém prvku iframe, použijte k zajištění, že iframe neuvízne na přihlašovací stránce a okamžitě se vrátí. |
| login_hint |Požaduje se |Chcete-li aktualizovat a získat tokeny ve skrytém prvku iframe, zahrňte do této nápovědy uživatelské jméno uživatele, abyste rozlišili mezi více relacemi, které uživatel může mít v daném okamžiku. Uživatelské jméno můžete extrahovat z dřívějšího `preferred_username` přihlášení pomocí `profile` deklarace (obor je `preferred_username` vyžadován pro příjem deklarace). |
| domain_hint |Požaduje se |Může být `consumers` nebo `organizations`.  Pro aktualizaci a získání tokenů ve `domain_hint` skrytém prvku iframe zahrňte hodnotu do požadavku.  Extrahujte `tid` deklaraci z tokenu ID dřívějšího přihlášení a `profile` určete, jakou hodnotu použít (obor je vyžadován pro příjem `tid` deklarace). Pokud `tid` je `9188040d-6c67-4c5b-b112-36a304b66dad`hodnota deklarace pohledávky , použijte `domain_hint=consumers`.  V opačném `domain_hint=organizations`případě použijte . |

Nastavením `prompt=none` parametru tento požadavek buď uspěje, nebo se nezdaří okamžitě a vrátí se do vaší aplikace.  Úspěšná odpověď je odeslána do aplikace na uvedené přesměrování URI, `response_mode` pomocí metody zadané v parametru.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď `response_mode=fragment` pomocí vypadá takto:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parametr | Popis |
| --- | --- |
| access_token |Token, který aplikace požadovala. |
| token_type |Typ tokenu bude vždy Nosič. |
| state |Pokud `state` je parametr zahrnut v požadavku, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla `state` ověřit, že hodnoty v požadavku a odpovědi jsou identické. |
| expires_in |Jak dlouho je přístupový token platný (v sekundách). |
| scope |Obory, pro které je platný přístupový token. |

### <a name="error-response"></a>Odpověď na chybu
Chybové odpovědi lze také odeslat do identifikátoru URI přesměrování, aby je aplikace mohla odpovídajícím způsobem zpracovat.  Očekávaná `prompt=none`chyba vypadá jako v tomto příkladu:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází. Můžete také použít řetězec reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomoci identifikovat hlavní příčinu chyby ověřování. |

Pokud se zobrazí tato chyba v požadavku iframe, uživatel musí interaktivně znovu přihlásit načíst nový token.

## <a name="refresh-tokens"></a>Aktualizovat tokeny
Platnost tokenů ID a přístupových tokenů vyprší po krátké době. Vaše aplikace musí být připravena pravidelně aktualizovat tyto tokeny.  Chcete-li aktualizovat některý typ tokenu, proveďte stejný skrytý požadavek `prompt=none` iframe, který jsme použili v předchozím příkladu, pomocí parametru pro řízení kroků Azure AD.  Chcete-li `id_token` získat novou hodnotu, `scope=openid`nezapomeňte `nonce` použít `response_type=id_token` a , a parametr.

## <a name="send-a-sign-out-request"></a>Odeslání žádosti o odhlášení
Pokud chcete odhlásit uživatele z aplikace, přesměrujte uživatele do Služby Azure AD, abyste se odhlásit. Pokud uživatele nepřesměrujete, může se mu znovu ověřit, aniž by znovu zadali svá přihlašovací údaje, protože mají platnou relaci jednotného přihlašování s Azure AD.

Můžete jednoduše přesměrovat uživatele `end_session_endpoint` na ten, který je uveden ve stejném dokumentu metadat OpenID Connect popsaném v [části Ověřit token ID](#validate-the-id-token). Například:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Požaduje se | Popis |
| --------- | -------- | ----------- |
| {tenant} | Ano | Název vašeho klienta Azure AD B2C |
| {zásady} | Ano | Tok uživatele, který chcete použít k odhlášení uživatele z vaší aplikace. |
| post_logout_redirect_uri | Ne | Adresa URL, na kterou by měl být uživatel po úspěšném odhlášení přesměrován. Pokud není zahrnuta, Azure AD B2C zobrazí uživateli obecnou zprávu. |
| state | Ne | Pokud `state` je parametr zahrnut v požadavku, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla `state` ověřit, zda jsou hodnoty v požadavku a odpovědi identické. |


> [!NOTE]
> Přesměrování uživatele na `end_session_endpoint` vymaže některé stavu jednotného přihlášení uživatele s Azure AD B2C. Však neodhlásí uživatele z relace poskytovatele sociální identity uživatele. Pokud uživatel vybere stejného zprostředkovatele identity během následného přihlášení, je uživatel znovu ověřen, aniž by zadali svá pověření. Pokud se uživatel chce odhlásit z vaší aplikace Azure AD B2C, nemusí to nutně znamenat, že se chce úplně odhlásit ze svého účtu na Facebooku, například. U místních účtů však bude relace uživatele správně ukončena.
>

## <a name="next-steps"></a>Další kroky

### <a name="code-sample-azure-ad-b2c-with-microsoft-authentication-library-for-javascript"></a>Ukázka kódu: Azure AD B2C s knihovnou Microsoft Authentication Library pro JavaScript

[Jednostránková aplikace vytvořená pomocí msal.js pro Azure AD B2C][github-msal-js-example] (GitHub)

Tato ukázka na GitHubu je určena k tomu, abyste mohli začít s Azure AD B2C v jednoduché webové aplikaci vytvořené pomocí [msal.js][github-msal-js] a pomocí ověřování ve stylu automaticky otevíraných míst.

<!-- Links - EXTERNAL -->
[github-msal-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
[github-msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
