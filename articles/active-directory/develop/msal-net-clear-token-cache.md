---
title: Vymazat mezipaměť tokenu pomocí knihovny Microsoft Authentication Library pro .NET – Azure
description: Zjistěte, jak vymazat mezipaměť tokenu pomocí knihovna Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6763c6b2b1f9b4de7d8669a50a4979a7aac00c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544125"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Vymazat mezipaměť token pomocí MSAL.NET

Pokud jste [získání přístupového tokenu](msal-acquire-cache-tokens.md) pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET), token se uloží do mezipaměti. Pokud aplikace potřebuje token, byste nejprve zavolat `AcquireTokenSilent` metodu k ověření, pokud je přijatelné token v mezipaměti. 

Vymazání mezipaměti je dosaženo odstraněním účty z mezipaměti. Soubor cookie relace, která je v prohlížeči, i když nebude odstraněn.  Následující příklad vytvoří instanci aplikace veřejným klientem, získá účty pro aplikaci a odstraní účty.

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

Další informace o získávání a ukládání do mezipaměti tokenů, [získání přístupového tokenu](msal-acquire-cache-tokens.md).
