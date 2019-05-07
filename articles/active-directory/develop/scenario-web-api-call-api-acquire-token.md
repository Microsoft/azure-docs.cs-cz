---
title: Webové rozhraní API, že volání dalších webových rozhraní API (získat token pro aplikace) – platforma identit Microsoft
description: Zjistěte, jak vytvořit webové rozhraní API, že ostatní volání webových rozhraní API (získání tokenu pro aplikaci).
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
ms.openlocfilehash: ada4323b0e61c6dc9fb87af133c40ec9c35e3834
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074977"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webové rozhraní API, která volá webové rozhraní API – získání tokenu pro aplikaci

Po začlenění klientskou aplikaci, použít k získání tokenu, který můžete použít k volání webového rozhraní API.

## <a name="code-in-the-controller"></a>Kód v kontroleru

Tady je příklad kódu, která bude volána v akcích kontrolery rozhraní API, volání příjem dat rozhraní API (s názvem seznam úkolů).

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
}
```

`BuildConfidentialClient()` je podobný jste viděli v článku [webového rozhraní API, která volá webové rozhraní API – konfigurace aplikace](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` vytvoří instanci `IConfidentialClientApplication` s mezipamětí, který obsahuje pouze informace pro jeden účet. Účet je poskytuje `GetAccountIdentifier` metody.

`GetAccountIdentifier` Metoda používá deklarace identity, které souvisejí s identitou uživatele, pro kterou přijal webového rozhraní API tokenů JWT:

```CSharp
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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Volání webového rozhraní API](scenario-web-api-call-api-call-api.md)
