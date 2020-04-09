---
title: Webové rozhraní API, které volá webová rozhraní API – platforma identit microsoftu | Azure
description: Zjistěte, jak vytvořit webové rozhraní API, které volá webová rozhraní API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6bbd24978891efd147b0c317c1746d13961ce5e9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885085"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Webové rozhraní API, které volá webová rozhraní API: Volání rozhraní API

Poté, co máte token, můžete volat chráněné webové rozhraní API. To provést z řadiče webovérozhraní API.

## <a name="controller-code"></a>Kód řadiče

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Následující kód pokračuje v ukázkovém kódu, který je zobrazen ve [webovém rozhraní API, které volá webová rozhraní API: Získání tokenu pro aplikaci](scenario-web-api-call-api-acquire-token.md). Kód se nazývá v akcích řadičů rozhraní API. Volá downstream API s názvem *todolista*.

Po získání tokenu ho použijte jako nosný token k volání rozhraní API pro příjem dat.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

# <a name="java"></a>[Java](#tab/java)

Následující kód pokračuje v ukázkovém kódu, který je zobrazen ve [webovém rozhraní API, které volá webová rozhraní API: Získání tokenu pro aplikaci](scenario-web-api-call-api-acquire-token.md). Kód se nazývá v akcích řadičů rozhraní API. Volá následné rozhraní API MS Graph.

Po získání tokenu ho použijte jako nosný token k volání rozhraní API pro příjem dat.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Ukázka demonstrující tento tok s MSAL Python ještě není k dispozici.

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové rozhraní API, které volá webová rozhraní API: Přechod do produkčního prostředí](scenario-web-api-call-api-production.md)
