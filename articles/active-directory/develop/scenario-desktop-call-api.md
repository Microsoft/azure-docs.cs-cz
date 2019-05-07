---
title: Aplikace klasické pracovní plochy, že volání webových rozhraní API (volání webového rozhraní API) - platforma identit Microsoft
description: Zjistěte, jak sestavit aplikaci klasické pracovní plochy, která volá webové rozhraní API (volání webového rozhraní API)
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
ms.openlocfilehash: 6fb240b54c3d698ead9d3427f603acca2b53745a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075922"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Aplikace klasické pracovní plochy, která volá webové rozhraní API – volání webového rozhraní API

Teď, když máte token, můžete volat chráněné webové rozhraní API.

## <a name="calling-a-web-api-from-net"></a>Volání webového rozhraní API z .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Volání několik rozhraní API – přírůstkové souhlasu a podmíněného přístupu

Pokud je potřeba volat několik rozhraní API pro stejného uživatele, když jste získali token pro prvního rozhraní API, ho prostě zavoláte `AcquireTokenSilent`, zobrazí se token pro jiná rozhraní API tiše většinu času.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Případy, ve kterém jsou vyžadována interakce je těchto případech:

- Uživatel souhlas pro prvního rozhraní API, ale teď je potřeba udělit souhlas pro další obory (přírůstkové souhlas)
- Prvního rozhraní API nepovedlo vyžadují více Multi-Factor authentication, ale provede další příkaz.

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejít do produkčního prostředí](scenario-desktop-production.md)
