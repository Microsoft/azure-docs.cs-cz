---
title: Zabezpečení jednostránkové aplikace pomocí implicitního toku Azure AD v2.0 | Microsoft Docs
description: Vytváření webových aplikací pomocí služby Azure AD v2.0 provádění implicitní tok pro jednostránkové aplikace.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 07fbda30cdc76e5e4e82b79954d0b0a56e032b50
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="v20-protocols---spas-using-the-implicit-flow"></a>v2.0 protokoly - SPA pomocí implicitního toku
S koncovým bodem v2.0 můžete přihlásit uživatele do vaší jednostránkové aplikace s osobní i pracovní nebo školní účty od společnosti Microsoft. Jednostránkové a další aplikace JavaScript, spusťte především v prohlížeči řez pár zajímavé vyzve, pokud jde o ověřování:

* Charakteristiky zabezpečení těchto aplikací se výrazně liší od tradiční na serveru webové aplikace.
* Mnoho serverů autorizace & zprostředkovatelů identity nepodporují požadavků CORS.
* Celou stránku prohlížeč přesměruje mimo aplikaci stane zvlášť invazivní uživatelského rozhraní.

Pro tyto aplikace (vezměte v úvahu: AngularJS, Ember.js, React.js, atd) Azure AD podporuje toku OAuth 2.0 implicitní Grant. Implicitní tok je podrobněji popsaná [specifikace OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). Jeho primární výhodou je, že umožňuje aplikaci získat tokeny z Azure AD bez provedení back-end serveru exchange přihlašovacích údajů. To umožňuje aplikaci přihlásit uživatele, Udržovat relaci a získat tokeny do dalších webových rozhraní API vše v rámci klienta kódu jazyka JavaScript. Existuje několik důležité informace o zabezpečení vzít v úvahu při použití implicitní tok – konkrétně přibližně [klienta](http://tools.ietf.org/html/rfc6749#section-10.3) a [vystupovat jako jiný uživatel](http://tools.ietf.org/html/rfc6749#section-10.3).

Pokud chcete přidat ověřování do aplikace JavaScript pomocí implicitního toku a Azure AD, doporučujeme používat naše knihovna JavaScript s otevřeným zdrojem, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js). Nejsou k dispozici několik kurzy AngularJS [sem](active-directory-appmodel-v2-overview.md#getting-started) můžete začít pracovat. 

Ale pokud si přejete není pro použití knihovny v jediné stránce aplikace a odesílání zpráv protokolu sami, postupujte podle následujících obecných pokynů.

> [!NOTE]
> Ne všechny scénáře Azure Active Directory a funkce jsou podporovány koncového bodu v2.0. Pokud chcete zjistit, pokud byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
> 
> 

## <a name="protocol-diagram"></a>Diagram protokolu
Celý implicitní přihlášení toku vypadá zhruba takhle – všechny kroky jsou podrobně popsány v níže.

![OpenId Connect plaveckých drah](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Odeslání žádosti o přihlášení
Na začátku přihlášení uživatele do vaší aplikace, můžete odeslat [OpenID Connect](active-directory-v2-protocols-oidc.md) požadavek ověřování a získání přístupu `id_token` z koncového bodu v2.0:

> [!IMPORTANT]
> V pořadí, které se úspěšně žádosti o token ID registrace aplikací v [portálu pro registraci](https://apps.dev.microsoft.com) musí mít **[implicitní grant](active-directory-v2-protocols-implicit.md)** povolené pro webového klienta. Pokud není povolený, `unsupported_response` bude vrácena chyba: "pro vstupní parametr 'response_type' zadaná hodnota není povolena pro tohoto klienta. Očekávaná hodnota je 'kód."

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Klikněte na odkaz níže k provedení této žádosti. Po přihlášení, prohlížeč přesměrováni na `https://localhost/myapp/` s `id_token` na panelu Adresa.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parametr |  | Popis |
| --- | --- | --- |
| tenant |povinné |`{tenant}` Hodnotu v cestě požadavku slouží k řízení, kdo může přihlásit k aplikaci. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů. Další podrobnosti naleznete v [protokolu Základy](active-directory-v2-protocols.md#endpoints). |
| client_id |povinné |Id aplikace, portálu pro registraci ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) přiřazené vaší aplikace. |
| response_type |povinné |Musí zahrnovat `id_token` pro OpenID Connect přihlášení. Může také zahrnovat response_type `token`. Pomocí `token` zde vám umožní aplikaci přijímat přístupového tokenu z koncového bodu authorize okamžitě bez nutnosti provádět další požadavek na koncový bod authorize. Pokud použijete `token` response_type, `scope` parametr musí obsahovat obor, která určuje, který prostředek vystavit token pro. |
| redirect_uri |Doporučená |O položku redirect_uri vaší aplikace, kde můžete odesílat a přijímat aplikace odpovědi ověřování. Se musí přesně shodovat s jedním redirect_uris, které jste zaregistrovali na portálu, s výjimkou musí být kódovaná adresou url. |
| scope |povinné |Seznam obory oddělených mezerami. Pro OpenID Connect, musí obsahovat rozsah `openid`, což znamená, že je na oprávnění "Přihlásit" v souhlasu uživatelského rozhraní. Volitelně můžete také chtít zahrnout `email` nebo `profile` [obory](active-directory-v2-scopes.md) pro získání přístupu k datům dalšího uživatele. V této žádosti o žádosti o souhlas k různým prostředkům může také zahrnovat další obory. |
| response_mode |Doporučená |Určuje metodu, která se má použít k odeslání výsledný token zpět do vaší aplikace. Musí být `fragment` pro implicitní tok. |
| state |Doporučená |Hodnota, zahrnuté v požadavku, který bude vrácen také v odpovědi tokenu. Může být řetězec o délce veškerý obsah, který chcete. Náhodně generované jedinečné hodnoty se obvykle používá u [prevence útoků padělání požadavku posílaného mezi weby](http://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky nebo zobrazení, které byly na. |
| hodnotu Nonce |povinné |Hodnota, zahrnuté v požadavku, vygenerovaný aplikací, který bude zahrnut v výsledné požadavku id_token jako deklarace identity. Aplikace pak může ověřit tuto hodnotu zmírnit útoky opětovného přehrání tokenu. Hodnota je obvykle náhodnou jedinečného řetězce, který můžete použít k identifikaci původcem požadavku. |
| řádku |nepovinné |Označuje typ interakce s uživatelem, který je vyžadován. Jedinými platnými hodnotami v tuto chvíli se "přihlášení", 'none' a 'souhlas'. `prompt=login` Vynutí uživatele k zadání přihlašovacích údajů tohoto požadavku negace jednotného přihlašování. `prompt=none` je opak - zajistí, že uživatel není uveden s žádné interaktivní výzvu jakkoli. Pokud požadavek nemůže být dokončena bez upozornění pomocí jednotného přihlašování, koncový bod v2.0 vrátí chybu. `prompt=consent` Aktivuje se v dialogovém okně souhlas OAuth po přihlášení uživatele, požádá uživatele a udělit oprávnění k aplikaci. |
| login_hint |nepovinné |Slouží k předem vyplnit pole uživatelské jméno nebo e-mailovou adresu přihlašovací stránka pro uživatele, pokud znáte svoje uživatelské jméno předem. Často aplikace bude používat tento parametr během opětovné ověření, uživatelské jméno s již extrahovat z předchozí přihlášení pomocí `preferred_username` deklarací identity. |
| domain_hint |nepovinné |Může být jedna z `consumers` nebo `organizations`. Pokud zahrnuty, přeskočí proces zjišťování na základě e-mailu tento uživatel prochází na v2.0 přihlašovací stránky, což mírně zefektivnění činnost koncového uživatele. Často aplikace bude používat tento parametr během opětovné ověření extrahováním `tid` deklarace identity z požadavku id_token. Pokud `tid` deklarace, hodnota je `9188040d-6c67-4c5b-b112-36a304b66dad` (Account Microsoft klienta příjemce), měli byste použít `domain_hint=consumers`. Jinak použijte `domain_hint=organizations`. |


V tomto okamžiku uživatel se vyzve k zadání přihlašovacích údajů a dokončení ověření. Koncový bod v2.0 také zajistí, že uživatel souhlasí s tím oprávnění uvedené v `scope` parametr dotazu. Pokud uživatel nebyla přijata některé z těchto oprávnění, požádá uživatele o souhlas pro požadovaná oprávnění. Podrobnosti o [oprávnění, souhlasu a víceklientské aplikace jsou tady uvedené](active-directory-v2-scopes.md).

Jakmile se uživatel ověří a uděluje souhlas, koncový bod v2.0 vrátí odpověď na aplikaci ve uvedené `redirect_uri`, pomocí metody popsané v `response_mode` parametr.

#### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď pomocí `response_mode=fragment` a `response_type=id_token+token` vypadá jako na následujícím obrázku se zalomení řádků pro čitelnost:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Popis |
| --- | --- |
| access_token |Zahrnuté v případě `response_type` zahrnuje `token`. Přístupový token, který aplikace, v takovém případě požadované pro Microsoft Graph. Přístupový token by neměly být dekódovat, nebo v opačném případě prověřovány, lze považovat za neprůhledný řetězec. |
| token_type |Zahrnuté v případě `response_type` zahrnuje `token`. Bude vždy `Bearer`. |
| expires_in |Zahrnuté v případě `response_type` zahrnuje `token`. Určuje počet sekund, po které token je platný pro ukládání do mezipaměti pro účely. |
| scope |Zahrnuté v případě `response_type` zahrnuje `token`. Určuje rozsahy, pro které bude platit access_token. |
| id_token |Požadavku id_token, který požadované aplikace. Požadavku id_token slouží k ověření identity uživatele a zahájit relaci s uživatelem. Další informace o id_tokens a jejich obsah je součástí [odkaz tokenu koncový bod v2.0](active-directory-v2-tokens.md). |
| state |Pokud parametr stavu je obsažena v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda jsou identické hodnoty stavu v požadavku a odpovědi. |

#### <a name="error-response"></a>Chybové odpovědi
Chybové odpovědi se taky může odeslat do `redirect_uri` tak aplikace můžete správně zpracovat:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým došlo a slouží k reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování. |

## <a name="validate-the-idtoken"></a>Ověření požadavku id_token
Právě přijetí požadavku id_token není dostatečná k ověření uživatele; musíte ověřit podpis požadavku id_token a ověřit deklarace identity v tokenu podle požadavků vaší aplikace. Koncový bod v2.0 používá [webové tokeny JSON (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) a kryptografie využívající veřejného klíče pro podepisování tokenů a ověřte, zda je platný.

Můžete ověřit `id_token` v klientovi kód, ale běžnou praxí je odeslat `id_token` back-end server a že k ověření. Jste-li ověřit podpis požadavku id_token, budete několik deklarace identity, které budete muset ověřit. Najdete v článku [odkaz tokenu v2.0](active-directory-v2-tokens.md) Další informace, včetně [ověřování tokenů](active-directory-v2-tokens.md#validating-tokens) a [důležité informace o podpisový klíč výměny](active-directory-v2-tokens.md#validating-tokens). Doporučujeme, abyste tokeny využívající knihovnu analýzy a ověřování – existuje alespoň jeden pro většinu jazyky a platformy.
<!--TODO: Improve the information on this-->

Můžete také ověřit další deklarace identity v závislosti na vašem scénáři. Některé běžné ověření patří:

* Zajištění uživatele či organizace má registraci pro aplikaci.
* Zajištění uživatel má správná oprávnění autorizace
* Zajištění sílu ověřování došlo k, jako je vícefaktorové ověřování.

Další informace o deklaracích identity v požadavku id_token najdete v tématu [odkaz tokenu koncový bod v2.0](active-directory-v2-tokens.md).

Jakmile úplně ověření požadavku id_token můžete zahájit relaci s uživatelem a používat deklarace identity v požadavku id_token získat informace o uživateli ve vaší aplikaci. Tyto informace můžete použít pro zobrazení, záznamy, oprávnění atd.

## <a name="get-access-tokens"></a>Získat přístupové tokeny
Teď, když jste přihlášení uživatele k jednostránkové aplikace, můžete získat přístupové tokeny pro volání webových rozhraní API, které jsou zabezpečené službou Azure AD, jako [Microsoft Graph](https://graph.microsoft.io). I v případě, že jste už dostali tokenu pomocí `token` response_type, můžete použít tuto metodu získat tokeny na další zdroje, aniž by museli přesměruje uživatele se znovu přihlásit.

V normálním toku, OpenID Connect/OAuth, by to provedete vytváření požadavku v2.0 `/token` koncový bod. Koncový bod v2.0 však nepodporuje požadavků CORS, takže volání AJAX získat a obnovovacích tokenů je mimo na otázku. Místo toho můžete použít implicitní tok v skrytá iframe získat nové tokeny pro jiné webové rozhraní API: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [!TIP]
> Zkuste kopírování a vkládání níže požadavku do na záložce prohlížeče! (Nezapomeňte nahradit `domain_hint` a `login_hint` a správné hodnoty pro vaše uživatele)
> 
> 

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Parametr |  | Popis |
| --- | --- | --- |
| tenant |povinné |`{tenant}` Hodnotu v cestě požadavku slouží k řízení, kdo může přihlásit k aplikaci. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů. Další podrobnosti naleznete v [protokolu Základy](active-directory-v2-protocols.md#endpoints). |
| client_id |povinné |ID aplikace, která na portál pro registraci ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) přiřazené vaší aplikace. |
| response_type |povinné |Musí zahrnovat `id_token` pro OpenID Connect přihlášení. Může také obsahovat další response_types, jako například `code`. |
| redirect_uri |Doporučená |O položku redirect_uri vaší aplikace, kde můžete odesílat a přijímat aplikace odpovědi ověřování. Se musí přesně shodovat s jedním redirect_uris, které jste zaregistrovali na portálu, s výjimkou musí být kódovaná adresou url. |
| scope |povinné |Seznam obory oddělených mezerami. Jak získat tokeny, zahrnout všechny [obory](active-directory-v2-scopes.md) budete potřebovat pro daný prostředek, které vás zajímají. |
| response_mode |Doporučená |Určuje metodu, která se má použít k odeslání výsledný token zpět do vaší aplikace. Může být jedna z `query`, `form_post`, nebo `fragment`. |
| state |Doporučená |Hodnota, zahrnuté v požadavku, který bude vrácen také v odpovědi tokenu. Může být řetězec o délce veškerý obsah, který chcete. Náhodně generované jedinečné hodnoty se obvykle používá pro prevence útoků padělání požadavku posílaného mezi weby. Stav se také používá ke kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky nebo zobrazení, které byly na. |
| hodnotu Nonce |povinné |Hodnota, zahrnuté v požadavku, vygenerovaný aplikací, který bude zahrnut v výsledné požadavku id_token jako deklarace identity. Aplikace pak může ověřit tuto hodnotu zmírnit útoky opětovného přehrání tokenu. Hodnota je obvykle náhodnou jedinečného řetězce, který můžete použít k identifikaci původcem požadavku. |
| řádku |povinné |Aktualizovat & získávání tokeny ve skryté iframe, měli byste použít `prompt=none` zajistit, že iframe není zablokuje na stránku pro přihlášení v2.0 a vrátí okamžitě. |
| login_hint |povinné |Pro aktualizaci a jak získat tokeny v skrytá iframe, musí obsahovat uživatelské jméno uživatele, tento pomocný aby bylo možné rozlišit mezi více relací, které uživatel může mít k danému bodu v čase. Uživatelské jméno lze extrahovat z předchozí přihlášení pomocí `preferred_username` deklarací identity. |
| domain_hint |povinné |Může být jedna z `consumers` nebo `organizations`. Aktualizovat & získávání tokeny ve skryté iframe, je třeba zahrnout domain_hint v požadavku. Měli extrahovat `tid` deklarace identity z požadavku id_token předchozí sign-in k určení hodnotu, která chcete použít. Pokud `tid` deklarace, hodnota je `9188040d-6c67-4c5b-b112-36a304b66dad`, měli byste použít `domain_hint=consumers`. Jinak použijte `domain_hint=organizations`. |

Poděkování `prompt=none` parametr tento požadavek se buď úspěšné nebo nezdaří okamžitě a vrátit do vaší aplikace. Úspěšná odpověď zašle do vaší aplikace na uvedené `redirect_uri`, pomocí metody popsané v `response_mode` parametr.

#### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď pomocí `response_mode=fragment` vypadá jako:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parametr | Popis |
| --- | --- |
| access_token |Token, který požadované aplikace. |
| token_type |Bude vždy `Bearer`. |
| state |Pokud parametr stavu je obsažena v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Aplikace by měla ověřte, zda jsou identické hodnoty stavu v požadavku a odpovědi. |
| expires_in |Jak dlouho přístupový token je platný (v sekundách). |
| scope |Obory, které je platný pro přístupový token. |

#### <a name="error-response"></a>Chybové odpovědi
Chybové odpovědi se taky může odeslat do `redirect_uri` tak aplikace můžete správně zpracovat. U `prompt=none`, bude očekávanou chybu:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým došlo a slouží k reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování. |

Pokud tato chyba se zobrazí v požadavku iframe, uživatel interaktivně přihlásit znovu načíst nový token. Můžete zpracovávat tento případ v jakémkoli způsob, jak má smysl pro vaši aplikaci.

## <a name="validating-access-tokens"></a>Ověřování přístupové tokeny

Jakmile se zobrazí access_token, nezapomeňte ověřit podpis tokenu a také následující deklarace identity. Můžete také ověřit další deklarace identity založené na váš scénář. 

* **Cílová skupina** deklarace identity, k zajištění, že token neměla být poskytnut do vaší aplikace
* **Vystavitel** deklarace identity, chcete-li ověřit, zda byl token vydán do vaší aplikace koncovým bodem v2.0
* **Neplatný před** a **čas vypršení platnosti** deklarací, chcete-li ověřit, zda nevypršela platnost tokenu

Další informace o deklaracích identity, která je přítomen v tokenu přístupu najdete v tématu [odkaz tokenu koncový bod v2.0](active-directory-v2-tokens.md)

## <a name="refreshing-tokens"></a>Aktualizace tokeny
Implicitní grant neposkytuje obnovovacích tokenů. Obě `id_token`s a `access_token`s vyprší po krátké době času, aby vaše aplikace musí být připraveno k aktualizaci těchto tokeny pravidelně. Chcete-li aktualizovat buď typ tokenu, můžete provádět stejném požadavku skrytá iframe výše pomocí `prompt=none` parametru můžete řídit chování Azure AD. Pokud chcete dostávat novou `id_token`, nezapomeňte použít `response_type=id_token` a `scope=openid`, jakož i `nonce` parametr.

## <a name="send-a-sign-out-request"></a>Odhlaste se žádost o odeslání
OpenIdConnect `end_session_endpoint` umožňuje aplikaci k odeslání požadavku na koncový bod v2.0 ukončení relace uživatele a vymažete soubory cookie nastavte koncovým bodem v2.0. Plně přihlášení uživatele z webové aplikace, aplikace by měla ukončení vlastní relaci s uživatele (obvykle zaškrtnutím nebo zrušením mezipamětí tokenů odstranit soubory cookie) a pak přesměrování prohlížeče:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr |  | Popis |
| --- | --- | --- |
| tenant |povinné |`{tenant}` Hodnotu v cestě požadavku slouží k řízení, kdo může přihlásit k aplikaci. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů. Další podrobnosti naleznete v [protokolu Základy](active-directory-v2-protocols.md#endpoints). |
| post_logout_redirect_uri | Doporučená | Adresa URL, která uživatel má být vrácen do po dokončení odhlášení. Tato hodnota se musí shodovat s jedním přesměrování, které se identifikátory URI registrované pro danou aplikaci. Pokud není zahrnut, uživateli se zobrazí obecná zpráva koncovým bodem v2.0. |
