---
title: Osvědčené postupy při zpracování chyb klientské aplikace ADAL | Azure
description: Poskytuje pokyny pro zpracování chyb a osvědčené postupy pro klientské aplikace ADAL.
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 02/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: ad5595f7eebc8feca2f00a6f95e10c547ded9529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383730"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Došlo k chybě při zpracování osvědčených postupů pro klienty knihovny Azure Active Directory Authentication Library (ADAL).

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Tento článek poskytuje pokyny týkající se typu chyb, ke kterým můžou vývojáři narazit, při použití ADAL k ověřování uživatelů. Při použití ADAL se může stát, že vývojář bude potřebovat krokování a zpracování chyb. Správné zpracování chyb zajišťuje skvělou činnost koncového uživatele a omezuje počet pokusů, kolikrát se koncový uživatel musí přihlásit.

V tomto článku se podíváme na konkrétní případy pro každou platformu, kterou podporuje ADAL, a jak vaše aplikace dokáže každý případ správně zpracovat. Pokyny k chybě jsou rozdělené do dvou širších kategorií na základě vzorů pro získání tokenů poskytovaných rozhraními API ADAL:

- **AcquireTokenSilent**: klient se pokusí získat token v tichém režimu (bez uživatelského rozhraní) a může selhat, pokud je ADAL neúspěšná. 
- **AcquireToken**: klient se může pokusit o tiché získání, ale může také provádět interaktivní požadavky, které vyžadují přihlášení.

> [!TIP]
> Při použití ADAL a Azure AD je vhodné protokolovat všechny chyby a výjimky. Protokoly nejsou užitečné pouze pro porozumění celkovému stavu aplikace, ale jsou důležité i při ladění širších problémů. I když se vaše aplikace může zotavit z určitých chyb, může pocházet v dalších problémech s návrhem, které vyžadují změny kódu, aby je bylo možné vyřešit. 
> 
> Při implementaci chybových stavů popsaných v tomto dokumentu byste měli protokolovat kód chyby a popis z výše zmíněných důvodů. Příklady kódu protokolování najdete v [referenčních informacích o chybách a protokolování](#error-and-logging-reference) . 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent se pokusí získat token s jistotou, že koncový uživatel nevidí uživatelské rozhraní (UI). Existuje několik případů, kdy může dojít k selhání tichého získání a je třeba je zpracovat prostřednictvím interaktivních požadavků nebo pomocí výchozí obslužné rutiny. Podrobně se na specifiky, kdy a jak používat jednotlivé případy v níže uvedených částech.

K dispozici je sada chyb generovaných operačním systémem, které mohou vyžadovat zpracování chyb specifického pro aplikaci. Další informace najdete v části chyby operačního systému v tématu [Chyba a odkaz na protokolování](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scénáře aplikací

- [Nativní klientské](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#native-client) aplikace (iOS, Android, .NET Desktop nebo Xamarin)
- [Webové klientské](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#web-client) aplikace volající [prostředek](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Chybové případy a postupující akce

V podstatě existují dva případy AcquireTokenSilent chyb:

| Obchodní případ | Description |
|------|-------------|
| **Případ 1**: Chyba je možné přeložit pomocí interaktivního přihlašování. | V případě chyb způsobených chybějícími platnými tokeny je nutný interaktivní požadavek. Hledání v mezipaměti a neplatný/vypršení obnovovací token vyžaduje, aby AcquireToken volání vyřešilo.<br><br>V těchto případech musí být koncový uživatel vyzván, aby se přihlásil. Aplikace se může rozhodnout provést interaktivní požadavek okamžitě po interakci koncového uživatele (například při stisknutí tlačítka pro přihlášení) nebo později. Volba závisí na požadovaném chování aplikace.<br><br>V tomto konkrétním případě se podívejte na kód v následující části a chyby, které ho diagnostikují.|
| **Případ 2**: Chyba nejde přeložit pomocí interaktivního přihlášení. | V případě sítě a přechodných/dočasných chyb nebo jiných selhání nevyřeší interaktivní žádost AcquireToken problém. Nepotřebné interaktivní výzvy pro přihlášení mohou také frustrovat koncové uživatele. ADAL se automaticky pokusí o jeden pokus o většinu chyb při selhání AcquireTokenSilent.<br><br>Klientská aplikace se také může pokusit o opakování později v pozdějším okamžiku, ale kdy a jak závisí na chování aplikace a na požadovaném prostředí pro koncové uživatele. Aplikace může například provést AcquireTokenSilent opakování po několika minutách nebo v reakci na akci koncového uživatele. Okamžité opakování bude mít za následek omezení aplikace a nemělo by se proto zkoušet.<br><br>Následné opakování se stejnou chybou neznamená, že klient provede interaktivní požadavek pomocí AcquireToken, protože nevyřešil chybu.<br><br>V tomto konkrétním případě se podívejte na kód v následující části a chyby, které ho diagnostikují. |

### <a name="net"></a>.NET

Následující doprovodné materiály obsahují příklady zpracování chyb ve spojení s metodami ADAL: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [zastaralé] acquireTokenSilent (...)
- [zastaralé] acquireTokenByRefreshToken (...) 

Váš kód by byl implementován následujícím způsobem:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

Následující doprovodné materiály obsahují příklady zpracování chyb ve spojení s metodami ADAL: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- [zastaralé] acquireTokenSilent (...)

Váš kód by byl implementován následujícím způsobem:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

Následující doprovodné materiály obsahují příklady zpracování chyb ve spojení s metodami ADAL: 

- acquireTokenSilentWithResource(...)

Váš kód by byl implementován následujícím způsobem:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken je výchozí metoda ADAL, která se používá k získání tokenů. V případech, kdy se vyžaduje identita uživatele, se AcquireToken pokusí získat nejprve token a potom v případě potřeby zobrazit uživatelské rozhraní (Pokud není předáno PromptBehavior. Never). V případech, kdy je požadována identita aplikace, se AcquireToken pokusí získat token, ale nezobrazuje uživatelské rozhraní, protože není koncový uživatel. 

Při zpracování chyb AcquireToken závisí zpracování chyb na platformě a scénáři, se kterou se aplikace pokouší dosáhnout. 

Operační systém může také generovat sadu chyb, které vyžadují zpracování chyb závislých na konkrétní aplikaci. Další informace najdete v části chyby operačního systému v tématu [Chyba a odkaz na protokolování](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scénáře aplikací

- Nativní klientské aplikace (iOS, Android, .NET Desktop nebo Xamarin)
- Webové aplikace, které volají rozhraní API prostředků (.NET)
- Jednostránkové aplikace (JavaScript)
- Aplikace služeb pro služby (.NET, Java)
  - Všechny scénáře, včetně přihlášeného uživatele
  - Konkrétní scénáře jménem

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Chybové případy a postupující akce: nativní klientské aplikace

Pokud vytváříte nativní klientskou aplikaci, je potřeba vzít v úvahu několik případů, které se týkají problémů se sítí, přechodných selhání a dalších chyb specifických pro konkrétní platformu. Ve většině případů by aplikace neměla provádět okamžité opakování, ale místo toho čekat na interakci koncového uživatele, která vyzve k přihlášení. 

Existuje několik zvláštních případů, ve kterých může jeden opakovaný pokus problém vyřešit. Například když uživatel potřebuje povolit data v zařízení nebo po počátečním selhání dokončí stahování služby Azure AD Broker. 

V případě selhání může aplikace zobrazit uživatelské rozhraní, aby mohl koncový uživatel provést určitou interakci s výzvou k opakování. Pokud se například zařízení nepovedlo kvůli chybě v režimu offline, zobrazí se výzva k opakovanému pokusu o přihlášení k AcquireToken, místo toho, aby se toto selhání opakovalo okamžitě. 

Zpracování chyb v nativních aplikacích lze definovat dvěma případy:

|  |  |
|------|-------------|
| **Případ 1**:<br>Chyba bez opakování (ve většině případů) | 1. nepokusit se o okamžité opakování Prezentujte uživatelské rozhraní koncového uživatele na základě konkrétní chyby, která vyvolá opakování (například "zkusit se znovu přihlásit" nebo "stáhnout aplikaci služby Azure AD Broker"). |
| **Případ 2**:<br>Opakovaná chyba | 1. proveďte jeden pokus znovu, protože koncový uživatel mohl zadat stav, který má za následek úspěch.<br><br>2. Pokud se to nepovede znovu, přihlaste uživatelské rozhraní koncového uživatele na základě konkrétní chyby, která vyvolá opakování (zkuste se znovu přihlásit, Stáhněte si aplikaci Azure AD Broker atd.). |

> [!IMPORTANT]
> Pokud se uživatelský účet předává do ADAL v tichém volání a dojde k selhání, pak následující interaktivní požadavek umožní koncovému uživateli přihlásit se pomocí jiného účtu. Po úspěšném AcquireToken pomocí uživatelského účtu musí aplikace ověřit, jestli přihlášený uživatel odpovídá objektu místního uživatele aplikace. Neshoda negeneruje výjimku (s výjimkou v cíli C), ale měla by být zvážena v případech, kdy je uživatel známý místně před požadavky na ověření (například neúspěšné tiché volání).
>

#### <a name="net"></a>.NET

Následující doprovodné materiály obsahují příklady zpracování chyb ve spojení se všemi netichými AcquireToken (...). ADAL – metody *s výjimkou*: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync(..., ClientAssertion, ...)
- AcquireTokenAsync (..., UserAssertion,...)   

Váš kód by byl implementován následujícím způsobem:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> Rozhraní ADAL .NET má další pozornost, protože podporuje PromptBehavior. Never, což má chování jako AcquireTokenSilent.
>

Následující doprovodné materiály obsahují příklady zpracování chyb ve spojení s metodami ADAL: 

- acquireToken (..., PromptBehavior. Never)

Váš kód by byl implementován následujícím způsobem:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

Následující doprovodné materiály obsahují příklady zpracování chyb ve spojení se všemi netichými AcquireToken (...). ADAL – metody. 

Váš kód by byl implementován následujícím způsobem:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

Následující doprovodné materiály obsahují příklady zpracování chyb ve spojení se všemi netichými AcquireToken (...). ADAL – metody. 

Váš kód by byl implementován následujícím způsobem:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Chybové případy a akční kroky: webové aplikace, které volají rozhraní API prostředků (.NET)

Pokud vytváříte webovou aplikaci .NET, která volá získání tokenu pomocí autorizačního kódu pro určitý prostředek, je vyžadován pouze kód výchozí obslužná rutina pro obecný případ. 

Následující doprovodné materiály obsahují příklady zpracování chyb ve spojení s metodami ADAL: 

- AcquireTokenByAuthorizationCodeAsync(...)

Váš kód by byl implementován následujícím způsobem:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Chybové případy a postupující akce: jednostránkové aplikace (adal.js)

Pokud vytváříte jednostránkovou aplikaci pomocí adal.js s AcquireToken, kód pro zpracování chyb je podobný jako typický tiché volání. Konkrétně v adal.js AcquireToken nikdy nezobrazuje uživatelské rozhraní. 

Neúspěšné AcquireToken má následující případy:

|  |  |
|------|-------------|
| **Případ 1**:<br>Přeložit pomocí interaktivního požadavku | 1. Pokud se přihlášení () nepovede, neprovádějte okamžité opakování. Zkuste to znovu jenom po akci uživatele s výzvou k opakování.|
| **Případ 2**:<br>Nelze přeložit s interaktivním požadavkem. Chyba je opakovaná. | 1. proveďte jeden pokus znovu, protože hlavní uživatel zadal stav, který má za následek úspěch.<br><br>2. Pokud se opakování nepovede, přeprezentujte koncovému uživateli akci na základě konkrétní chyby, která může vyvolat opakování (zkuste se znovu přihlásit). |
| **Případ 3**:<br>Nelze přeložit s interaktivním požadavkem. Chyba není opakovaná. | 1. nepokusit se o okamžité opakování Prezentovat koncovému uživateli akci v závislosti na konkrétní chybě, která může vyvolat opakování ("Zkuste se znovu přihlásit"). |

Váš kód by byl implementován následujícím způsobem:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Chybové případy a kroky, které lze provést: aplikace od služby po služby (jenom .NET)

Pokud vytváříte aplikaci Service-to-Service, která používá AcquireToken, je k dispozici několik klíčových chyb, které váš kód musí zpracovat. Jediným návratem k selhání je vrácení chyby zpět do volající aplikace (pro případy, kdy jsou v zastoupení), nebo použití strategie opakování. 

#### <a name="all-scenarios"></a>Všechny scénáře

Pro *všechny* scénáře aplikací služby na službu, včetně:

- Nepokoušejte se pokusit o okamžité opakování. ADAL se pokusí o jedno opakování u některých neúspěšných požadavků. 
- Pokračovat pouze v dalším pokusu po akci uživatele nebo aplikace se zobrazí dotaz na opakování. Například aplikace démona, která funguje v některém intervalu sady, by měla počkat do dalšího intervalu, než se zopakuje.

Následující doprovodné materiály obsahují příklady zpracování chyb ve spojení s metodami ADAL: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync(...,ClientAssertion, ...)
- AcquireTokenAsync(...,UserAssertion, ...)

Váš kód by byl implementován následujícím způsobem:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>Scénáře jménem

Pro *scénáře aplikací služby na službu* .

Následující doprovodné materiály obsahují příklady zpracování chyb ve spojení s metodami ADAL: 

- AcquireTokenAsync(..., UserAssertion, ...)

Váš kód by byl implementován následujícím způsobem:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Sestavili jsme [kompletní ukázku](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) , která demonstruje tento scénář.

## <a name="error-and-logging-reference"></a>Chyba a odkaz na protokolování

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Protokolování osobních identifikovatelných informací & identifikovatelné informace organizace 
Protokolování ADAL ve výchozím nastavení nezachycuje ani neprotokoluje žádné osobní údaje nebo identifikovatelné informace v organizaci. Knihovna umožňuje vývojářům aplikací tuto možnost zapínat prostřednictvím metody setter ve třídě protokolovacího nástroje. Když přihlásíte osobní identifikovatelné informace nebo organizační údaje, aplikace vezme zodpovědnost za bezpečné zpracování vysoce citlivých dat a dodržování zákonných požadavků.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Chyby knihovny ADAL

Aby bylo možné prozkoumat konkrétní chyby ADAL, je nejlepším odkazem na chybu zdrojový kód v [úložišti Azure-Active Directory-for-dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) .

#### <a name="guidance-for-error-logging-code"></a>Pokyny pro kód protokolování chyb

Změny protokolování ADAL .NET v závislosti na platformě, na které se pracovalo. Informace o tom, jak povolit protokolování, najdete na [wikiwebu protokolování](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) .

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Chyby knihovny ADAL

Aby bylo možné prozkoumat konkrétní chyby ADAL, je nejlepším odkazem na chybu zdrojový kód v [úložišti Azure-Active Directory-Library-for-Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) .

#### <a name="operating-system-errors"></a>Chyby operačního systému

Chyby OS Androidu jsou zpřístupněné prostřednictvím AuthenticationException – v ADAL, jsou identifikovatelné jako "SERVER_INVALID_REQUEST" a je možné je dále členit prostřednictvím popisů chyb. 

Úplný seznam běžných chyb a kroky, které je potřeba provést, když se jim aplikace nebo koncoví uživatelé dostanou, najdete na [wikiwebu na wikiwebu ADAL pro Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Pokyny pro kód protokolování chyb

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>Chyby knihovny ADAL

Pro zkoumání konkrétních chyb ADAL se jedná o nejlepší odkaz na zdrojový kód v [úložišti Azure-AD-Library-for-objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) .

#### <a name="operating-system-errors"></a>Chyby operačního systému

chyby iOS můžou vzniknout během přihlašování, když uživatelé používají Webová zobrazení, a povaha ověřování. To může být způsobeno podmínkami, jako jsou chyby TLS, vypršení časového limitu nebo chyby sítě:

- V případě sdílení oprávnění nejsou přihlášení trvalá a mezipaměť se zobrazí jako prázdná. Můžete vyřešit přidáním následujícího řádku kódu do řetězce klíčů: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- V případě NsUrlDomain sady chyb se akce mění v závislosti na logice aplikace. Konkrétní instance, které je možné zpracovat, najdete v [referenční dokumentaci k NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) .
- V tématu běžné problémy s modulem [ADAL obj. c](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) najdete seznam běžných chyb udržovaných týmem s přístupem ADAL v rámci objektivu.

#### <a name="guidance-for-error-logging-code"></a>Pokyny pro kód protokolování chyb

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Pokyny pro kód protokolování chyb – JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```

## <a name="related-content"></a>Související obsah

* [Knihovny ověřování Azure AD][AAD-Auth-Libraries]
* [Scénáře ověřování Azure AD][AAD-Auth-Scenarios]
* [Integrace aplikací s Azure Active Directory][AAD-Integrating-Apps]

Použijte níže uvedené komentáře, které vám poskytnou zpětnou vazbu a pomáhají nám zdokonalit a poobrazovat náš obsah.

[![Zobrazuje tlačítko Přihlásit se účtem Microsoft.][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Integrating-Apps]:../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

