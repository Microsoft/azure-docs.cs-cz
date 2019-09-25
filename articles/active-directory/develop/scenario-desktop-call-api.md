---
title: Aplikace klasické pracovní plochy, která volá webová rozhraní API (volání webového rozhraní API) – Microsoft Identity Platform
description: Zjistěte, jak vytvořit desktopovou aplikaci, která volá webová rozhraní API (volání webového rozhraní API).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d3d01e39adfeb6bf2ef5e7e7d595f49c90f5a5
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268276"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Aplikace klasické pracovní plochy, která volá webové rozhraní API – volá webové rozhraní API.

Teď, když máte token, můžete zavolat chráněné webové rozhraní API.

## <a name="calling-a-web-api-from-net"></a>Volání webového rozhraní API z .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>Volání webového rozhraní API v MSAL pro iOS a macOS

Metody získání tokenů vrací `MSALResult` objekt. `MSALResult`zpřístupňuje `accessToken` vlastnost, která se dá použít k volání webového rozhraní API. Aby bylo volání přístupu k chráněnému webovému rozhraní API, mělo by se do hlavičky autorizace protokolu HTTP přidat přístupový token.

Cíl-C:

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

Swift:

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Volání několika rozhraní API – přírůstkový souhlas a podmíněný přístup

Pokud pro stejného uživatele potřebujete zavolat několik rozhraní API, stačí, když získáte token pro první rozhraní API, stačí zavolat `AcquireTokenSilent`a získáte token pro ostatní rozhraní API tiše v tichém čase.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Případy, kdy je interakce požadována, je:

- Uživatel souhlasil s prvním rozhraním API, ale teď musí souhlasit s více obory (postupný souhlas).
- První rozhraní API nevyžadovalo vícenásobné ověřování, ale ten další.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přesunout do produkčního prostředí](scenario-desktop-production.md)
