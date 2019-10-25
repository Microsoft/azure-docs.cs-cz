---
title: Získání souhlasu pro několik prostředků (Microsoft Authentication Library pro .NET)
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak může uživatel získat předem souhlas s několika prostředky pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a3d50e94f76341dc349eda997d6e25ca96dec0
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802701"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Uživatel získá souhlas s několika prostředky pomocí MSAL.NET.
Koncový bod platformy Microsoft Identity vám neumožňuje získat token pro několik prostředků najednou. Pokud používáte Microsoft Authentication Library for .NET (MSAL.NET), parametr scopes v metodě získat token by měl obsahovat jenom obory pro jeden prostředek. Nicméně můžete předem udělit souhlas několika prostředkům a zadat další rozsahy pomocí metody `.WithExtraScopeToConsent` Builder.

> [!NOTE]
> Získání souhlasu pro několik prostředků funguje pro platformu Microsoft identity, ale ne pro Azure AD B2C. Azure AD B2C podporuje jenom souhlas správce, ne pro vyjádření souhlasu s uživatelem.

Například pokud máte dva prostředky, které mají 2 obory, každý:

- https:\//mytenant.onmicrosoft.com/customerapi (se 2 obory `customer.read` a `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (se 2 obory `vendor.read` a `vendor.write`)

Použijte modifikátor `.WithExtraScopeToConsent`, který obsahuje parametr *extraScopesToConsent* , jak je znázorněno v následujícím příkladu:

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
