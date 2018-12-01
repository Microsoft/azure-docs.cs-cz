---
title: Jednostránkové aplikace pomocí implicitního toku v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet jednostránkové aplikace přímo pomocí implicitního toku OAuth 2.0 s Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 20d1e39a2f2cda66f3b490000f48dd6c5fb72915
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727324"
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Azure AD B2C: Jednostránková aplikace přihlásit pomocí implicitního toku OAuth 2.0

Řada moderních aplikací mají front-end jednostránkové aplikace, která primárně je napsána v jazyce JavaScript. Často je aplikace vytvořená pomocí architektury, jako jsou AngularJS, Ember.js nebo Durandal. Jednostránkové aplikace a jiných aplikací JavaScript především v prohlížeči máte některé další výzvy ověřování:

* Vlastnosti zabezpečení těchto aplikací se značně liší od tradiční serverových webových aplikací.
* Mnoho serverů autorizace a zprostředkovatelů identity nepodporují prostředků mezi zdroji (CORS) žádosti o sdílení.
* Přesměrování mají formu celostránkového prohlížeče směrem od aplikace může být výrazně invazivní s uživatelským prostředím.

Pro podporu těchto aplikací Azure Active Directory B2C (Azure AD B2C) používá implicitní tok OAuth 2.0. Tok autorizačního implicitní grant OAuth 2.0 je popsaný v [části 4.2 specifikaci OAuth 2.0](http://tools.ietf.org/html/rfc6749). V implicitní tok, aplikace obdrží tokeny přímo z Azure Active Directory (Azure AD) zajistí autorizaci koncového bodu, bez jakékoli k serveru exchange. Všechna logika ověřování a relace zpracování trvá umístit zcela v klientovi JavaScript bez přesměrování další stránky.

Azure AD B2C rozšiřuje standardní implicitního toku OAuth 2.0 k více než jednoduché ověřování a autorizace. Azure AD B2C zavádí [parametr zásad](active-directory-b2c-reference-policies.md). S parametrem zásad vám pomůže OAuth 2.0 přidání zásad do vaší aplikace, jako například registrace, přihlášení a Profilovat toky uživatelů pro správu. V tomto článku ukážeme, jak implementaci každého z těchto možností v jednostránkové aplikace pomocí implicitního toku a Azure AD. Abyste mohli začít, podívejte se na naše [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) a [rozhraní Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) ukázky.

V žádosti o příklad HTTP v tomto článku používáme naše ukázka adresář Azure AD B2C, **fabrikamb2c.onmicrosoft.com**. Také používáme vlastní ukázkové aplikace a uživatele toky. Požadavky můžete také vyzkoušet sami s využitím těchto hodnot, nebo je můžete nahradit vlastními hodnotami.
Zjistěte, jak [získat vlastní toky adresáře, aplikace a uživatele Azure AD B2C](#use-your-own-b2c-tenant).


## <a name="protocol-diagram"></a>Diagram protokolu

Implicitní tok přihlášení vypadá podobně jako na následujícím obrázku. Každý krok je podrobně popsáno v později v tomto článku.

![OpenID Connect plaveckých drah](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Odesílat požadavky na ověřování
Pokud vaše webová aplikace potřebuje ověřit uživatele a spustit tok uživatele, přesměruje uživatele `/authorize` koncového bodu. Toto je interaktivní spotřebovanou část toku, kdy uživatel provede akci, v závislosti na toku uživatele. Uživatel získá ID tokenu z koncového bodu Azure AD.

V této žádosti, klient naznačuje v `scope` parametr oprávnění, která je potřeba získat od uživatele. V `p` parametru, označuje tok uživatele k provedení. Následující tři příklady (pomocí konců řádků pro lepší čitelnost) každý použít tok jiný uživatel. Chcete-li získat představu pro každý požadavek jak funguje, zkuste žádost vložením do prohlížeče a jeho spuštění.

### <a name="use-a-sign-in-user-flow"></a>Použijte tok přihlášení uživatele
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

### <a name="use-a-sign-up-user-flow"></a>Použijte tok registrace uživatele
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

### <a name="use-an-edit-profile-user-flow"></a>Použít tok, který upravit profil uživatele
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

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci v [webu Azure portal](https://portal.azure.com). |
| response_type |Požaduje se |Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Typ odpovědi také může obsahovat `token`. Pokud používáte `token`, vaše aplikace může přitom druhou žádost do koncového bodu authorize okamžitě přijímat přístupový token z koncového bodu authorize.  Pokud používáte `token` typ odpovědi `scope` parametr musí obsahovat obor, který označuje, který prostředek se má token vydat. |
| redirect_uri |Doporučené |Identifikátor URI přesměrování vaší aplikace, kde můžete odesílat a přijímat aplikací pro žádosti o ověření. Ho musí přesně odpovídat jedné z přesměrování identifikátory URI, které jste zaregistrovali na portálu s tím rozdílem, že musí být kódovaná adresou URL. |
| response_mode |Doporučené |Určuje metodu sloužící k odeslání výsledný token zpátky do vaší aplikace.  Implicitní tok, použijte `fragment`. |
| scope |Požaduje se |Seznam oborů oddělených mezerami. Hodnota jeden obor značí do služby Azure AD i oprávnění, které jsou požadovány. `openid` Rozsah Určuje oprávnění pro uživatele a získat data o uživateli v podobě ID tokenů. (Budeme mluvit o to více později v tomto článku.) `offline_access` Obor je nepovinné pro webové aplikace. Znamená to, že vaše aplikace potřebuje obnovovací token pro dlouhodobé přístup k prostředkům. |
| state |Doporučené |Hodnota v požadavku, který je vrácený v odpovědi tokenu. Může být řetězec jakéhokoli obsahu, který chcete použít. Obvykle, náhodně generované, jedinečné je použita hodnota, aby se zabránilo útokům padělání žádosti více webů. Stav slouží také ke kódování informace o stavu uživatele v aplikaci předtím, než došlo k žádosti o ověření, jako jsou stránky na. |
| Hodnota Nonce |Požaduje se |Hodnota zahrnutý v požadavku (generovaný aplikací), který je součástí výsledný token ID jako deklarace identity. Aplikace pak můžete ověřit tuto hodnotu a zmírnění útoků opětovného přehrání tokenu. Hodnota je obvykle náhodnou jedinečného řetězce, který můžete použít k identifikaci původcem požadavku. |
| p |Požaduje se |Zásady ke spuštění. Jde o název zásady (tok uživatele), která se vytvoří ve vašem tenantovi Azure AD B2C. Hodnota názvu zásad by měl začínat **b2c\_1\_**. Další informace najdete v tématu [toky uživatelů Azure AD B2C](active-directory-b2c-reference-policies.md). |
| řádek |Nepovinné |Typ interakce s uživatelem, který je potřeba. V současné době je jedinou platnou hodnotou `login`. To přinutí uživatele k zadání přihlašovacích údajů tohoto požadavku. Jednotné přihlašování se projeví. |

V tomto okamžiku se uživateli výzva k dokončení pracovního postupu zásady. To může zahrnovat uživatele při zadávání uživatelského jména a hesla, přihlašování přes sociální identity, k adresáři nebo jakékoli jiné číslo kroky. Uživatel akce závisí na tok uživatele bude definován takhle.

Když uživatel dokončil tok uživatele, Azure AD vrátí odpověď do vaší aplikace na hodnotu jste použili pro `redirect_uri`. Používá metodu určenou v příkazu `response_mode` parametru. Odpověď je přesně stejný pro jednotlivé uživatele akce scénáře, nezávisle na spuštěný tok uživatele.

### <a name="successful-response"></a>Úspěšné odpovědi
Úspěšná odpověď, která používá `response_mode=fragment` a `response_type=id_token+token` vypadá podobně jako následující okno, kde konců řádků pro čitelnost:

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
| --- | --- |
| access_token |Přístupový token, který požadované aplikace.  Přístupový token by neměly být dekódovat nebo v opačném případě ho zkontrolovat. To lze považovat za neprůhledný řetězec. |
| token_type |Hodnota, typ tokenu. Jediný typ, který podporuje Azure AD je nosiče. |
| expires_in |Časový úsek, který je přístupový token platný (v sekundách). |
| scope |Obory, které je token platný pro. Můžete také pomocí oborů tokeny v mezipaměti pro pozdější použití. |
| id_token |ID tokenu požadovanou aplikaci. ID token můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. Další informace o tokeny typu ID a jejich obsah, najdete v článku [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| state |Pokud `state` parametr je zahrnutý v požadavku, stejnou hodnotu by se měla zobrazit v odpovědi. Aplikace by měla ověřte, že `state` hodnoty v požadavku a odpovědi jsou identické. |

### <a name="error-response"></a>Odpověď na chybu
Chybové odpovědi lze také odešle do identifikátor URI pro přesměrování tak, aby aplikace můžete odpovídajícím způsobem zpracovat:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --- | --- |
| error |Chybový řetězec kódu slouží ke klasifikaci typy chyb, ke kterým dochází. Kód chyby: můžete použít také pro zpracování chyb. |
| error_description |Určité chybové zprávě, které vám pomohou identifikovat hlavní příčinu chyby ověřování. |
| state |Viz úplný popis v předchozí tabulce. Pokud `state` parametr je zahrnutý v požadavku, stejnou hodnotu by se měla zobrazit v odpovědi. Aplikace by měla ověřte, že `state` hodnoty v požadavku a odpovědi jsou identické.|

## <a name="validate-the-id-token"></a>Ověřit ID token
Přijetí tokenu ID není dostatečná k ověření uživatele. Je nutné ověřit ID token podpisu a ověřte, deklarace identity v tokenu podle požadavků vaší aplikace. Azure AD B2C používá [webové tokeny JSON (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografii využívající veřejného klíče pro podepisování tokenů a ověřte, že jsou platné.

Řada open source knihovny jsou dostupné pro ověřování tokeny Jwt, v závislosti na jazyk, který chcete použít. Vezměte v úvahu zkoumání dostupných knihoven open source, spíše než implementace logiky ověření. Můžete použít informace v tomto článku vám pomůžou získat informace tom, jak správně používat tyto knihovny.

Azure AD B2C má koncový bod metadat OpenID Connect. Aplikaci můžete použít koncový bod se načíst informace o Azure AD B2C v době běhu. Tyto informace zahrnují koncových bodů, obsah tokenu a tokenu podpisových klíčů. Existuje dokument metadat JSON pro každý tok uživatele ve vašem tenantovi Azure AD B2C. Například v dokumentu metadat b2c_1_sign_in tok uživatele v tenantovi fabrikamb2c.onmicrosoft.com je umístěn zde:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jedna z vlastností dokumentu tato konfigurace je `jwks_uri`. Hodnota pro stejný tok uživatele by být:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Chcete-li zjistit, které toku uživatele se použil k podepsání tokenu ID (a kde k načtení metadat z), máte dvě možnosti. Nejprve je součástí názvu toku uživatele `acr` deklarací identity v `id_token`. Informace o tom, jak analyzovat deklarací z tokenu ID najdete v tématu [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). Další možností je určený ke kódování tok uživatele v hodnotě `state` parametr, když vydáte požadavek. Potom, dekódování `state` parametr k určení, které tok uživatele. byl použit. Některé z metod je platný.

Po dokumentu metadat z koncových bodů metadat OpenID Connect, které jste získali, můžete se ověřit podpis tokenu ID veřejné klíče RSA-256 (nachází se v tomto koncovém bodu). Může být uvedené v tomto koncovém bodu v daném okamžiku více klíčů, nichž každá je označena `kid`. Záhlaví `id_token` obsahuje také `kid` deklarací identity. Označuje, který tyto klíče se použil k podepsání ID token. Další informace, včetně informací o [ověřování tokenů](active-directory-b2c-reference-tokens.md#token-validation), najdete v článku [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Po ověření podpisu tokenu ID několik deklarací vyžadovat ověření. Příklad:

* Ověření `nonce` deklaraci identity pro zabránění útokům opětovného přehrání tokenu. Jeho hodnota musí být zadaná v žádosti o přihlášení.
* Ověření `aud` deklaraci identity pro zajištění, že byl vydán ID token pro vaši aplikaci. Jeho hodnota by měla být ID aplikace pro vaši aplikaci.
* Ověření `iat` a `exp` tvrdí, ujistěte se, že nevypršela platnost tokenu ID.

Několik další ověření, které byste měli provést jsou popsány podrobně [OpenID Connect základní specifikace](http://openid.net/specs/openid-connect-core-1_0.html). Můžete také ověřit další deklarace identity, v závislosti na vašem scénáři. Některé běžné ověření patří:

* Zajištění, že na uživatele nebo organizaci zaregistroval k aplikaci.
* Zajištění, že uživatel má příslušná oprávnění a oprávnění.
* Zajištění, že sílu ověřování došlo, například jak pomocí Azure Multi-Factor Authentication.

Další informace o deklarace identity v tokenu ID najdete v článku [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md).

Poté, co jste ověřili zcela ID token, můžete začít relaci s uživatelem. Ve vaší aplikaci použijte deklarace identity v tokenu ID k získání informací o uživateli. Tyto informace můžete použít pro zobrazení, záznamy, autorizace a tak dále.

## <a name="get-access-tokens"></a>Získání přístupových tokenů
Je-li jediné, co vaše webové aplikace potřebuje umět spouštět toky uživatelů, můžete přeskočit několik částí. Informace v následujících částech se dají použít jenom k webovým aplikacím, které potřebujete k provádění ověřených volání do webového rozhraní API a které jsou chráněné službou Azure AD B2C.

Teď, když jste přihlášení uživatele pro jednostránkovou aplikaci, můžete získat přístupové tokeny pro volání webových rozhraní API, které jsou zabezpečené pomocí Azure AD. I v případě, že jste už dostali token s použitím `token` typ odpovědi, tuto metodu můžete použít k získání tokenů pro další prostředky bez přesměrování uživatel znovu přihlásil.

V toku běžné webové aplikace, provedli byste to tak, že požadavek `/token` koncového bodu.  Koncový bod však nepodporuje požadavků CORS, takže volání jazyka AJAX k získání a obnovovacích tokenů není možné. Místo můžete použít implicitní tok ve skrytém elementu iframe HTML k získání nových tokenů pro jiné webové rozhraní API. Tady je příklad, pomocí konců řádků pro čitelnost:

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
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci v [webu Azure portal](https://portal.azure.com). |
| response_type |Požaduje se |Musí zahrnovat `id_token` pro přihlášení OpenID Connect.  Může taky obsahovat typ odpovědi `token`. Pokud používáte `token` tady, vaše aplikace může okamžitě přijímat přístupový token z koncového bodu authorize přitom druhou žádost do koncového bodu authorize. Pokud používáte `token` typ odpovědi `scope` parametr musí obsahovat obor, který označuje, který prostředek se má token vydat. |
| redirect_uri |Doporučené |Identifikátor URI přesměrování vaší aplikace, kde můžete odesílat a přijímat aplikací pro žádosti o ověření. Se musí přesně odpovídat jednu registraci na portálu pro identifikátory URI přesměrování s tím rozdílem, že musí být kódovaná adresou URL. |
| scope |Požaduje se |Seznam oborů oddělených mezerami.  Jak získat tokeny, zahrnují všechny obory, které požadujete pro požadovaný prostředek. |
| response_mode |Doporučené |Určuje metodu, která se používá k odesílání výsledný token zpátky do vaší aplikace.  Může být `query`, `form_post`, nebo `fragment`. |
| state |Doporučené |Hodnota v požadavku, který je vrácený v odpovědi tokenu.  Může být řetězec jakéhokoli obsahu, který chcete použít.  Obvykle, náhodně generované, jedinečné je použita hodnota, aby se zabránilo útokům padělání žádosti více webů.  Stav se také používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě. Například stránky nebo zobrazení uživatel byl v. |
| Hodnota Nonce |Požaduje se |Hodnota zahrnutý v požadavku, vygenerované aplikaci, která je součástí výsledný token ID jako deklarace identity.  Aplikace pak můžete ověřit tuto hodnotu a zmírnění útoků opětovného přehrání tokenu. Hodnota je obvykle náhodného, jedinečný řetězec, který identifikuje původcem požadavku. |
| řádek |Požaduje se |Chcete-li obnovit a získat tokeny v skrytý element iframe, použijte `prompt=none` zajistíte, že iframe není zablokuje na přihlašovací stránku a vrátí hodnotu okamžitě. |
| login_hint |Požaduje se |Aktualizace a získat tokeny v skrytý element iframe, zahrňte uživatelské jméno uživatele, tato Nápověda k rozlišení mezi více relací, které uživatel může mít v daném okamžiku. Uživatelské jméno se dají extrahovat z předchozí znak v s použitím `preferred_username` deklarací identity. |
| domain_hint |Požaduje se |Může být `consumers` nebo `organizations`.  Pro aktualizaci a získávání tokenů v skrytý element iframe, musíte zahrnout `domain_hint` hodnota v požadavku.  Extrahovat `tid` deklarací z tokenu ID starší sign-in Chcete-li zjistit, jaká hodnota se má použít.  Pokud `tid` deklarace identity, je hodnota `9188040d-6c67-4c5b-b112-36a304b66dad`, použijte `domain_hint=consumers`.  Jinak použijte `domain_hint=organizations`. |

Tím, že nastavíte `prompt=none` parametr, tento požadavek buď uspěje nebo selže okamžitě a vrátí do vaší aplikace.  Úspěšné odpovědi odeslané do vaší aplikace na adrese zadaný identifikátor URI pro přesměrování, pomocí metody popsané v `response_mode` parametru.

### <a name="successful-response"></a>Úspěšné odpovědi
Úspěšné odpovědi pomocí `response_mode=fragment` vypadá přibližně takto:

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
| access_token |Token, který požadované aplikace. |
| token_type |Typ tokenu bude vždy nosiče. |
| state |Pokud `state` parametr je zahrnutý v požadavku, stejnou hodnotu by se měla zobrazit v odpovědi. Aplikace by měla ověřte, že `state` hodnoty v požadavku a odpovědi jsou identické. |
| expires_in |Jak dlouho je přístupový token platný (v sekundách). |
| scope |Obory, které je přístupový token platný pro. |

### <a name="error-response"></a>Odpověď na chybu
Chybové odpovědi lze také odešle do identifikátor URI pro přesměrování tak, aby aplikace můžete odpovídajícím způsobem zpracovat.  Pro `prompt=none`, očekávané chybové vypadá takto:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který slouží ke klasifikaci typy chyb, ke kterým dochází. Můžete také použít řetězec reagovat na chyby. |
| error_description |Určité chybové zprávě, které vám pomohou identifikovat hlavní příčinu chyby ověřování. |

Pokud tato chyba se zobrazí v žádosti o prvku iframe, uživatel musí interaktivně Přihlaste se znovu získat nový token. Dokáže zpracovat to tak, aby to mělo smysl pro vaše aplikace.

## <a name="refresh-tokens"></a>Obnovovacích tokenů
Tokeny typu ID a přístupové tokeny vyprší po krátké době. Aplikace musí být připravené na tyto tokeny se pravidelně aktualizují.  Pokud chcete aktualizovat buď typ tokenu, provést stejný požadavek skryté iframe jsme použili v předchozím příkladu se pomocí `prompt=none` parametr řídit kroky v Azure AD.  Pro příjem nové `id_token` hodnotu, nezapomeňte použít `response_type=id_token` a `scope=openid`a `nonce` parametru.

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení
Pokud chcete přihlášení uživatele z aplikace, přesměruje uživatele na Azure AD a odhlásit se. Pokud to neuděláte, může uživatel moci donutit k vaší aplikaci bez nutnosti zadávat své přihlašovací údaje znovu. Je to proto, že mají platný jednotné přihlašování relaci s Azure AD.

Můžete jednoduše přesměrovat uživatele `end_session_endpoint` , který je uveden v stejném OpenID Connect dokumentu metadat je popsáno v [ověřit ID token](#validate-the-id-token). Příklad:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| p |Požaduje se |Zásadu, který se má použít pro přihlášení uživatele z vaší aplikace. |
| post_logout_redirect_uri |Doporučené |Adresu URL, kterou uživatel by měl být přesměrován na po úspěšném odhlášení. Pokud neuvedete, Azure AD B2C zobrazí obecná zpráva uživateli. |

> [!NOTE]
> Směruje uživatele `end_session_endpoint` vymaže část jednotné přihlašování – stav uživatele v Azure AD B2C. Ale odhlášeni uživatele mimo relaci zprostředkovatele sociální identity uživatele. Pokud si uživatel vybere stejné identifikovat zprostředkovatele během následné přihlášení, uživatel k novému ověření, bez nutnosti zadávat své přihlašovací údaje. Pokud chce uživatel se odhlásit z aplikace Azure AD B2C, neznamená nutně, že chtějí zcela odhlásit z jejich účtu na Facebooku, např. Ale pro místní účty uživatelské relace bude ukončena správně.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>Použití vašeho vlastního tenanta Azure AD B2C
Pokud chcete vyzkoušet tyto požadavky, proveďte následující tři kroky. Nahraďte vzorové hodnoty, které používáme v tomto článku s vlastními hodnotami:

1. [Vytvoření tenanta Azure AD B2C](active-directory-b2c-get-started.md). Použijte název vašeho tenanta v požadavcích.
2. [Vytvoření aplikace](active-directory-b2c-app-registration.md) k získání ID aplikace a `redirect_uri` hodnotu. Zahrnout webovou aplikaci nebo webové rozhraní API ve vaší aplikaci. Volitelně můžete vytvořit tajný klíč aplikace.
3. [Vytvářet toky uživatelů](active-directory-b2c-reference-policies.md) získat názvy tok uživatele.

## <a name="samples"></a>Ukázky

* [Vytvořit jednostránkovou aplikaci s využitím Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Vytvoření jednostránkové aplikace s použitím rozhraní .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

