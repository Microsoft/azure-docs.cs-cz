---
title: Mobilní aplikace, že volání webového rozhraní API – volání webového rozhraní API | Platforma identit Microsoft
description: Informace o sestavení mobilní aplikace, která volá webové rozhraní API (volání webového rozhraní API)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd65b9f97c373c55a3486e06e83fca7cf824cad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075112"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Mobilní aplikace, která volá webové rozhraní API – volání webového rozhraní API

Jakmile vaše aplikace má přihlášení uživatele a přijaté tokeny, MSAL uvádí různé druhy informací o uživateli, jejich prostředí a tokeny vystavené. Vaše aplikace tyto hodnoty můžete použít k volání webového rozhraní API nebo zobrazení uvítací zprávy pro uživatele.

Nejprve se podíváme na výsledek MSAL a jak pomocí přístupového tokenu z `AuthenticationResult` nebo `result` volat chráněné webové rozhraní API.

## <a name="msal-result"></a>Výsledek MSAL

- `AccessToken`: Umožňuje volat chráněné webové rozhraní API v požadavku HTTP nosiče.
- `IdToken`: Obsahuje užitečné deklarace identity o přihlášeného uživatele jako jejich název domovském tenantovi a jedinečný identifikátor pro úložiště.
- `ExpiresOn`: čas vypršení platnosti tokenu. Knihovna MSAL zpracovává automatické aktualizace pro aplikace.
- `TenantId`: Identifikátor tenanta uživatele použít k přihlášení. Pro uživatele typu Host (Azure AD B2B) bude jím tenanta uživatel přihlášený pomocí jejich domovském tenantovi.  
- `Scopes`: obory, které se udělují s svůj token. To může být podmnožinou jste požádali.

Kromě toho knihovna MSAL také poskytuje abstrakci pro `Account`. Účet představuje aktuálního uživatele přihlášení účtu.

- `HomeAccountIdentifier`: Identifikátor domovském tenantovi uživatele.
- `UserName`: Upřednostňované uživatelské jméno uživatele. To může být prázdná pro uživatele Azure AD B2C.
- `AccountIdentifier`: Identifikátor přihlášeného uživatele. To bude stejné jako `HomeAccountIdentifier` ve většině případů není-li uživatel typu Host v jiném tenantovi.

## <a name="calling-an-api"></a>Volání rozhraní API

Jakmile budete mít připravený přístupový token, je jednoduchá volání webového rozhraní API. Vaše aplikace bude zkuste tohle token, vytváření požadavků HTTP a proveďte jej.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called graph, process data and send to UI 
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put Access Token in HTTP request 
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

        // Put Access token in HTTP Request
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

            // Successfully got data from Graph
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put Access token in HTTP request 
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Několik požadavků rozhraní API

Pokud budete muset několikrát volat stejného rozhraní API nebo více rozhraní API, některé další aspekty při vytváření aplikace:

- ***Přírůstkové souhlasu***: Platforma identit Microsoft umožňuje aplikacím, kde získáte oprávnění jsou požadované, nikoli všech přímých souhlasu uživatele. Pokaždé, když vaše aplikace je připravená k volání rozhraní API, ji by měl požádat o rozsahy, které chce použít.
- ***Podmíněný přístup***: V některých případech se může zobrazit další požadavky podmíněného přístupu při provádění požadavků několik rozhraní API. Pro zpracování tohoto scénáře, je potřeba zachytávat chyby tiché požadavků a připravte se na interaktivní požadavku. To může nastat, pokud první požadavek nemá žádné zásady podmíněného přístupu a vaší aplikace se pokusí tiše přístup k nové rozhraní API, která vyžaduje podmíněný přístup. Další informace najdete v tématu [pokyny pro podmíněný přístup](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejít do produkčního prostředí](scenario-mobile-production.md)
