---
title: Webové rozhraní API, které volá webová rozhraní API – Microsoft Identity Platform | Azure
description: Naučte se vytvářet webové rozhraní API, které volá webová rozhraní API.
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
ms.openlocfilehash: 5829ca41aaa4bd61f8878657e5eedbf6351b5df4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423583"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Webové rozhraní API, které volá webová rozhraní API – volá rozhraní API.

Jakmile máte token, můžete zavolat chráněné webové rozhraní API. To se provádí z kontroleru webového rozhraní API ASP.NET/ASP.NET Core.

## <a name="controller-code"></a>Kód kontroleru

Zde je pokračování ukázkového kódu zobrazeného v [chráněném webovém rozhraní API volá webová rozhraní API – získává token](scenario-web-api-call-api-acquire-token.md), který se volá v akcích ŘADIČŮ rozhraní API a volá rozhraní API pro příjem dat (s názvem ToDoList).

Po získání tokenu ho použijte jako nosný token pro volání rozhraní API pro příjem dat.

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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přesunout do produkčního prostředí](scenario-web-api-call-api-production.md)
