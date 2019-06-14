---
title: Webového rozhraní API, která volá webové rozhraní API (volání rozhraní API) - platforma identit Microsoft
description: Zjistěte, jak vytvořit webové rozhraní API tohoto volání podřízené webové rozhraní API (volání webového rozhraní API).
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
ms.openlocfilehash: 1cd093cc68a21558dc326221eeaa8c034c24f1c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074722"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Webové rozhraní API, která volá webové rozhraní API – volání rozhraní API

Jakmile máte token, můžete volat chráněné webové rozhraní API. To provedete z kontroleru webového rozhraní API vaší ASP.NET/ASP.NET Core.

## <a name="controller-code"></a>Kódu kontroleru

Tady je pokračování příklad kódu ukazuje [volání chráněného webového rozhraní API webové rozhraní API – získávání tokenu](scenario-web-api-call-api-acquire-token.md), označované jako v akcích kontrolery rozhraní API, volání příjem dat rozhraní API (s názvem seznam úkolů).

Jakmile jste získali token, použijte jako nosného tokenu pro volání rozhraní API pro příjem dat.

```CSharp
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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejít do produkčního prostředí](scenario-web-api-call-api-production.md)
