---
title: Tok přihlašovacích údajů klienta OAuth 2,0 na platformě Microsoft identity | Azure
description: Sestavujte webové aplikace pomocí implementace ověřovacího protokolu OAuth 2,0 od Microsoft Identity Platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 7/27/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e5fe8e751077bc04850879d27827c197767a81c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87759066"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft Identity Platform a tok přihlašovacích údajů klienta OAuth 2,0

Můžete použít [udělení přihlašovacích údajů klienta OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.4) zadané v dokumentu RFC 6749, někdy označované jako *legged OAuth*, pro přístup k prostředkům hostovaným na webu pomocí identity aplikace. Tento typ grantu se běžně používá pro interakce mezi servery, které musí běžet na pozadí bez okamžité interakce s uživatelem. Tyto typy aplikací se často označují jako *démoni* nebo *účty služeb*.

Tento článek popisuje, jak programovat přímo s protokolem ve vaší aplikaci. Pokud je to možné, doporučujeme místo toho použít podporované knihovny Microsoft Authentication Library (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Podívejte se také na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

Tok udělení přihlašovacích údajů klienta OAuth 2,0 umožňuje webové službě (důvěrnému klientovi) použít vlastní přihlašovací údaje místo zosobnění uživatele k ověření při volání jiné webové služby. V tomto scénáři je klient obvykle webová služba střední vrstvy, služba démona nebo Web. Pro zajištění vyšší úrovně zabezpečení umožňuje volající službě také použít certifikát (namísto sdíleného tajného klíče) jako přihlašovací údaje.

V toku přihlašovacích údajů klienta jsou oprávnění udělena přímo aplikaci samotné správcem. Když aplikace prezentuje token prostředku, prostředek vynutil, že aplikace sama má autorizaci k provedení akce, protože k ověřování není zapojen žádný uživatel.  Tento článek se zabývá kroky potřebnými k [autorizaci aplikace pro volání rozhraní API](#application-permissions)a také k tomu, [Jak získat tokeny potřebné pro volání rozhraní API](#get-a-token).

## <a name="protocol-diagram"></a>Diagram protokolu

Tok celého přihlašovacích údajů klienta vypadá podobně jako v následujícím diagramu. Každý z těchto kroků popisujeme dále v tomto článku.

![Diagram znázorňující tok přihlašovacích údajů klienta](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Získat přímou autorizaci

Aplikace obvykle získá přímou autorizaci pro přístup k prostředku jedním ze dvou způsobů:

* [Přes seznam řízení přístupu (ACL) na prostředku](#access-control-lists)
* [Prostřednictvím přiřazení oprávnění aplikace v Azure AD](#application-permissions)

Tyto dvě metody jsou nejběžnější ve službě Azure AD a doporučujeme je pro klienty a prostředky, které provádějí tok přihlašovacích údajů klienta. Prostředek může také zvolit autorizaci klientů jiným způsobem. Každý server prostředků může zvolit metodu, která má pro svou aplikaci smysl.

### <a name="access-control-lists"></a>Seznamy řízení přístupu

Poskytovatel prostředků může vymáhat kontrolu autorizace na základě seznamu ID aplikace (klientů), který zná, a udělí konkrétní úroveň přístupu k. Když prostředek obdrží token z koncového bodu Microsoft Identity Platform, může token dekódovat a extrahovat ID aplikace klienta z `appid` `iss` deklarací identity a. Pak porovná aplikaci se seznamem řízení přístupu (ACL), které udržuje. Členitost a metoda seznamu ACL se můžou podstatně lišit mezi prostředky.

Běžným případem použití je použití seznamu ACL ke spuštění testů pro webovou aplikaci nebo webové rozhraní API. Webové rozhraní API může udělit pouze podmnožinu úplných oprávnění pro konkrétního klienta. Chcete-li spustit ucelené testy v rozhraní API, vytvořte testovacího klienta, který získá tokeny z koncového bodu Microsoft Identity Platform a poté je odešle do rozhraní API. Rozhraní API pak zkontroluje seznam ACL pro ID aplikace testovacího klienta, aby měl úplný přístup k celé funkci rozhraní API. Použijete-li tento druh seznamu ACL, ověřte, zda není pouze hodnota volajícího, `appid` ale také ověřte, zda `iss` je hodnota tokenu důvěryhodná.

Tento typ autorizace je společný pro démony a účty služeb, které potřebují přístup k datům vlastněných uživateli, kteří mají osobní účty Microsoft. Pro data vlastněná organizacemi doporučujeme, abyste získali potřebnou autorizaci prostřednictvím oprávnění aplikace.

> [!NOTE]
> Aby bylo možné povolit tento autorizační vzor založený na seznamu ACL, Azure AD nevyžaduje, aby aplikace byly autorizované k získání tokenů pro jinou aplikaci, takže tokeny jenom pro aplikace se dají vydávat bez `roles` deklarace identity. Aplikace, které zveřejňují rozhraní API, musí implementovat kontroly oprávnění, aby mohli přijímat tokeny.

### <a name="application-permissions"></a>Oprávnění aplikace

Místo používání seznamů ACL můžete použít rozhraní API k vystavení sady **oprávnění aplikace**. Oprávnění aplikace je uděleno správcem organizace a je možné je používat jenom pro přístup k datům, která patří do této organizace a jejích zaměstnanců. Například Microsoft Graph zveřejňuje několik oprávnění aplikace, aby bylo možné provést následující akce:

* Čtení pošty ve všech poštovních schránkách
* Čtení a zápis pošty ve všech poštovních schránkách
* Poslat e-mail jako libovolný uživatel
* Čtení dat z adresáře

Pokud chcete použít oprávnění aplikace s vlastním rozhraním API (na rozdíl od Microsoft Graph), musíte nejdřív [vystavit rozhraní API](quickstart-configure-app-expose-web-apis.md) definováním oborů v registraci aplikace rozhraní api v Azure Portal. Pak [nakonfigurujte přístup k rozhraní API](quickstart-configure-app-access-web-apis.md) tak, že v registraci aplikace klientských aplikací vyberete tato oprávnění. Pokud jste neodhalili žádné obory v registraci aplikace vašeho rozhraní API, nebudete moct do tohoto rozhraní API v registraci aplikace klientské aplikace v Azure Portal zadat oprávnění aplikace.

Při ověřování jako aplikace (na rozdíl od uživatele) nemůžete použít *delegovaná oprávnění* – obory, které jsou uděleny uživatelem. Je nutné použít oprávnění aplikace, označované také jako role, které jsou uděleny správcem aplikace nebo prostřednictvím předběžného ověřování webového rozhraní API.

Další informace o oprávněních aplikací najdete v tématu [oprávnění a souhlas](v2-permissions-and-consent.md#permission-types).

#### <a name="recommended-sign-the-user-into-your-app"></a>Doporučeno: podepsat uživatele do aplikace

Při vytváření aplikace, která používá oprávnění aplikace, obvykle aplikace vyžaduje stránku nebo zobrazení, ve kterém správce schválí oprávnění aplikace. Tato stránka může být součástí toku přihlášení aplikace, části nastavení aplikace nebo může být vyhrazeným tokem "Connect". V mnoha případech má aplikace smysl zobrazit toto zobrazení "připojit" až po přihlášení uživatele pomocí pracovní nebo školní účet Microsoft.

Pokud uživatele podepíšete do vaší aplikace, můžete určit organizaci, ke které uživatel patří, a potom požádat uživatele o schválení oprávnění aplikace. I když není nezbytně nutné, může vám to usnadnit vytváření intuitivnějšího prostředí pro uživatele. Pokud chcete uživatele podepsat, postupujte podle [kurzů Microsoft Identity Platform Protocol](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Vyžádání oprávnění od správce adresáře

Až budete připraveni požádat o oprávnění od správce organizace, můžete uživatele přesměrovat na *koncový bod souhlasu správce*Microsoft Identity Platform.

> [!TIP]
> Zkuste tento požadavek provést v nástroji post! (Pro nejlepší výsledky použijte vlastní ID aplikace – kurz aplikace nebude vyžadovat užitečná oprávnění.) [ ![ Pokus o spuštění této žádosti v předzálohovacím](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

Tip pro: zkuste do prohlížeče vložit následující požadavek.

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametr | Stav | Popis |
| --- | --- | --- |
| `tenant` | Povinné | Tenant adresáře, ze kterého chcete požádat o oprávnění. Může se jednat o formát GUID nebo popisný název. Pokud nevíte, ke kterému tenantovi uživatel patří, a chcete jim umožnit, aby se přihlásili pomocí libovolného tenanta, použijte `common` . |
| `client_id` | Vyžadováno | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `redirect_uri` | Vyžadováno | Identifikátor URI přesměrování, kde má být odeslána odpověď pro zpracování vaší aplikace. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu, s výjimkou toho, že musí být zakódovaný URL a může mít další segmenty cesty. |
| `state` | Doporučeno | Hodnota, která je obsažena v požadavku, který je také vrácen v odpovědi na token. Může to být řetězec libovolného obsahu, který chcete. Stav se používá ke kódování informací o stavu uživatele v aplikaci předtím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |

V tomto okamžiku Azure AD vynutilo, že k dokončení žádosti se může přihlásit jenom správce klienta. Správce bude požádán o schválení všech přímých oprávnění aplikace, která jste si vyžádali pro vaši aplikaci na portálu pro registraci aplikací.

##### <a name="successful-response"></a>Úspěšná odpověď

Pokud správce schválí oprávnění pro vaši aplikaci, úspěšná odpověď vypadá takto:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Popis |
| --- | --- |
| `tenant` | Tenant adresáře, který vaší aplikaci udělil požadovaná oprávnění, ve formátu identifikátoru GUID. |
| `state` | Hodnota, která je obsažena v požadavku, který je vrácen také v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Stav se používá ke kódování informací o stavu uživatele v aplikaci předtím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
| `admin_consent` | Nastavte na **hodnotu true**. |

##### <a name="error-response"></a>Chybová odezva

Pokud správce neschválí oprávnění pro vaši aplikaci, neúspěšná odpověď vypadá takto:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb a který můžete použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která vám může pomáhat identifikovat hlavní příčinu chyby. |

Po přijetí úspěšné odpovědi z koncového bodu zřizování aplikace získala aplikace přímo požadovaná oprávnění aplikace. Nyní můžete požádat o token pro prostředek, který chcete.

## <a name="get-a-token"></a>Získání tokenu

Po získání potřebné autorizace pro vaši aplikaci pokračujte v získání přístupových tokenů pro rozhraní API. Pokud chcete získat token pomocí udělení přihlašovacích údajů klienta, odešlete požadavek POST na `/token` koncový bod Microsoft Identity Platform:

> [!TIP]
> Zkuste tento požadavek provést v nástroji post! (Pro nejlepší výsledky použijte vlastní ID aplikace – kurz aplikace nebude vyžadovat užitečná oprávnění.) [ ![ Pokus o spuštění této žádosti v předzálohovacím](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První případ: žádost přístupového tokenu se sdíleným tajným klíčem

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parametr | Stav | Popis |
| --- | --- | --- |
| `tenant` | Povinné | Tenant adresáře, na který aplikace plánuje pracovat, v identifikátoru GUID nebo ve formátu názvu domény. |
| `client_id` | Vyžadováno | ID aplikace přiřazené vaší aplikaci. Tyto informace najdete na portálu, kde jste zaregistrovali vaši aplikaci. |
| `scope` | Vyžadováno | Hodnota předaná pro `scope` parametr v této žádosti by měla být identifikátorem prostředku (identifikátor URI ID aplikace) prostředku, který chcete připojit, a to s `.default` příponou. V příkladu Microsoft Graph je hodnota `https://graph.microsoft.com/.default` . <br/>Tato hodnota oznamuje koncovému bodu Microsoft Identity Platform, který má všechna přímá oprávnění aplikace nakonfigurovaná pro vaši aplikaci, koncový bod by měl vydávat token pro ty, které jsou přidružené k prostředku, který chcete použít. Další informace o tomto `/.default` oboru najdete v [dokumentaci k vyjádření souhlasu](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Vyžadováno | Tajný kód klienta, který jste vygenerovali pro vaši aplikaci na portálu pro registraci aplikací. Tajný klíč klienta musí být před odesláním zakódovaný na adrese URL. |
| `grant_type` | Vyžadováno | Musí být nastaven na hodnotu `client_credentials` . |

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: žádost o přístupový token s certifikátem

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parametr | Stav | Popis |
| --- | --- | --- |
| `tenant` | Povinné | Tenant adresáře, na který aplikace plánuje pracovat, v identifikátoru GUID nebo ve formátu názvu domény. |
| `client_id` | Vyžadováno |ID aplikace (klienta), které je přiřazeno vaší aplikaci. |
| `scope` | Vyžadováno | Hodnota předaná pro `scope` parametr v této žádosti by měla být identifikátorem prostředku (identifikátor URI ID aplikace) prostředku, který chcete připojit, a to s `.default` příponou. V příkladu Microsoft Graph je hodnota `https://graph.microsoft.com/.default` . <br/>Tato hodnota informuje koncový bod platformy Microsoft Identity Platform, který obsahuje všechna přímá oprávnění aplikace, která jste pro svou aplikaci nakonfigurovali, a měla by vydávat token pro ty, které jsou přidružené k prostředku, který chcete použít. Další informace o tomto `/.default` oboru najdete v [dokumentaci k vyjádření souhlasu](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Vyžadováno | Hodnota musí být nastavena na `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Vyžadováno | Kontrolní výraz (webový token JSON), který potřebujete k vytvoření a podepsání certifikátu, který jste zaregistrovali jako přihlašovací údaje pro vaši aplikaci. Přečtěte si informace o [přihlašovacích údajích k certifikátu](active-directory-certificate-credentials.md) , kde se dozvíte, jak zaregistrovat certifikát a formát kontrolního výrazu.|
| `grant_type` | Vyžadováno | Musí být nastaven na hodnotu `client_credentials` . |

Všimněte si, že parametry jsou skoro stejné jako v případě požadavku pomocí sdíleného tajného klíče s tím rozdílem, že parametr client_secret je nahrazen dvěma parametry: client_assertion_type a client_assertion.

### <a name="successful-response"></a>Úspěšná odpověď

Úspěšná odpověď vypadá takto:

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametr | Popis |
| --- | --- |
| `access_token` | Požadovaný přístupový token Aplikace může tento token použít k ověření zabezpečeného prostředku, například k webovému rozhraní API. |
| `token_type` | Určuje hodnotu typu tokenu. Jediným typem, který podporuje platforma Microsoft Identity Platform, je `bearer` . |
| `expires_in` | Doba, po kterou je přístupový token platný (v sekundách). |

### <a name="error-response"></a>Chybová odezva

Reakce na chybu vypadá takto:

```json
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
| `error` | Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým dochází, a k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která vám může pomáhat identifikovat hlavní příčinu chyby ověřování. |
| `error_codes` | Seznam chybových kódů specifických pro službu STS, které mohou pomáhat s diagnostikou. |
| `timestamp` | Čas, kdy došlo k chybě. |
| `trace_id` | Jedinečný identifikátor pro požadavek, který vám může pomáhat s diagnostikou. |
| `correlation_id` | Jedinečný identifikátor požadavku, který bude pomáhat s diagnostikou napříč komponentami. |

## <a name="use-a-token"></a>Použití tokenu

Teď, když jste získali token, použijte token k vytvoření požadavků na prostředek. Po vypršení platnosti tokenu opakujte požadavek na `/token` koncový bod pro získání nového přístupového tokenu.

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Ukázky kódu a další dokumentace

Přečtěte si [dokumentaci přehled přihlašovacích údajů klienta](https://aka.ms/msal-net-client-credentials) z knihovny Microsoft Authentication Library.

| Ukázka | Platforma |Description |
|--------|----------|------------|
|[Active-Directory-dotnetcore-démon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | Konzola .NET Core 2,1 | Jednoduchá aplikace .NET Core, která zobrazuje uživatele tenanta, který dotazuje Microsoft Graph pomocí identity aplikace, a ne jménem uživatele. Ukázka také znázorňuje variaci pomocí certifikátů pro ověřování. |
|[Active-Directory-dotnet-démon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Webová aplikace, která synchronizuje data z Microsoft Graph pomocí identity aplikace, nikoli jménem uživatele. |
