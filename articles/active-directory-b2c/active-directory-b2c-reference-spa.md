---
title: 'Azure Active Directory B2C: Jednostránkové aplikace pomocí implicitního toku | Microsoft Docs'
description: Naučte se vytvářet jednostránkové aplikace přímo pomocí implicitního toku OAuth 2.0 s Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/06/2017
ms.author: davidmu
ms.openlocfilehash: 3347eac16e447091ffcaaf403e1291e2c7175a2d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Azure AD B2C: Jednostránkové aplikace přihlášení pomocí implicitního toku OAuth 2.0

Řada moderních aplikací využívá jednostránkový front-end, který je primárně napsané v jazyce JavaScript. Aplikace je často zapsány pomocí rozhraní jako AngularJS, Ember.js nebo Durandal. Jednostránkové aplikace a další aplikace JavaScript, které běží především v prohlížeči mají některé další problémy týkající se ověřování:

* Charakteristiky zabezpečení těchto aplikací se výrazně liší od tradiční na serveru webové aplikace.
* Mnoho serverů autorizace a zprostředkovatelů identity nepodporují požadavků (CORS) pro sdílení prostředků různého původu.
* Přesměrování celou stránku prohlížeče směrem od aplikace může být výrazně invazivní uživatelského rozhraní.

Pro podporu těchto aplikací Azure Active Directory B2C (Azure AD B2C) používá implicitního toku OAuth 2.0. Tok implicitní udělení autorizace OAuth 2.0 je popsaná v [části 4.2 specifikace OAuth 2.0](http://tools.ietf.org/html/rfc6749). V implicitní tok, aplikace přijímá tokeny přímo ze služby Azure Active Directory (Azure AD) zajistí autorizaci koncového bodu, bez jakékoli exchange serveru na server. Všechny logiku ověřování a relace trvá zpracování umístit zcela v JavaScript klienta, bez přesměrování další stránky.

Azure AD B2C rozšiřuje standardní implicitního toku OAuth 2.0 více než jednoduché ověřování a autorizace. Azure AD B2C zavádí [zásad parametr](active-directory-b2c-reference-policies.md). S parametrem zásad vám pomůže OAuth 2.0 přidejte koncových uživatelů na aplikaci, například registrace, přihlašování a správy profilů. V tomto článku jsme ukazují, jak každý z těchto prostředí implementovat vaše jednostránkové aplikace pomocí implicitního toku a Azure AD. Chcete-li začít, podívejte se na naše [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) a [rozhraní Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) ukázky.

V žádosti o příklad HTTP v tomto článku používáme naše ukázka adresáře Azure AD B2C, **fabrikamb2c.onmicrosoft.com**. Také používáme vlastní ukázkové aplikace a zásady. Požadavky můžete také zkusit sami pomocí těchto hodnot, nebo je můžete nahradit vlastními hodnotami.
Zjistěte, jak [získat vlastní adresář Azure AD B2C, aplikace a zásady](#use-your-own-b2c-tenant).


## <a name="protocol-diagram"></a>Diagram protokolu

Implicitní tok přihlášení vypadá podobně jako na následujícím obrázku. Každý krok je podrobně popsaná v dále v tomto článku.

![OpenID Connect plaveckých drah](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Odeslání žádosti o ověření
Když webové aplikace potřebuje k ověření uživatele a spustit zásadu, ho uživatele přesměruje `/authorize` koncový bod. Toto je interaktivní část toku, kde uživatel provede akci, v závislosti na zásady. Uživatel získá ID tokenu z koncového bodu Azure AD.

V této žádosti o označuje klienta v `scope` parametr oprávnění, která je nutné získat od uživatele. V `p` parametr, znamená to zásady provést. Následující tři příklady (pomocí konců řádků čitelnější) každou pomocí různých zásad. Chcete-li se podívat, jak funguje každý požadavek, zkuste vkládání požadavek do prohlížeče a spustit ho.

### <a name="use-a-sign-in-policy"></a>Použít zásadu přihlášení
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>Použít zásadu registrace
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>Pomocí zásady úpravy profilu
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
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
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci v [portál Azure](https://portal.azure.com). |
| response_type |Požaduje se |Musí zahrnovat `id_token` pro OpenID Connect přihlášení. Typ odpovědi také může zahrnovat `token`. Pokud používáte `token`, vaše aplikace může bez provedení další požadavek na koncový bod authorize okamžitě přijímat přístupového tokenu z koncového bodu authorize.  Pokud použijete `token` typ odpovědi `scope` parametr musí obsahovat obor, který určuje, který prostředek vystavit token pro. |
| redirect_uri |Doporučené |Identifikátor URI přesměrování vaší aplikace, kde můžete odesílat a přijímat aplikace odpovědi ověřování. Ho musí přesně shodovat s jedním přesměrování identifikátory URI, který je zaregistrovaný na portálu, s tím rozdílem, že musí být kódovaná adresou URL. |
| response_mode |Doporučené |Určuje metodu sloužící k odeslání výsledný token zpět do vaší aplikace.  Implicitní toky, použijte `fragment`. |
| scope |Požaduje se |Seznam obory oddělených mezerami. Hodnota jeden obor označuje do služby Azure AD i oprávnění, která se vyžadují. `openid` Oboru označuje oprávnění přihlásit uživatele a získat data o uživateli ve formě ID tokeny. (Budeme mluvit o tom víc později v článku.) `offline_access` Obor je volitelné pro webové aplikace. Označuje, že vaše aplikace musí obnovovací token pro dlouhodobé přístup k prostředkům. |
| state |Doporučené |Hodnota, zahrnuté v požadavku, která je vrácena v odpovědi tokenu. Může být řetězec o délce veškerý obsah, který chcete použít. Obvykle náhodně generované, jedinečné hodnoty se používá, aby se zabránilo útokům padělání požadavku posílaného mezi weby. Stav se také používá ke kódování informace o stavu uživatele v aplikaci, než požadavek na ověření došlo k chybě, jako jsou stránky byly na. |
| hodnotu Nonce |Požaduje se |Hodnota, zahrnuté v požadavku (generované aplikace), který je součástí výsledný token ID jako deklarace identity. Aplikace pak může ověřit tuto hodnotu zmírnit útoky opětovného přehrání tokenu. Hodnota je obvykle náhodnou jedinečného řetězce, který můžete použít k identifikaci původcem požadavku. |
| p |Požaduje se |Zásada provést. Je název zásady, který je vytvořen v klientovi Azure AD B2C. Hodnota název zásad by měl začínat obráceným **b2c\_1\_**. Další informace najdete v tématu [integrovaných zásad Azure AD B2C](active-directory-b2c-reference-policies.md). |
| řádku |Nepovinné |Typ interakci s uživatelem, které je nutné. V současné době je jedinou platnou hodnotou `login`. Vynutí se tak, aby uživatel zadal své přihlašovací údaje tohoto požadavku. Jednotné přihlašování se neprojeví. |

V tomto okamžiku uživatel požádán o dokončení pracovního postupu nastavení zásad. To může zahrnovat uživatele zadáním uživatelského jména a hesla, přihlášení pomocí sociálních identitu, přihlašování k adresáři, nebo jakékoli jiné číslo kroky. Uživatel akce závisí na tom, jak je definované zásady.

Poté, co uživatel vykoná zásadu, Azure AD vrátí odpověď do vaší aplikace na hodnotu jste použili pro `redirect_uri`. Používá metody popsané v `response_mode` parametr. Odpověď je přesně stejný pro všechny uživatele akce scénářů, nezávisle na zásadu, která byla spuštěna.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď, který používá `response_mode=fragment` a `response_type=id_token+token` vypadá jako na následujícím obrázku se zalomení řádků pro čitelnost:

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
| access_token |Přístupový token, který požadované aplikace.  Přístupový token by neměl být dekódovat nebo jinak prověřovány. Může být považovány za neprůhledný řetězec. |
| token_type |Typ tokenu hodnota. Pouze typ, který podporuje Azure AD je nosiče. |
| expires_in |Časový interval, který přístupový token je platný (v sekundách). |
| scope |Obory, které token je platný pro. Také můžete pomocí oborů tokeny mezipaměti pro pozdější použití. |
| id_token |ID token, který požadované aplikace. ID token můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. Další informace o ID tokeny a jejich obsah, najdete v článku [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| state |Pokud `state` parametr je zahrnuta v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda `state` hodnoty v požadavku a odpovědi jsou identické. |

### <a name="error-response"></a>Chybové odpovědi
Chybové odpovědi můžete také odešle do identifikátor URI přesměrování tak, aby aplikace můžete správně zpracovat:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Popis |
| --- | --- |
| error |Chybový řetězec kód slouží ke klasifikaci typů chyb, ke kterým dochází. Kód chyby můžete použít také pro zpracování chyb. |
| error_description |Konkrétní chybová zpráva, která vám může pomoct určit hlavní příčinu chyby ověřování. |
| state |Viz úplný popis v předchozí tabulce. Pokud `state` parametr je zahrnuta v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda `state` hodnoty v požadavku a odpovědi jsou identické.|

## <a name="validate-the-id-token"></a>Ověřit ID token
Přijetí tokenu ID není dostatečně k ověření uživatele. Musíte ověřit ID token podpisu a ověřit deklarace identity v tokenu podle požadavků vaší aplikace. Používá Azure AD B2C [webové tokeny JSON (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografie využívající veřejného klíče pro podepisování tokenů a ověřte, zda je platný.

Mnoho knihovny open-source jsou k dispozici pro ověření tokeny Jwt, v závislosti na jazyk, který chcete použít. Vezměte v úvahu zkoumat dostupné knihovny open-source než implementace vlastní logiky ověřování. Informace v tomto článku vám pomůže vám pomůže naučit odpovídajícím způsobem používat tyto knihovny.

Azure AD B2C má koncový bod metadat OpenID Connect. Aplikace můžete použít koncový bod se načíst informace o Azure AD B2C za běhu. Tyto informace zahrnují koncových bodů, obsah tokenu a token podpisových klíčů. Je dokument metadat JSON pro každou zásadu v klientovi služby Azure AD B2C. Dokument metadat pro zásady b2c_1_sign_in v fabrikamb2c.onmicrosoft.com klientovi je například v umístění:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Jedna z vlastností tohoto dokumentu konfigurace je `jwks_uri`. Hodnota pro stejné zásady by být:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Chcete-li zjistit, jaké zásady se použil k podepsání ID token (a kde se načíst metadata z), máte dvě možnosti. Nejprve je součástí název zásady `acr` deklarací identity v `id_token`. Informace o tom, jak analyzovat deklarací z tokenu ID najdete v tématu [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md). Vaše další možností je určený ke kódování zásad v hodnotě `state` parametr, pokud vydáte požadavek. Potom dekódovat `state` parametr k určení, jaké zásady byl použit. Buď metoda je platná.

Poté, co jste získali dokument metadat z koncového bodu metadat OpenID Connect, můžete k ověření podpisu tokenu ID veřejné klíče RSA-256 (nachází se na tento koncový bod). Může být více klíčů uvedený na tento koncový bod v každém okamžiku, každý se identifikovanou pomocí `kid`. Záhlaví `id_token` také obsahuje `kid` deklarací identity. Označuje, která tyto klíče se použil k podepsání ID token. Další informace, včetně informací o [ověřování tokenů](active-directory-b2c-reference-tokens.md#token-validation), najdete v článku [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Po ověření podpisu tokenu ID několik deklarací vyžadovat ověření. Příklad:

* Ověření `nonce` deklarace identity, aby se zabránilo útokům opětovného přehrání tokenu. Její hodnota musí být zadaná v žádosti o přihlášení.
* Ověření `aud` deklarace identity k zajištění, že ID token vydán pro vaši aplikaci. Její hodnota musí být ID aplikace vaší aplikace.
* Ověření `iat` a `exp` Ujistěte se, zda nevypršela platnost tokenu ID deklarace identity.

Několik další ověření, které byste měli provést jsou podrobně popsány v [OpenID Connect základní specifikace](http://openid.net/specs/openid-connect-core-1_0.html). Můžete také ověřit další deklarace identity, v závislosti na vašem scénáři. Některé běžné ověření patří:

* Zajištění, že uživatele nebo organizace má zápis aplikace.
* Zajištění, že uživatel má příslušná oprávnění a oprávnění.
* Zajištění, že sílu ověřování došlo k chybě, tak, jak pomocí Azure Multi-Factor Authentication.

Další informace o deklaracích identity v tokenu ID najdete v tématu [odkaz tokenu Azure AD B2C](active-directory-b2c-reference-tokens.md).

Po zcela ověření tokenu ID můžete začít relaci s uživatelem. Ve vaší aplikaci pomocí deklarace identity v tokenu ID můžete získat informace o uživateli. Tyto informace můžete použít pro zobrazení, záznamy, autorizace a tak dále.

## <a name="get-access-tokens"></a>Získat přístupové tokeny
Pokud jediné, co vaše webové aplikace je potřeba provést spuštění zásady, můžete přeskočit několik dalších částech. Informace v následujících částech se vztahuje pouze na webové aplikace, které je potřeba ověřené volání webového rozhraní API a které jsou chráněné službou Azure AD B2C.

Teď, když jste přihlášení uživatele do jednostránkové aplikace, můžete získat přístupové tokeny pro volání webových rozhraní API, která jsou zabezpečená službou Azure AD. I v případě, že jste už dostali token pomocí `token` typ odpovědi, tuto metodu můžete použít získat tokeny pro další prostředky bez přesměrování uživatele se znovu přihlásit.

V toku typické webové aplikace, by to uděláte tak, že žádost o `/token` koncový bod.  Koncový bod však nepodporuje požadavků CORS, takže volání AJAX získat a obnovovacích tokenů není možné. Místo toho můžete implicitního toku ve skrytém elementu iframe HTML získat nové tokeny pro jiné webové rozhraní API. Tady je příklad, pomocí konců řádků pro čitelnost:

```

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
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
| client_id |Požaduje se |ID aplikace přiřazené vaší aplikaci v [portál Azure](https://portal.azure.com). |
| response_type |Požaduje se |Musí zahrnovat `id_token` pro OpenID Connect přihlášení.  Může také obsahovat typ odpovědi `token`. Pokud používáte `token` zde, vaše aplikace může okamžitě přijímat přístupového tokenu z koncového bodu authorize bez vytváření druhé požadavku koncového bodu authorize. Pokud použijete `token` typ odpovědi `scope` parametr musí obsahovat obor, který určuje, který prostředek vystavit token pro. |
| redirect_uri |Doporučené |Identifikátor URI přesměrování vaší aplikace, kde můžete odesílat a přijímat aplikace odpovědi ověřování. Ho musí přesně shodovat s jedním přesměrování identifikátory URI, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódovaná adresou URL. |
| scope |Požaduje se |Seznam obory oddělených mezerami.  Jak získat tokeny, zahrnují všechny obory, které požadujete pro určený prostředek. |
| response_mode |Doporučené |Určuje metodu, která se používá k odesílání výsledný token zpět do vaší aplikace.  Může být `query`, `form_post`, nebo `fragment`. |
| state |Doporučené |Hodnota součástí požadavek, který je vrácený v odpovědi tokenu.  Může být řetězec o délce veškerý obsah, který chcete použít.  Obvykle náhodně generované, jedinečné hodnoty se používá, aby se zabránilo útokům padělání požadavku posílaného mezi weby.  Stav se také používá ke kódování informace o stavu uživatele v aplikaci, než požadavek na ověření došlo k chybě. Například stránky nebo zobrazení uživatel byl na. |
| hodnotu Nonce |Požaduje se |Hodnota, zahrnuté v požadavku, vygenerovaný aplikací, který je součástí výsledný token ID jako deklarace identity.  Aplikace pak může ověřit tuto hodnotu zmírnit útoky opětovného přehrání tokenu. Hodnota je obvykle, náhodné, jedinečný řetězec, který identifikuje původcem požadavku. |
| řádku |Požaduje se |Chcete-li obnovit a získat tokeny v skrytá iframe, použijte `prompt=none` zajistit, že iframe není uváznout na přihlašovací stránce a vrátí okamžitě. |
| login_hint |Požaduje se |Chcete obnovit a získat tokeny v skrytá iframe, zahrnout uživatelské jméno uživatele, tento pomocný k rozlišení mezi více relací uživatel může mít v daném okamžiku. Uživatelské jméno může extrahovat z dřívějších sign-in pomocí `preferred_username` deklarací identity. |
| domain_hint |Požaduje se |Může být `consumers` nebo `organizations`.  Pro aktualizaci a jak získat tokeny v skrytá iframe, uvést `domain_hint` hodnota v požadavku.  Extrahování `tid` deklarací z tokenu ID starší sign-in k určení hodnotu, která chcete použít.  Pokud `tid` deklarace, hodnota je `9188040d-6c67-4c5b-b112-36a304b66dad`, použijte `domain_hint=consumers`.  Jinak použijte `domain_hint=organizations`. |

Nastavením `prompt=none` parametr tuto žádost buď úspěšné nebo selže okamžitě a vrátí do vaší aplikace.  Úspěšná odpověď je odeslána do vaší aplikace na uvedené URI přesměrování pomocí metody popsané v `response_mode` parametr.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď pomocí `response_mode=fragment` vypadá podobně jako tento:

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
| state |Pokud `state` parametr je zahrnuta v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda `state` hodnoty v požadavku a odpovědi jsou identické. |
| expires_in |Jak dlouho přístupový token je platný (v sekundách). |
| scope |Obory, které je platný pro přístupový token. |

### <a name="error-response"></a>Chybové odpovědi
Chybové odpovědi můžete také odešle do identifikátor URI přesměrování tak, aby aplikace můžete správně zpracovat.  Pro `prompt=none`, chybu očekávané vypadá takto:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, kterou můžete použít ke klasifikaci typů chyb, ke kterým dochází. Můžete také použít řetězec reagování na chyby. |
| error_description |Konkrétní chybová zpráva, která vám může pomoct určit hlavní příčinu chyby ověřování. |

Pokud tato chyba se zobrazí v požadavku iframe, uživatel interaktivně přihlásit znovu načíst nový token. To může zpracovávat způsobem, který dává smysl pro vaši aplikaci.

## <a name="refresh-tokens"></a>Obnovovacích tokenů
ID tokeny a přístupové tokeny vyprší po krátkou dobu. Aplikace musí být připraveny pravidelně aktualizovat tyto tokeny.  Aktualizace buď typu token, proveďte stejné skrytá iframe žádost jsme použili v předchozí příklad, pomocí `prompt=none` parametr řídit kroky Azure AD.  Pro příjem nové `id_token` hodnotu, je nutné použít `response_type=id_token` a `scope=openid`a `nonce` parametr.

## <a name="send-a-sign-out-request"></a>Poslat žádost o odhlášení
Pokud se chcete přihlásit uživatele mimo aplikaci, přesměruje uživatele na Azure AD se odhlásíte. Pokud to neuděláte, uživatel může být schopný k novému ověření do vaší aplikace bez opětovného zadávání svých přihlašovacích údajů. Je to proto, že budou mít platný jedné přihlášení relace s Azure AD.

Můžete jednoduše přesměruje uživatele na `end_session_endpoint` uvedenou v stejné OpenID Connect dokument metadat popsané v [ověřit ID token](#validate-the-id-token). Příklad:

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Povinné? | Popis |
| --- | --- | --- |
| p |Požaduje se |Zásady, který se mají použít pro přihlášení uživatele z vaší aplikace. |
| post_logout_redirect_uri |Doporučené |Adresu URL, kterou uživatel by měl být přesměrován na po úspěšné odhlášení. Pokud nezadáte, Azure AD B2C zobrazí obecná zpráva uživateli. |

> [!NOTE]
> Odkazovat uživatele `end_session_endpoint` vymaže některé z jedné přihlašování stavu uživatele v Azure AD B2C. Ale odhlášeni uživatele z relace zprostředkovatele sociální identity uživatele. Pokud uživatel vybere stejné identifikaci zprostředkovatele při následné přihlášení, uživatel k novému ověření, bez nutnosti zadávat své přihlašovací údaje. Pokud chce uživatel se odhlásit z vaší aplikace Azure AD B2C, je však nemusí znamenat, že chtějí úplně Odhlásit se ze svého účtu sítě Facebook, třeba. Ale pro místní účty relace uživatele bude ukončen správně.
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>Použití vlastního klienta Azure AD B2C
Chcete-li vyzkoušet tyto požadavky, proveďte následující tři kroky. Nahraďte ukázkové hodnoty, které používáme v tomto článku vlastními hodnotami:

1. [Vytvoření klienta Azure AD B2C](active-directory-b2c-get-started.md). Použijte název vašeho klienta v žádosti.
2. [Vytvoření aplikace](active-directory-b2c-app-registration.md) získat ID aplikace a `redirect_uri` hodnotu. Zahrňte do vaší aplikace na webovou aplikaci nebo webové rozhraní API. Volitelně můžete vytvořit tajný klíč aplikace.
3. [Vytvoření vlastních zásad](active-directory-b2c-reference-policies.md) získat názvy zásad.

## <a name="samples"></a>Ukázky

* [Vytvoření jednostránkové aplikace pomocí Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [Vytvoření jednostránkové aplikace s použitím rozhraní .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

