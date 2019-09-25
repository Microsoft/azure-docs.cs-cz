---
title: Jak vyžádat vlastní deklarace identity pomocí MSAL pro iOS a macOS | Platforma Microsoft identity
description: Přečtěte si, jak vyžádat vlastní deklarace identity.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e09d58742bffd74f07f79b3ec55c1e81533632
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268983"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Postup: Vyžádat si vlastní deklarace identity pomocí MSAL pro iOS a macOS

OpenID Connect umožňuje volitelně požádat o vrácení individuálních deklarací identity z koncového bodu UserInfo nebo do tokenu ID. Požadavek na deklarace identity je reprezentován jako objekt JSON, který obsahuje seznam požadovaných deklarací. Další podrobnosti najdete v tématu [OpenID Connect Core 1,0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) .

Knihovna Microsoft Authentication Library (MSAL) pro iOS a macOS umožňuje žádat o konkrétní deklarace ve scénářích interaktivního i tichého získání tokenu. Provede to přes `claimsRequest` parametr.

Je třeba provést několik scénářů. Příklad:

- Vyžádání deklarací identity mimo standardní sadu pro vaši aplikaci.
- Požaduje se konkrétní kombinace standardních deklarací identity, které se nedají zadat pomocí oborů pro vaši aplikaci. Pokud se například přístupový token zamítl z důvodu chybějících deklarací, aplikace může vyžádat chybějící deklarace identity pomocí MSAL.

> [!NOTE]
> MSAL obchází mezipaměť přístupového tokenu pokaždé, když je zadána žádost o deklarace identity. Je důležité, abyste zadali parametr `claimsRequest` jenom v případě, že se vyžadují další deklarace identity (na rozdíl od `claimsRequest` vždy, když se v každém volání rozhraní MSAL API vždycky zadávají stejné parametry).

`claimsRequest`lze zadat v `MSALSilentTokenParameters` a `MSALInteractiveTokenParameters`:

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
`MSALClaimsRequest`může být sestaven z NSString reprezentace žádosti o deklarace JSON. 

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



Můžete ho také upravit vyžádáním dalších specifických deklarací identity:

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



`MSALClaimsRequest`měla by být nastavená v parametrech tokenu a musí se zadat pro jedno z rozhraní API pro získání tokenu MSAL:

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
