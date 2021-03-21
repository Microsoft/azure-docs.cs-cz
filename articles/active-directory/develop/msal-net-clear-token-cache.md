---
title: Vymazat mezipaměť tokenů (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vymazat mezipaměť tokenů pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3049a1213b8b92153fc0fce96b2dadace01a4ca8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064755"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Vymazání mezipaměti tokenů pomocí MSAL.NET

Při [získání přístupového tokenu](msal-acquire-cache-tokens.md) pomocí knihovny Microsoft Authentication Library pro .net (MSAL.NET) se token uloží do mezipaměti. Když aplikace potřebuje token, měla by nejdřív zavolat `AcquireTokenSilent` metodu pro ověření, jestli je přijatelný token v mezipaměti. 

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
