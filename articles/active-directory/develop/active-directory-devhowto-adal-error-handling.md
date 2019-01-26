---
title: Osvědčené postupy pro klienty Azure Active Directory Authentication Library (ADAL) pro zpracování chyb
description: Poskytuje pokyny a osvědčené postupy pro ADAL klientské aplikace pro zpracování chyb.
services: active-directory
documentationcenter: ''
author: danieldobalian
manager: mtillman
ms.author: celested
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.custom: ''
ms.openlocfilehash: e39c2716cd31e97ad71764f25befc1fc6e47e2a0
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079031"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Osvědčené postupy pro klienty Azure Active Directory Authentication Library (ADAL) pro zpracování chyb

Tento článek obsahuje pokyny pro typ chyby, že vývojáři mohou nastat, když používá ADAL k ověřování uživatelů. Při použití knihovny ADAL, existuje několik případů, kde může vývojář potřebovat a zpracování chyb. Zpracování chyb správné zajišťuje skvělé koncovým a omezuje počet pokusů, které koncový uživatel musí přihlásit.

V tomto článku se podíváme na konkrétní případů pro jednotlivé platformy podporované ADAL a jak vaše aplikace dokáže zpracovat každý případ správně. Pokyny k chybě je rozdělený do dvou širší kategorií na základě způsobů získání tokenu služby poskytované rozhraní API pro ADAL:

- **AcquireTokenSilent**: Klient se pokusí získat token tiše (bez uživatelského rozhraní) a může selhat, pokud neproběhne úspěšně ADAL. 
- **AcquireToken**: Klient se může pokusit tiché pořízení, ale můžete také provádět interaktivní požadavky, které vyžadují přihlášení.

> [!TIP]
> Je vhodné protokolovat všechny chyby a výjimky a při použití ADAL a Azure AD. Protokoly nejsou pouze užitečné k pochopení celkového stavu aplikace, ale jsou důležité i při ladění širší problémy. Když vaše aplikace může obnovení z některých chyb, může pomocného parametru na širší problémy návrhu, které vyžadovat změny kódu. aby bylo možné přeložit. 
> 
> Pokud implementace chybové stavy zahrnuté v tomto dokumentu, by měla protokolovat kód chyby a popis kvůli jak jsme vysvětlili výše. Zobrazit [chyba a odkaz na protokolování](#error-and-logging-reference) příklady kódu protokolování. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent pokusí se získat token se zárukou, že koncový uživatel neuvidí uživatelské rozhraní (UI). Existuje několik případů, kdy může selhat pasivní získávání a je potřeba zpracovat pomocí interaktivních požadavků nebo výchozí obslužnou rutinou. Začneme zabývat nespecifikuje, kdy a jak využívat každý případ v následujících částech.

Existuje sada chyby vygenerované nástrojem operačního systému, může být nutné specifické pro aplikace pro zpracování chyb. Další informace najdete v části "Operační systém" chyby v [chyba a odkaz na protokolování](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scénáře aplikací

- [Nativní klient systému](developer-glossary.md#native-client) aplikací (iOS, Android, .NET Desktop nebo Xamarin)
- [Webový klient](developer-glossary.md#web-client) volání aplikace [prostředků](developer-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Případy chyb a praktické kroky

Zásadním způsobem existují dva případy AcquireTokenSilent chyb:

| Případ | Popis |
|------|-------------|
| **Případ 1**: Došlo k chybě je možné přeložit pomocí interaktivnímu přihlášení | Chyby způsobené chybějící platné tokeny je nutné interaktivní žádosti. Konkrétně vyhledávání v mezipaměti a tokenu vypršela platnost nebo neplatná aktualizace vyžadují volání rozhraní AcquireToken vyřešit.<br><br>V těchto případech koncový uživatel musí být vyzváni k přihlášení. Aplikace můžete provádět interaktivní žádosti okamžitě po interakce s koncovým uživatelem (například klepnutím tlačítko Přihlásit) nebo novější. Výběr závisí na požadované chování aplikace.<br><br>Zobrazit kód v následující části pro tento konkrétní případ a chyb, které ji diagnostikovat.|
| **Případ 2**: Chyba není možné přeložit pomocí interaktivnímu přihlášení | Síť a přechodná nebo dočasná chyby nebo jiné chyby, provádí požadavek interaktivní AcquireToken problém nevyřeší. Zbytečné interaktivní výzvy k přihlášení můžete také frustrovat koncovým uživatelům. ADAL automaticky pokusí jednoho opakování pro většinu chyb v AcquireTokenSilent selhání.<br><br>Klientská aplikace může také pokusí zkuste to znovu později, ale kdy a jak na to závisí na chování aplikace a činnost koncového uživatele požadované. Aplikace například můžete provést AcquireTokenSilent zkuste to znovu za pár minut, nebo v reakci na určitou akci koncového uživatele. Okamžité opakování způsobí aplikace dochází k omezení a nesmí se pokusit.<br><br>Dalším pokusem služeb při selhání ke stejné chybě neznamená, že klient by měl provést požadavek interaktivní pomocí AcquireToken, jako chyba nevyřeší.<br><br>Zobrazit kód v následující části pro tento konkrétní případ a chyb, které ji diagnostikovat. |

### <a name="net"></a>.NET

Následující pokyny jsou uvedeny příklady pro zpracování chyb v společně s metodami ADAL: 

- acquireTokenSilentAsync(…)
- acquireTokenSilentSync(…) 
- [zastaralé] acquireTokenSilent(...)
- [zastaralé] acquireTokenByRefreshToken(...) 

Váš kód by implementován takto:

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

Následující pokyny jsou uvedeny příklady pro zpracování chyb v společně s metodami ADAL: 

- acquireTokenSilentSync(…)
- acquireTokenSilentAsync(...)
- [zastaralé] acquireTokenSilent(...)

Váš kód by implementován takto:

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

Následující pokyny jsou uvedeny příklady pro zpracování chyb v společně s metodami ADAL: 

- acquireTokenSilentWithResource(...)

Váš kód by implementován takto:

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

AcquireToken je výchozí metodou ADAL použít k získání tokenů. AcquireToken v případech, kdy je požadovaná identita uživatele, pokusí se získat token tiše první pak zobrazí uživatelské rozhraní v případě potřeby (Pokud je předán PromptBehavior.Never). V případech, kdy je požadovaná identita aplikace AcquireToken pokusí se získat token, ale nezobrazí uživatelské rozhraní, protože neexistuje žádný koncový uživatel. 

Při zpracování chyb AcquireToken, zpracování chyb je závislý na platformě a scénář aplikace se snaží dosáhnout. 

Operační systém můžete vygenerovat také sadu chyby, které vyžadují závisí na konkrétní aplikaci pro zpracování chyb. Další informace najdete v tématu "Chyby operačního systému" [chyba a odkaz na protokolování](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scénáře aplikací

- Nativní klientské aplikace (iOS, Android, .NET Desktop nebo Xamarin)
- Webové aplikace, které volají prostředku rozhraní API (.NET)
- Jednostránkové aplikace (JavaScript)
- Služba-služba aplikace (.NET, Java)
  - Všechny scénáře, včetně on-behalf-of
  - On-Behalf-of konkrétních scénářů

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Případy chyb a praktické kroky: Nativní klientské aplikace

Pokud už vytváříte nativní klientskou aplikaci, existuje několik případů zpracování chyb vzít v úvahu, které se týkají problémy se sítí, přechodná selhání a další chyby specifické pro platformu. Ve většině případů by neměl aplikace provést okamžité opakování, ale místo toho počkejte interakce s koncovým uživatelem, který vyzve k přihlášení. 

Existuje několik zvláštní případy, ve kterých jednoho opakování může problém vyřešit. Například když uživatel musí povolit dat na zařízení, nebo dokončení zprostředkovatele služby Azure AD stáhnout po počáteční selhání. 

V případě selhání ale aplikace může představovat uživatelského rozhraní umožňující koncovému uživateli umožňují provádět některé interakce s výzvou zkuste to znovu. Například pokud zařízení se nepodařilo chybu v režimu offline, s výzvou AcquireToken tlačítko "Zkuste se znovu přihlásit" opakovat místo okamžitě opakování selhání. 

Zpracování chyb v nativních aplikací lze definovat ve dvou případech:

|  |  |
|------|-------------|
| **Případ 1**:<br>Neopakovatelná chyba (většinou) | 1. Nepokoušejte okamžité opakování. K dispozici koncového uživatele, uživatelského rozhraní v závislosti na specifické chybě, která volá opakování ("Zkuste to znovu se přihlaste", "Stažení služby Azure AD zprostředkovatele aplikace" atd.). |
| **Případ 2**:<br>Došlo k opakovatelné chybě | 1. Provedení jednoho opakování jako koncový uživatel pravděpodobně přešla do stavu, která vede k úspěchu.<br><br>2. Pokud znovu selže, k dispozici koncového uživatele, uživatelského rozhraní v závislosti na specifické chybě, která volá opakování ("Zkuste to znovu se přihlaste", "Stažení služby Azure AD zprostředkující aplikace" atd.). |

> [!IMPORTANT]
> Pokud uživatelský účet je předán ADAL v tichém volání a selže, další interaktivní požadavek umožňuje koncovému uživateli se přihlásit pomocí jiného účtu. Po úspěšném AcquireToken pomocí uživatelského účtu musí aplikace ověřte, zda že přihlášeného uživatele odpovídá objekt místní uživatele v aplikacích. Neshoda negeneruje výjimku (s výjimkou v Objective C), ale měli vzít v úvahu v případech, kde je uživatel známý místně před žádosti o ověření (třeba nezdařeného volání tichou).
>

#### <a name="net"></a>.NET

Následující pokyny jsou uvedeny příklady pro zpracování ve spojení s všechny AcquireToken(...) tichý režim chyb ADAL metody *s výjimkou*: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion,…)   

Váš kód by implementován takto:

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
> Podporuje PromptBehavior.Never, jehož chování jako AcquireTokenSilent ADAL .NET má zvláštní pozornost.
>

Následující pokyny jsou uvedeny příklady pro zpracování chyb v společně s metodami ADAL: 

- acquireToken(..., PromptBehavior.Never)

Váš kód by implementován takto:

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

Váš kód by implementován takto:

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

Váš kód by implementován takto:

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

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Případy chyb a praktické kroky: Webové aplikace, které volají prostředku rozhraní API (.NET)

Využijete při vytváření webové aplikace .NET, která volá získá token pomocí autorizačního kódu pro určitý prostředek, je požadován pouze kód je výchozí obslužnou rutinu pro případ, Obecné. 

Následující pokyny jsou uvedeny příklady pro zpracování chyb v společně s metodami ADAL: 

- AcquireTokenByAuthorizationCodeAsync(…)

Váš kód by implementován takto:

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

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Případy chyb a praktické kroky: Jednostránkové aplikace (adal.js)

Pokud vytváříte jednostránkové aplikace s využitím adal.js s AcquireToken, kód pro zpracování chyb je podobná typické tiché volání. Konkrétně v adal.js AcquireToken už nebude zobrazovat uživatelské rozhraní. 

Neúspěšné AcquireToken má následující případy:

|  |  |
|------|-------------|
| **Případ 1**:<br>Přeložit s interaktivní žádosti | 1. Pokud se nezdaří login() neprovádějte okamžité opakování. Opakujte jenom po akce uživatele vyzve k opakování.|
| **Případ 2**:<br>Není Resolvable s požadavkem na interaktivní. Je Opakovatelná chyba. | 1. Provedení jednoho opakování jako hlavní koncový uživatel zadali stavu, který vede k úspěchu.<br><br>2. Pokud znovu selže, prezentovat koncovému uživateli se akce v závislosti na specifické chybě, můžete vyvolat opakování ("zkuste se znovu přihlásit"). |
| **Případ 3**:<br>Není Resolvable s požadavkem na interaktivní. Není Opakovatelná chyba. | 1. Nepokoušejte okamžité opakování. Prezentovat koncovému uživateli se akce v závislosti na specifické chybě, můžete vyvolat opakování ("zkuste se znovu přihlásit"). |

Váš kód by implementován takto:

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

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Případy chyb a praktické kroky: Služba služba aplikace (pouze .NET)

Pokud už vytváříte aplikace služba služba, která používá AcquireToken, existuje několik chyb klíčů, kterou musí zvládnout váš kód. Pouze obrátit na selhání je vrátí chybu, vraťte se do volání aplikace (pro on-behalf-of případů) nebo použijte strategii opakování. 

#### <a name="all-scenarios"></a>Všechny scénáře

Pro *všechny* service to service aplikačních scénářů, včetně on-behalf-of:

- Nepokoušejte okamžité opakování. ADAL pokusů o zadání jednoho opakování pro určité neúspěšných požadavků. 
- Pokračujte pouze v opakování akce uživatele nebo aplikaci po výzvy zkuste to znovu. Démon aplikaci, která funguje na některé nastavit interval by měl třeba počkat na další interval opakování.

Následující pokyny jsou uvedeny příklady pro zpracování chyb v společně s metodami ADAL: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion, …)

Váš kód by implementován takto:

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

Pro *on-behalf-of* scénáře aplikací service-to-service.

Následující pokyny jsou uvedeny příklady pro zpracování chyb v společně s metodami ADAL: 

- AcquireTokenAsync(…, UserAssertion, …)

Váš kód by implementován takto:

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

Sestavili jsme [úplnou ukázku](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) , kde uvidíte tento scénář.

## <a name="error-and-logging-reference"></a>Protokolování chyb a odkaz

### <a name="logging-personal-identifiable-information-pii--organizational-identifiable-information-oii"></a>Protokolování osobní identifikovatelné údaje (PII) & organizační údaje (OII)
Ve výchozím nastavení ADAL protokolování zachycení ani protokolů všechny identifikovatelné osobní údaje nebo OII. Knihovny umožňuje vývojářům zapnout prostřednictvím metody setter ve třídě protokolovacího nástroje. Když zapnete identifikovatelné osobní údaje nebo OII, aplikace přebírá odpovědnost za bezpečně zpracování vysoce citlivá data, které splňují všechny zákonné požadavky.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Knihovna ADAL chyby

Prozkoumat konkrétní ADAL chyby, zdrojový kód v [úložiště azure-activedirectory knihovny pro dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) je nejlepší odkaz chyby.

#### <a name="guidance-for-error-logging-code"></a>Pokyny pro kód protokolování chyb

Protokolování ADAL .NET mění v závislosti na platformě se pracuje. Odkazovat [protokolování wiki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) pro kód o tom, jak povolit protokolování.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Knihovna ADAL chyby

Prozkoumat konkrétní ADAL chyby, zdrojový kód v [úložiště azure-activedirectory knihovny for-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) je nejlepší odkaz chyby.

#### <a name="operating-system-errors"></a>Chyby operačního systému

Android chyby operačního systému jsou přístupné prostřednictvím authenticationexception – v ADAL, identifikovat jako "SERVER_INVALID_REQUEST" a může být dále detailní prostřednictvím popisy chyb. 

Úplný seznam běžných chyb a popíše, co se má provést, když vaše aplikace nebo koncoví uživatelé dotknou, najdete [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

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

#### <a name="adal-library-errors"></a>Knihovna ADAL chyby

Prozkoumat konkrétní ADAL chyby, zdrojový kód v [úložiště azure-activedirectory-library pro objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) je nejlepší odkaz chyby.

#### <a name="operating-system-errors"></a>Chyby operačního systému

chyb zařízení s Iosem může nastat při přihlašování, uživatelé budou používat webové zobrazení a povaze ověřování. Může to být způsobeno podmínky, třeba chyby protokolu SSL, vypršení časového limitu nebo chyby sítě:

- Oprávnění pro sdílení obsahu přihlašovací jména nejsou trvalé a mezipaměti se zobrazí prázdné. Lze vyřešit tak, že přidáte následující řádek kódu do řetězce klíčů: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Pro sadu chyby NsUrlDomain akce mění v závislosti na aplikaci logiky. Zobrazit [NSURLErrorDomain referenční dokumentaci](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) pro konkrétní instance, které mohou být zpracovány.
- Zobrazit [ADAL běžné problémy s Obj C](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) seznam běžných chyb udržované týmem ADAL Objective-C.

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

### <a name="guidance-for-error-logging-code---javascript"></a>Pokyny k protokolování chyb kódu – JavaScript 

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

* [Průvodce vývojáře Azure AD][AAD-Dev-Guide]
* [Knihovny ověřování služby Azure AD][AAD-Auth-Libraries]
* [Scénáře ověřování služby Azure AD][AAD-Auth-Scenarios]
* [Integrace aplikací s Azure Active Directory][AAD-Integrating-Apps]

Pomocí následujících pokynů můžete svůj názor a Pomozte nám vylepšit a náš obsah obrazce oddílu pro komentáře.

[![Přihlaste se tlačítko][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

