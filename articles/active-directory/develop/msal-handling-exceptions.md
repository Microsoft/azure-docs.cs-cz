---
title: Chyby a výjimky (MSAL) | Azure
description: Zjistěte, jak zpracování chyb a výjimek, podmíněného přístupu a deklarace identity vybízí v aplikacích MSAL.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 43c98181c926410bea2acf64bf1ed4d588c12616
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138975"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Zpracování výjimek a chyb s použitím MSAL
Výjimky v Microsoft Authentication Library (MSAL) jsou určené pro vývojáře aplikací pro řešení potíží a ne pro zobrazení koncovým uživatelům. Zprávy o výjimkách nejsou lokalizovány.

Při zpracování výjimek a chyb, můžete použít vlastní typ výjimky nebo kód chyby: k rozlišení mezi výjimky.  Seznam kódů chyb najdete v tématu [kódy chyb ověřování a autorizace](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>Výjimky .NET
Při zpracování výjimek, můžete použít samotný datový typ výjimky a `ErrorCode` člen rozlišovat mezi výjimky. Hodnoty `ErrorCode` jsou konstanty typu [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet#fields).

Podívejte se na pole můžete mít také [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet#fields), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet#fields).

Pokud [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) je vyvolána chyba kódu může obsahovat kód, který můžete najít v [kódy chyb ověřování a autorizace](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Běžné výjimky
Tady jsou běžné výjimky, které by mohla být vyvolána a některé možné způsoby zmírnění rizik.

| Výjimka | Kód chyby | Omezení rizik|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: Uživatel nebo správce nevyjádřil souhlas s použitím aplikace s ID {appId} s názvem {appName}. Odeslat žádost o interaktivní autorizaci pro tohoto uživatele a prostředek.| Potřebujete získat souhlas uživatele jako první. Pokud nepoužíváte .NET Core (která nemá žádné webové uživatelské rozhraní), volání (pouze jednou) `AcquireTokeninteractive`. Pokud používáte .NET core nebo to dělat nechcete `AcquireTokenInteractive`, uživatel přejít na adresu URL k udělení souhlasu doplňku: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read . Chcete-li volat `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: Uživatel je potřeba pomocí služby Multi-Factor authentication.| Neexistuje žádné zmírňující opatření – pokud MFA je nakonfigurovaný pro vašeho tenanta a AAD se rozhodne je vynutit, budete muset nouzového řešení ověření pomocí interaktivního toku, jako `AcquireTokenInteractive` nebo `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) |AADSTS90010: Typ udělení oprávnění není podporováno u */common* nebo */consumers* koncových bodů. Použití */organizations* nebo koncový bod specifickým pro tenanta. Použili jste */common*.| Jak je vysvětleno v zprávy ze služby Azure AD, musí mít tenanta oprávnění nebo jinak */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) | AADSTS70002: Text požadavku musí obsahovat následující parametr: hodnotu client_secret nebo client_assertion ".| To může nastat, pokud vaše aplikace nebyla registrována jako veřejné klientskou aplikaci ve službě Azure AD. Na webu Azure Portal, upravit manifest pro aplikace a nastavení `allowPublicClient` k `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user zpráva: Nelze identifikovat přihlášeného uživatele| Knihovny nemohl zadat dotaz aktuální Windows přihlášeného uživatele nebo tento uživatel není AD nebo připojená k AAD (připojené k doméně uživatelů pracovního místa se nepodporují). Zmírnění dopadů 1: na UPW, zkontrolujte, že aplikace má následující možnosti: Podnikové ověřování, privátní sítě (klient a Server), informace o uživatelském účtu. Zmírnění dopadů 2: Implementace vlastní logiky načíst uživatelské jméno (třeba john@contoso.com) a použít `AcquireTokenByIntegratedWindowsAuth` formulář, který přebírá uživatelské jméno.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Tato metoda závisí na protokolu vystavený službou Active Directory (AD). Pokud uživatel vytvořil ve službě Azure Active Directory bez zálohování AD ("spravovaný" uživatel), tato metoda se nezdaří. Uživatelé vytvořili ve službě AD a se opírá o AAD ("federovaných" uživatelů) mohou mít prospěch z této metody jako neinteraktivní ověřování. Omezení rizik: Použití interaktivního ověřování.|

## <a name="javascript-errors"></a>Chyby jazyka JavaScript

MSAL.js poskytuje chyba objekty, které abstraktní a klasifikovat různé druhy běžné chyby a rozhraní, přístup ke konkrétní podrobnosti o chybách, jako je například chybové zprávy, které je odpovídajícím způsobem zpracovat.

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
Rozšíření třídy chyb, máte přístup k následující vlastnosti:
* **AuthError.message:** To je stejný jako chybová zpráva.
* **AuthError.stack:** Trasování zásobníku pro vyvolané chyby. Umožňuje trasování, aby místo původu chyby.

**Typy chyb**

K dispozici jsou následující typy chyb:

* *AuthError:* Základní chyba třídy pro knihovnu MSAL.js také používat pro neočekávané chyby.

* *ClientAuthError:* Chyba třída, která označuje problém s ověření klienta. Většina chyb, které pocházejí z knihovny bude ClientAuthErrors. To může být chyby jako např. volání metody přihlašování po přihlášení v průběhu, uživatele ruší přihlášení atd.

* *ClientConfigurationError:* Chyba třídy rozšíření ClientAuthError vyvolána předtím, než když jsou vytvářeny požadavky parametry konfigurace daného uživatele je poškozený nebo chybí.

* *ServerError:* Chyba třída představující chybové řetězce odeslané serverem ověřování. Může to být chyby jako např. neplatný požadavek formáty nebo parametry nebo jiné chyby, které brání serveru ověřování nebo autorizace uživatele.

* *InteractionRequiredAuthError:* Chyba třídy rozšíření ServerError představující chyby serveru, které vyžadují interaktivní volání. To je vyvolána `acquireTokenSilent` Pokud musí uživatel zadat komunikovat se serverem k zadání přihlašovacích údajů nebo souhlas pro ověřování/autorizace. Kódy chyb zahrnují "interaction_required", "login_required", "consent_required".

Pro přesměrování zpracování chyb v ověřování toků pomocí metody (`loginRedirect`, `acquireTokenRedirect`), budete muset zaregistrovat úspěch nebo neúspěch zpětné volání, které je voláno po použití přesměrování `handleRedirectCallback()` metodu následujícím způsobem:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

Metody pro místní prostředí (`loginPopup`, `acquireTokenPopup`) vrátí příslibů, abyste mohli použít model promise (.then a .catch), jejich zpracování, jak je znázorněno:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Součinnosti chyby

Pokud je nutné použít interakce s uživatelským rozhraním, vrátí se chyba. To znamená, že jste se pokusili použít neinteraktivní způsob získávání tokenu (například `acquireTokenSilent`), ale nelze MSAL provést tiše. Možné příčiny:

* budete potřebovat k přihlášení
* potřebujete souhlas
* budete muset přejít prostřednictvím prostředí ověřování službou Multi-Factor Authentication.

Náprava je zavolat metodu interaktivní jako `acquireTokenPopup` nebo `acquireTokenRedirect`:

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

## <a name="conditional-access-and-claims-challenges"></a>Podmíněného přístupu a deklarace identity výzvy
Při získávání tokenů tiše, vaše aplikace může dojít k chybám při [podmíněného přístupu deklarací challenge](conditional-access-dev-guide.md) , jako je rozhraní API vyžaduje zásad vícefaktorového ověřování se pokoušíte získat přístup.

Vzor pro zpracování této chyby je interaktivní získání tokenu s využitím MSAL. Interaktivní získávání tokenu se zobrazí výzva a dává jim možnost splňovat zásady podmíněného přístupu vyžaduje.

V některých případech při volání rozhraní API, které vyžadují podmíněný přístup může přijímat deklarace identity výzvu v chybě z rozhraní API. Pro instance, pokud zásady podmíněného přístupu je, aby spravovaná zařízení (Intune) Chyba bude vypadat [AADSTS53000: Vaše zařízení je potřeba spravovat přístup k danému prostředku](reference-aadsts-error-codes.md) nebo něco podobného. V takovém případě můžete předat deklarace identity v tokenu volání získání tak, že bude uživatel vyzván k uspokojení příslušnou zásadu.

### <a name="net"></a>.NET
Při volání rozhraní API, které vyžadují podmíněný přístup z MSAL.NET, aplikace bude vyžadovat pro zpracování výjimek challenge deklarace identity. Tím se zobrazí jako [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) kde [deklarace identity](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) vlastnost nesmí být prázdná.

Pro zpracování před obrovskou výzvou – deklarace identity, budete muset použít `.WithClaim()` metodu `PublicClientApplicationBuilder` třídy.

### <a name="javascript"></a>JavaScript
Při získávání tokenů tiše (pomocí `acquireTokenSilent`) využitím MSAL.js, vaše aplikace může dojít k chybám při [podmíněného přístupu deklarací challenge](conditional-access-dev-guide.md) , jako je rozhraní API vyžaduje zásad vícefaktorového ověřování se pokoušíte získat přístup k.

Vzor pro zpracování této chyby je, aby interaktivní volání k získání tokenu v MSAL.js například `acquireTokenPopup` nebo `acquireTokenRedirect` jako v následujícím příkladu:

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

Interaktivní získání tokenu se zobrazí výzva a dává jim možnost splňovat zásady podmíněného přístupu vyžaduje.

Při volání rozhraní API, které vyžadují podmíněný přístup, se zobrazí výzva deklarací identity v chybě z rozhraní API. V takovém případě může předat deklarace identity vrátil chybu jako `extraQueryParameters` při volání funkce získávat tokeny tak, že bude uživatel vyzván k uspokojení příslušnou zásadu:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Opakování po chyby a výjimky

### <a name="http-error-codes-500-600"></a>Kódy chyb protokolu HTTP 500-600
MSAL.NET implementuje jednoduché opakování – Jakmile kódů mechanismus pro chyby s chybou HTTP 500-600.

### <a name="http-429"></a>HTTP 429
Když služba tokenu serveru (STS) je příliš zaneprázdněn a nemůže z důvodu "příliš mnoho požadavků", vrací chybu HTTP 429 s informace o, kde je to možné opakujte (pole Retry-After odpovědi) jako zpoždění v sekundách, nebo datum.

#### <a name="net"></a>.NET
[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) výjimka povrchy `System.Net.Http.Headers.HttpResponseHeaders` jako vlastnost `namedHeaders`. Proto je možné využívat další informace z kódu chyby kvůli vyšší spolehlivosti vašich aplikací. V tomto případě jsme právě popsaný, můžete použít `RetryAfterproperty` (typu `RetryConditionHeaderValue`) a výpočetní, pokud to chcete zkusit znovu.

Tady je příklad pro proces démon aplikace (pomocí toku přihlašovacích údajů klienta), ale může přizpůsobit, že na některé z metod získávání tokenu.

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
