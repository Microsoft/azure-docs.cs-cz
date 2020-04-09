---
title: Tok pověření klienta OAuth 2.0 na platformě identit microsoftu | Azure
description: Vytvářejte webové aplikace pomocí implementace platformy microsoftu pro identity ověřovacího protokolu OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a03f8cb412b6d6ae95165331ae836bdfde5d670d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886292"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Platforma identit microsoftu a tok pověření klienta OAuth 2.0

Můžete použít [odejmuta pověření klienta OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.4) zadaný v RFC 6749, někdy nazývaný *dvounohý OAuth*, pro přístup k webovým hostovaným prostředkům pomocí identity aplikace. Tento typ grantu se běžně používá pro interakce mezi servery, které musí být spuštěny na pozadí, bez okamžité interakce s uživatelem. Tyto typy aplikací jsou často označovány jako *daemons* nebo *účty služeb*.

Tento článek popisuje, jak programovat přímo proti protokolu ve vaší aplikaci. Pokud je to možné, doporučujeme místo toho použít podporované knihovny ověřování společnosti Microsoft (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Také se podívejte na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

Pověření klienta OAuth 2.0 udělit tok umožňuje webové službě (důvěrný klient) používat vlastní pověření, namísto zosobnění uživatele, k ověření při volání jiné webové služby. V tomto scénáři klient je obvykle webové služby střední vrstvy, daemon služby nebo webové stránky. Pro vyšší úroveň záruky platforma identit microsoftu také umožňuje volající službě používat certifikát (namísto sdíleného tajného klíče) jako pověření.

> [!NOTE]
> Koncový bod platformy identit Microsoftu nepodporuje všechny scénáře a funkce Azure AD. Chcete-li zjistit, zda byste měli používat koncový bod platformy identit společnosti Microsoft, přečtěte si o [omezení platformy identit společnosti Microsoft](active-directory-v2-limitations.md).

V typičtější *třínohý OAuth*klientská aplikace je uděleno oprávnění k přístupu k prostředku jménem konkrétního uživatele. Oprávnění je delegováno uživatelem do aplikace, obvykle během procesu [souhlasu.](v2-permissions-and-consent.md) Však v klientské pověření (*dvounohý OAuth*) toku, oprávnění jsou udělena přímo do samotné aplikace. Když aplikace představuje token pro prostředek, prostředek vynucuje, že samotná aplikace má oprávnění k provedení akce a ne uživatele.

## <a name="protocol-diagram"></a>Protokolový diagram

Celý tok pověření klienta vypadá podobně jako v následujícím diagramu. Popisujeme každý z kroků dále v tomto článku.

![Diagram znázorňující tok pověření klienta](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Získejte přímou autorizaci

Aplikace obvykle získává přímou autorizaci pro přístup k prostředku jedním ze dvou způsobů:

* [Prostřednictvím seznamu řízení přístupu (ACL) v prostředku](#access-control-lists)
* [Prostřednictvím přiřazení oprávnění aplikace ve službě Azure AD](#application-permissions)

Tyto dvě metody jsou nejběžnější ve službě Azure AD a doporučujeme je pro klienty a prostředky, které provádějí tok pověření klienta. Prostředek může také autorizovat své klienty jinými způsoby. Každý server prostředků můžete zvolit metodu, která dává největší smysl pro jeho aplikaci.

### <a name="access-control-lists"></a>Seznamy řízení přístupu

Poskytovatel prostředků může vynutit kontrolu autorizace na základě seznamu ID aplikací (klienta), které zná, a uděluje určitou úroveň přístupu. Když prostředek obdrží token z koncového bodu platformy identity Microsoft, může dekódovat token a `appid` `iss` extrahovat ID aplikace klienta z deklarací identity a. Potom porovná aplikaci proti seznamu řízení přístupu (ACL), který udržuje. Rozlišovací schopnost a metoda acl se může značně lišit mezi prostředky.

Běžným případem použití je použití acl ke spuštění testů pro webovou aplikaci nebo pro webové rozhraní API. Webové rozhraní API může udělit pouze podmnožinu úplných oprávnění konkrétnímu klientovi. Chcete-li spustit end-to-end testy v rozhraní API, vytvořte testovacího klienta, který získává tokeny z koncového bodu platformy identity Microsoftu a pak je odešle do rozhraní API. Rozhraní API pak zkontroluje acl pro id aplikace testovacího klienta pro úplný přístup k celé funkce rozhraní API. Pokud používáte tento druh seznamu ACL, nezapomeňte ověřit `appid` nejen hodnotu volajícího, ale také ověřit, zda je `iss` hodnota tokenu důvěryhodná.

Tento typ autorizace je běžný u daemonů a účtů služeb, které potřebují přístup k datům vlastněným uživateli, kteří mají osobní účty Microsoft. U dat vlastněných organizacemi doporučujeme získat potřebnou autorizaci prostřednictvím oprávnění aplikací.

### <a name="application-permissions"></a>Oprávnění aplikace

Místo použití seznamu ACs můžete pomocí souborů API vystavit sadu **oprávnění aplikace**. Oprávnění aplikace je uděleno aplikaci správcem organizace a lze jej použít pouze pro přístup k datům vlastněným této organizací a jejími zaměstnanci. Například Microsoft Graph zveřejňuje několik oprávnění aplikace k provedení následujících akcí:

* Čtení pošty ve všech poštovních schránkách
* Čtení a zápis pošty ve všech poštovních schránkách
* Odeslat poštu jako kterýkoli uživatel
* Čtení dat z adresáře

Další informace o oprávněních aplikací naleznete v aplikaci [Microsoft Graph](https://developer.microsoft.com/graph).

Pokud chcete ve své aplikaci používat oprávnění k aplikacím, postupujte podle kroků popsaných v dalších částech.


> [!NOTE]
> Při ověřování jako aplikace, na rozdíl od uživatele, nelze použít "delegovaná oprávnění" (obory, které jsou uděleny uživatelem).  Musíte použít "oprávnění aplikace", označované také jako "role", které jsou uděleny správcem pro aplikaci (nebo prostřednictvím předběžné autorizace webového rozhraní API).    


#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Vyžádání oprávnění na portálu pro registraci aplikací

1. Zaregistrujte a vytvořte aplikaci prostřednictvím nového [prostředí registrace aplikací (Preview).](quickstart-register-app.md)
2. V prostředí registrace aplikací (preview) přejděte do aplikace. Přejděte do oddílu **Certifikáty & tajných kódů** a přidejte **nový tajný klíč klienta**, protože k vyžádání tokenu budete potřebovat alespoň jeden tajný klíč klienta.
3. Vyhledejte oddíl **Oprávnění rozhraní API** a přidejte oprávnění **aplikace,** která vaše aplikace vyžaduje.
4. **Uložte** registraci aplikace.

#### <a name="recommended-sign-the-user-into-your-app"></a>Doporučené: Přihlášení uživatele do aplikace

Obvykle při vytváření aplikace, která používá oprávnění aplikace, aplikace vyžaduje stránku nebo zobrazení, na kterém správce schvaluje oprávnění aplikace. Tato stránka může být součástí toku přihlášení aplikace, součástí nastavení aplikace, nebo to může být vyhrazený tok "připojení". V mnoha případech má smysl, aby aplikace zobrazila toto zobrazení "připojení" až poté, co se uživatel přihlásí pomocí pracovního nebo školního účtu Microsoft.

Pokud uživatele přihlásíte do aplikace, můžete určit organizaci, do které uživatel patří, než ho požádáte o schválení oprávnění aplikace. I když to není nezbytně nutné, může vám pomoci vytvořit intuitivnější prostředí pro vaše uživatele. Chcete-li uživatele přihlásit, postupujte podle [našich výukových programů protokolu platformy identit společnosti Microsoft](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Vyžádání oprávnění od správce adresáře

Až budete připraveni požádat o oprávnění od správce organizace, můžete uživatele přesměrovat na *koncový bod souhlasu správce*platformy identit Microsoftu .

> [!TIP]
> Zkuste provést tento požadavek v Pošťák! (Pro dosažení nejlepších výsledků použijte vlastní ID aplikace – výukové aplikace nebude vyžadovat užitečná oprávnění.) [Zkuste spustit tento požadavek v Pošťáku. ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Klient adresáře, od kterého chcete požádat o oprávnění. To může být ve formátu GUID nebo popisný název. Pokud nevíte, kterému tenantovi uživatel patří, a chcete, aby se `common`přihlašovali pomocí libovolného klienta, použijte . |
| `client_id` | Požaduje se | **ID aplikace (klienta),** které je k vaší aplikaci přiřazené [na portálu Azure – možnosti registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `redirect_uri` | Požaduje se | Identifikátor URI přesměrování, kam chcete odeslat odpověď, aby ji vaše aplikace zpracovat. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódován adresou URL a může mít další segmenty cesty. |
| `state` | Doporučené | Hodnota, která je zahrnuta v požadavku, který je také vrácena v odpovědi tokenu. Může se jedná o řetězec libovolného obsahu, který chcete. Stav se používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, jako je například stránka nebo zobrazení, na které se nachází. |

V tomto okamžiku Azure AD vynucuje, že pouze správce klienta můžete přihlásit k dokončení požadavku. Správce bude požádán o schválení všech přímých oprávnění aplikace, která jste pro aplikaci požadovali na portálu pro registraci aplikací.

##### <a name="successful-response"></a>Úspěšná odpověď

Pokud správce schválí oprávnění pro vaši aplikaci, úspěšná odpověď vypadá takto:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Popis |
| --- | --- |
| `tenant` | Klient adresáře, který vaší aplikaci udělil požadovaná oprávnění ve formátu GUID. |
| `state` | Hodnota, která je zahrnuta v požadavku, který je také vrácenv odpovědi tokenu. Může se jedná o řetězec libovolného obsahu, který chcete. Stav se používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, jako je například stránka nebo zobrazení, na které se nachází. |
| `admin_consent` | Nastaveno na **hodnotu True**. |

##### <a name="error-response"></a>Odpověď na chybu

Pokud správce neschválí oprávnění pro vaši aplikaci, neúspěšná odpověď vypadá takto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb a který můžete použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která vám může pomoci identifikovat hlavní příčinu chyby. |

Poté, co jste obdrželi úspěšnou odpověď z koncového bodu zřizování aplikace, vaše aplikace získala přímá oprávnění aplikace, která požadovala. Nyní můžete požádat o token pro prostředek, který chcete.

## <a name="get-a-token"></a>Získání žetonu

Po získání potřebné autorizace pro vaši aplikaci pokračujte v získávání přístupových tokenů pro api. Chcete-li získat token pomocí udělení pověření klienta, `/token` odešlete požadavek POST koncovému bodu platformy identity Společnosti Microsoft:

> [!TIP]
> Zkuste provést tento požadavek v Pošťák! (Pro dosažení nejlepších výsledků použijte vlastní ID aplikace – výukové aplikace nebude vyžadovat užitečná oprávnění.) [Zkuste spustit tento požadavek v Pošťáku. ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První případ: Žádost o přístupový token se sdíleným tajným tajemstvím

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
// Replace {tenant} with your tenant! 
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Klient adresáře, se kterýma aplikace plánuje pracovat, ve formátu GUID nebo názvu domény. |
| `client_id` | Požaduje se | ID aplikace, které je přiřazené k vaší aplikaci. Tyto informace najdete na portálu, kde jste aplikaci zaregistrovali. |
| `scope` | Požaduje se | Hodnota předaná `scope` parametru v tomto požadavku by měla být identifikátor prostředku (identifikátor URI `.default` aplikace) požadovaného prostředku, který je opatřen příponou. V příkladu aplikace Microsoft `https://graph.microsoft.com/.default`Graph je hodnota . <br/>Tato hodnota říká koncovému bodu platformy identity Microsoftu, že u všech přímých oprávnění aplikací, která jste pro vaši aplikaci nakonfigurovali, by měl koncový bod vydat token pro ty, které jsou přidruženy k prostředku, který chcete použít. Další informace o `/.default` oboru naleznete v dokumentaci k [souhlasu](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Požaduje se | Tajný klíč klienta, který jste vygenerovali pro vaši aplikaci na portálu pro registraci aplikací. Tajný klíč klienta musí být před odesláním kódován adresou URL. |
| `grant_type` | Požaduje se | Musí být `client_credentials`nastavena na . |

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: Žádost o přístupový token s certifikátem

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Klient adresáře, se kterýma aplikace plánuje pracovat, ve formátu GUID nebo názvu domény. |
| `client_id` | Požaduje se |ID aplikace (klienta), které je přiřazené k vaší aplikaci. |
| `scope` | Požaduje se | Hodnota předaná `scope` parametru v tomto požadavku by měla být identifikátor prostředku (identifikátor URI `.default` aplikace) požadovaného prostředku, který je opatřen příponou. V příkladu aplikace Microsoft `https://graph.microsoft.com/.default`Graph je hodnota . <br/>Tato hodnota informuje koncový bod platformy identit microsoftu, že ze všech přímých oprávnění aplikací, které jste nakonfigurovali pro vaši aplikaci, by měla vydat token pro ty, které jsou přidruženy k prostředku, který chcete použít. Další informace o `/.default` oboru naleznete v dokumentaci k [souhlasu](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Požaduje se | Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`nastavena na . |
| `client_assertion` | Požaduje se | Kontrolní výraz (webový token JSON), který potřebujete vytvořit a podepsat pomocí certifikátu, který jste zaregistrovali jako pověření pro vaši aplikaci. Přečtěte si o [pověření certifikátu,](active-directory-certificate-credentials.md) kde se dozvíte, jak registrovat certifikát a formát kontrolního výrazu.|
| `grant_type` | Požaduje se | Musí být `client_credentials`nastavena na . |

Všimněte si, že parametry jsou téměř stejné jako v případě požadavku sdíleným tajným tajemstvím s tím rozdílem, že parametr client_secret je nahrazen dvěma parametry: client_assertion_type a client_assertion.

### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď vypadá takto:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametr | Popis |
| --- | --- |
| `access_token` | Požadovaný přístupový token. Aplikace můžete použít tento token k ověření zabezpečeného prostředku, jako je například webové rozhraní API. |
| `token_type` | Označuje hodnotu typu tokenu. Jediný typ, který podporuje platforma identit společnosti Microsoft `bearer`je . |
| `expires_in` | Doba platnosti přístupového tokenu (v sekundách). |

### <a name="error-response"></a>Odpověď na chybu

Odpověď na chybu vypadá takto:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým dochází, a reagovat na chyby. |
| `error_description` | Konkrétní chybová zpráva, která vám může pomoci identifikovat hlavní příčinu chyby ověřování. |
| `error_codes` | Seznam kódů chyb specifických pro STS, které mohou pomoci s diagnostikou. |
| `timestamp` | Čas, kdy došlo k chybě. |
| `trace_id` | Jedinečný identifikátor žádosti, který má pomoci s diagnostikou. |
| `correlation_id` | Jedinečný identifikátor žádosti, který vám pomůže s diagnostikou napříč součástmi. |

## <a name="use-a-token"></a>Použití tokenu

Teď, když jste získali token, použijte token k vyžádání prostředků. Po vypršení platnosti tokenu opakujte požadavek na `/token` koncový bod získat nový přístupový token.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Ukázky kódu a další dokumentace

Přečtěte si [dokumentaci k přehledu pověření klienta](https://aka.ms/msal-net-client-credentials) z knihovny Microsoft Authentication Library

| Ukázka | Platforma |Popis |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | Konzola .NET Core 2.1 | Jednoduchá aplikace .NET Core, která zobrazuje uživatele klienta dotazujícího se na Microsoft Graph pomocí identity aplikace, nikoli jménem uživatele. Ukázka také ilustruje variantu pomocí certifikátů pro ověřování. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Webová aplikace, která synchronizuje data z microsoft graphu pomocí identity aplikace, nikoli jménem uživatele. |
