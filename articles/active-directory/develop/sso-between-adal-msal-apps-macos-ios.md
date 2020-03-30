---
title: Připojovací služby mezi ADAL & aplikace Misal (iOS/macOS) – platforma identit microsoftu | Azure
description: ''
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 2fbb6e837ae898daf4bc78d5cccc75660463e8a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085414"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Postup: Přizpůsobovat služby při přijmutí mezi aplikacemi ADAL a MSAL v systému macOS a iOS

Knihovna ověřování Microsoft (MSAL) pro iOS můžete sdílet stav spři přihlašování s [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) mezi aplikacemi. Aplikace můžete migrovat do služby MSAL vlastním tempem a zajistit, aby vaši uživatelé měli stále prospěch z vlastního přiřazování služeb při práci mezi aplikacemi – a to i díky kombinaci aplikací založených na Službě ADAL a MSAL.

Pokud hledáte pokyny k nastavení přihlašování mezi aplikacemi pomocí sady MSAL SDK, přečtěte si část [Tiché přihlašování mezi více aplikacemi](single-sign-on-macos-ios.md#silent-sso-between-apps). Tento článek se zaměřuje na sací byt mezi ADAL a MSAL.

Specifika implementace spři závisí na verzi ADAL, které používáte.

## <a name="adal-27x"></a>ADAL 2.7.x

Tato část se zabývá rozdíly v prohlášení sondami mezi msal a adal 2.7.x

### <a name="cache-format"></a>Formát mezipaměti

ADAL 2.7.x může číst formát mezipaměti MSAL. Nemusíte dělat nic zvláštního pro cross-app spři přihlašovat s verzí ADAL 2.7.x. Je však třeba si uvědomit rozdíly v identifikátory účtů, které tyto dvě knihovny podporují.

### <a name="account-identifier-differences"></a>Rozdíly identifikátoru účtu

MSAL a ADAL používají různé identifikátory účtů. ADAL používá hlavní číslo sítě jako svůj primární identifikátor účtu. MSAL používá nezobrazitelný identifikátor účtu, který je založen na ID objektu a ID klienta pro účty AAD a `sub` deklaraci pro jiné typy účtů.

Když obdržíte `MSALAccount` objekt ve výsledku MSAL, obsahuje `identifier` identifikátor účtu ve vlastnosti. Aplikace by měla použít tento identifikátor pro následné tiché požadavky.

Kromě `identifier`objektu `MSALAccount` obsahuje zobrazitelný identifikátor `username`s názvem . To se `userId` překládá do ADAL. `username`není považován za jedinečný identifikátor a může kdykoli změnit, takže by měl být použit pouze pro scénáře zpětné kompatibility s ADAL. MSAL podporuje dotazy mezipaměti pomocí buď `username` nebo `identifier`, kde dotazování podle `identifier` se doporučuje.

Následující tabulka shrnuje rozdíly v identifikátoru účtu mezi ADAL a MSAL:

| Identifikátor účtu                | MSAL                                                         | ADAL 2.7.x      | Starší ADAL (před ADAL 2.7.x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| zobrazitelný identifikátor            | `username`                                                   | `userId`        | `userId`                       |
| jedinečný nezobrazitelný identifikátor | `identifier`                                                 | `homeAccountId` | Není dostupné.                            |
| Není známo žádné id účtu.               | Dotaz na `allAccounts:` všechny účty prostřednictvím rozhraní API`MSALPublicClientApplication` | Není dostupné.             | Není dostupné.                            |

Toto `MSALAccount` je rozhraní poskytující tyto identifikátory:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO od MSAL do ADAL

Pokud máte aplikaci MSAL a aplikaci ADAL a uživatel se nejprve přihlásí do aplikace založené na MSAL, `MSALAccount` můžete získat semanové služby `userId`v aplikaci ADAL uložením `username` z objektu a jeho předáním do aplikace založené na ADAL jako . ADAL pak můžete najít informace o `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` účtu tiše s rozhraním API.

### <a name="sso-from-adal-to-msal"></a>SSO od ADAL do MSAL

Pokud máte aplikaci MSAL a aplikaci ADAL a uživatel se nejprve přihlásí do aplikace založené na ADAL, můžete použít identifikátory uživatelů ADAL pro vyhledávání účtů v MSAL. To platí i při migraci z ADAL do MSAL.

#### <a name="adals-homeaccountid"></a>ADAL je homeAccountId

ADAL 2.7.x `homeAccountId` vrátí `ADUserInformation` v objektu ve výsledku prostřednictvím této vlastnosti:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`v ADAL je ekvivalentní `identifier` v MSAL. Tento identifikátor můžete uložit pro použití v MSAL `accountForIdentifier:error:` pro vyhledávání účtů s rozhraním API.

#### <a name="adals-userid"></a>ADAL je`userId`

Pokud `homeAccountId` není k dispozici, nebo máte pouze zobrazitelný identifikátor, `userId` můžete použít ADAL k vyhledávání účtu v MSAL.

V MSAL nejprve vyhledejte `username` `identifier`účet podle nebo . Vždy `identifier` používejte pro dotazování, pokud ji `username` máte, a používejte pouze jako záložní. Pokud je účet nalezen, použijte `acquireTokenSilent` účet ve volání.

Cíl-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL podporované řešení API vyhledávání účtů:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

Tato část se zabývá rozdíly s přihlašuje mezi MSAL a ADAL 2.x-2.6.6.

Starší verze ADAL nativně nepodporují formát mezipaměti MSAL. Chcete-li však zajistit hladkou migraci z ADAL na MSAL, může msal číst starší formát mezipaměti ADAL bez výzvy k vytvoření pověření uživatele.

Protože `homeAccountId` není k dispozici ve starších verzích ADAL, budete `username`muset vyhledat účty pomocí:

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Například:

Cíl-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Případně si můžete přečíst všechny účty, které budou také číst informace o účtu z ADAL:

Cíl-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Další kroky

Další informace o [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md)
