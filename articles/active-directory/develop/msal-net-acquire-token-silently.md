---
title: Získání tokenu tiše (knihovna Microsoft Authentication Library pro .NET) | Azure
description: Zjistěte, jak získat přístupový token tiše (z mezipaměti. token) pomocí knihovna Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e49dd85503475feab81a14148588981b04ad954
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158847"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Získání tokenu z mezipaměti token pomocí MSAL.NET

Při získání přístupového tokenu pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET) token, který je do mezipaměti. Pokud aplikace potřebuje token, byste nejprve zavolat `AcquireTokenSilent` metodu k ověření, pokud je přijatelné token v mezipaměti. V mnoha případech je možné získat nový token s více obory na základě tokenu v mezipaměti. Je také možné aktualizovat token, pokud je to stále blízko vypršení platnosti (jako mezipaměť tokenu obsahuje také obnovovací token).

Doporučený model je zavolat `AcquireTokenSilent` metoda první.  Pokud `AcquireTokenSilent` selže, pak získání tokenu pomocí jiných metod.

V následujícím příkladu se aplikace pokusí nejprve k získání tokenu z mezipaměti tokenů.  Pokud `MsalUiRequiredException` je vyvolána výjimka, aplikace získá token interaktivně. 

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
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
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