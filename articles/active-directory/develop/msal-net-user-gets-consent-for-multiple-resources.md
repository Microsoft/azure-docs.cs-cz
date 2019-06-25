---
title: Vyžadovat souhlas pro několik prostředků (knihovna Microsoft Authentication Library pro .NET) | Azure
description: Přečtěte si, jak může uživatel získat předběžné souhlas pro několik prostředků pomocí knihovna Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8bd9a86d5ec0d39a7f1c26adac52f41e6420283
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121985"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Uživatel získá souhlas pro několik prostředků pomocí MSAL.NET
Koncový bod Microsoft identity platform neumožňuje získání tokenu pro několik prostředků najednou. Při použití knihovna Microsoft Authentication Library pro .NET (MSAL.NET), obory parametr v metodě získání tokenu může obsahovat jenom obory pro jeden prostředek. Však můžete předem souhlasíte s předem několik prostředků tak, že zadáte další obory pomocí `.WithExtraScopeToConsent` metodě Tvůrce.

> [!NOTE]
> Získávání souhlas pro několik prostředků funguje platforma identit Microsoft, ale ne pro Azure AD B2C. Azure AD B2C podporuje pouze souhlas správce, není souhlasu uživatele.

Například pokud máte dva prostředky, které mají 2 obory každé:

- protokol https:\//mytenant.onmicrosoft.com/customerapi (s 2 obory `customer.read` a `customer.write`)
- protokol https:\//mytenant.onmicrosoft.com/vendorapi (s 2 obory `vendor.read` a `vendor.write`)

Měli byste použít `.WithExtraScopeToConsent` modifikátor, který má *extraScopesToConsent* parametru, jak je znázorněno v následujícím příkladu:

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

Získáte přístupový token pro první webové rozhraní API. Potom když budete potřebovat pro přístup k druhého webového rozhraní API vás tiše získat token z mezipamětí tokenů:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
