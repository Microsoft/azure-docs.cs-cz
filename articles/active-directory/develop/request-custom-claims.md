---
title: Vyžádání vlastních deklarací (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak požádat o vlastní deklarace identity.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 4974fe3b387683f662d7a7b4f3ccb4935153f07e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883092"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Postup: Vyžádání vlastních deklarací pomocí msal pro iOS a macOS

OpenID Connect umožňuje volitelně požadovat vrácení jednotlivých deklarací z koncového bodu UserInfo a/nebo v tokenu ID. Požadavek na deklarace identity je reprezentován jako objekt JSON, který obsahuje seznam požadovaných deklarací. Další podrobnosti najdete [v tématu OpenID Connect Core 1.0.](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter)

Knihovna ověřování Microsoft (MSAL) pro iOS a macOS umožňuje vyžádání konkrétních deklarací identity ve scénářích interaktivních i tichých tokenů. Činí tak prostřednictvím parametru. `claimsRequest`

Existuje více scénářů, kde je to potřeba. Příklad:

- Vyžádání deklarací mimo standardní sadu pro vaši aplikaci.
- Vyžádání konkrétní kombinace standardní deklarace, které nelze zadat pomocí oborů pro vaši aplikaci. Například pokud přístupový token získá odmítnutz důvodu chybějící deklarace identity, aplikace může požádat o chybějící deklarace pomocí MSAL.

> [!NOTE]
> MSAL obchází mezipaměti přístupových tokenů vždy, když je zadán požadavek na deklarace identity. Je důležité zadat `claimsRequest` pouze parametr, pokud jsou potřeba další deklarace `claimsRequest` identity (na rozdíl od vždy poskytuje stejný parametr v každém volání rozhraní API MSAL).

`claimsRequest`lze zadat `MSALSilentTokenParameters` v `MSALInteractiveTokenParameters`písmenech a) a c):

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`lze vytvořit z nsstring reprezentace požadavku JSON Claims. 

Cíl-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Může být také upravena vyžádáním dalších specifických deklarací:

Cíl-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest`by pak měla být nastavena v parametrech tokenu a poskytnuta jednomu z rozhraní API pro získání tokenů MSAL:

Cíl-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Další kroky

Další informace o [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md)
