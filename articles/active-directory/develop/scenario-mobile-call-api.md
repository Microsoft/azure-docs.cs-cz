---
title: Mobilní aplikace, která volá webová rozhraní API – volá webové rozhraní API.
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vytvořit mobilní aplikaci, která volá webová rozhraní API (volání webového rozhraní API).
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e70b828219fc497fc07e2bc128eb480a532a176
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802567"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Mobilní aplikace, která volá webová rozhraní API – volá webové rozhraní API.

Jakmile se aplikace přihlásí k uživateli a obdržela tokeny, MSAL zpřístupní několik informací o uživateli, prostředí uživatele a vydaných tokenech. Vaše aplikace může tyto hodnoty použít k volání webového rozhraní API nebo zobrazení uvítací zprávy uživateli.

Nejdřív se podíváme na výsledek MSAL. Pak se podíváme na použití přístupového tokenu z `AuthenticationResult` nebo `result` k volání chráněného webového rozhraní API.

## <a name="msal-result"></a>Výsledek MSAL
MSAL poskytuje následující hodnoty: 

- `AccessToken`: používá se pro volání chráněných webových rozhraní API v žádosti nosiče HTTP.
- `IdToken`: obsahuje užitečné informace o přihlášeném uživateli, jako je jméno uživatele, domovský tenant a jedinečný identifikátor úložiště.
- `ExpiresOn`: čas vypršení platnosti tokenu. MSAL zpracovává automatické aktualizace aplikací.
- `TenantId`: identifikátor tenanta, pomocí kterého se uživatel přihlásil. Pro uživatele typu Host (Azure Active Directory B2B) Tato hodnota identifikuje tenanta, ke kterému se uživatel přihlásil, a ne jeho domovského tenanta.  
- `Scopes`: obory, které byly uděleny s tokenem. Udělené obory můžou být podmnožinou požadovaných oborů.

MSAL také poskytuje abstrakci pro `Account`. `Account` představuje účet přihlášený k aktuálnímu uživateli.

- `HomeAccountIdentifier`: identifikátor domovského tenanta uživatele.
- `UserName`: upřednostňované uživatelské jméno uživatele. To může být pro Azure Active Directory B2C uživatelů prázdné.
- `AccountIdentifier`: identifikátor přihlášeného uživatele. Tato hodnota bude ve většině případů stejná jako hodnota `HomeAccountIdentifier`, pokud se uživatel nejedná o hosta v jiném tenantovi.

## <a name="call-an-api"></a>Volání rozhraní API

Po získání přístupového tokenu je snadné volat webové rozhraní API. Vaše aplikace použije token k vytvoření požadavku HTTP a pak žádost spustí.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL pro iOS a MacOS

Metody získání tokenů vrací objekt `MSALResult`. `MSALResult` zpřístupňuje vlastnost `accessToken`, která se dá použít k volání webového rozhraní API. Aby bylo volání přístupu k chráněnému webovému rozhraní API, mělo by se do hlavičky autorizace protokolu HTTP přidat přístupový token.

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

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="making-several-api-requests"></a>Provádění několika požadavků rozhraní API

Pokud potřebujete volat stejné rozhraní API několikrát nebo pokud potřebujete volat více rozhraní API, při sestavování aplikace Vezměte v úvahu následující skutečnosti:

- **Přírůstkový souhlas**: platforma Microsoft Identity Platform umožňuje aplikacím získat souhlas s uživatelem, protože jsou potřeba oprávnění, a ne vše na začátku. Pokaždé, když je vaše aplikace připravená volat rozhraní API, měla by vyžadovat pouze rozsahy, které musí použít.
- **Podmíněný přístup**: v některých scénářích můžete při provádění několika požadavků na rozhraní API získat další požadavky na podmíněný přístup. K tomu může dojít, pokud první požadavek nemá použité žádné zásady podmíněného přístupu a vaše aplikace se pokusí o tichý přístup k novému rozhraní API, které vyžaduje podmíněný přístup. Pro zpracování tohoto scénáře nezapomeňte zachytit chyby z tichých požadavků a připravit se na vytvoření interaktivního požadavku.  Další informace najdete v tématu [pokyny pro podmíněný přístup](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Volání několika rozhraní API v Xamarin nebo UWP – přírůstkový souhlas a podmíněný přístup

Pokud pro stejného uživatele potřebujete zavolat několik rozhraní API, můžete po získání tokenu pro uživatele vyhnout opakovanému vyžádání přihlašovacích údajů tím, že následně zavoláte `AcquireTokenSilent`, abyste získali token.

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
> [Přesunout do produkčního prostředí](scenario-mobile-production.md)
