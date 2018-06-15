---
title: Chyba při zpracování osvědčené postupy pro klienty Azure Active Directory Authentication Library (ADAL)
description: Poskytuje pokyny a osvědčené postupy pro ADAL klientské aplikace pro zpracování chyb.
services: active-directory
documentationcenter: ''
author: danieldobalian
manager: mtillman
ms.author: celested
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.custom: ''
ms.openlocfilehash: 27315262ff64b640acc3af16a26fc3887d852a00
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157624"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Chyba při zpracování osvědčené postupy pro klienty Azure Active Directory Authentication Library (ADAL)

Tento článek obsahuje pokyny pro typ chyby, setkat vývojáři, při použití ADAL pro ověřování uživatelů. Při použití ADAL, existuje několik případů, kdy vývojář muset krok v a zpracování chyb. Zpracování chyb správné zajišťuje prostředí skvělé koncového uživatele a omezí počet časy, kdy koncový uživatel musí přihlásit.

V tomto článku jsme prozkoumat konkrétní případy pro každou platformu podporované ADAL a jak vaše aplikace může zpracovat každý případ správně. Chyba pokyny je rozdělená do dvou širší kategorií na základě tokenu pořízení způsobů poskytované ADAL rozhraní API:

- **AcquireTokenSilent**: klient se pokusí získat token tiše (bez uživatelského rozhraní) a může selhat, pokud neproběhne ADAL. 
- **AcquireToken**: klient se mohou pokusit tichou pořízení, ale můžete také provést interaktivní žádosti, které vyžadují přihlášení.

> [!TIP]
> Je vhodné do protokolu všechny chyby a výjimky při použití ADAL a Azure AD. Protokoly nejsou jenom užitečné pro pochopení celkového stavu aplikace, ale jsou důležité i při ladění širší problémy. Zatímco vaše aplikace může obnovení z některých chyb, může pomocného parametru v širší návrhu problémy, které vyžadují změny kódu za účelem vyřešení. 
> 
> Při implementaci chybové stavy v tomto dokumentu, je zapotřebí přihlásit kód chyby a popis důvodů uvedenými výše. Najdete v článku [chyby a odkaz na protokolování](#error-and-logging-reference) příklady kódu protokolování. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent, pokusí se získat token se záruku, že koncový uživatel nezná uživatelské rozhraní (UI). Existuje několik případů, kde může selhat tichou pořízení a musí být manipulaci prostřednictvím interaktivní žádostí, nebo výchozí obslužnou rutinu. Jsme ponořte do specifikace kdy a jak využívat každém případu v následujících částech.

Není sadu chybách vygenerovaných operačního systému, který může být chyba při zpracování specifické pro aplikaci. Další informace najdete v kapitole "Operační systém" chyby v [chyby a odkaz na protokolování](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scénáře aplikací

- [Nativní klient](active-directory-dev-glossary.md#native-client) aplikacemi (iOS, Android, plocha rozhraní .NET nebo Xamarin)
- [Webový klient](active-directory-dev-glossary.md#web-client) aplikace volání [prostředků](active-directory-dev-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Chyba případy a možné použít kroky

Zásadně jsou dva případy AcquireTokenSilent chyb:

| Případ | Popis |
|------|-------------|
| **Případ 1**: Chyba je přeložit interaktivní přihlášení | Pro chyby způsobená chybějícím platné tokeny je nutné interaktivní žádosti. Konkrétně vyhledávání v mezipaměti a tokenu vypršela platnost nebo neplatná aktualizace vyžadují volání AcquireToken vyřešit.<br><br>V těchto případech koncový uživatel musí být vyzváni k přihlášení. Aplikace můžete provést požadavek interaktivní okamžitě, po interakci s koncovým uživatelem (například stiskne tlačítko přihlášení) nebo novější. Výběr závisí na požadované chování aplikace.<br><br>Zobrazit kód v následující části pro tento konkrétní případ a chybách, ke kterým ji diagnostikovat.|
| **Případ 2**: Chyba není přeložit interaktivní přihlášení | Pro síť a přechodná nebo dočasných chyby nebo jiné chyby provádění požadavek interaktivní AcquireToken problém nevyřeší. Nepotřebné interaktivní přihlašování vyzve můžete také frustrovat koncovým uživatelům. ADAL automaticky pokusí jeden opakování pro většinu chyby na AcquireTokenSilent selhání.<br><br>Klientská aplikace může také pokusí akci opakovat v určitém okamžiku novější, ale kdy a jak to udělat, je závislá na chování aplikace a činnost požadovaného koncového uživatele. Aplikace může například provádět AcquireTokenSilent opakovaných pokusů, po několik minut, nebo v reakci na některé akce koncového uživatele. Okamžitou opakování způsobí aplikace omezené a nesmí být pokus.<br><br>Následné opakování došlo k chybě stejné neznamená, že klient má provést požadavek interaktivní pomocí AcquireToken, jak nevyřeší chyba.<br><br>Zobrazit kód v následující části pro tento konkrétní případ a chybách, ke kterým ji diagnostikovat. |

### <a name="net"></a>.NET

Následující pokyny jsou uvedeny příklady pro zpracování ve spojení s ADAL metody chyb: 

- acquireTokenSilentAsync(…)
- acquireTokenSilentSync(…) 
- [zastaralé] acquireTokenSilent(...)
- [zastaralé] acquireTokenByRefreshToken(...) 

Váš kód by být implementováno takto:

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

Následující pokyny jsou uvedeny příklady pro zpracování ve spojení s ADAL metody chyb: 

- acquireTokenSilentSync(…)
- acquireTokenSilentAsync(...)
- [zastaralé] acquireTokenSilent(...)

Váš kód by být implementováno takto:

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

Následující pokyny jsou uvedeny příklady pro zpracování ve spojení s ADAL metody chyb: 

- acquireTokenSilentWithResource(...)

Váš kód by být implementováno takto:

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

AcquireToken je výchozí metodou ADAL použije k získání tokeny. V případech, kdy je potřeba identitu uživatele AcquireToken pokusí se získat token bezobslužně první a potom zobrazí uživatelské rozhraní v případě potřeby (Pokud je předán PromptBehavior.Never). V případech, kdy je potřeba identita aplikace AcquireToken pokusí se získat token, ale nezobrazí uživatelského rozhraní, protože neexistuje žádný koncový uživatel. 

Při zpracování chyb AcquireToken, je závislý na platformě zpracování chyb a scénář aplikace se pokouší dosáhnout. 

Operační systém můžete také vygenerovat sadu chyb, které vyžadují chyba zpracování závisí na konkrétní aplikaci. Další informace najdete v části "Chyby operačního systému" v [chyby a odkaz na protokolování](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scénáře aplikací

- Nativní klientské aplikace (iOS, Android, plocha .NET nebo Xamarin)
- Webové aplikace, které volají prostředek rozhraní API (.NET)
- Jednostránkové aplikace (JavaScript)
- Service-to-Service aplikace (.NET, Java)
  - Všechny scénáře, včetně on-behalf-of
  - On-Behalf-of konkrétních scénářů

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Chyba případy a možné použít kroky: nativní klientské aplikace

Pokud již vytváříte nativní klientskou aplikaci, existuje několik případů chyba zpracování vzít v úvahu, které se týkají problémů se sítí, přechodných chyb a dalších chyb specifické pro platformu. Ve většině případů by neměl aplikace provést okamžitou opakování, ale spíš počkejte interakci s koncovým uživatelem, který zobrazí výzvu přihlášení. 

Existuje několik zvláštních případech, ve kterých jeden opakování může problém vyřešit. Například pokud uživatel musí povolit dat na zařízení, nebo byla dokončena zprostředkovatele služby Azure AD stahování po počáteční selhání. 

V případě selhání může být aplikace uživatelského rozhraní umožňuje koncovému uživateli umožňují provádět některé interakce vyzývající zkuste to znovu. Například pokud se zařízení se nezdařila pro chybu offline, tlačítko "Pokuste se znovu přihlaste" výzvy AcquireToken opakujte místo okamžitě opakování selhání. 

Zpracování chyb v nativních aplikací je možné definovat ve dvou případech:

|  |  |
|------|-------------|
| **Případ 1**:<br>Neopakovatelná chyba (většinou) | 1. Nepokoušejte okamžitou opakování. K dispozici koncového uživatele, uživatelského rozhraní v závislosti na specifické chybě, která volá opakování ("Opakujte k přihlášení", "Stažení služby Azure AD zprostředkovatele aplikace", atd). |
| **Případ 2**:<br>Opakovatelná chyba | 1. Proveďte jeden opakování, jak koncový uživatel pravděpodobně přešla do stavu, jejímž výsledkem úspěšné.<br><br>2. Pokud se nezdaří opakování, k dispozici koncového uživatele, uživatelského rozhraní v závislosti na specifické chybě, která volá opakování ("Opakujte k přihlášení", "Stažení služby Azure AD zprostředkovatele aplikace", atd.). |

> [!IMPORTANT]
> Pokud je uživatelský účet je předán ADAL v tichém volání a selže, další interaktivní požadavek umožňuje koncový uživatel se přihlásit pomocí jiného účtu. Po úspěšné AcquireToken pomocí uživatelského účtu musí aplikace ověřte, zda že přihlášeného uživatele odpovídá objekt místního uživatele aplikacích. Neshoda negeneruje výjimku (s výjimkou v Objective C), ale měli považovat za v případech, kde je uživatel známý místně před žádosti o ověření (např. nezdařeného volání tichou).
>

#### <a name="net"></a>.NET

Následující pokyny jsou uvedeny příklady pro zpracování ve spojení s všechny AcquireToken(...) tichý režim chyb ADAL metody *s výjimkou*: 

- AcquireTokenAsync (..., IClientAssertionCertification,...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync(...,UserAssertion,...)   

Váš kód by být implementováno takto:

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
> ADAL .NET má další pozornost, jako podporuje PromptBehavior.Never, který má chování jako AcquireTokenSilent.
>

Následující pokyny jsou uvedeny příklady pro zpracování ve spojení s ADAL metody chyb: 

- acquireToken(..., PromptBehavior.Never)

Váš kód by být implementováno takto:

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

Následující pokyny jsou uvedeny příklady pro zpracování ve spojení s všechny AcquireToken(...) tichý režim chyb ADAL metody. 

Váš kód by být implementováno takto:

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

Následující pokyny jsou uvedeny příklady pro zpracování ve spojení s všechny AcquireToken(...) tichý režim chyb ADAL metody. 

Váš kód by být implementováno takto:

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

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Chyba případy a možné použít kroky: webové aplikace, které volají prostředek rozhraní API (.NET)

Pokud vytváříte webové aplikace .NET, která volá získá token pomocí autorizační kód pro prostředek, vyžaduje pouze kód je výchozí obslužnou rutinu pro obecný případ. 

Následující pokyny jsou uvedeny příklady pro zpracování ve spojení s ADAL metody chyb: 

- AcquireTokenByAuthorizationCodeAsync(...)

Váš kód by být implementováno takto:

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

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Chyba případy a možné použít kroky: jednostránkové aplikace (adal.js)

Pokud jste vytvoření jednostránkové aplikace pomocí adal.js AcquireToken, je podobná typické tichou volání kód pro zpracování chyb. Konkrétně v adal.js AcquireToken už nebude zobrazovat uživatelského rozhraní. 

Neúspěšné AcquireToken má v následujících případech:

|  |  |
|------|-------------|
| **Případ 1**:<br>Přeložit interaktivní požadavku | 1. V případě selhání login() neprovádět okamžitou opakování. Pouze po akce uživatele vyzve opakovaný pokus opakujte.|
| **Případ 2**:<br>Není Resolvable s požadavkem na interaktivní. Je Opakovatelná chyba. | 1. Proveďte jeden opakování jako hlavní koncového uživatele zadali stavu, který vede k úspěchu.<br><br>2. V případě selhání opakování prezentovat koncovému uživateli akci v závislosti na specifické chybě, můžete vyvolat opakování ("zkuste se znovu přihlásit"). |
| **Případ 3**:<br>Není Resolvable s požadavkem na interaktivní. Není Opakovatelná chyba. | 1. Nepokoušejte okamžitou opakování. Koncový uživatel představovat akci v závislosti na specifické chybě, můžete vyvolat opakování ("zkuste se znovu přihlásit"). |

Váš kód by být implementováno takto:

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

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Chyba případy a možné použít kroky: service-to-service aplikace (jenom .NET)

Pokud vytváříte aplikace service-to-service, která používá AcquireToken, existuje několik chyb klíčů, které váš kód musí zpracovat. Pouze obrátit na selhání je vrátí chybu zpět do volání aplikace (pro případy on-behalf-of) nebo použít strategie opakování. 

#### <a name="all-scenarios"></a>Všechny scénáře

Pro *všechny* scénáře pro služby aplikací, včetně on-behalf-of:

- Nepokoušejte okamžitou opakování. ADAL pokusy o jeden opakujte pro určité chybné žádosti. 
- Pokračujte pouze v opakování akce uživatele nebo aplikace po výzvy zkuste to znovu. Démon aplikace, která funguje na některé nastavený interval by například počkejte na další interval, opakujte.

Následující pokyny jsou uvedeny příklady pro zpracování ve spojení s ADAL metody chyb: 

- AcquireTokenAsync (..., IClientAssertionCertification,...)
- AcquireTokenAsync (..., ClientCredential,...)
- AcquireTokenAsync (..., ClientAssertion,...)
- AcquireTokenAsync(…,UserAssertion, …)

Váš kód by být implementováno takto:

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

#### <a name="on-behalf-of-scenarios"></a>On-behalf-of scénáře

Pro *on-behalf-of* scénáře pro služby aplikací.

Následující pokyny jsou uvedeny příklady pro zpracování ve spojení s ADAL metody chyb: 

- AcquireTokenAsync(…, UserAssertion, …)

Váš kód by být implementováno takto:

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

Využili jsme [ucelenou ukázku](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) , představuje tento scénář.

## <a name="error-and-logging-reference"></a>Protokolování chyb a referenční dokumentace

### <a name="logging-personal-identifiable-information-pii--organizational-identifiable-information-oii"></a>Protokolování osobní identifikovatelné údaje (PII) & organizační osobní údaje (OII)
Ve výchozím nastavení ADAL protokolování nemá zachycení nebo protokolu žádné identifikovatelné osobní údaje nebo OII. Knihovny umožňuje vývojářům aplikací tuto možnost zapnout pomocí nastavení ve třídě protokolovacího nástroje. Když zapnete identifikovatelné osobní údaje nebo OII, trvá aplikace odpovědnost za bezpečně zpracování vysoce citlivá data, které splňují všechny zákonné požadavky.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Chyby knihovny ADAL

Prozkoumat konkrétní chyby ADAL, zdrojový kód v [úložiště azure-Active Directory – knihovna pro dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) je odkaz na nejlepší chyby.

#### <a name="guidance-for-error-logging-code"></a>Pokyny pro protokolování kód chyby

Protokolování ADAL .NET změny v závislosti na platformě pracuje. Odkazovat [protokolování wiki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) pro kód na tom, jak povolit protokolování.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Chyby knihovny ADAL

Prozkoumat konkrétní chyby ADAL, zdrojový kód v [úložiště azure-Active Directory – knihovna for-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) je odkaz na nejlepší chyby.

#### <a name="operating-system-errors"></a>Chyby operačního systému

Android chyby operačního systému jsou k dispozici prostřednictvím authenticationexception – v ADAL, lze identifikovat jako "SERVER_INVALID_REQUEST" a může být další podrobné prostřednictvím popisy chyb. 

Úplný seznam běžných chyb a kroky při aplikace nebo koncoví uživatelé dojde k jejich, najdete v části [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Pokyny pro protokolování kód chyby

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

Prozkoumat konkrétní chyby ADAL, zdrojový kód v [úložiště azure-Active Directory – knihovna pro objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) je odkaz na nejlepší chyby.

#### <a name="operating-system-errors"></a>Chyby operačního systému

iOS chyby mohou vzniknout při přihlášení, uživatelům při používání webové zobrazení a povaha ověřování. Může to být způsobeno podmínky například chyby SSL, překročení časového limitu nebo chyby sítě:

- Pro nárok sdílení přihlášení nejsou trvalé a mezipaměti se zobrazí prázdné. Můžete vyřešit přidáním následující řádek kódu do řetězce klíčů: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Pro sadu NsUrlDomain chyby změní akce v závislosti na logice aplikace. Najdete v článku [NSURLErrorDomain referenční dokumentaci k nástroji](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) pro určité instance, které mohou být zpracovány.
- V tématu [běžných problémů s ADAL Obj-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) seznam běžných chyb udržované týmem ADAL Objective-C.

#### <a name="guidance-for-error-logging-code"></a>Pokyny pro protokolování kód chyby

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

### <a name="guidance-for-error-logging-code---javascript"></a>Pokyny k protokolování chyb kódu - JavaScript 

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

* [Průvodce Azure AD vývojáře][AAD-Dev-Guide]
* [Knihovny ověřování služby Azure AD][AAD-Auth-Libraries]
* [Scénáře ověřování služby Azure AD][AAD-Auth-Scenarios]
* [Integrace aplikací s Azure Active Directory][AAD-Integrating-Apps]

Použijte následující k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah části komentáře.

[![Přihlaste se tlačítko][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->
[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

