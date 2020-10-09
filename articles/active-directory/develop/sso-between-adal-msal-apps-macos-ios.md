---
title: Jednotné přihlašování mezi aplikacemi ADAL & MSAL (iOS/macOS) – Microsoft Identity Platform | Azure
description: ''
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 7a8a1667ba1ca2a99c053c6941e3ba778299fd53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80880746"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Postupy: jednotné přihlašování mezi aplikacemi ADAL a MSAL v macOS a iOS

Knihovna Microsoft Authentication Library (MSAL) pro iOS může sdílet stav jednotného přihlašování s [cíli ADAL-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) mezi aplikacemi. Své aplikace můžete migrovat tak, aby se MSAL vlastním tempem, což zajistí, že uživatelé budou mít pořád možnost jednotného přihlašování mezi aplikacemi, a to i s kombinací aplikací založených na ADAL a MSAL.

Pokud hledáte pokyny k nastavení jednotného přihlašování mezi aplikacemi pomocí sady MSAL SDK, přečtěte si téma [bezobslužné jednotné přihlašování mezi několika aplikacemi](single-sign-on-macos-ios.md#silent-sso-between-apps). Tento článek se zaměřuje na jednotné přihlašování mezi ADAL a MSAL.

Konkrétní implementace jednotného přihlašování závisí na verzi ADAL, kterou používáte.

## <a name="adal-27x"></a>ADAL 2.7. x

Tato část popisuje rozdíly v jednotném přihlašování mezi MSAL a ADAL 2.7. x

### <a name="cache-format"></a>Formát mezipaměti

ADAL 2.7. x může číst formát MSAL cache. Pro jednotné přihlašování mezi aplikacemi nemusíte nic dělat s verzí ADAL 2.7. x. Je však třeba mít na paměti rozdíly v identifikátorech účtů, které tyto dvě knihovny podporují.

### <a name="account-identifier-differences"></a>Rozdíly v identifikátorech účtů

MSAL a ADAL používají jiné identifikátory účtu. ADAL používá jako svůj primární identifikátor účtu hlavní název uživatele (UPN). MSAL používá nezobrazitelný identifikátor účtu, který je založen na ID objektu a ID tenanta pro účty AAD, a `sub` deklarace identity pro jiné typy účtů.

Když obdržíte `MSALAccount` objekt ve výsledku MSAL, obsahuje identifikátor účtu ve `identifier` Vlastnosti. Aplikace by měla tento identifikátor používat pro následné tiché požadavky.

Kromě `identifier` toho `MSALAccount` objekt obsahuje zobrazitelný identifikátor s názvem `username` . To se týká i `userId` v ADAL. `username` není považován za jedinečný identifikátor a může být kdykoli změněn, takže by měl být použit pouze pro scénáře zpětné kompatibility s ADAL. MSAL podporuje dotazy mezipaměti pomocí `username` nebo `identifier` , kde se doporučuje dotazování podle `identifier` .

Následující tabulka shrnuje rozdíly v identifikátorech účtů mezi ADAL a MSAL:

| Identifikátor účtu                | MSAL                                                         | ADAL 2.7. x      | Starší knihovna ADAL (před ADAL 2.7. x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| zobrazitelný identifikátor            | `username`                                                   | `userId`        | `userId`                       |
| jedinečný nezobrazitelný identifikátor | `identifier`                                                 | `homeAccountId` | Není k dispozici                            |
| Není známé žádné ID účtu.               | Dotazování všech účtů prostřednictvím `allAccounts:` rozhraní API v `MSALPublicClientApplication` | N/A             | N/A                            |

Toto je rozhraní, které `MSALAccount` poskytuje tyto identifikátory:

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

### <a name="sso-from-msal-to-adal"></a>Jednotné přihlašování z MSAL do ADAL

Pokud máte aplikaci MSAL a aplikaci ADAL a uživatel se poprvé přihlásí do aplikace založené na MSAL, můžete v aplikaci ADAL získat jednotné přihlašování, a to tak, že `username` ho uložíte z `MSALAccount` objektu a předáte ho do aplikace založené na knihovně ADAL `userId` . ADAL pak může najít informace o účtu v tichém režimu pomocí `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` rozhraní API.

### <a name="sso-from-adal-to-msal"></a>Jednotné přihlašování z ADAL do MSAL

Pokud máte aplikaci MSAL a aplikaci ADAL a uživatel se poprvé přihlásí do aplikace založené na ADAL, můžete použít identifikátory uživatelů ADAL pro vyhledávání účtů v MSAL. To platí také při migraci z ADAL do MSAL.

#### <a name="adals-homeaccountid"></a>HomeAccountId ADAL

ADAL 2.7. x vrátí `homeAccountId` `ADUserInformation` objekt ve výsledku prostřednictvím této vlastnosti:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` v ADAL je ekvivalentem `identifier` v MSAL. Tento identifikátor můžete uložit pro použití v MSAL pro vyhledávání účtů s `accountForIdentifier:error:` rozhraním API.

#### <a name="adals-userid"></a>ADAL `userId`

Pokud `homeAccountId` není k dispozici nebo máte jenom zobrazitelný identifikátor, můžete `userId` k vyhledání účtu v MSAL použít ADAL.

V MSAL nejdřív vyhledejte účet pomocí `username` nebo `identifier` . Vždy používejte `identifier` pro dotazování, pokud ho máte, a používejte ho jenom `username` jako záložní. Pokud je účet nalezen, použijte účet při `acquireTokenSilent` volání.

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

SWIFT

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



Rozhraní API pro vyhledávání MSAL podporovaných účtů:

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

## <a name="adal-2x-266"></a>ADAL 2. x – 2.6.6

Tato část popisuje rozdíly v jednotném přihlašování mezi MSAL a ADAL 2. x-2.6.6.

Starší verze ADAL nepodporují nativně formát mezipaměti MSAL. Aby se ale zajistila bezproblémová migrace z ADAL na MSAL, může MSAL načíst starší formát mezipaměti ADAL bez výzvy k zadání přihlašovacích údajů uživatele.

Protože `homeAccountId` není k dispozici ve starších verzích ADAL, budete potřebovat vyhledat účty pomocí `username` :

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

SWIFT

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



Případně si můžete přečíst všechny účty, které budou také číst informace o účtech z ADAL:

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

SWIFT

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
