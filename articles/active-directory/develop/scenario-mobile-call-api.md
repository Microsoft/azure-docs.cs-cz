---
title: Mobilní aplikace, která volá webová rozhraní API – volání webového rozhraní API | Platforma Microsoft identity
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
ms.openlocfilehash: 76f0cddfa889376d3795726e74d82e53417b31f1
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413575"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Mobilní aplikace, která volá webová rozhraní API – volá webové rozhraní API.

Jakmile se aplikace přihlásí k uživateli a obdržela tokeny, MSAL zpřístupní několik informací o uživateli, prostředí uživatele a vydaných tokenech. Vaše aplikace může tyto hodnoty použít k volání webového rozhraní API nebo zobrazení uvítací zprávy uživateli.

Nejdřív se podíváme na výsledek MSAL. Pak se podíváme na použití přístupového tokenu z `AuthenticationResult` nebo `result` k volání chráněného webového rozhraní API.

## <a name="msal-result"></a>Výsledek MSAL
MSAL poskytuje následující hodnoty: 

- `AccessToken`: Slouží k volání chráněných webových rozhraní API v žádosti nosiče HTTP.
- `IdToken`: Obsahuje užitečné informace o přihlášeném uživateli, jako je jméno uživatele, domovský tenant a jedinečný identifikátor úložiště.
- `ExpiresOn`: Čas vypršení platnosti tokenu. MSAL zpracovává automatické aktualizace aplikací.
- `TenantId`: Identifikátor tenanta, se kterým se uživatel přihlásil. Pro uživatele typu Host (Azure Active Directory B2B) Tato hodnota identifikuje tenanta, ke kterému se uživatel přihlásil, a ne jeho domovského tenanta.  
- `Scopes`: Obory, které byly uděleny s tokenem. Udělené obory můžou být podmnožinou požadovaných oborů.

MSAL také poskytuje abstrakci pro `Account`. `Account` Představuje účet přihlášeného aktuálního uživatele.

- `HomeAccountIdentifier`: Identifikátor domovského tenanta uživatele.
- `UserName`: Preferované uživatelské jméno uživatele To může být pro Azure Active Directory B2C uživatelů prázdné.
- `AccountIdentifier`: Identifikátor přihlášeného uživatele. Tato hodnota bude stejná jako `HomeAccountIdentifier` hodnota ve většině případů, pokud se uživatel nejedná o hosta v jiném tenantovi.

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

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put access token in HTTP request.
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph.
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="making-several-api-requests"></a>Provádění několika požadavků rozhraní API

Pokud potřebujete volat stejné rozhraní API několikrát nebo pokud potřebujete volat více rozhraní API, při sestavování aplikace Vezměte v úvahu následující skutečnosti:

- **Přírůstkový souhlas**: Platforma Microsoft Identity Platform umožňuje aplikacím získat souhlas uživatele, jak jsou potřeba oprávnění, a ne vše na začátku. Pokaždé, když je vaše aplikace připravená volat rozhraní API, měla by vyžadovat pouze rozsahy, které musí použít.
- **Podmíněný přístup**: V některých scénářích můžete při provádění několika požadavků na rozhraní API získat další požadavky na podmíněný přístup. K tomu může dojít, pokud první požadavek nemá použité žádné zásady podmíněného přístupu a vaše aplikace se pokusí o tichý přístup k novému rozhraní API, které vyžaduje podmíněný přístup. Pro zpracování tohoto scénáře nezapomeňte zachytit chyby z tichých požadavků a připravit se na vytvoření interaktivního požadavku.  Další informace najdete v tématu [pokyny pro podmíněný přístup](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Volání několika rozhraní API v Xamarin nebo UWP – přírůstkový souhlas a podmíněný přístup

Pokud pro stejného uživatele potřebujete zavolat několik rozhraní API, můžete po získání tokenu pro uživatele vyhnout opakovanému vyžádání přihlašovacích údajů, a to tak, že následně zavoláte `AcquireTokenSilent` k získání tokenu.

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
