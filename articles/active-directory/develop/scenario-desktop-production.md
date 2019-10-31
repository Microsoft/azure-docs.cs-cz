---
title: Aplikace klasické pracovní plochy, která volá webová rozhraní API (přesunout do produkčního prostředí) – Microsoft Identity Platform
description: Zjistěte, jak vytvořit desktopovou aplikaci, která volá webová rozhraní API (přesunout do produkčního prostředí).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5331f01c5dc6acf01f567dbe4c332853bf7aa47e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175551"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Aplikace klasické pracovní plochy, která volá webová rozhraní API – přesun do produkčního prostředí

V tomto článku najdete podrobné informace o dalším vylepšení aplikace a jejím přesunu do produkčního prostředí.

## <a name="handling-errors-in-desktop-applications"></a>Zpracování chyb v aplikacích klasické pracovní plochy

V různých tocích jste se naučili, jak zpracovávat chyby pro tiché toky (jak je znázorněno v části fragmenty kódu). Také jste viděli, že existují případy, kdy je interakce nutná (přírůstkový souhlas a podmíněný přístup).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Jak dát uživateli souhlas před několika prostředky

> [!NOTE]
> Získání souhlasu pro několik prostředků funguje pro platformu Microsoft identity, ale ne pro Azure Active Directory (Azure AD) B2C. Azure AD B2C podporuje jenom souhlas správce, ne pro vyjádření souhlasu s uživatelem.

Koncový bod Microsoft Identity Platform (v 2.0) neumožňuje získat token pro několik prostředků najednou. Proto parametr `scopes` může obsahovat pouze obory pro jeden prostředek. Pomocí parametru `extraScopesToConsent` můžete zajistit, aby se uživatel předem poslal několika prostředkům.

Například pokud máte dva prostředky, které mají dva obory:

- `https://mytenant.onmicrosoft.com/customerapi` – 2 obory `customer.read` a `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` – 2 obory `vendor.read` a `vendor.write`

Měli byste použít modifikátor `.WithAdditionalPromptToConsent`, který má parametr `extraScopesToConsent`.

Např.:

### <a name="in-msalnet"></a>V MSAL.NET

```CSharp
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

### <a name="in-msal-for-ios-and-macos"></a>V MSAL pro iOS a macOS

Cíl-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

SWIFT

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Toto volání vám poskytne přístupový token pro první webové rozhraní API.

Pokud potřebujete zavolat druhé webové rozhraní API, můžete volat `AcquireTokenSilent` rozhraní API:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Osobní účet Microsoft vyžaduje přesouhlasení při každém spuštění aplikace.

Pro uživatele osobních účtů Microsoft se znovu zobrazí výzva k zadání souhlasu každého nativního klienta (Desktop/mobilní aplikace) volání metody autorizovat je zamýšlené chování. Identita nativního klienta je ze své podstaty nezabezpečená (v rozporu s důvěrnými klientskými aplikacemi, které vyměňují tajný klíč s platformou Microsoft identity k prokázání identity). Platforma Microsoft identity se rozhodla zmírnit toto zabezpečení pro zákazníky pomocí výzvy k zadání souhlasu uživatele, pokaždé, když je aplikace autorizována.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
