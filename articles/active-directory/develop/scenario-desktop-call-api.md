---
title: Volání webových rozhraní API z desktopové aplikace | Azure
titleSuffix: Microsoft identity platform
description: Informace o tom, jak vytvořit desktopovou aplikaci, která volá webová rozhraní API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 51619fe049d580c21b338af2f86b2afea6765b04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756529"
---
# <a name="desktop-app-that-calls-web-apis-call-a-web-api"></a>Aplikace klasické pracovní plochy, která volá webová rozhraní API: volání webového rozhraní API

Teď, když máte token, můžete zavolat chráněné webové rozhraní API.

## <a name="call-a-web-api"></a>Volání webového rozhraní API

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->
# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="macos"></a>[MacOS](#tab/macOS)

## <a name="call-a-web-api-in-msal-for-ios-and-macos"></a>Volání webového rozhraní API v MSAL pro iOS a macOS

Metody získání tokenů vrací `MSALResult` objekt. `MSALResult` zpřístupňuje `accessToken` vlastnost, která se dá použít k volání webového rozhraní API. Než provedete volání pro přístup k chráněnému webovému rozhraní API, přidejte přístupový token do hlavičky autorizace protokolu HTTP.

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

SWIFT

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]

let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="call-several-apis-incremental-consent-and-conditional-access"></a>Volání několika rozhraní API: přírůstkový souhlas a podmíněný přístup

Chcete-li volat několik rozhraní API pro stejného uživatele, poté, co získáte token pro první rozhraní API, zavolejte `AcquireTokenSilent` . Token pro ostatní rozhraní API bude v tichosti většinou.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interakce je povinná v těchto případech:

- Uživatel souhlasil s prvním rozhraním API, ale teď musí souhlasit s více obory. Tento druh souhlasu se označuje jako přírůstkový souhlas.
- První rozhraní API nevyžadovalo vícefaktorové ověřování, ale ten další.

```csharp
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
---

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [přejděte do produkčního](scenario-desktop-production.md)prostředí.
