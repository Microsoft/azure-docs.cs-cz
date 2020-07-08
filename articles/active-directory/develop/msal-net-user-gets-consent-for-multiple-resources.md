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
ms.custom: aaddev
ms.openlocfilehash: e36237e67b4498ca6aad4b7ffa8c645abeff6143
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477290"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Uživatel získá souhlas s několika prostředky pomocí MSAL.NET.
Koncový bod platformy Microsoft Identity vám neumožňuje získat token pro několik prostředků najednou. Pokud používáte Microsoft Authentication Library for .NET (MSAL.NET), parametr scopes v metodě získat token by měl obsahovat jenom obory pro jeden prostředek. Nicméně můžete předem udělit souhlas několika prostředkům a zadat další rozsahy pomocí `.WithExtraScopeToConsent` metody Tvůrce.

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

Tím získáte přístupový token pro první webové rozhraní API. Když pak budete potřebovat přístup k druhému webovému rozhraní API, můžete z mezipaměti tokenů tiše získat token:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
