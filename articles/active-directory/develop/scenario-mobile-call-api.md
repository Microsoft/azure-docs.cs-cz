---
title: Volání webového rozhraní API z mobilní aplikace | Azure
titleSuffix: Microsoft identity platform
description: Naučte se, jak vytvořit mobilní aplikaci, která volá webová rozhraní API. (Volání webového rozhraní API.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 656cb167fb47e44c28922afed75d8c46a460aaf1
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584393"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Volání webového rozhraní API z mobilní aplikace

Jakmile se vaše aplikace přihlásí uživateli a obdrží tokeny, MSAL (Microsoft Authentication Library) zpřístupňuje informace o uživateli, prostředí uživatele a vydaných tokenech. Vaše aplikace může tyto hodnoty použít k volání webového rozhraní API nebo zobrazení uvítací zprávy uživateli.

V tomto článku se nejprve podíváme na výsledek MSAL. Pak se podíváme na použití přístupového tokenu z `AuthenticationResult` nebo `result` k volání chráněného webového rozhraní API.

## <a name="msal-result"></a>Výsledek MSAL
MSAL poskytuje následující hodnoty: 

- `AccessToken` volá chráněná webová rozhraní API v žádosti nosiče HTTP.
- `IdToken` obsahuje užitečné informace o přihlášeném uživateli. Tyto informace zahrnují jméno uživatele, domácího tenanta a jedinečný identifikátor úložiště.
- `ExpiresOn` je čas vypršení platnosti tokenu. MSAL zpracovává automatickou aktualizaci aplikace.
- `TenantId` je identifikátor tenanta, ke kterému se uživatel přihlásil. Pro uživatele typu Host ve službě Azure Active Directory (Azure AD) B2B tato hodnota identifikuje tenanta, ke kterému se uživatel přihlásil. Hodnota neidentifikuje svého domovského tenanta uživatele.  
- `Scopes` označuje rozsahy, které byly uděleny s tokenem. Udělené obory můžou být podmnožinou požadovaných oborů.

MSAL také poskytuje abstrakci pro `Account` hodnotu. `Account`Hodnota představuje účet přihlášeného aktuálního uživatele:

- `HomeAccountIdentifier` Identifikuje svého domovského tenanta uživatele.
- `UserName` je preferované uživatelské jméno uživatele. Tato hodnota může být pro Azure AD B2C uživatele prázdná.
- `AccountIdentifier` identifikuje přihlášeného uživatele. Ve většině případů je tato hodnota stejná jako `HomeAccountIdentifier` hodnota, pokud se uživatel nejedná o hosta v jiném tenantovi.

## <a name="call-an-api"></a>Volání rozhraní API

Po získání přístupového tokenu můžete zavolat webové rozhraní API. Vaše aplikace použije token k vytvoření požadavku HTTP a pak žádost spustí.

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

Metody získání tokenů vrací `MSALResult` objekt. `MSALResult` zpřístupňuje `accessToken` vlastnost. Můžete použít `accessToken` k volání webového rozhraní API. Před voláním k chráněnému webovému rozhraní API přidejte tuto vlastnost do hlavičky Authorization protokolu HTTP.

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

## <a name="make-several-api-requests"></a>Provedení několika požadavků rozhraní API

Chcete-li volat stejné rozhraní API několikrát nebo volat více rozhraní API, při sestavování aplikace Vezměte v úvahu následující předměty:

- **Přírůstkový souhlas**: platforma Microsoft Identity Platform umožňuje aplikacím získat souhlas uživatele, když jsou požadovaná oprávnění, a ne vše na začátku. Pokaždé, když je vaše aplikace připravená volat rozhraní API, měla by vyžadovat pouze ty rozsahy, které potřebuje.

- **Podmíněný přístup**: když provedete několik požadavků rozhraní API, může v některých scénářích být nutné splnit další požadavky podmíněného přístupu. Požadavky se můžou zvýšit tak, že první požadavek nemá žádné zásady podmíněného přístupu, a vaše aplikace se pokusí o tichý přístup k novému rozhraní API, které vyžaduje podmíněný přístup. Chcete-li tento problém vyřešit, Zachyťte chyby z tichých požadavků a připravte se na vytvoření interaktivního požadavku.  Další informace najdete v tématu [pokyny pro podmíněný přístup](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Volání několika rozhraní API pomocí přírůstkového souhlasu a podmíněného přístupu

Chcete-li volat několik rozhraní API pro stejného uživatele, po získání tokenu pro uživatele se můžete vyhnout opakovanému vyžádání přihlašovacích údajů uživatelem, a to tak, že následně zavoláte `AcquireTokenSilent` k získání tokenu:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interakce je povinná v těchto případech:

- Uživatel souhlasil s prvním rozhraním API, ale teď musí souhlasit s více obory. V takovém případě použijete přírůstkový souhlas.
- První rozhraní API nevyžaduje [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), ale další rozhraní API.

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

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [přejděte do produkčního](scenario-mobile-production.md)prostředí.
