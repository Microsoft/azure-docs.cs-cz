---
title: Implicitní tok grantů OAuth 2.0 – platforma identit microsoftu | Azure
description: Zabezpečte jednostránkové aplikace pomocí implicitního toku platformy identit Microsoftu.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 6e3f021fd888bbb408fa66964c54d22f0d68e84e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297702"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Platforma identit Microsoftu a implicitní tok grantů

S koncovým bodem platformy identit Microsoftu můžete uživatele přihlásit k jednostránkovým aplikacím s osobními i pracovními nebo školními účty od Microsoftu. Jednostránkové a jiné javascriptové aplikace, které běží především v prohlížeči, čelí několika zajímavým výzvám, pokud jde o ověřování:

* Charakteristiky zabezpečení těchto aplikací se výrazně liší od tradičních webových aplikací založených na serveru.
* Mnoho autorizačních serverů a zprostředkovatelů identit nepodporuje požadavky CORS.
* Celostránkový prohlížeč přesměrování z aplikace se stává obzvláště invazivní pro uživatelské prostředí.

Pro tyto aplikace (AngularJS, Ember.js, React.js a tak dále), platforma identit y Microsoft podporuje tok implicitní hodu OAuth 2.0. Implicitní tok je popsán ve [specifikaci OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Jeho hlavní výhodou je, že umožňuje aplikaci získat tokeny z platformy identit microsoftu bez provedení výměny pověření back-endového serveru. To umožňuje aplikaci přihlásit se k uživateli, udržovat relaci a získat tokeny do jiných webových rozhraní API v rámci klientského kódu JavaScriptu. Existuje několik důležitých aspektů zabezpečení, které je třeba vzít v úvahu při použití implicitního toku konkrétně kolem [zosobnění](https://tools.ietf.org/html/rfc6749#section-10.3) [klienta](https://tools.ietf.org/html/rfc6749#section-10.3) a uživatele .

Tento článek popisuje, jak programovat přímo proti protokolu ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny ověřování společnosti Microsoft (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Také se podívejte na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

Pokud však nechcete používat knihovnu v jednostránkové aplikaci a odesílat zprávy protokolu sami, postupujte podle následujících obecných kroků.

> [!NOTE]
> Ne všechny scénáře a funkce Služby Azure Active Directory (Azure AD) jsou podporované koncovým bodem platformy identit Microsoftu. Chcete-li zjistit, zda byste měli používat koncový bod platformy identit společnosti Microsoft, přečtěte si o [omezení platformy identit společnosti Microsoft](active-directory-v2-limitations.md).

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Vhodné scénáře pro implicitní grant OAuth2

Specifikace OAuth2 prohlašuje, že implicitní grant byl navržen tak, aby umožňoval aplikace user-agent - to znamená, javascriptové aplikace spouštěné v prohlížeči. Definující charakteristikou těchto aplikací je, že kód JavaScript se používá pro přístup k prostředkům serveru (obvykle webové rozhraní API) a pro odpovídajícím způsobem aktualizaci uživatelského prostředí aplikace. Přemýšlejte o aplikacích, jako je Gmail nebo Outlook Web Access: když vyberete zprávu z doručené pošty, změní se pouze panel vizualizace zpráv, aby se zobrazil nový výběr, zatímco zbytek stránky zůstane nezměněn. Tato charakteristika je v kontrastu s tradičními webovými aplikacemi založenými na přesměrování, kde každá interakce uživatele vede k zpětnému odeslání celé stránky a vykreslování nové odpovědi na server na celé stránce.

Aplikace, které se javascript založený přístup k jeho extrému se nazývají jednostránkové aplikace, nebo SPA. Myšlenka spočine na to, že tyto aplikace slouží pouze počáteční stránce HTML a přidruženému JavaScriptu, přičemž všechny následné interakce jsou řízeny voláním webového rozhraní API prováděnými prostřednictvím JavaScriptu. Hybridní přístupy, kde je aplikace většinou řízena postbackem, ale provádí příležitostné hovory JS, však nejsou neobvyklé – diskuse o implicitním využití toku je relevantní i pro ty.

Aplikace založené na přesměrování obvykle zabezpečují své požadavky prostřednictvím souborů cookie, tento přístup však nefunguje stejně dobře pro aplikace JavaScript. Soubory cookie fungují pouze proti doméně, pro kterou byly vygenerovány, zatímco volání javascriptu mohou směřovat do jiných domén. Ve skutečnosti to bude často případ: představte si aplikace s vyvoláním rozhraní Microsoft Graph API, rozhraní API Office, rozhraní Azure API – všechny s bydlištěm mimo doménu, ze které se aplikace obsluhuje. Rostoucí trend pro javascriptové aplikace je mít žádný back-end vůbec, spoléhat se 100% na webová api třetích stran k implementaci jejich obchodní funkce.

V současné době upřednostňovanou metodou ochrany volání webového rozhraní API je použití přístupu tokenu nosiče OAuth2, kde je každé volání doprovázeno přístupovým tokenem OAuth2. Webové rozhraní API zkontroluje příchozí přístupový token a pokud v něm najde potřebné obory, udělí přístup k požadované operaci. Implicitní tok poskytuje pohodlný mechanismus pro javascriptové aplikace pro získání přístupových tokenů pro webové rozhraní API, které nabízejí řadu výhod, pokud jde o soubory cookie:

* Tokeny lze spolehlivě získat bez nutnosti volání křížového původu – povinná registrace identifikátoru URI přesměrování, ke kterému jsou tokeny vráceny, zaručuje, že tokeny nejsou posunuty
* Aplikace JavaScriptu mohou získat tolik přístupových tokenů, kolik potřebují, pro tolik webových api, na které cílí – bez omezení domén
* Funkce HTML5, jako je relace nebo místní úložiště, poskytují plnou kontrolu nad ukládáním tokenů do mezipaměti a správou životnosti, zatímco správa souborů cookie je pro aplikaci neprůhledná
* Přístupové tokeny nejsou náchylné k útokům padělání požadavků na příčové stránky (CSRF)

Implicitní grantový tok nevydává tokeny aktualizace, většinou z bezpečnostních důvodů. Obnovovací token není tak úzce vymezenjako přístupové tokeny, což poskytuje mnohem větší moc, a tím způsobuje mnohem větší škody v případě, že je unikly. V implicitním toku jsou tokeny doručovány v adrese URL, proto je riziko zachycení vyšší než v udělení autorizačního kódu.

Aplikace JavaScript má však jiný mechanismus k dispozici pro obnovení přístupových tokenů bez opakovaného dotazování uživatele na pověření. Aplikace můžete použít skrytý iframe k provádění nových požadavků na tokeny proti koncovému bodu autorizace Azure AD: pokud prohlížeč má stále aktivní relaci (čti: má soubor cookie relace) proti doméně Azure AD, požadavek na ověření může úspěšně dojít bez nutnosti interakce s uživatelem.

Tento model uděluje aplikaci JavaScript možnost nezávisle obnovovat přístupové tokeny a dokonce získat nové pro nové rozhraní API (za předpokladu, že uživatel s nimi dříve souhlasil). Tím se zabrání další zátěž získání, údržbu a ochranu artefakt vysoké hodnoty, jako je například token aktualizace. Artefakt, který umožňuje tiché obnovení, soubor cookie relace Azure AD, se spravuje mimo aplikaci. Další výhodou tohoto přístupu je, že se uživatel může odhlásit z Azure AD pomocí libovolné aplikace přihlašované do Služby Azure AD, které běží na kterékoli záložce prohlížeče. To má za následek odstranění souboru cookie relace Azure AD a aplikace JavaScript automaticky ztratí možnost obnovit tokeny pro odpojiného uživatele.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Je implicitní grant vhodný pro mou aplikaci?

Implicitní grant představuje více rizik než jiné granty a oblasti, které je třeba věnovat pozornost, jsou dobře zdokumentovány (například [zneužití přístupového tokenu k zosobnění vlastníka prostředků v implicitním toku][OAuth2-Spec-Implicit-Misuse] a [OAuth 2.0 Threat Model a bezpečnostní aspekty][OAuth2-Threat-Model-And-Security-Implications]). Vyšší rizikový profil je však z velké části způsobeno skutečností, že je určen k povolení aplikací, které spouštějí aktivní kód, obsluhovaný vzdáleným prostředkem do prohlížeče. Pokud plánujete architekturu SPA, nemáte žádné back-endové komponenty nebo chcete vyvolat webové rozhraní API prostřednictvím JavaScriptu, doporučujeme použít implicitní tok pro získání tokenu.

Pokud je vaše aplikace nativní klient, implicitní tok není skvělé přizpůsobení. Absence souboru cookie relace Azure AD v kontextu nativního klienta zbavuje vaši aplikaci prostředků pro údržbu relace s dlouhou životností. To znamená, že vaše aplikace bude opakovaně vyzvat uživatele při získávání přístupových tokenů pro nové prostředky.

Pokud vyvíjíte webovou aplikaci, která obsahuje back-end a spotřebovává rozhraní API z jeho back-endového kódu, implicitní tok také není vhodné. Jiné granty vám dávají mnohem větší moc. Například udělení pověření klienta OAuth2 poskytuje možnost získat tokeny, které odrážejí oprávnění přiřazená samotné aplikaci, na rozdíl od delegování uživatelů. To znamená, že klient má možnost udržovat programový přístup k prostředkům i v případě, že uživatel není aktivně zapojen do relace a tak dále. Nejen to, ale takové granty poskytují vyšší bezpečnostní záruky. Například přístupové tokeny nikdy neprocházejí uživatelským prohlížečem, neriskují, že budou uloženy v historii prohlížeče a tak dále. Klientská aplikace může také provádět silné ověřování při požadování tokenu.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>Protokolový diagram

Následující diagram znázorňuje, jak vypadá celý implicitní tok přihlášení a následující oddíly popisují každý krok podrobněji.

![Diagram znázorňující implicitní tok přihlášení](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Chcete-li zpočátku přihlásit uživatele do vaší aplikace, můžete odeslat žádost o ověření [OpenID Connect](v2-protocols-oidc.md) a získat `id_token` z koncového bodu platformy identity Microsoftu.

> [!IMPORTANT]
> Chcete-li úspěšně požádat o token ID nebo přístupový token, registrace aplikace na [webu Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránka musí mít odpovídající implicitní tok grantu povoleno, výběrem **Tokeny ID** a.nebo **přístup tokeny** v části **Implicitní udělení.** Pokud není povolena, `unsupported_response` bude vrácena chyba: **Zadaný parametr pro vstupní parametr "response_type" není pro tohoto klienta povolen. Očekávaná hodnota je "kód"**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Chcete-li otestovat přihlášení pomocí <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize..</a> implicitního toku, klepněte na tlačítko . Po přihlášení by měl být prohlížeč `https://localhost/myapp/` přesměrován na adresu s adresním `id_token` panelem.
>

| Parametr |  | Popis |
| --- | --- | --- |
| `tenant` | Požadovaný |Hodnotu `{tenant}` v cestě požadavku lze použít k řízení, kdo se může přihlásit do aplikace. Povolené hodnoty `common`jsou `organizations` `consumers`identifikátory , , a tenant. Další podrobnosti naleznete v [základech protokolu](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Požadovaný | ID aplikace (klienta), které je k vaší aplikaci přiřazena stránka [Registrace aplikací – a registrací aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `response_type` | Požadovaný |Musí `id_token` obsahovat pro přihlášení OpenID Connect. Může také zahrnovat `token`response_type . Použití `token` zde umožní vaší aplikaci přijímat přístupový token okamžitě z autorizovat koncový bod, aniž by museli provést druhý požadavek na autorizovat koncový bod. Pokud použijete `token` response_type, `scope` parametr musí obsahovat obor označující, pro který prostředek má být vydán token (například user.read v aplikaci Microsoft Graph).  |
| `redirect_uri` | Doporučené |Redirect_uri vaší aplikace, kde mohou být odpovědi na ověření odesílány a přijímány vaší aplikací. Musí přesně odpovídat jednomu z redirect_uris, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódována adresou URL. |
| `scope` | Požadovaný |Mezery oddělený seznam [oborů](v2-permissions-and-consent.md). Pro OpenID Connect (id_tokens) musí `openid`obsahovat obor , který se překládá k oprávnění "Přihlásit se k vám" v uznaném souhlasu. Volitelně můžete také zahrnout `email` obory a `profile` pro získání přístupu k dalším uživatelským datům. Pokud je požadován přístupový token, můžete do této žádosti zahrnout také další obory žádosti o souhlas s různými prostředky. |
| `response_mode` | optional |Určuje metodu, která by měla být použita k odeslání výsledného tokenu zpět do vaší aplikace. Výchozí dotaz pouze přístupový token, ale fragment, pokud požadavek obsahuje id_token. |
| `state` | Doporučené |Hodnota zahrnutá v požadavku, která bude také vrácena v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně generovaná jedinečná hodnota se obvykle používá k [zabránění útokům padělání požadavků na příčce webu](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, jako je například stránka nebo zobrazení, na které se nachází. |
| `nonce` | Požadovaný |Hodnota zahrnutá v požadavku vygenerovaná aplikací, která bude zahrnuta do výsledné ho id_token jako deklarace. Aplikace pak můžete ověřit tuto hodnotu ke zmírnění útoků přehrání tokenu. Hodnota je obvykle randomizované, jedinečný řetězec, který lze použít k identifikaci původu požadavku. Vyžadováno pouze v případě, že je požadováno id_token. |
| `prompt` | optional |Označuje typ interakce uživatele, která je požadována. Jediné platné hodnoty v tomto okamžiku jsou "login", "none", "select_account" a "consent". `prompt=login`vynutí uživateli zadat své přihlašovací údaje na tuto žádost, negovat single-sign on. `prompt=none`je naopak - zajistí, že uživatel nebude prezentován s žádnou interaktivní výzvou. Pokud požadavek nelze dokončit bezobslužně prostřednictvím jednotného přihlášení, koncový bod platformy identity Microsoftvrátí chybu. `prompt=select_account`odešle uživatele do výběrčího účtu, kde se zobrazí všechny účty zapamatované v relaci. `prompt=consent`spustí dialogové okno souhlasu OAuth po přihlášení uživatele a požádá jej o udělení oprávnění k aplikaci. |
| `login_hint`  |optional |Lze použít k předvyplnění pole uživatelského jména/e-mailové adresy přihlašovací stránky pro uživatele, pokud znáte jeho uživatelské jméno předem. Aplikace často budou používat tento parametr při opětovném ověřování, protože již extrahoval uživatelské jméno z předchozího přihlášení pomocí deklarace. `preferred_username`|
| `domain_hint` | optional |Pokud je zahrnuta, přeskočí proces zjišťování na základě e-mailu, který uživatel prochází na přihlašovací stránce, což vede k mírně efektivnějšímu uživatelskému prostředí. To se běžně používá pro aplikace line of business, které fungují v jednom tenantovi, kde budou poskytovat název domény v rámci daného klienta.  To bude předávat uživatele zprostředkovatele federace pro tohoto klienta.  Všimněte si, že to zabrání hostům v přihlášení do této aplikace.  |

V tomto okamžiku bude uživatel vyzván k zadání svých přihlašovacích údajů a dokončení ověřování. Koncový bod platformy identit microsoft také zajistí, že uživatel má souhlas `scope` s oprávněními uvedenými v parametru dotazu. Pokud uživatel nesouhlasil s **žádným** z těchto oprávnění, požádá jej o souhlas s požadovanými oprávněními. Další informace najdete v [tématu oprávnění, souhlas a víceklientské aplikace](v2-permissions-and-consent.md).

Jakmile uživatel ověří a udělí souhlas, koncový bod platformy identit microsoftu `redirect_uri`vrátí odpověď do vaší `response_mode` aplikace na uvedené , pomocí metody zadané v parametru.

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď `response_mode=fragment` `response_type=id_token+token` pomocí a vypadá takto (s zalomení řádku pro čitelnost):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Popis |
| --- | --- |
| `access_token` |Zahrnuto, pokud `response_type` obsahuje `token`. Přístupový token, který aplikace požadovala. Přístupový token by neměl být dekódován nebo jinak kontrolován, měl by být považován za neprůhledný řetězec. |
| `token_type` |Zahrnuto, pokud `response_type` obsahuje `token`. Bude vždy `Bearer`. |
| `expires_in`|Zahrnuto, pokud `response_type` obsahuje `token`. Označuje počet sekund, po které je token platný pro účely ukládání do mezipaměti. |
| `scope` |Zahrnuto, pokud `response_type` obsahuje `token`. Označuje obory, pro které bude access_token platný. Nemusí obsahovat všechny požadované obory, pokud nebyly použitelné pro uživatele (v případě oborů pouze Azure AD, které jsou požadovány při použití osobního účtu k přihlášení). |
| `id_token` | Podepsaný webový token JSON (JWT). Aplikace může dekódovat segmenty tohoto tokenu a požádat o informace o uživateli, který se přihlásil. Aplikace může ukládat hodnoty do mezipaměti a zobrazovat je, ale neměla by se na ně spoléhat pro žádné hranice autorizace nebo zabezpečení. Další informace o id_tokens [`id_token reference`](id-tokens.md)naleznete v tématu . <br> **Poznámka:** Pouze pokud `openid` byl požadován obor. |
| `state` |Pokud je parametr stavu zahrnut a požadavek, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v požadavku a odpovědi jsou identické. |

#### <a name="error-response"></a>Odpověď na chybu

Odpovědi na chyby mohou `redirect_uri` být také odeslány, aby je aplikace mohla správně zpracovat:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| `error` |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází, a lze jej použít k reakci na chyby. |
| `error_description` |Konkrétní chybová zpráva, která může vývojáři pomoci identifikovat hlavní příčinu chyby ověřování. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Získání přístupových tokenů tiše na pozadí

Teď, když jste uživatele přihlásili do jednostránkové aplikace, můžete tiše získat přístupové tokeny pro volání webových rozhraní API zabezpečených platformou identit microsoftu, jako je například [Microsoft Graph](https://developer.microsoft.com/graph). I v případě, že `token` jste již obdrželi token pomocí response_type, můžete tuto metodu získat tokeny pro další prostředky bez nutnosti přesměrovat uživatele znovu přihlásit.

V normálním toku OpenID Connect/OAuth byste to udělali tak, `/token` že byste požádali koncový bod platformy identit y Microsoft. Koncový bod platformy identit y Microsoft však nepodporuje požadavky CORS, takže volání AJAX získat a aktualizovat tokeny je však vyloučeno. Místo toho můžete použít implicitní tok ve skrytém prvku iframe k získání nových tokenů pro jiná webová api: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Podrobnosti o parametrech dotazu v adrese URL naleznete v [tématu odeslání žádosti o přihlášení](#send-the-sign-in-request).

> [!TIP]
> Zkuste zkopírovat & vložení níže uvedené žádosti do záložky prohlížeče! (Nezapomeňte nahradit `login_hint` hodnoty správnou hodnotou pro uživatele)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Díky parametru `prompt=none` bude tento požadavek buď úspěšné nebo okamžitě nezdaří a vrátit se do aplikace. Úspěšná odpověď bude odeslána do vaší `redirect_uri`aplikace na uvedené `response_mode` , pomocí metody uvedené v parametru.

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď `response_mode=fragment` pomocí vypadá takto:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parametr | Popis |
| --- | --- |
| `access_token` |Zahrnuto, pokud `response_type` obsahuje `token`. Přístupový token, který aplikace požadovala, v tomto případě pro Microsoft Graph. Přístupový token by neměl být dekódován nebo jinak kontrolován, měl by být považován za neprůhledný řetězec. |
| `token_type` | Bude vždy `Bearer`. |
| `expires_in` | Označuje počet sekund, po které je token platný pro účely ukládání do mezipaměti. |
| `scope` | Označuje obory, pro které bude access_token platný. Nemusí obsahovat všechny požadované obory, pokud nebyly použitelné pro uživatele (v případě oborů pouze Azure AD, které jsou požadovány při použití osobního účtu k přihlášení). |
| `id_token` | Podepsaný webový token JSON (JWT). Zahrnuto, pokud `response_type` obsahuje `id_token`. Aplikace může dekódovat segmenty tohoto tokenu a požádat o informace o uživateli, který se přihlásil. Aplikace může ukládat hodnoty do mezipaměti a zobrazovat je, ale neměla by se na ně spoléhat pro žádné hranice autorizace nebo zabezpečení. Další informace o id_tokens [ `id_token` ](id-tokens.md)naleznete v odkazu . <br> **Poznámka:** Pouze pokud `openid` byl požadován obor. |
| `state` |Pokud je parametr stavu zahrnut a požadavek, měla by se v odpovědi zobrazit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v požadavku a odpovědi jsou identické. |

#### <a name="error-response"></a>Odpověď na chybu

Chybové odpovědi mohou být `redirect_uri` také odeslány tak, aby je aplikace mohla správně zpracovat. V případě `prompt=none`, očekávaná chyba bude:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Popis |
| --- | --- |
| `error` |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází, a lze jej použít k reakci na chyby. |
| `error_description` |Konkrétní chybová zpráva, která může vývojáři pomoci identifikovat hlavní příčinu chyby ověřování. |

Pokud se zobrazí tato chyba v požadavku iframe, uživatel musí interaktivně znovu přihlásit načíst nový token. Můžete si vybrat, zda chcete tento případ zpracovat jakýmkoli způsobem, který má smysl pro vaši aplikaci.

## <a name="refreshing-tokens"></a>Osvěžující žetony

Implicitní grant neposkytuje obnovovací tokeny. Platnost `id_token`s `access_token`i s vyprší po krátké době, takže vaše aplikace musí být připravena tyto tokeny pravidelně obnovovat. Chcete-li aktualizovat některý typ tokenu, můžete provést stejný `prompt=none` skrytý požadavek iframe shora pomocí parametru k řízení chování platformy identity. Pokud chcete dostávat `id_token`nový , nezapomeňte `id_token` použít `response_type` `scope=openid`v a , `nonce` stejně jako parametr.

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

OpenID Connect `end_session_endpoint` umožňuje vaší aplikaci odeslat požadavek koncovému bodu platformy identit microsoftu na ukončení relace uživatele a vymazání souborů cookie nastavených koncovým bodem platformy identit Microsoftu. Chcete-li uživatele plně odhlásit z webové aplikace, měla by vaše aplikace ukončit svou vlastní relaci s uživatelem (obvykle vymazáním mezipaměti tokenů nebo zrušením souborů cookie) a poté přesměrovat prohlížeč na:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr |  | Popis |
| --- | --- | --- |
| `tenant` |Požadovaný |Hodnotu `{tenant}` v cestě požadavku lze použít k řízení, kdo se může přihlásit do aplikace. Povolené hodnoty `common`jsou `organizations` `consumers`identifikátory , , a tenant. Další podrobnosti naleznete v [základech protokolu](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Doporučené | Adresa URL, na kterou by měl být uživatel po dokončení odhlášení vrácen. Tato hodnota se musí shodovat s jedním z identifikátorů URI přesměrování registrovaných pro aplikaci. Pokud není zahrnuta, uživatel se zobrazí obecná zpráva koncový bod platformy identity společnosti Microsoft. |

## <a name="next-steps"></a>Další kroky

* Projděte si [ukázky MSAL JS](sample-v2-code.md) a můžete začít s kódováním.
