---
title: Postup implicitního udělení OAuth 2,0 – Microsoft Identity Platform | Azure
description: Používejte k zabezpečení jednostránkovéch aplikací Microsoft Identity Platform implicitní tok.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: f3598c6f072d09d7e427db66dcfbf8721b92a3a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226484"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity Platform a implicitní tok udělení

Platforma Microsoft identity podporuje tok implicitního udělení OAuth 2,0, jak je popsáno ve [specifikaci oauth 2,0](https://tools.ietf.org/html/rfc6749#section-4.2). Definováním charakteristiky implicitního udělení je, že tokeny (ID tokeny nebo přístupové tokeny) jsou vráceny přímo z koncového bodu/Authorize namísto koncového bodu/token. Tato možnost se často používá jako součást [toku autorizačního kódu](v2-oauth2-auth-code-flow.md), v čem se říká "hybridní tok" – načítání tokenu ID na žádost/Authorize spolu s autorizačním kódem.

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>Preferovat tok kódu ověřování

S plány pro [Odebrání souborů cookie třetích stran z prohlížečů](reference-third-party-cookies-spas.md) **již není tok implicitního udělení vhodnou metodou ověřování**.  [Tiché funkce jednotného přihlašování](#getting-access-tokens-silently-in-the-background) implicitního toku nefungují bez souborů cookie třetích stran, což způsobuje, že se aplikace při pokusu o získání nového tokenu přeruší. Důrazně doporučujeme, aby všechny nové aplikace používaly [tok autorizačního kódu](v2-oauth2-auth-code-flow.md) , který teď podporuje jednotlivé stránky místo implicitního toku a že [existující jednostránkové aplikace začnou migrovat i na tok autorizačního kódu](migrate-spa-implicit-to-auth-code.md) .

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Vhodné scénáře pro implicitní udělení OAuth2

Implicitní udělení je spolehlivé jenom pro počáteční, interaktivní část toku přihlašování, kde neexistují [soubory cookie třetích stran](reference-third-party-cookies-spas.md) , které by měly mít vliv na vaši aplikaci. Toto omezení znamená, že byste ho měli použít výhradně jako součást hybridního toku, kde aplikace požaduje kód i token z koncového bodu autorizace. Tím se zajistí, že aplikace získá kód, který se dá uplatnit pro obnovovací token, takže přihlašovací relace vaší aplikace zůstane v průběhu času platná.

## <a name="protocol-diagram"></a>Diagram protokolu

Následující diagram ukazuje, jak celý tok implicitního přihlašování vypadá a níže uvedené části popisují jednotlivé kroky podrobněji.

![Diagram znázorňující implicitní tok přihlašování](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Odeslat žádost o přihlášení

Pokud chcete uživatele zpočátku podepsat do vaší aplikace, můžete odeslat žádost o ověření [OpenID Connect](v2-protocols-oidc.md) a získat `id_token` od platformy Microsoft identity.

> [!IMPORTANT]
> K úspěšnému vyžádání tokenu ID nebo přístupového tokenu musí mít registrace aplikace na stránce [Azure Portal-registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) povolený odpovídající implicitní tok udělení oprávnění, a to tak, že v části **implicitní udělení a hybridní toky** vyberou **tokeny ID** a **přístupové tokeny** . Pokud není povolená, vrátí se `unsupported_response` Chyba: `The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'`

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
| `response_type` | vyžadováno |Musí zahrnovat `id_token` pro přihlášení OpenID Connect. Může taky zahrnovat response_type `token` . Pomocí `token` tohoto odkazu umožníte, aby aplikace přijímala přístupový token hned z autorizačního koncového bodu bez nutnosti vytvořit druhý požadavek na autorizační koncový bod. Použijete-li `token` response_type, `scope` musí parametr obsahovat obor, který určuje, který prostředek má být vydaný token pro (například User. read on Microsoft Graph). Může také obsahovat `code` místo `token` k poskytnutí autorizačního kódu pro použití v [toku autorizačního kódu](v2-oauth2-auth-code-flow.md). Tato id_token + odpověď kódu se někdy označuje jako hybridní tok.  |
| `redirect_uri` | doporučil |Redirect_uri vaší aplikace, ve které vaše aplikace může odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z redirect_uris, který jste zaregistrovali na portálu, s výjimkou musí být zakódovaný URL. |
| `scope` | vyžadováno |Mezerou oddělený seznam [oborů](v2-permissions-and-consent.md). Pro OpenID Connect (id_tokens) musí zahrnovat obor `openid` , který překládá oprávnění "přihlásit se k" v uživatelském rozhraní pro vyjádření souhlasu. Volitelně můžete také zahrnout `email` `profile` obory a pro získání přístupu k dalším uživatelským datům. V této žádosti můžete také zahrnout další obory pro žádosti o souhlas s různými prostředky, pokud je požadován přístupový token. |
| `response_mode` | optional |Určuje metodu, která se má použít k odeslání výsledného tokenu zpátky do vaší aplikace. Ve výchozím nastavení se dotazuje pouze na přístupový token, ale fragment, pokud požadavek obsahuje id_token. |
| `state` | doporučil |Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Náhodně vygenerovaná jedinečná hodnota se obvykle používá k [prevenci útoků proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
| `nonce` | vyžadováno |Hodnota obsažená v požadavku, která se vygenerovala aplikací, která se zahrne do výsledného id_token jako deklarace. Aplikace pak může tuto hodnotu ověřit a zmírnit tak útoky na opakované přehrání tokenů. Hodnota je obvykle náhodný jedinečný řetězec, který lze použít k identifikaci původu žádosti. Požadováno pouze v případě, že je požadováno id_token. |
| `prompt` | optional |Určuje typ interakce uživatele, která je povinná. V tuto chvíli jsou k dispozici pouze platné hodnoty "login," none "," select_account "a" souhlas ". `prompt=login` vynutí, aby uživatel zadal přihlašovací údaje k danému požadavku, přičemž se pro ně použije negace jednotného přihlašování. `prompt=none` je opakem, zajistí, že se uživateli nebude zobrazovat žádná interaktivní výzva. Pokud se žádost nedá v tichém režimu dokončit pomocí jednotného přihlašování, bude platforma Microsoft Identity platformou vracet chybu. `prompt=select_account` pošle uživateli výběr účtu, kde se zobrazí všechny účty, které jsou v relaci zapamatovat. `prompt=consent` aktivuje dialog souhlasu OAuth po přihlášení uživatele a vyzve uživatele, aby aplikaci udělil oprávnění. |
| `login_hint`  |optional |Dá se použít k předvyplnění pole uživatelské jméno a e-mailová adresa uživatele přihlašovací stránky, pokud znáte své uživatelské jméno předem. Aplikace budou často používat tento parametr během opakovaného ověřování, kteří už rozvěděli uživatelské jméno z předchozího přihlášení pomocí `preferred_username` deklarace identity.|
| `domain_hint` | optional |Pokud se tato možnost zahrne, přeskočí proces zjišťování e-mailu, který uživatel prochází na přihlašovací stránce, což vede k poněkud efektivnějšímu uživatelskému prostředí. Tento parametr se běžně používá pro obchodní aplikace, které pracují v jednom tenantovi, kde budou poskytovat název domény v rámci daného tenanta, který uživatele přesměruje do poskytovatele federace pro tohoto tenanta.  Všimněte si, že tato Nápověda brání hostům v přihlašování k této aplikaci a omezuje použití přihlašovacích údajů cloudu, jako je FIDO.  |

V tomto okamžiku se uživateli zobrazí výzva k zadání přihlašovacích údajů a dokončení ověřování. Platforma Microsoft Identity také zajistí, že uživatel souhlasí s oprávněními uvedenými v `scope` parametru dotazu. Pokud uživatel souhlasí s **žádným** z těchto oprávnění, požádá uživatele o souhlas s požadovanými oprávněními. Další informace najdete v tématu [oprávnění, souhlas a víceklientské aplikace](v2-permissions-and-consent.md).

Jakmile se uživatel ověří a udělí souhlas, platforma Microsoftu identity vrátí odpověď do vaší aplikace na uvedené adrese `redirect_uri` pomocí metody uvedené v `response_mode` parametru.

#### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď pomocí `response_mode=fragment` a `response_type=id_token+code` vypadá takto (s konci řádků pro čitelnost):

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Popis |
| --- | --- |
| `code` | Zahrnuto `response_type` , pokud zahrnuje `code` . Toto je autorizační kód vhodný k použití v [toku autorizačního kódu](v2-oauth2-auth-code-flow.md).  |
| `access_token` |Zahrnuto `response_type` , pokud zahrnuje `token` . Přístupový token, který aplikace požadovala Přístupový token by neměl být dekódovat nebo jinak zkontrolován, měl by se považovat za neprůhledný řetězec. |
| `token_type` |Zahrnuto `response_type` , pokud zahrnuje `token` . Bude vždycky `Bearer` . |
| `expires_in`|Zahrnuto `response_type` , pokud zahrnuje `token` . Označuje počet sekund, po který je token platný, pro účely ukládání do mezipaměti. |
| `scope` |Zahrnuto `response_type` , pokud zahrnuje `token` . Určuje rozsahy, pro které bude access_token platná. Nemusí obsahovat všechny požadované obory, pokud se nevztahují na uživatele (v případě, že se pro přihlášení použijí jenom obory Azure AD). |
| `id_token` | Podepsaný JSON Web Token (JWT). Aplikace může dekódovat segmenty tohoto tokenu a vyžádat si informace o uživateli, který se přihlásil. Aplikace může hodnoty ukládat do mezipaměti a zobrazovat je, ale neměla by je spoléhat na jakékoli autorizace nebo hranice zabezpečení. Další informace o id_tokens najdete v tématu [`id_token reference`](id-tokens.md) . <br> **Poznámka:** Zadáno pouze v případě `openid` , že byl požadován a `response_type` zahrnut obor `id_tokens` . |
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

> [!Important]
> Tato část implicitního toku není pravděpodobně funkční pro vaši aplikaci, protože se používá v různých prohlížečích z důvodu [Odebrání souborů cookie třetích stran ve výchozím nastavení](reference-third-party-cookies-spas.md).  I když stále funguje v prohlížečích založených na Chromu, které nejsou v anonymním, vývojáři by měli zvážit použití této části toku. V prohlížečích, které nepodporují soubory cookie třetích stran, se zobrazí chyba s oznámením, že žádní uživatelé nejsou přihlášení, protože soubory cookie relace přihlašovací stránky byly odstraněny prohlížečem. 

Teď, když jste uživatele podepsali do své jednostránkové aplikace, můžete v tichém režimu získat přístupové tokeny pro volání webových rozhraní API zabezpečených platformou Microsoft identity, jako je například [Microsoft Graph](https://developer.microsoft.com/graph). I v případě, že jste už token použili pomocí `token` response_type, můžete pomocí této metody získat tokeny dalších prostředků bez nutnosti přesměrovat uživatele na opětovné přihlášení.

V normálním toku OpenID Connect/OAuth to provedete tak, že vytvoříte žádost na koncový bod Microsoft Identity Platform `/token` . Požadavek můžete vytvořit ve skrytém prvku IFRAME a získat tak nové tokeny pro jiná webová rozhraní API:

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
> Mějte na paměti, že tato akce bude fungovat i v prohlížečích bez podpory souborů cookie třetích stran, protože přímo na panelu prohlížeče zadáváte na rozdíl od jejího otevření v rámci prvku IFRAME. 

Díky `prompt=none` parametru bude tento požadavek buď úspěšný, nebo neúspěšný, a vrátí se do vaší aplikace. Odpověď se odešle do vaší aplikace na základě uvedené `redirect_uri` metody, která používá metodu určenou v `response_mode` parametru.

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

V prohlížečích, které nepodporují soubory cookie třetích stran, dojde k chybě s oznámením, že není přihlášený žádný uživatel. 

## <a name="send-a-sign-out-request"></a>Odeslat žádost o odhlášení

OpenID Connect `end_session_endpoint` umožňuje vaší aplikaci odeslat požadavek na platformu Microsoft identity, aby ukončila relaci uživatele a vymazala soubory cookie nastavené platformou Microsoft identity. Aby bylo možné uživatele z webové aplikace úplně podepsat, měla by vaše aplikace ukončit svou vlastní relaci s uživatelem (obvykle vymazáním mezipaměti tokenů nebo vyřazení souborů cookie) a pak přesměrovat prohlížeč na:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr | Typ | Description |
| --- | --- | --- |
| `tenant` |vyžadováno |`{tenant}`Hodnotu v cestě k požadavku lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou `common` `organizations` `consumers` identifikátory klientů,, a. Další podrobnosti najdete v tématu [základy protokolu](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | doporučil | Adresa URL, na kterou se má uživatel vrátit po odhlášení, se dokončí. Tato hodnota musí odpovídat jednomu z registrovaných identifikátorů URI přesměrování pro aplikaci. Pokud tato součást není zahrnutá, zobrazí se uživateli obecná zpráva platformy Microsoft identity. |

## <a name="next-steps"></a>Další kroky

* Začněte kódováním [ukázek MSAL js](sample-v2-code.md) .
* Zkontrolujte [tok autorizačního kódu](v2-oauth2-auth-code-flow.md) jako novější a lepší alternativu k implicitnímu udělení. 
