---
title: Získání souhlasu pro několik prostředků (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak může uživatel získat předem souhlas s několika prostředky pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: d22b4109cdcdc965b1b2e03aba592022c58f773b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583786"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Uživatel získá souhlas s několika prostředky pomocí MSAL.NET.
Platforma Microsoft Identity vám neumožňuje získat token pro několik prostředků najednou. Pokud používáte Microsoft Authentication Library for .NET (MSAL.NET), parametr scopes v metodě získat token by měl obsahovat jenom obory pro jeden prostředek. Nicméně můžete předem udělit souhlas několika prostředkům a zadat další rozsahy pomocí `.WithExtraScopeToConsent` metody Tvůrce.

> [!NOTE]
> Získání souhlasu pro několik prostředků funguje pro platformu Microsoft identity, ale ne pro Azure AD B2C. Azure AD B2C podporuje jenom souhlas správce, ne pro vyjádření souhlasu s uživatelem.

Například pokud máte dva prostředky, které mají 2 obory, každý:

- https: \/ /mytenant.onmicrosoft.com/customerapi (s 2 obory `customer.read` a `customer.write` )
- https: \/ /mytenant.onmicrosoft.com/vendorapi (s 2 obory `vendor.read` a `vendor.write` )

Použijte `.WithExtraScopeToConsent` modifikátor, který má parametr *extraScopesToConsent* , jak je znázorněno v následujícím příkladu:

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

Tím získáte přístupový token pro první webové rozhraní API. Přístup k druhému webovému rozhraní API pak můžete v tichém získání tokenu z mezipaměti tokenů:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
