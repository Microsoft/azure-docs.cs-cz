---
title: Volání webového rozhraní API z mobilní aplikace | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak vytvořit mobilní aplikaci, která volá webová API. (Volání webového rozhraní API.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 28f57c5657ce2f8537a654a7f67ed4481fab2c91
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882688"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Volání webového rozhraní API z mobilní aplikace

Poté, co vaše aplikace podepíše v uživateli a obdrží tokeny, Microsoft Authentication Library (MSAL) zveřejňuje informace o uživateli, prostředí uživatele a vydané tokeny. Vaše aplikace může tyto hodnoty použít k volání webového rozhraní API nebo zobrazení uvítací zprávy uživateli.

V tomto článku se nejprve podíváme na výsledek MSAL. Pak se podíváme na to, jak `AuthenticationResult` `result` používat přístupový token z nebo volat chráněné webové rozhraní API.

## <a name="msal-result"></a>Výsledek MSAL
MSAL poskytuje následující hodnoty: 

- `AccessToken`volá chráněná webová api v požadavku na nosiče HTTP.
- `IdToken`obsahuje užitečné informace o přihlášeném uživateli. Tyto informace zahrnují jméno uživatele, domovského klienta a jedinečný identifikátor pro úložiště.
- `ExpiresOn`je doba vypršení platnosti tokenu. MSAL zpracovává automatické aktualizace aplikace.
- `TenantId`je identifikátor klienta, ve kterém se uživatel přihlásil. Pro uživatele typu Host ve službě Azure Active Directory (Azure AD) B2B tato hodnota identifikuje klienta, kde se uživatel přihlásil. Hodnota neidentifikuje domácího tenanta uživatele.  
- `Scopes`označuje obory, které byly uděleny pomocí tokenu. Udělené obory mohou být podmnožinou oborů, které jste požadovali.

MSAL také poskytuje abstrakci `Account` pro hodnotu. Hodnota `Account` představuje přihlášený účet aktuálního uživatele:

- `HomeAccountIdentifier`identifikuje domovského klienta uživatele.
- `UserName`je uživatelské jméno upřednostňované uživatelem. Tato hodnota může být prázdná pro uživatele Azure AD B2C.
- `AccountIdentifier`identifikuje přihlášeného uživatele. Ve většině případů je tato hodnota `HomeAccountIdentifier` stejná jako hodnota, pokud uživatel není hostem v jiném tenantovi.

## <a name="call-an-api"></a>Volání rozhraní API

Po vytvoření přístupového tokenu můžete volat webové rozhraní API. Vaše aplikace použije token k vytvoření požadavku HTTP a následném spuštění požadavku.

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

Metody pro získání tokenů `MSALResult` vrátí objekt. `MSALResult`zpřístupní `accessToken` vlastnost. Můžete volat `accessToken` webové rozhraní API. Tuto vlastnost přidejte do hlavičky autorizace protokolu HTTP před voláním pro přístup k chráněnému webovému rozhraní API.

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

## <a name="make-several-api-requests"></a>Vytvořit několik požadavků rozhraní API

Pokud potřebujete volat stejné rozhraní API několikrát, nebo pokud potřebujete volat více rozhraní API, pak zvažte následující témata při vytváření aplikace:

- **Přírůstkový souhlas**: Platforma identit microsoftu umožňuje aplikacím získat souhlas uživatele, když jsou vyžadována oprávnění, a ne všechna na začátku. Pokaždé, když je vaše aplikace připravená k volání rozhraní API, měla by požadovat jenom obory, které potřebuje.

- **Podmíněný přístup**: Při několika požadavcích rozhraní API, v určitých scénářích budete muset splnit další požadavky podmíněného přístupu. Požadavky se tímto způsobem mohou zvýšit, pokud první požadavek nemá žádné zásady podmíněného přístupu a vaše aplikace se pokusí o tichý přístup k novému rozhraní API, které vyžaduje podmíněný přístup. Chcete-li tento problém vyřešit, ujistěte se, že zachytit chyby z tichých požadavků a být připraveni provést interaktivní požadavek.  Další informace naleznete v [tématu Pokyny pro podmíněný přístup](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Volání několika pravidel API pomocí přírůstkového souhlasu a podmíněného přístupu

Pokud potřebujete volat několik rozhraní API pro stejného uživatele, po získání tokenu pro uživatele, můžete se vyhnout `AcquireTokenSilent` opakovanému dotazování uživatele o pověření následným voláním získat token:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interakce je vyžadována, pokud:

- Uživatel souhlasil s prvním rozhraním API, ale nyní musí souhlasit s více obory. V takovém případě použijete přírůstkový souhlas.
- První rozhraní API nevyžaduje vícefaktorové ověřování, ale další rozhraní API ano.

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

> [!div class="nextstepaction"]
> [Přechod k produkčnímu prostředí](scenario-mobile-production.md)
