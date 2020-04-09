---
title: Zpracování osvědčených postupů klientské aplikace ADAL | Azure
description: Poskytuje pokyny pro zpracování chyb a osvědčené postupy pro klientské aplikace ADAL.
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 8973412b2d6575d524874ba05b34af7661655e19
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981065"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Při zpracování osvědčených postupů pro klienty ADAL (Azure Active Directory) došlo k chybám.

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Tento článek obsahuje pokyny k typu chyb, které mohou vývojáři setkat při použití ADAL k ověření uživatelů. Při použití ADAL, existuje několik případů, kdy vývojář může potřebovat krok a zpracování chyb. Správné zpracování chyb zajišťuje skvělé prostředí pro koncové uživatele a omezuje počet, kolikrát se koncový uživatel potřebuje k přihlášení.

V tomto článku se podíváme na konkrétní případy pro každou platformu podporovanou ADAL a jak vaše aplikace může správně zpracovat každý případ. Pokyny k chybám jsou rozděleny do dvou širších kategorií na základě vzorů získávání tokenů poskytovaných rozhraními API ADAL:

- **AcquireTokenSilent**: Klient se pokusí získat token tiše (žádné ui) a může selhat, pokud ADAL je neúspěšný. 
- **AcquireToken**: Klient se může pokusit o tiché získání, ale může také provádět interaktivní požadavky, které vyžadují přihlášení.

> [!TIP]
> Při použití ADAL a Azure AD je vhodné protokolovat všechny chyby a výjimky. Protokoly jsou užitečné nejen pro pochopení celkového stavu vaší aplikace, ale jsou také důležité při ladění širší problémy. Zatímco vaše aplikace může zotavit z určitých chyb, mohou naznačovat širší problémy návrhu, které vyžadují změny kódu k vyřešení. 
> 
> Při implementaci chybových stavů uvedených v tomto dokumentu byste měli protokolovat kód chyby a popis z důvodů popsaných dříve. Příklady kódu protokolování naleznete v [odkazu Na chybu a protokolování.](#error-and-logging-reference) 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent pokusí získat token se zárukou, že koncový uživatel nevidí uživatelské rozhraní (UI). Existuje několik případů, kdy tiché získání může selhat a je třeba zpracovat prostřednictvím interaktivní požadavky nebo výchozí obslužné rutiny. Ponoříme se do specifik, kdy a jak zaměstnat každý případ v následujících částech.

Existuje sada chyb generovaných operačním systémem, které mohou vyžadovat zpracování chyb specifické pro aplikaci. Další informace naleznete v části Chyby operačního systému v [tématu Chyba a odkaz na protokolování](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scénáře aplikací

- [Nativní klientské](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#native-client) aplikace (iOS, Android, .NET Desktop nebo Xamarin)
- [Aplikace webového klienta](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#web-client) volající [prostředek](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Případy chyb a kroky, které lze žalovat

V zásadě existují dva případy chyb AcquireTokenSilent:

| Obchodní případ | Popis |
|------|-------------|
| **Případ 1**: Chyba je řešitelná pomocí interaktivního přihlášení | Pro chyby způsobené nedostatkem platných tokenů je nutný interaktivní požadavek. Konkrétně vyhledávání mezipaměti a neplatný/vypršela obnovovací token vyžadují volání AcquireToken k vyřešení.<br><br>V těchto případech musí být koncový uživatel vyzván k přihlášení. Aplikace se může rozhodnout provést interaktivní požadavek okamžitě, po interakci s koncovým uživatelem (například stisknutí přihlašovacího tlačítka) nebo později. Volba závisí na požadovaném chování aplikace.<br><br>Naleznete kód v následující části pro tento konkrétní případ a chyby, které diagnostikovat.|
| **Případ 2**: Chyba nelze řešit pomocí interaktivního přihlášení | U síťových a přechodných/dočasných chyb nebo jiných chyb nevyřeší problém provedení interaktivního požadavku AcquireToken. Zbytečné interaktivní přihlašovací výzvy mohou také zmařit koncové uživatele. ADAL se automaticky pokusí o jeden pokus o většinu chyb při selhání AcquireTokenSilent.<br><br>Klientská aplikace může také pokusit o opakování v určitém pozdějším okamžiku, ale kdy a jak je závislá na chování aplikace a požadované prostředí koncového uživatele. Například aplikace může provést ReTokenSilent opakování po několika minutách nebo v reakci na některé akce koncového uživatele. Okamžité opakování bude mít za následek aplikace je omezena a by neměl být pokus.<br><br>Následné opakování selhání se stejnou chybou neznamená, že klient by měl provést interaktivní požadavek pomocí AcquireToken, protože neřeší chybu.<br><br>Naleznete kód v následující části pro tento konkrétní případ a chyby, které diagnostikovat. |

### <a name="net"></a>.NET

Následující pokyny obsahují příklady pro zpracování chyb ve spojení s metodami ADAL: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [zastaralé] acquireTokenSilent(...)
- [zastaralé] získatTokenByRefreshToken(...) 

Váš kód by být implementovány takto:

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

Následující pokyny obsahují příklady pro zpracování chyb ve spojení s metodami ADAL: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- [zastaralé] acquireTokenSilent(...)

Váš kód by být implementovány takto:

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

Následující pokyny obsahují příklady pro zpracování chyb ve spojení s metodami ADAL: 

- acquireTokenSilentWithResource(...)

Váš kód by být implementovány takto:

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

## <a name="acquiretoken"></a>Získattoken

AcquireToken je výchozí metoda ADAL používaná k získání tokenů. V případech, kdy je vyžadována identita uživatele AcquireToken pokusí získat token tiše první, pak se zobrazí uživatelské rozhraní v případě potřeby (pokud PromptBehavior.Never je předán). V případech, kdy je vyžadována identita aplikace AcquireToken pokusí získat token, ale nezobrazuje uživatelské rozhraní, protože neexistuje žádný koncový uživatel. 

Při zpracování AcquireToken chyby zpracování chyb závisí na platformě a scénář aplikace se snaží dosáhnout. 

Operační systém může také generovat sadu chyb, které vyžadují zpracování chyb v závislosti na konkrétní aplikaci. Další informace naleznete v tématu Chyby operačního systému v [tématu Chyba a protokolování odkazu](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Scénáře aplikací

- Nativní klientské aplikace (iOS, Android, .NET Desktop nebo Xamarin)
- Webové aplikace, které volají rozhraní API prostředků (.NET)
- Jednostránkové aplikace (JavaScript)
- Aplikace servis-service (.NET, Java)
  - Všechny scénáře, včetně
  - Jménem konkrétních scénářů

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Chybové případy a kroky, které lze žalovat: Nativní klientské aplikace

Pokud vytváříte nativní klientskou aplikaci, je třeba zvážit několik případů zpracování chyb, které se týkají problémů se sítí, přechodných chyb a dalších chyb specifických pro platformu. Ve většině případů by aplikace neměla provádět okamžité opakování, ale spíše čekat na interakci s koncovým uživatelem, která vyzve k přihlášení. 

Existuje několik zvláštních případů, ve kterých může problém vyřešit jeden pokus. Například když uživatel potřebuje povolit data na zařízení nebo dokončení zprostředkovatele Azure AD stáhnout po počáteční selhání. 

V případě selhání aplikace může prezentovat uživatelské rozhraní, aby koncový uživatel mohl provést určitou interakci, která vyzve k opakování. Například pokud se zařízení nezdařilo pro chybu offline, tlačítko "Zkuste se přihlásit znovu" výzvu AcquireToken opakování spíše než okamžitě opakování selhání. 

Zpracování chyb v nativních aplikacích lze definovat dvěma případy:

|  |  |
|------|-------------|
| **Případ 1**:<br>Neopakovatelná chyba (ve většině případů) | 1. Nepokoušejte se o okamžitou akci. Prezentovat uživatelské rozhraní koncového uživatele na základě konkrétní chyby, která vyvolá opakování (například "Zkuste se přihlásit znovu" nebo "Stáhnout aplikaci zprostředkovatele Azure AD"). |
| **Případ 2**:<br>Opakovatelná chyba | 1. Proveďte jeden pokus, protože koncový uživatel pravděpodobně vstoupil do stavu, který má za následek úspěch.<br><br>2. Pokud se opakování nezdaří, prezentujte uživatelské rozhraní koncového uživatele na základě konkrétní chyby, která vyvolá opakování ("Zkuste se znovu přihlásit", "Stáhnout aplikaci zprostředkovatele Azure AD" atd.). |

> [!IMPORTANT]
> Pokud je uživatelský účet předán ADAL v tichém volání a selže, následný interaktivní požadavek umožňuje koncovému uživateli přihlásit pomocí jiného účtu. Po úspěšném AcquireToken pomocí uživatelského účtu aplikace musí ověřit, že přihlášený uživatel odpovídá objektu místního uživatele aplikace. Neshoda negeneruje výjimku (s výjimkou cíle C), ale měla by být zvážena v případech, kdy je uživatel místně znám před požadavky na ověření (například neúspěšné tiché volání).
>

#### <a name="net"></a>.NET

Následující pokyny obsahuje příklady pro zpracování chyb ve spojení se všemi netichými AcquireToken(...) Metody ADAL, *s výjimkou*: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(..., ClientCredential, ...)
- AcquireTokenAsync(..., ClientAssertion, ...)
- AcquireTokenAsync(..., UserAssertion,...)   

Váš kód by být implementovány takto:

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
> ADAL .NET má další pozornost, protože podporuje PromptBehavior.Never, který má chování jako AcquireTokenSilent.
>

Následující pokyny obsahují příklady pro zpracování chyb ve spojení s metodami ADAL: 

- acquireToken(..., PromptBehavior.Never)

Váš kód by být implementovány takto:

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

Následující pokyny obsahuje příklady pro zpracování chyb ve spojení se všemi netichými AcquireToken(...) ADAL metody. 

Váš kód by být implementovány takto:

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

Následující pokyny obsahuje příklady pro zpracování chyb ve spojení se všemi netichými AcquireToken(...) ADAL metody. 

Váš kód by být implementovány takto:

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

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Chybové případy a kroky, které lze přijmout: Webové aplikace, které volají rozhraní API pro prostředky (.NET)

Pokud vytváříte webovou aplikaci .NET, která volá, získá token pomocí autorizačního kódu pro prostředek, je jediným požadovaným kódem výchozí obslužná rutina pro obecný případ. 

Následující pokyny obsahují příklady pro zpracování chyb ve spojení s metodami ADAL: 

- AcquireTokenByAuthorizationCodeAsync(...)

Váš kód by být implementovány takto:

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

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Chybové případy a kroky, které lze přijmout: Jednostránkové aplikace (adal.js)

Pokud vytváříte jednostránkovou aplikaci pomocí adal.js s AcquireToken, kód zpracování chyb je podobný kódu typického tichého volání. Konkrétně v adal.js AcquireToken nikdy nezobrazuje uživatelské ho dohledu. 

Selhání AcquireToken má následující případy:

|  |  |
|------|-------------|
| **Případ 1**:<br>Řešitelné s interaktivním požadavkem | 1. Pokud login() selže, neprovádějte okamžitou akci. Opakujte akci pouze po akci uživatele vyzve opakování.|
| **Případ 2**:<br>Nelze řešit elné s interaktivním požadavkem. Chyba je opakovatelná. | 1. Proveďte jeden pokus, protože hlavní koncový uživatel vstoupil do stavu, který má za následek úspěch.<br><br>2. Pokud opakování selže, prezentujte koncovému uživateli akci založenou na konkrétní chybě, která může vyvolat opakování ("Zkuste se znovu přihlásit"). |
| **Případ 3**:<br>Nelze řešit elné s interaktivním požadavkem. Chyba není opakovatelná. | 1. Nepokoušejte se o okamžitou akci. Představit koncovému uživateli akci založenou na konkrétní chybě, která může vyvolat opakování ("Zkuste se přihlásit znovu"). |

Váš kód by být implementovány takto:

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

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Chybové případy a použitelné kroky: aplikace mezi službami (pouze rozhraní .NET)

Pokud vytváříte aplikaci služby služby, která používá AcquireToken, existuje několik klíčových chyb, které musí váš kód zpracovat. Jediným řešením selhání je vrátit chybu zpět do volající aplikace (pro telefonické případy) nebo použít strategii opakování. 

#### <a name="all-scenarios"></a>Všechny scénáře

Pro *všechny* scénáře aplikace mezi službami, včetně jménem:

- Nepokoušejte se o okamžitou akci. ADAL se pokusí o jeden pokus o opakování pro některé neúspěšné požadavky. 
- Opakování pokračujte pouze poté, co uživatel nebo akce aplikace vyzve k opakování. Například aplikace daemon, který funguje na některé nastavené interval by měl počkat až do dalšího intervalu opakovat.

Následující pokyny obsahují příklady pro zpracování chyb ve spojení s metodami ADAL: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(...,ClientCredential, ...)
- AcquireTokenAsync(...,ClientAssertion, ...)
- AcquireTokenAsync(...,UserAssertion, ...)

Váš kód by být implementovány takto:

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

#### <a name="on-behalf-of-scenarios"></a>Jménem scénářů

Pro *on-behalf-of* scénáře aplikace služby služby.

Následující pokyny obsahují příklady pro zpracování chyb ve spojení s metodami ADAL: 

- AcquireTokenAsync(..., UserAssertion, ...)

Váš kód by být implementovány takto:

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

Vytvořili jsme [kompletní ukázku,](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) která ukazuje tento scénář.

## <a name="error-and-logging-reference"></a>Odkaz na chybu a protokolování

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Zaznamenávání identifikovatelných osobních údajů & identifikovatelné organizační informace 
Ve výchozím nastavení protokolování ADAL nezachycuje ani nezaznamenává žádné osobní identifikovatelné informace nebo identifikovatelné informace organizace. Knihovna umožňuje vývojářům aplikací zapnout prostřednictvím setter ve třídě Logger. Protokolováním osobních identifikovatelných informací nebo identifikovatelných informací o organizaci přebírá aplikace odpovědnost za bezpečné nakládání s vysoce citlivými údaji a za dodržování jakýchkoli regulačních požadavků.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Chyby knihovny ADAL

Chcete-li prozkoumat konkrétní chyby ADAL, zdrojový kód v [azure-activedirectory-library-for-dotnet úložiště](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) je nejlepší odkaz na chybu.

#### <a name="guidance-for-error-logging-code"></a>Pokyny pro kód protokolování chyb

ADAL .NET protokolování se mění v závislosti na platformě, na které pracuje. Kód, jak protokolování povolit, najdete na [wiki protokolování.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net)

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Chyby knihovny ADAL

Chcete-li prozkoumat konkrétní chyby ADAL, zdrojový kód v [azure-activedirectory-library-for-android úložiště](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) je nejlepší odkaz na chybu.

#### <a name="operating-system-errors"></a>Chyby operačního systému

Android OS chyby jsou vystaveny prostřednictvím AuthenticationException v ADAL, jsou identifikovatelné jako "SERVER_INVALID_REQUEST" a může být dále podrobné prostřednictvím popisů chyb. 

Úplný seznam běžných chyb a jaké kroky je třeba provést, když se s nimi vaše aplikace nebo koncoví uživatelé setkají, najdete v [adalandroid wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

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

Chcete-li prozkoumat konkrétní chyby ADAL, zdrojový kód v [úložišti azure-activedirectory-library-for-objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) je nejlepší odkaz na chybu.

#### <a name="operating-system-errors"></a>Chyby operačního systému

Chyby iOS mohou nastat během přihlášení, když uživatelé používají webová zobrazení, a povahu ověřování. Příčinou mohou být podmínky, jako jsou chyby TLS, časové výtažky nebo chyby sítě:

- U sdílení nároků nejsou přihlášení trvalá a mezipaměť se zobrazí prázdná. Můžete vyřešit přidáním následujícířádek kódu do řetězce klíčů:`[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Pro sadu chyb NsUrlDomain se akce mění v závislosti na logice aplikace. Naleznete [nsURLErrorDomain referenční dokumentaci](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) pro konkrétní instance, které mohou být zpracovány.
- Seznam běžných chyb udržovaných týmem ADAL Objective-C naleznete v části [Běžné problémy ADAL Obj-C.](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror)

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

### <a name="guidance-for-error-logging-code---javascript"></a>Pokyny pro kód protokolování chyb - JavaScript 

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
* [Scénáře ověřování azure ad][AAD-Auth-Scenarios]
* [Integrace aplikací s Azure Active Directory][AAD-Integrating-Apps]

Použijte následující sekci komentářů, abyste poskytli zpětnou vazbu a pomohli nám vylepšit a utvářet náš obsah.

[![Zobrazuje tlačítko "Přihlásit se pomocí microsoftu"][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Integrating-Apps]:../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

