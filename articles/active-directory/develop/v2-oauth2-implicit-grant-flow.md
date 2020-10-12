---
title: Protokol OAuth 2,0 – implicitní udělení udělení – Microsoft Identity Platform | Azure
description: Používejte k zabezpečení jednostránkovéch aplikací Microsoft Identity Platform implicitní tok.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: fbe74b62352babf7a1fdd93bf19a6e1475e3f032
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85553586"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity Platform a implicitní tok udělení

Pomocí koncového bodu Microsoft Identity Platform můžete uživatele v rámci jedné stránky podepisovat pomocí osobních i pracovních nebo školních účtů od Microsoftu. Jedna stránka a další JavaScriptové aplikace, které se primárně spouštějí v prohlížeči, čelí několika zajímavým problémům, když se přijdou k ověřování:

* Bezpečnostní charakteristiky těchto aplikací se výrazně liší od tradičních webových aplikací založených na serveru.
* Mnoho autorizačních serverů a zprostředkovatelů identity nepodporuje žádosti CORS.
* Úplné přesměrování prohlížeče stránek od aplikace se stanou obzvlášť invazivní pro činnost koncového uživatele.

Pro tyto aplikace (Ember.js, React.js atd.) platforma Microsoft Identity Platform podporuje tok implicitního udělení OAuth 2,0. Implicitní tok je popsaný ve [specifikaci OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.2). Jeho primární výhodou je, že umožňuje aplikaci získat tokeny z platformy Microsoft Identity Platform, aniž by bylo nutné provést výměnu přihlašovacích údajů back-end serveru. To umožňuje aplikaci přihlašovat uživatele, udržovat relaci a získávat tokeny do jiných webových rozhraní API v kódu JavaScriptu klienta. Při použití implicitního toku konkrétně v případě [zosobnění](https://tools.ietf.org/html/rfc6749#section-10.3) [klienta](https://tools.ietf.org/html/rfc6749#section-10.3) a uživatele je potřeba vzít v úvahu několik důležitých bezpečnostních důvodů.

Tento článek popisuje, jak programovat přímo s protokolem ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny Microsoft Authentication Library (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Podívejte se také na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

Pokud ale nechcete používat knihovnu v rámci jednostránkové aplikace a odesílat zprávy protokolu sami, postupujte podle obecných kroků níže.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Vhodné scénáře pro implicitní udělení OAuth2

Specifikace OAuth2 deklaruje, že implicitní Grant byl vytvořen za účelem povolení aplikací uživatelského agenta – to znamená, že aplikace JavaScriptu spouštěné v prohlížeči. Definováním charakteristických vlastností těchto aplikací je, že kód jazyka JavaScript se používá pro přístup k prostředkům serveru (typicky k webovému rozhraní API) a odpovídajícím způsobem pro aktualizaci uživatelského prostředí aplikace. Můžete si představit aplikace jako Gmail nebo Outlook Web Access: Když vyberete zprávu z doručené pošty, změní se jenom panel vizualizace zprávy, aby se zobrazil nový výběr, zatímco zbytek stránky zůstane beze změny. Tato vlastnost je v kontrastu s tradičními webovými aplikacemi založenými na přesměrování, kde každá interakce uživatele má za následek úplné vystavení stránky a úplné vykreslování nové serverové odpovědi.

Aplikace, které přijímají přístup na základě JavaScriptu na své extrémní, se nazývají jednostránkové aplikace nebo jednostránkové. Nápad je, že tyto aplikace poskytují pouze počáteční stránku HTML a přidružený JavaScript, přičemž všechny následné interakce jsou ovládány voláními webového rozhraní API provedenými prostřednictvím JavaScriptu. Nicméně hybridní přístupy, kde je aplikace většinou řízené postbackem, ale provádí občasné volání JS, nejsou neobvyklá – diskuze o použití implicitního toku je relevantní i pro tyto.

Aplikace založené na přesměrování obvykle zabezpečují své žádosti prostřednictvím souborů cookie, ale tento přístup nefunguje i pro aplikace JavaScriptu. Soubory cookie pracují pouze s doménou, pro kterou byly vygenerovány, zatímco volání JavaScriptu mohou být směrována k ostatním doménám. V takovém případě to často znamená, že aplikace vyvolají rozhraní Microsoft Graph API, rozhraní Office API, rozhraní API Azure – všechny nacházející se mimo doménu, ze kterých se aplikace obsluhuje. Rostoucí trend pro aplikace JavaScriptu nemá žádný back-end. spoléháme se 100% na webová rozhraní API třetích stran a implementujte svou obchodní funkci.

V současné době upřednostňovaná metoda ochrany volání webového rozhraní API je použít přístup k tokenu OAuth2 Bearer, kde každé volání je doprovázeno přístupovým tokenem OAuth2. Webové rozhraní API kontroluje token příchozího přístupu a pokud v něm najde potřebné obory, udělí přístup k požadované operaci. Implicitní tok poskytuje pohodlný mechanismus pro aplikace JavaScriptu k získání přístupových tokenů pro webové rozhraní API, který nabízí mnoho výhod v souvislosti s soubory cookie:

* Tokeny lze spolehlivě získat bez nutnosti pro volání mezi zdroji – povinnou registrací identifikátoru URI přesměrování, na které tokeny vrátí záruky, že tokeny nejsou posunuty.
* Aplikace JavaScriptu můžou získat tolik přístupových tokenů, kolik potřebují, a to u tolika webových rozhraní API, která cílí – bez omezení domén.
* Funkce HTML5, jako je například Session nebo místní úložiště, poskytují úplnou kontrolu nad ukládáním do mezipaměti a správou životnosti tokenů, zatímco Správa souborů cookie je neprůhledná
* Přístupové tokeny nejsou náchylné k útokům prostřednictvím CSRF (mezi lokalitami).

Tok implicitního udělení nevydá aktualizační tokeny, většinou z bezpečnostních důvodů. Obnovovací token není tak Zúžený rozsah jako přístupové tokeny. tím se ještě vyšší výkon, což způsobí mnohem větší škodu pro případ, že dojde k úniku. V implicitním toku jsou tokeny dodány v adrese URL, takže riziko zachycení je vyšší než v udělení autorizačního kódu.

Aplikace JavaScriptu ale má jiný mechanismus pro obnovení přístupových tokenů, aniž by bylo nutné opakovaně vyzvat uživatele k zadání přihlašovacích údajů. Aplikace může pomocí skrytého prvku IFRAME provádět nové žádosti o tokeny proti koncovému bodu autorizace služby Azure AD: Pokud má prohlížeč stále aktivní relaci (čtení: má soubor cookie relace) vůči doméně Azure AD, může k žádosti o ověření dojít úspěšně bez nutnosti zásahu uživatele.

Tento model uděluje aplikaci JavaScriptu možnost nezávisle obnovit přístupové tokeny a dokonce získat nové pro nové rozhraní API (za předpokladu, že ho uživatel dřív poslal). Tím se zabrání zvýšení zátěže, která získává, zachovává a chrání vysoce hodnotový artefakt, jako je aktualizační token. Artefakt, který způsobuje tiché obnovení, je soubor cookie relace služby Azure AD spravován mimo aplikaci. Další výhodou tohoto přístupu je, že se uživatel může odhlásit z Azure AD pomocí kterékoli z aplikací přihlášených do služby Azure AD, které běží na kterékoli z karet prohlížeče. Výsledkem je odstranění souboru cookie relace Azure AD a aplikace JavaScriptu bude automaticky ztratit schopnost obnovit tokeny pro odhlášeného uživatele.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Je implicitní grant vhodný pro moji aplikaci?

Implicitní grant představuje více rizik než jiné dotace a oblasti, které je potřeba věnovat pozornost, jsou dobře zdokumentováné (například [zneužití přístupového tokenu k zosobnění vlastníka prostředku v implicitním toku][OAuth2-Spec-Implicit-Misuse] a v [modelu hrozeb 2,0 OAuth a na zabezpečení][OAuth2-Threat-Model-And-Security-Implications]). Vyšší rizikový profil je však z velké části vzhledem k tomu, že je určeno k povolení aplikací, které spouštějí aktivní kód, obsluhované vzdáleným prostředkem do prohlížeče. Pokud plánujete architekturu SPA, nemáte žádné back-endové součásti ani nechtěli vyvolat webové rozhraní API prostřednictvím JavaScriptu, doporučuje se použít implicitní tok pro získání tokenu.

Pokud je vaše aplikace nativním klientem, implicitní tok se nevejde na skvělé. Neexistence souboru cookie relace Azure AD v kontextu nativního klienta odznamená vaši aplikaci z prostředků údržby dlouhotrvající relace. To znamená, že aplikace bude při získávání přístupových tokenů pro nové prostředky opakovaně vyzvat uživatele.

Pokud vyvíjíte webovou aplikaci, která zahrnuje back-end a spotřebováváte rozhraní API z back-endu kódu, implicitní tok také není vhodný. Další výkon vám poskytne mnohem víc. Například udělení přihlašovacích údajů klienta OAuth2 poskytuje možnost získat tokeny, které odrážejí oprávnění přiřazená samotné aplikaci, a to na rozdíl od delegování uživatelů. To znamená, že klient může spravovat programový přístup k prostředkům i v případě, že uživatel aktivně není v relaci, a tak dále. Nejen to, ale takové granty poskytují vyšší záruky zabezpečení. Přístupové tokeny se nikdy nemigrují přes prohlížeč uživatelů, ale neohrožují uložení v historii prohlížeče atd. Klientská aplikace může také při požadavku na token provádět silné ověřování.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>Diagram protokolu

Následující diagram ukazuje, jak celý tok implicitního přihlašování vypadá a níže uvedené části popisují jednotlivé kroky podrobněji.

![Diagram znázorňující implicitní tok přihlašování](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Pokud chcete uživatele zpočátku podepsat do vaší aplikace, můžete odeslat žádost o ověření [OpenID Connect](v2-protocols-oidc.md) a získat `id_token` od koncového bodu Microsoft Identity Platform.

> [!IMPORTANT]
> K úspěšnému vyžádání tokenu ID nebo přístupového tokenu musí mít registrace aplikace na stránce [Azure Portal-registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) povolený odpovídající implicitní tok udělení oprávnění, a to tak, že v části **implicitního udělení udělí** možnost **tokeny ID** a **tokeny nebo přístupové tokeny** . Pokud není povolená, vrátí se `unsupported_response` Chyba: **zadaná hodnota pro vstupní parametr ' response_type ' není pro tohoto klienta povolena. Očekávaná hodnota je Code.**

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
> Chcete-li otestovat přihlášení pomocí implicitního toku, klikněte na tlačítko <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. .</a> Po přihlášení by měl být v prohlížeči přesměrován na adresu na adresním `https://localhost/myapp/` `id_token` řádku.
>

| Parametr | Typ | Description |
| --- | --- | --- |
| `tenant` | vyžadováno |`{tenant}`Hodnotu v cestě k požadavku lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common` `organizations` `consumers` identifikátory klientů,, a. Další podrobnosti najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints). |
| `client_id` | vyžadováno | ID aplikace (klienta), ke které se stránka [Azure Portal registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) přiřazená vaší aplikaci. |
| `response_type` | vyžadováno |Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Může taky zahrnovat response_type `token` . Pomocí `token` tohoto odkazu umožníte, aby aplikace přijímala přístupový token hned z autorizačního koncového bodu bez nutnosti vytvořit druhý požadavek na autorizační koncový bod. Použijete-li `token` response_type, `scope` musí parametr obsahovat obor, který určuje, který prostředek má být vydaný token pro (například User. read on Microsoft Graph).  |
| `redirect_uri` | doporučil |Redirect_uri vaší aplikace, ve které vaše aplikace může odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z redirect_uris, který jste zaregistrovali na portálu, s výjimkou musí být zakódovaný URL. |
| `scope` | vyžadováno |Mezerou oddělený seznam [oborů](v2-permissions-and-consent.md). Pro OpenID Connect (id_tokens) musí zahrnovat obor `openid` , který překládá oprávnění "přihlásit se k" v uživatelském rozhraní pro vyjádření souhlasu. Volitelně můžete také zahrnout `email` `profile` obory a pro získání přístupu k dalším uživatelským datům. V této žádosti můžete také zahrnout další obory pro žádosti o souhlas s různými prostředky, pokud je požadován přístupový token. |
| `response_mode` | optional |Určuje metodu, která se má použít k odeslání výsledného tokenu zpátky do vaší aplikace. Ve výchozím nastavení se dotazuje pouze na přístupový token, ale fragment, pokud požadavek obsahuje id_token. |
| `state` | doporučil |Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně vygenerovaná jedinečná hodnota se obvykle používá k [prevenci útoků proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
| `nonce` | vyžadováno |Hodnota obsažená v požadavku, která se vygenerovala aplikací, která se zahrne do výsledného id_token jako deklarace. Aplikace pak může tuto hodnotu ověřit a zmírnit tak útoky na opakované přehrání tokenů. Hodnota je obvykle náhodný jedinečný řetězec, který lze použít k identifikaci původu žádosti. Požadováno pouze v případě, že je požadováno id_token. |
| `prompt` | optional |Určuje typ interakce uživatele, která je povinná. V tuto chvíli jsou k dispozici pouze platné hodnoty "login," none "," select_account "a" souhlas ". `prompt=login` vynutí, aby uživatel zadal přihlašovací údaje k danému požadavku, přičemž se pro ně použije negace jednotného přihlašování. `prompt=none` je opakem, zajistí, že se uživateli nebude zobrazovat žádná interaktivní výzva. Pokud se žádost nedá v tichém režimu dokončit pomocí jednotného přihlašování, vrátí koncová platforma Microsoft Identity platformu chybu. `prompt=select_account` pošle uživateli výběr účtu, kde se zobrazí všechny účty, které jsou v relaci zapamatovat. `prompt=consent` aktivuje dialog souhlasu OAuth po přihlášení uživatele a vyzve uživatele, aby aplikaci udělil oprávnění. |
| `login_hint`  |optional |Dá se použít k předvyplnění pole uživatelské jméno a e-mailová adresa uživatele přihlašovací stránky, pokud znáte své uživatelské jméno předem. Aplikace budou často používat tento parametr během opakovaného ověřování, kteří už z předchozího přihlášení extrahovali uživatelské jméno, a to pomocí `preferred_username` deklarace identity.|
| `domain_hint` | optional |Pokud se tato možnost zahrne, přeskočí proces zjišťování e-mailu, který uživatel prochází na přihlašovací stránce, což vede k poněkud efektivnějšímu uživatelskému prostředí. To se běžně používá pro obchodní aplikace, které pracují v jednom tenantovi, kde budou v rámci daného tenanta poskytovat název domény.  Tím se uživatel přepošle poskytovateli federace pro daného tenanta.  Všimněte si, že tato akce zabrání hostům v přihlašování k této aplikaci.  |

V tomto okamžiku se uživateli zobrazí výzva k zadání přihlašovacích údajů a dokončení ověřování. Koncový bod platformy Microsoft Identity také zajistí, že uživatel souhlasí s oprávněními uvedenými v `scope` parametru dotazu. Pokud uživatel souhlasí s **žádným** z těchto oprávnění, požádá uživatele o souhlas s požadovanými oprávněními. Další informace najdete v tématu [oprávnění, souhlas a víceklientské aplikace](v2-permissions-and-consent.md).

Jakmile uživatel ověří a udělí souhlas, koncový bod platformy Microsoft Identity Platform vrátí odpověď do vaší aplikace na uvedené adrese `redirect_uri` pomocí metody zadané v `response_mode` parametru.

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď pomocí `response_mode=fragment` a `response_type=id_token+token` vypadá takto (s konci řádků pro čitelnost):

```HTTP
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Popis |
| --- | --- |
| `access_token` |Zahrnuto `response_type` , pokud zahrnuje `token` . Přístupový token, který aplikace požadovala Přístupový token by neměl být dekódovat nebo jinak zkontrolován, měl by se považovat za neprůhledný řetězec. |
| `token_type` |Zahrnuto `response_type` , pokud zahrnuje `token` . Bude vždycky `Bearer` . |
| `expires_in`|Zahrnuto `response_type` , pokud zahrnuje `token` . Označuje počet sekund, po který je token platný, pro účely ukládání do mezipaměti. |
| `scope` |Zahrnuto `response_type` , pokud zahrnuje `token` . Určuje rozsahy, pro které bude access_token platná. Nemusí obsahovat všechny požadované obory, pokud se nevztahují na uživatele (v případě, že se pro přihlášení použijí jenom obory Azure AD). |
| `id_token` | Podepsaný JSON Web Token (JWT). Aplikace může dekódovat segmenty tohoto tokenu a vyžádat si informace o uživateli, který se přihlásil. Aplikace může hodnoty ukládat do mezipaměti a zobrazovat je, ale neměla by je spoléhat na jakékoli autorizace nebo hranice zabezpečení. Další informace o id_tokens najdete v tématu [`id_token reference`](id-tokens.md) . <br> **Poznámka:** Zadáno pouze v případě `openid` , že byl požadován obor. |
| `state` |Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

#### <a name="error-response"></a>Chybová odezva

Odpovědi na chyby mohou být také odesílány do, `redirect_uri` aby je aplikace mohla správně zpracovat:

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| `error` |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| `error_description` |Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Tiché získání přístupových tokenů na pozadí

Teď, když jste uživatele podepsali do své jednostránkové aplikace, můžete v tichém režimu získat přístupové tokeny pro volání webových rozhraní API zabezpečených platformou Microsoft identity, jako je například [Microsoft Graph](https://developer.microsoft.com/graph). I v případě, že jste už token použili pomocí `token` response_type, můžete pomocí této metody získat tokeny dalších prostředků bez nutnosti přesměrovat uživatele na opětovné přihlášení.

V normálním toku OpenID Connect/OAuth to provedete tak, že vytvoříte žádost na koncový bod Microsoft Identity Platform `/token` . Koncový bod platformy Microsoft Identity však nepodporuje požadavky CORS, takže volání jazyka AJAX pro získání a aktualizaci tokenů je mimo příslušný dotaz. Místo toho můžete použít implicitní tok ve skrytém prvku IFRAME a získat nové tokeny pro jiná webová rozhraní API:

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

Podrobnosti o parametrech dotazu v adrese URL najdete v tématu [odeslání žádosti o přihlášení](#send-the-sign-in-request).

> [!TIP]
> Zkuste zkopírovat níže uvedený požadavek na kartu prohlížeče, &. (Nezapomeňte nahradit `login_hint` hodnoty správnou hodnotou pro uživatele)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Díky `prompt=none` parametru bude tento požadavek buď úspěšný, nebo neúspěšný, a vrátí se do vaší aplikace. Do vaší aplikace se pošle úspěšná odpověď `redirect_uri` pomocí metody zadané v `response_mode` parametru.

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď pomocí `response_mode=fragment` vypadá takto:

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parametr | Popis |
| --- | --- |
| `access_token` |Zahrnuto `response_type` , pokud zahrnuje `token` . Přístupový token, který aplikace požádala, v tomto případě pro Microsoft Graph. Přístupový token by neměl být dekódovat nebo jinak zkontrolován, měl by se považovat za neprůhledný řetězec. |
| `token_type` | Bude vždycky `Bearer` . |
| `expires_in` | Označuje počet sekund, po který je token platný, pro účely ukládání do mezipaměti. |
| `scope` | Určuje rozsahy, pro které bude access_token platná. Nemusí obsahovat všechny požadované obory, pokud se nevztahují na uživatele (v případě, že se pro přihlášení použijí jenom obory Azure AD). |
| `id_token` | Podepsaný JSON Web Token (JWT). Zahrnuto `response_type` , pokud zahrnuje `id_token` . Aplikace může dekódovat segmenty tohoto tokenu a vyžádat si informace o uživateli, který se přihlásil. Aplikace může hodnoty ukládat do mezipaměti a zobrazovat je, ale neměla by je spoléhat na jakékoli autorizace nebo hranice zabezpečení. Další informace o id_tokens najdete v [ `id_token` referenčních](id-tokens.md)informacích. <br> **Poznámka:** Zadáno pouze v případě `openid` , že byl požadován obor. |
| `state` |Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

#### <a name="error-response"></a>Chybová odezva

Odpovědi na chyby mohou být také odeslány do `redirect_uri` aplikace, aby je mohl odpovídajícím způsobem zpracovat. V případě `prompt=none` bude očekávaná chyba:

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Popis |
| --- | --- |
| `error` |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| `error_description` |Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování. |

Pokud se tato chyba zobrazí v žádosti IFRAME, uživatel se musí znovu interaktivně přihlásit, aby získal nový token. Můžete se rozhodnout, že tento případ povedete jakýmkoli způsobem, který je pro vaši aplikaci smysluplný.

## <a name="refreshing-tokens"></a>Aktualizace tokenů

Implicitní udělení neposkytuje aktualizační tokeny. `id_token` `access_token` Po krátké době vyprší platnost s i s, takže vaše aplikace musí být připravené aktualizovat tyto tokeny pravidelně. Pokud chcete aktualizovat libovolný typ tokenu, můžete pomocí `prompt=none` parametru pro kontrolu chování platformy identity použít stejný skrytý požadavek IFRAME. Pokud chcete získat nový `id_token` , je nutné použít `id_token` v `response_type` a i `scope=openid` `nonce` parametr.

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

OpenID Connect `end_session_endpoint` umožňuje vaší aplikaci odeslat žádost do koncového bodu Microsoft Identity Platform, aby ukončila relaci uživatele a vymazala soubory cookie nastavené koncovým bodem Microsoft Identity Platform. Aby bylo možné uživatele z webové aplikace úplně podepsat, měla by vaše aplikace ukončit svou vlastní relaci s uživatelem (obvykle vymazáním mezipaměti tokenů nebo vyřazení souborů cookie) a pak přesměrovat prohlížeč na:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr | Typ | Description |
| --- | --- | --- |
| `tenant` |vyžadováno |`{tenant}`Hodnotu v cestě k požadavku lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common` `organizations` `consumers` identifikátory klientů,, a. Další podrobnosti najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | doporučil | Adresa URL, na kterou se má uživatel vrátit po odhlášení, se dokončí. Tato hodnota musí odpovídat jednomu z registrovaných identifikátorů URI přesměrování pro aplikaci. Pokud tato akce není zahrnutá, zobrazí se uživateli obecná zpráva od koncového bodu Microsoft Identity Platform. |

## <a name="next-steps"></a>Další kroky

* Začněte kódováním [ukázek MSAL js](sample-v2-code.md) .

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
