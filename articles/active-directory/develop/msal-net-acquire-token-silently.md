---
title: Bezobslužné získání tokenu (Microsoft Authentication Library pro .NET)
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak získat přístupový token v tichém režimu (z mezipaměti tokenu) pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc573c37aab95006f647509080fb9883d8650c7
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802938"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Získání tokenu z mezipaměti tokenů pomocí MSAL.NET

Při získání přístupového tokenu pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET) se token uloží do mezipaměti. Když aplikace potřebuje token, měla by nejdřív zavolat metodu `AcquireTokenSilent`, aby se ověřilo, jestli je v mezipaměti přijatelný token. V mnoha případech je možné získat další token s více rozsahy na základě tokenu v mezipaměti. Je také možné aktualizovat token, když se blíží vypršení platnosti (protože mezipaměť tokenů obsahuje také obnovovací token).

Doporučeným vzorem je nejprve zavolat metodu `AcquireTokenSilent`.  Pokud `AcquireTokenSilent` selžou, Získejte token pomocí jiných metod.

V následujícím příkladu se aplikace poprvé pokusí získat token z mezipaměti tokenu.  Pokud je vyvolána výjimka `MsalUiRequiredException`, aplikace získá token interaktivně. 

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
