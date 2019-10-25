---
title: Vymazání mezipaměti tokenů pomocí knihovny Microsoft Authentication Library pro .NET
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vymazat mezipaměť tokenů pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bd48078ec87b085a71ee23006d18917881894bf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802925"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Vymazání mezipaměti tokenů pomocí MSAL.NET

Při [získání přístupového tokenu](msal-acquire-cache-tokens.md) pomocí knihovny Microsoft Authentication Library pro .net (MSAL.NET) se token uloží do mezipaměti. Když aplikace potřebuje token, měla by nejdřív zavolat metodu `AcquireTokenSilent`, aby se ověřilo, jestli je v mezipaměti přijatelný token. 

Vymazání mezipaměti se dosáhne odebráním účtů z mezipaměti. Tím nedojde k odebrání souboru cookie relace, který je v prohlížeči, ale.  Následující příklad vytvoří instanci veřejné klientské aplikace, získá účty pro aplikaci a odebere účty.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Pokud chcete získat další informace o získání a ukládání tokenů do mezipaměti, přečtěte si téma [získání přístupového tokenu](msal-acquire-cache-tokens.md).
