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
ms.openlocfilehash: 46d8b138a566727f9172b627b8df3353e7216fa5
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550321"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Mobilní aplikace, která volá webové rozhraní API – volání webového rozhraní API

Poté, co vaše aplikace má přihlášení uživatele a přijaté tokeny, MSAL uvádí různé druhy informací o uživateli, uživatelské prostředí a tokeny vystavené. Vaše aplikace tyto hodnoty můžete použít k volání webového rozhraní API nebo zobrazení uvítací zprávy pro uživatele.

Nejprve podíváme na MSAL výsledek. Potom se podíváme na tom, jak pomocí přístupového tokenu z `AuthenticationResult` nebo `result` volat chráněné webové rozhraní API.

## <a name="msal-result"></a>Výsledek MSAL
Knihovna MSAL poskytuje následující hodnoty: 

- `AccessToken`: Umožňuje volat chráněné webové rozhraní API v požadavku HTTP nosiče.
- `IdToken`: Obsahuje užitečné informace o přihlášeného uživatele, jako je uživatelské jméno, adresu tenanta a jedinečný identifikátor pro úložiště.
- `ExpiresOn`: Čas vypršení platnosti tokenu. Knihovna MSAL zpracovává automatické aktualizace pro aplikace.
- `TenantId`: Identifikátor tenanta, který přihlášení uživatele. Pro uživatele typu Host (Azure Active Directory B2B) bude tato hodnota identifikaci tenanta služby, který uživatel přihlášený pomocí není domovském tenantovi uživatele.  
- `Scopes`: Obory, které se udělují s svůj token. Udělená oborů může být podmnožinou obory, které jste požádali.

Knihovna MSAL také poskytuje abstrakci pro `Account`. `Account` Reprezentuje aktuálního uživatele přihlášeného účtu.

- `HomeAccountIdentifier`: Identifikátor domovském tenantovi uživatele.
- `UserName`: Upřednostňované uživatelské jméno uživatele. To může být prázdná pro uživatele Azure Active Directory B2C.
- `AccountIdentifier`: Identifikátor přihlášeného uživatele. Tato hodnota bude stejná jako `HomeAccountIdentifier` hodnota ve většině případů, kdy uživatel je Host v jiném tenantovi.

## <a name="call-an-api"></a>Volání rozhraní API

Až budete mít přístupový token, je snadné je zavolat webové rozhraní API. Aplikace použije token k vytváření požadavků HTTP a poté spusťte požadavek.

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

```CSharp
httpClient = new HttpClient();

// Put access token in HTTP request.
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Několik požadavků rozhraní API

Pokud je potřeba volat několikrát stejného rozhraní API, nebo pokud je potřeba volat více rozhraní API, zvažte následující v úvahu při vytváření vaší aplikace:

- **Přírůstkové souhlasu**: Platforma identit Microsoft umožňuje aplikacím získat souhlas uživatele, jako jsou vyžadována oprávnění, nikoli na začátku. Pokaždé, když vaše aplikace je připravená k volání rozhraní API, ji by měl požádat o rozsahy, které je třeba použít.
- **Podmíněný přístup**: V některých scénářích se mohou zobrazovat požadavky další podmíněného přístupu při provedení žádosti více rozhraní API. To může nastat, pokud první požadavek nemá žádné zásady podmíněného přístupu a vaší aplikace se pokusí tiše přístup k nové rozhraní API, která vyžaduje podmíněný přístup. Pro zpracování tohoto scénáře, je potřeba zachytávat chyby tiché požadavků a připravte se na interaktivní požadavku.  Další informace najdete v tématu [pokyny pro podmíněný přístup](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejít do produkčního prostředí](scenario-mobile-production.md)
