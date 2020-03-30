---
title: Získání tokenu z mezipaměti (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Zjistěte, jak získat přístupový token tiše (z mezipaměti tokenů) pomocí knihovny Microsoft Authentication Library pro rozhraní .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084829"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Získejte token z mezipaměti tokenů pomocí MSAL.NET

Když získáte přístupový token pomocí Knihovny ověřování Microsoft pro .NET (MSAL.NET), token je uložen do mezipaměti. Když aplikace potřebuje token, měla by `AcquireTokenSilent` nejprve zavolat metodu k ověření, zda je přijatelný token v mezipaměti. V mnoha případech je možné získat jiný token s více obory na základě tokenu v mezipaměti. Je také možné aktualizovat token, když se blíží vypršení platnosti (jako mezipaměť tokenu také obsahuje obnovovací token).

Doporučený vzor je nejprve volat metodu. `AcquireTokenSilent`  Pokud `AcquireTokenSilent` se nezdaří, pak získat token pomocí jiných metod.

V následujícím příkladu se aplikace nejprve pokusí získat token z mezipaměti tokenu.  Pokud `MsalUiRequiredException` je vyvolána výjimka, aplikace získá token interaktivně. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
