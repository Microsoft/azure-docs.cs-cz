---
title: Zpracování chyb a výjimek v MSAL pro iOS/macOS
titleSuffix: Microsoft identity platform
description: Naučte se zpracovávat chyby a výjimky, problémy s deklarací identity podmíněného přístupu a opakované pokusy v MSAL pro aplikace iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, oldalton
ms.custom: aaddev
ms.openlocfilehash: f7f2abadb7586e1b19168eb46a54bad53caa05cc
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761108"
---
# <a name="handle-errors-and-exceptions-in-msal-for-iosmacos"></a>Zpracování chyb a výjimek v MSAL pro iOS/macOS

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-iosmacos"></a>Zpracování chyb v MSAL pro iOS/macOS

Úplný seznam chyb MSAL pro iOS a macOS najdete v části [výčet MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Všechny chyby MSAL jsou vráceny s `MSALErrorDomain` doménou.

V případě systémových chyb vrátí MSAL originál `NSError` z rozhraní API systému. Pokud se například získání tokenu nezdařilo z důvodu nedostatku síťového připojení, MSAL vrátí chybu s `NSURLErrorDomain` doménou a `NSURLErrorNotConnectedToInternet` kódem.

Na straně klienta doporučujeme zpracovat alespoň následující dvě chyby MSAL:

- `MSALErrorInteractionRequired`: Uživatel musí provést interaktivní požadavek. Existuje mnoho podmínek, které mohou vést k této chybě, jako je například relace ověřování s vypršenou platností nebo nutnost dalších požadavků na ověření. Pro obnovení zavolejte rozhraní API pro získání interaktivního tokenu MSAL. 

- `MSALErrorServerDeclinedScopes`: Některé nebo všechny obory byly odmítnuty. Rozhodněte, zda chcete pokračovat pouze s udělenými obory, nebo Zastavte proces přihlášení.

> [!NOTE]
> `MSALInternalError`Výčet by měl být použit pouze pro referenci a ladění. Nepokoušejte se tyto chyby automaticky zpracovat za běhu. Pokud vaše aplikace narazí na jakékoli chyby, `MSALInternalError` ke kterým došlo, můžete zobrazit obecné informace o uživateli, které objasňují, co se stalo.

Například `MSALInternalErrorBrokerResponseNotReceived` znamená, že uživatel nedokončil ověřování a ručně se vrátil do aplikace. V takovém případě by se měla v aplikaci zobrazit obecná chybová zpráva s vysvětlením, že ověřování nebylo dokončeno a navrhuje, aby se znovu pokusila o ověření.

Následující vzorový kód v jazyce C předvádí osvědčené postupy pro zpracování některých běžných chybových podmínek.

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

MSAL pro iOS a macOS umožňuje žádat o konkrétní deklarace ve scénářích interaktivního i tichého získání tokenu.

Chcete-li požádat o vlastní deklarace identity, zadejte `claimsRequest` v `MSALSilentTokenParameters` nebo `MSALInteractiveTokenParameters` .

Další informace najdete v tématu [žádosti o vlastní deklarace identity pomocí MSAL pro iOS a MacOS](request-custom-claims.md) .

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Další kroky

Zvažte možnost povolit [protokolování v MSAL pro iOS/MacOS](msal-logging-ios.md) , které vám pomůžou diagnostikovat a ladit problémy.
