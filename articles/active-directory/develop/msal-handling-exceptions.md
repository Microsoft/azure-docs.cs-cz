---
title: Chyby a výjimky (MSAL) | Azure
description: Naučte se zpracovávat chyby a výjimky, podmíněný přístup a výzvy deklarací identity v aplikacích MSAL.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c294e3bd8ac04454c2d715c665e0da4f9a4f4535
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835020"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Zpracování výjimek a chyb pomocí MSAL
Výjimky v knihovně Microsoft Authentication Library (MSAL) jsou určené vývojářům aplikací k řešení potíží a nikoli k jejich zobrazení koncovým uživatelům. Zprávy výjimek nejsou lokalizovány.

Při zpracování výjimek a chyb můžete použít samotný typ výjimky a kód chyby k rozlišení mezi výjimkami.  Seznam kódů chyb najdete v tématu [kódy chyb ověřování a autorizace](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>Výjimky rozhraní .NET
Při zpracování výjimek můžete použít samotný typ výjimky a `ErrorCode` člen k rozlišení mezi výjimkami. Hodnoty `ErrorCode` jsou konstanty typu [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Můžete se také podívat na pole [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Pokud je vyvolána výjimka [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) , kód chyby může obsahovat kód, který můžete najít v části [kódy chyb ověřování a autorizace](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Běžné výjimky
Zde jsou uvedeny běžné výjimky, které mohou být vyvolány, a některé možné zmírnění.

| Výjimka | Kód chyby | Omezení rizik|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: Uživatel nebo správce nesouhlasí s používáním aplikace s ID {appId} s názvem {appName}. Odešlete interaktivní žádost o autorizaci pro tohoto uživatele a prostředek.| Nejdřív musíte získat souhlas s uživatelem. Pokud nepoužíváte .NET Core (který neobsahuje žádné webové uživatelské rozhraní), zavolejte (pouze jednou) `AcquireTokeninteractive`. Pokud používáte .NET Core nebo ho nechcete provést `AcquireTokenInteractive`, uživatel může přejít na adresu URL, aby mohl udělit souhlas:. https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read Pro volání `AcquireTokenInteractive`:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: Uživatel musí používat službu Multi-Factor Authentication.| Nedochází ke zmírnění rizik – Pokud je pro vašeho tenanta nakonfigurovaná MFA a AAD se rozhodne ji vyhovět, je nutné přejít na interaktivní tok, například `AcquireTokenInteractive` nebo. `AcquireTokenByDeviceCode`|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Typ udělení není podporován v koncových bodech */běžné* nebo */consumers* . Použijte */Organizations* nebo koncový bod pro konkrétního tenanta. Použili jste */běžné*.| Jak je vysvětleno ve zprávě z Azure AD, autorita musí mít tenanta nebo jinak */Organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: Tělo žádosti musí obsahovat následující parametr: ' client_secret nebo client_assertion '.| K tomu může dojít, pokud vaše aplikace nebyla registrována jako veřejná klientská aplikace v Azure AD. V Azure Portal upravte manifest pro aplikaci a nastavte `allowPublicClient` `true`na. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user zpráva: Nepovedlo se identifikovat přihlášeného uživatele.| Knihovna se nemohla dotázat na aktuálně přihlášeného uživatele systému Windows nebo tento uživatel není připojen ke službě AD nebo AAD (nepřipojení uživatelé připojení k pracovišti). Zmírnění 1: na UWP ověřte, že aplikace má následující funkce: Podnikové ověřování, privátní sítě (klient a Server), informace o uživatelském účtu. Zmírnění 2: Implementujte vlastní logiku pro načtení uživatelského jména (například john@contoso.com) a `AcquireTokenByIntegratedWindowsAuth` použijte formulář, který převezme uživatelské jméno.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Tato metoda spoléhá na protokol vystavený službou Active Directory (AD). Pokud byl uživatel vytvořen v Azure Active Directory bez služby AD ("spravovaný" uživatel), tato metoda se nezdaří. Uživatelům vytvořeným ve službě AD a zálohovaným pomocí AAD ("federované" uživatelé) může tato neinteraktivní metoda ověřování těžit z výhod. Zmírnění Použijte interaktivní ověřování.|

## <a name="javascript-errors"></a>Chyby JavaScriptu

MSAL. js poskytuje objekty chyb, které abstraktní a klasifikují různé typy běžných chyb a mají rozhraní pro přístup k určitým podrobnostem o chybách, jako jsou například chybové zprávy pro jejich správné zpracování.

**Error – objekt**

```javascript                                
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```                
Rozšířením třídy Error máte přístup k následujícím vlastnostem:
* **AuthError. Message:** To je totéž jako errorMessage.
* **AuthError. stack:** Trasování zásobníku pro vyvolané chyby. Umožňuje trasování do zdrojového bodu chyby.

**Typy chyb**

K dispozici jsou následující typy chyb:

* *AuthError:* Základní třída Error pro knihovnu MSAL. js, která se používá také pro neočekávané chyby.

* *ClientAuthError:* Třída Error, která označuje problém s ověřením klienta. Většina chyb, které pocházejí z knihovny, bude ClientAuthErrors. Může se jednat o chyby, jako je volání metody přihlášení, když probíhá přihlášení, uživatelé zruší přihlášení atd.

* *ClientConfigurationError:* Třída Error rozšířící ClientAuthError vyvolala před odesláním požadavků, pokud jsou zadané parametry konfigurace uživatele poškozené nebo chybí.

* *ServerError* Třída Error představující chybové řetězce odesílané ověřovacím serverem Můžou to být chyby, jako jsou neplatné formáty nebo parametry požadavků nebo jakékoli jiné chyby, které brání serveru v ověřování nebo autorizaci uživatele.

* *InteractionRequiredAuthError:* Třída Error rozšiřuje ServerError tak, aby představovala chyby serveru, které vyžadují interaktivní volání. Tato výjimka je vyvolána `acquireTokenSilent` , pokud uživatel potřebuje k interakci se serverem k poskytnutí přihlašovacích údajů nebo souhlasu s ověřováním/autorizací. Kódy chyb zahrnují "interaction_required", "login_required", "consent_required".

Pro zpracování chyb v tokůch ověřování pomocí metod přesměrování`loginRedirect`( `acquireTokenRedirect`,) budete muset zaregistrovat zpětné volání, které je voláno s funkcí úspěch nebo neúspěch po přesměrování `handleRedirectCallback()` pomocí metody následujícím způsobem:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

Metody pro místní prostředí (`loginPopup`, `acquireTokenPopup`) vracejí příslibů, takže můžete použít vzor Promise (. then a. catch) k jejich zpracování, jak je znázorněno níže:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Interakce – požadované chyby

Pokud je vyžadována interakce uživatelského rozhraní, je vrácena chyba. To znamená, že jste se pokusili použít neinteraktivní metodu získání tokenu (například `acquireTokenSilent`), ale MSAL to neudělá v tichém režimu. Možné důvody:

* musíte se přihlásit.
* musíte souhlasit.
* musíte projít prostředím Multi-Factor Authentication.

Nápravou je zavolat interaktivní metodu, například `acquireTokenPopup` nebo: `acquireTokenRedirect`

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="conditional-access-and-claims-challenges"></a>Podmíněný přístup a výzvy k deklaracím
Při tichém získávání tokenů může vaše aplikace obdržet chyby, [](conditional-access-dev-guide.md) když se v rozhraní API, ke kterému se snažíte získat přístup, vyžaduje zásada MFA, jako je například zásada MFA.

Vzor, který zpracovává tuto chybu, je interaktivní získání tokenu pomocí MSAL. Při interaktivním získání tokenu se uživateli zobrazí výzva, aby si vyžádali splnění požadovaných zásad podmíněného přístupu.

V některých případech při volání rozhraní API, které vyžaduje podmíněný přístup, můžete z rozhraní API obdržet výzvu s deklarací identity. Pokud má například zásada podmíněného přístupu spravované zařízení (Intune), bude chyba něco jako [AADSTS53000: Vaše zařízení musí být spravované pro přístup k tomuto prostředku](reference-aadsts-error-codes.md) nebo něco podobného. V takovém případě můžete deklarace identity předat voláním metody získat token, aby se uživateli zobrazila výzva, aby splnila příslušné zásady.

### <a name="net"></a>.NET
Při volání rozhraní API, které vyžaduje podmíněný přístup ze MSAL.NET, bude vaše aplikace muset zpracovat výjimky pro výzvy k deklaracím identity. Tato vlastnost se zobrazí jako [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) , kde vlastnost [deklarací](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) nebude prázdná.

Pro zpracování výzvy deklarací identity budete muset použít `.WithClaim()` metodu `PublicClientApplicationBuilder` třídy.

### <a name="javascript"></a>JavaScript
Při tichém získávání tokenů ( `acquireTokenSilent`pomocí) pomocí MSAL. js může vaše aplikace obdržet chyby, [](conditional-access-dev-guide.md) když rozhraní API, ke kterému se snažíte získat přístup, vyžaduje zásadu MFA, jako je například zásada MFA.

Vzor pro zpracování této chyby je provést interaktivní volání metody získání tokenu v MSAL. js, `acquireTokenPopup` jako je například nebo `acquireTokenRedirect` jako v následujícím příkladu:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

K interaktivnímu získání tokenu se uživateli zobrazí výzva a dává jim možnost splnit požadované zásady podmíněného přístupu.

Při volání rozhraní API, které vyžaduje podmíněný přístup, můžete z rozhraní API obdržet výzvu s deklarací identity. V takovém případě můžete předat deklarace vracené v chybě jako `extraQueryParameters` v volání metody získat tokeny, aby se uživateli zobrazila výzva k splnění příslušných zásad:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Opakování po chybách a výjimkách

### <a name="http-error-codes-500-600"></a>Kódy chyb HTTP 500-600
MSAL.NET implementuje jednoduchý mechanismus opakovaného pokusu o chyby s kódy chyb HTTP 500-600.

### <a name="http-429"></a>HTTP 429
Pokud je server tokenu služby (STS) příliš zaneprázdněn z důvodu příliš velkého počtu požadavků, vrátí chybu protokolu HTTP 429 s pomocným parametrem, kdy se můžete pokusit znovu (pole pro odpověď s opakováním) na zpoždění v sekundách nebo datum.

#### <a name="net"></a>.NET
`namedHeaders` [](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) Plochy`System.Net.Http.Headers.HttpResponseHeaders` výjimky MsalServiceException jako vlastnost. Proto můžete využít další informace z kódu chyby ke zlepšení spolehlivosti aplikací. V případě, že jsme právě popsali, můžete `RetryAfterproperty` použít (typ `RetryConditionHeaderValue`) a výpočetní čas, kdy se to opakuje.

Tady je příklad pro aplikaci démona (pomocí toku přihlašovacích údajů klienta), ale můžete ji přizpůsobit kterékoli z metod, které získávají token.

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```
