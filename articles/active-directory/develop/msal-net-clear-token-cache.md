---
title: Vymazání mezipaměti tokenů (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vymazat mezipaměť tokenů pomocí knihovny Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a10efb5ff0a2c6a3ced3631dfe82c86e3e8a72fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084768"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Vymazání mezipaměti tokenů pomocí MSAL.NET

Když [získáte přístupový token](msal-acquire-cache-tokens.md) pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET), token je uložen do mezipaměti. Když aplikace potřebuje token, měla by `AcquireTokenSilent` nejprve zavolat metodu k ověření, zda je přijatelný token v mezipaměti. 

Vymazání mezipaměti je dosaženo odebráním účtů z mezipaměti. Tím se však neodstraní soubor cookie relace, který je v prohlížeči.  Následující příklad vytvoří instance aplikace veřejného klienta, získá účty pro aplikaci a odebere účty.

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

Další informace o získávání a ukládání tokenů do mezipaměti získáte [přístupový token](msal-acquire-cache-tokens.md).
