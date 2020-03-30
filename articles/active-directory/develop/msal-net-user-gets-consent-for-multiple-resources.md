---
title: Získat souhlas s několika zdroji (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak může uživatel získat předběžný souhlas s několika prostředky pomocí knihovny Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 94c9a2b6a46262ad293da9ca3ba493d6f898c870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085836"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Uživatel získá souhlas s několika prostředky pomocí MSAL.NET
Koncový bod platformy identity Společnosti Microsoft neumožňuje získat token pro několik prostředků najednou. Při použití knihovny Microsoft Authentication Library pro .NET (MSAL.NET) by parametr oborů v metodě tokenu získání měl obsahovat pouze obory pro jeden prostředek. Můžete však předem souhlasit s několika prostředky předem zadáním `.WithExtraScopeToConsent` dalšíobory pomocí metody tvůrce.

> [!NOTE]
> Získání souhlasu pro několik prostředků funguje pro platformu identit Microsoftu, ale ne pro Azure AD B2C. Azure AD B2C podporuje pouze souhlas správce, ne souhlas uživatele.

Například pokud máte dva prostředky, které mají dva obory každý:

- https:\//mytenant.onmicrosoft.com/customerapi (se `customer.read` 2 `customer.write`obory a )
- https:\//mytenant.onmicrosoft.com/vendorapi (se `vendor.read` 2 `vendor.write`obory a )

Měli byste `.WithExtraScopeToConsent` použít modifikátor, který má *parametr extraScopesToConsent,* jak je znázorněno v následujícím příkladu:

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

Tím získáte přístupový token pro první webové rozhraní API. Potom, když potřebujete získat přístup k druhému webovému rozhraní API, můžete tiše získat token z mezipaměti tokenu:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
