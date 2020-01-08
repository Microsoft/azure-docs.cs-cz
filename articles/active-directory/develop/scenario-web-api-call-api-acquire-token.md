---
title: Získání tokenu pro webové rozhraní API, které volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet webové rozhraní API, které volá webová rozhraní API, která vyžadují získání tokenu pro aplikaci.
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
ms.openlocfilehash: cc92fb7bc5ddf451279e6c157f9e93aa7fe9a12a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423624"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webové rozhraní API, které volá webová rozhraní API – získá token pro aplikaci.

Po vytvoření objektu klientské aplikace ho použijte k získání tokenu, který můžete použít k volání webového rozhraní API.

## <a name="code-in-the-controller"></a>Kód v kontroleru

Tady je příklad kódu, který se bude volat v akcích řadičů rozhraní API, volání rozhraní API pro příjem dat (s názvem ToDoList).

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
}
```

`BuildConfidentialClient()` se podobá tomu, co jste viděli v článku [webové rozhraní API, které volá rozhraní Web API – konfigurace aplikace](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` vytvoří instanci `IConfidentialClientApplication` s mezipamětí, která obsahuje pouze informace pro jeden účet. Účet je poskytován metodou `GetAccountIdentifier`.

Metoda `GetAccountIdentifier` používá deklarace identity přidružené k identitě uživatele, pro kterou webové rozhraní API přijalo token JWT:

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-web-api-call-api-call-api.md)
