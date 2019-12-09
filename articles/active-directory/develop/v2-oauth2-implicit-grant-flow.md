---
title: Zabezpečení jednostránkovéch aplikací pomocí implicitního toku Microsoft Identity Platform | Azure
description: Vytváření webových aplikací s využitím implementace implicitního toku pro jednostránkové aplikace Microsoft Identity Platform
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8afae9535c190c05bca3153dfbe5279cd4c47968
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919218"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity Platform a implicitní tok udělení

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Pomocí koncového bodu Microsoft Identity Platform můžete uživatele v rámci jedné stránky podepisovat pomocí osobních i pracovních nebo školních účtů od Microsoftu. Jedna stránka a další JavaScriptové aplikace, které se primárně spouštějí v prohlížeči, čelí několika zajímavým problémům, když se přijdou k ověřování:

* Bezpečnostní charakteristiky těchto aplikací se výrazně liší od tradičních webových aplikací založených na serveru.
* Mnoho autorizačních serverů a zprostředkovatelů identity nepodporuje žádosti CORS.
* Úplné přesměrování prohlížeče stránek od aplikace se stanou obzvlášť invazivní pro činnost koncového uživatele.

Pro tyto aplikace (AngularJS, života. js, reagovat. js atd.), Microsoft Identity Platform podporuje tok implicitního udělení OAuth 2,0. Implicitní tok je popsaný ve [specifikaci OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.2). Jeho primární výhodou je, že umožňuje aplikaci získat tokeny z platformy Microsoft Identity Platform, aniž by bylo nutné provést výměnu přihlašovacích údajů back-end serveru. To umožňuje aplikaci přihlašovat uživatele, udržovat relaci a získávat tokeny do jiných webových rozhraní API v kódu JavaScriptu klienta. Při použití implicitního toku konkrétně v případě [zosobnění](https://tools.ietf.org/html/rfc6749#section-10.3) [klienta](https://tools.ietf.org/html/rfc6749#section-10.3) a uživatele je potřeba vzít v úvahu několik důležitých bezpečnostních důvodů.

Tento článek popisuje, jak programovat přímo s protokolem ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny Microsoft Authentication Library (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Podívejte se také na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

Pokud ale nechcete používat knihovnu v rámci jednostránkové aplikace a odesílat zprávy protokolu sami, postupujte podle obecných kroků níže.

> [!NOTE]
> V rámci koncového bodu Microsoft Identity Platform nejsou podporovány všechny scénáře a funkce Azure Active Directory (Azure AD). Pokud chcete zjistit, jestli byste měli použít koncový bod platformy Microsoft identity, přečtěte si informace o [omezeních platformy Microsoft Identity](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagram protokolu

Následující diagram ukazuje, jak celý tok implicitního přihlašování vypadá a níže uvedené části popisují jednotlivé kroky podrobněji.

![Diagram znázorňující implicitní tok přihlašování](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Pokud chcete uživatele zpočátku podepsat do vaší aplikace, můžete odeslat žádost o ověření [OpenID Connect](v2-protocols-oidc.md) a získat `id_token` z koncového bodu Microsoft Identity Platform.

> [!IMPORTANT]
> K úspěšnému vyžádání tokenu ID nebo přístupového tokenu musí mít registrace aplikace na stránce [Azure Portal-registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) povolený odpovídající implicitní tok udělení oprávnění, a to tak, že v části **implicitního udělení udělí** možnost **tokeny ID** a **tokeny nebo přístupové tokeny** . Pokud není povolena, bude vrácena `unsupported_response` Chyba: **zadaná hodnota pro vstupní parametr ' response_type ' není pro tohoto klienta povolena. Očekávaná hodnota je Code.**

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
> Chcete-li otestovat přihlášení pomocí implicitního toku, klikněte na tlačítko <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Po přihlášení by měl být váš prohlížeč přesměrován na `https://localhost/myapp/` s `id_token` na adresním řádku.
>

| Parametr |  | Popis |
| --- | --- | --- |
| `tenant` | required |Hodnotu `{tenant}` v cestě k žádosti lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů. Další podrobnosti najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints). |
| `client_id` | required | ID aplikace (klienta), ke které se stránka [Azure Portal registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) přiřazená vaší aplikaci. |
| `response_type` | required |Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Může taky zahrnovat response_type `token`. Když použijete `token`, umožníte aplikaci získat přístupový token hned z autorizačního koncového bodu, aniž byste museli udělat druhý požadavek na autorizační koncový bod. Použijete-li response_type `token`, musí parametr `scope` obsahovat obor, který určuje, který prostředek má být vydaný token (například User. Read on Microsoft Graph).  |
| `redirect_uri` | Doporučené |Redirect_uri vaší aplikace, ve které vaše aplikace může odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z redirect_uris, který jste zaregistrovali na portálu, s výjimkou musí být zakódovaný URL. |
| `scope` | required |Mezerou oddělený seznam [oborů](v2-permissions-and-consent.md). Pro OpenID Connect (id_tokens) musí zahrnovat obor `openid`, který se v uživatelském rozhraní souhlasu překládá na oprávnění přihlásit se. Volitelně můžete také zahrnout rozsahy `email` a `profile` pro získání přístupu k dalším uživatelským datům. V této žádosti můžete také zahrnout další obory pro žádosti o souhlas s různými prostředky, pokud je požadován přístupový token. |
| `response_mode` | volitelné |Určuje metodu, která se má použít k odeslání výsledného tokenu zpátky do vaší aplikace. Ve výchozím nastavení se dotazuje pouze na přístupový token, ale fragment, pokud požadavek obsahuje id_token. |
| `state` | Doporučené |Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně vygenerovaná jedinečná hodnota se obvykle používá k [prevenci útoků proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
| `nonce` | required |Hodnota obsažená v požadavku, která se vygenerovala aplikací, která se zahrne do výsledného id_token jako deklarace. Aplikace pak může tuto hodnotu ověřit a zmírnit tak útoky na opakované přehrání tokenů. Hodnota je obvykle náhodný jedinečný řetězec, který lze použít k identifikaci původu žádosti. Požadováno pouze v případě, že je požadováno id_token. |
| `prompt` | volitelné |Určuje typ interakce uživatele, která je povinná. V tuto chvíli jsou k dispozici pouze platné hodnoty "login," none "," select_account "a" souhlas ". `prompt=login` vynutí, aby uživatel zadal přihlašovací údaje k danému požadavku, přičemž se pro ně použije negace jednotného přihlašování. `prompt=none` je opak, zajistí, že se uživateli nebude zobrazovat žádná interaktivní výzva. Pokud se žádost nedá v tichém režimu dokončit pomocí jednotného přihlašování, vrátí koncová platforma Microsoft Identity platformu chybu. `prompt=select_account` odešle uživateli na výběr účtu, kde se zobrazí všechny účty, které jsou v relaci zapamatovat. `prompt=consent` spustí dialog pro vyjádření souhlasu OAuth po přihlášení uživatele a požádá uživatele, aby aplikaci udělil oprávnění. |
| `login_hint`  |volitelné |Dá se použít k předvyplnění pole uživatelské jméno a e-mailová adresa uživatele přihlašovací stránky, pokud znáte své uživatelské jméno předem. Aplikace budou často používat tento parametr během opakovaného ověřování, kteří již rozvěděli uživatelské jméno z předchozího přihlášení pomocí `preferred_username` deklarace identity.|
| `domain_hint` | volitelné |Pokud se tato možnost zahrne, přeskočí proces zjišťování e-mailu, který uživatel prochází na přihlašovací stránce, což vede k poněkud efektivnějšímu uživatelskému prostředí. To se běžně používá pro obchodní aplikace, které pracují v jednom tenantovi, kde budou v rámci daného tenanta poskytovat název domény.  Tím se uživatel přepošle poskytovateli federace pro daného tenanta.  Všimněte si, že tato akce zabrání hostům v přihlašování k této aplikaci.  |

V tomto okamžiku se uživateli zobrazí výzva k zadání přihlašovacích údajů a dokončení ověřování. Koncový bod platformy Microsoft Identity také zajistí, že uživatel souhlasí s oprávněními uvedenými v parametru `scope` dotazu. Pokud uživatel souhlasí s **žádným** z těchto oprávnění, požádá uživatele o souhlas s požadovanými oprávněními. Další informace najdete v tématu [oprávnění, souhlas a víceklientské aplikace](v2-permissions-and-consent.md).

Jakmile se uživatel ověří a udělí souhlas, koncový bod platformy Microsoft Identity vrátí odpověď do vaší aplikace na uvedené `redirect_uri`pomocí metody zadané v parametru `response_mode`.

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď pomocí `response_mode=fragment` a `response_type=id_token+token` vypadá takto: (s konci řádků pro čitelnost):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Popis |
| --- | --- |
| `access_token` |Zahrnuto, pokud `response_type` zahrnuje `token`. Přístupový token, který aplikace požadovala Přístupový token by neměl být dekódovat nebo jinak zkontrolován, měl by se považovat za neprůhledný řetězec. |
| `token_type` |Zahrnuto, pokud `response_type` zahrnuje `token`. Bude vždycky `Bearer`. |
| `expires_in`|Zahrnuto, pokud `response_type` zahrnuje `token`. Označuje počet sekund, po který je token platný, pro účely ukládání do mezipaměti. |
| `scope` |Zahrnuto, pokud `response_type` zahrnuje `token`. Určuje rozsahy, pro které bude access_token platná. Nemusí obsahovat všechny požadované obory, pokud se nevztahují na uživatele (v případě, že se pro přihlášení použijí jenom obory Azure AD). |
| `id_token` | Podepsaný JSON Web Token (JWT). Aplikace může dekódovat segmenty tohoto tokenu a vyžádat si informace o uživateli, který se přihlásil. Aplikace může hodnoty ukládat do mezipaměti a zobrazovat je, ale neměla by je spoléhat na jakékoli autorizace nebo hranice zabezpečení. Další informace o id_tokens najdete v [`id_token reference`](id-tokens.md). <br> **Poznámka:** Zadáno pouze v případě, že byl požadován obor `openid`. |
| `state` |Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

#### <a name="error-response"></a>Chybová odezva

Do `redirect_uri` lze také odeslat odpovědi na chyby, aby je aplikace mohla správně zpracovat:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| `error` |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| `error_description` |Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Tiché získání přístupových tokenů na pozadí

Teď, když jste uživatele podepsali do své jednostránkové aplikace, můžete v tichém režimu získat přístupové tokeny pro volání webových rozhraní API zabezpečených platformou Microsoft identity, jako je například [Microsoft Graph](https://developer.microsoft.com/graph). I v případě, že jste už token získali pomocí response_type `token`, můžete pomocí této metody získat tokeny k dalším prostředkům bez nutnosti přesměrovat uživatele, aby se znovu přihlásil.

V normálním toku OpenID Connect/OAuth to provedete tak, že vytvoříte požadavek na koncový bod Microsoft Identity Platform `/token`. Koncový bod platformy Microsoft Identity však nepodporuje požadavky CORS, takže volání jazyka AJAX pro získání a aktualizaci tokenů je mimo příslušný dotaz. Místo toho můžete použít implicitní tok ve skrytém prvku IFRAME a získat nové tokeny pro jiná webová rozhraní API: 

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
> Zkuste zkopírovat níže uvedený požadavek na kartu prohlížeče, &. (Nezapomeňte nahradit hodnoty `login_hint` správnou hodnotou pro uživatele)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Díky parametru `prompt=none` bude tato žádost buď úspěšná, nebo selže okamžitě a vrátí se do aplikace. Do vaší aplikace se pošle úspěšná odpověď na uvedené `redirect_uri`s použitím metody zadané v parametru `response_mode`.

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď pomocí `response_mode=fragment` vypadá takto:

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
| `access_token` |Zahrnuto, pokud `response_type` zahrnuje `token`. Přístupový token, který aplikace požádala, v tomto případě pro Microsoft Graph. Přístupový token by neměl být dekódovat nebo jinak zkontrolován, měl by se považovat za neprůhledný řetězec. |
| `token_type` | Bude vždycky `Bearer`. |
| `expires_in` | Označuje počet sekund, po který je token platný, pro účely ukládání do mezipaměti. |
| `scope` | Určuje rozsahy, pro které bude access_token platná. Nemusí obsahovat všechny požadované obory, pokud se nevztahují na uživatele (v případě, že se pro přihlášení použijí jenom obory Azure AD). |
| `id_token` | Podepsaný JSON Web Token (JWT). Zahrnuto, pokud `response_type` zahrnuje `id_token`. Aplikace může dekódovat segmenty tohoto tokenu a vyžádat si informace o uživateli, který se přihlásil. Aplikace může hodnoty ukládat do mezipaměti a zobrazovat je, ale neměla by je spoléhat na jakékoli autorizace nebo hranice zabezpečení. Další informace o id_tokens najdete v [referenčních](id-tokens.md)informacích k`id_token`. <br> **Poznámka:** Zadáno pouze v případě, že byl požadován obor `openid`. |
| `state` |Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Aplikace by měla ověřit, že hodnoty stavu v žádosti a odpovědi jsou identické. |

#### <a name="error-response"></a>Chybová odezva

Do `redirect_uri` lze také odeslat odpovědi na chyby, aby je aplikace mohla vhodně zpracovat. V případě `prompt=none`bude očekávaná chyba:

```
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

Implicitní udělení neposkytuje aktualizační tokeny. Po krátké době vyprší platnost `id_token`s i `access_token`s, takže aplikace musí být připravená aktualizovat tyto tokeny pravidelně. Pokud chcete aktualizovat libovolný typ tokenu, můžete pomocí parametru `prompt=none` použít pro řízení chování platformy identity stejný skrytý požadavek IFRAME. Pokud chcete dostávat novou `id_token`, nezapomeňte použít `id_token` v `response_type` a `scope=openid`a také na `nonce` parametr.

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

OpenID Connect `end_session_endpoint` umožňuje vaší aplikaci odeslat žádost do koncového bodu Microsoft Identity Platform, aby ukončila relaci uživatele a vymazala soubory cookie nastavené koncovým bodem platformy Microsoft identity. Aby bylo možné uživatele z webové aplikace úplně podepsat, měla by vaše aplikace ukončit svou vlastní relaci s uživatelem (obvykle vymazáním mezipaměti tokenů nebo vyřazení souborů cookie) a pak přesměrovat prohlížeč na:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr |  | Popis |
| --- | --- | --- |
| `tenant` |required |Hodnotu `{tenant}` v cestě k žádosti lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common`, `organizations`, `consumers`a identifikátory klientů. Další podrobnosti najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Doporučené | Adresa URL, na kterou se má uživatel vrátit po odhlášení, se dokončí. Tato hodnota musí odpovídat jednomu z registrovaných identifikátorů URI přesměrování pro aplikaci. Pokud tato akce není zahrnutá, zobrazí se uživateli obecná zpráva od koncového bodu Microsoft Identity Platform. |

## <a name="next-steps"></a>Další kroky

* Začněte kódováním [ukázek MSAL js](sample-v2-code.md) .
