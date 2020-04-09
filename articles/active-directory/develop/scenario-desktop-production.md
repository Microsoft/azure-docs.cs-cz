---
title: Přesunutí webových rozhraní API pro volání webových rozhraní API pro stolní počítače do produkčního prostředí – platforma identit Microsoftu | Azure
description: Přečtěte si, jak přesunout desktopovou aplikaci, která volá webová api do produkčního prostředí.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea564eb69f102d8e548bf8ae9a626598fa264cd4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882875"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Desktopová aplikace, která volá webová API: Přechod do produkčního prostředí

V tomto článku se dozvíte, jak přesunout desktopovou aplikaci, která volá webová api do produkčního prostředí.

## <a name="handle-errors-in-desktop-applications"></a>Zpracování chyb v aplikacích klasické pracovní plochy

V různých tocích jste se naučili, jak zpracovat chyby pro tiché toky, jak je znázorněno v fragmenty kódu. Viděli jste také, že existují případy, kdy je nutná interakce, jako v přírůstkovém souhlasu a podmíněném přístupu.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Mít souhlas uživatele předem pro několik zdrojů

> [!NOTE]
> Získání souhlasu pro několik prostředků funguje pro platformu identit Microsoftu, ale ne pro Azure Active Directory (Azure AD) B2C. Azure AD B2C podporuje pouze souhlas správce, ne souhlas uživatele.

Nelze získat token pro několik prostředků najednou s koncovým bodem platformy identit y Microsoft (v2.0). Parametr `scopes` může obsahovat obory pouze pro jeden prostředek. Pomocí `extraScopesToConsent` parametru můžete zajistit, aby uživatel předem souhlasil s několika prostředky.

Můžete mít například dva prostředky, které mají každý dva obory:

- `https://mytenant.onmicrosoft.com/customerapi`s rozsahy `customer.read` a`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`s rozsahy `vendor.read` a`vendor.write`

V tomto příkladu `.WithAdditionalPromptToConsent` použijte modifikátor, který má `extraScopesToConsent` parametr.

Například:

### <a name="in-msalnet"></a>V MSAL.NET

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

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Toto volání vám získá přístupový token pro první webové rozhraní API.

Když potřebujete volat druhé webové rozhraní `AcquireTokenSilent` API, zavolejte rozhraní API.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Osobní účet Microsoft vyžaduje souhlas při každém spuštění aplikace

Pro uživatele osobního účtu Microsoft je záměrné chování, které je pro uživatele osobního účtu Microsoft vyzvání k udělení souhlasu u každého nativního klienta (stolního nebo mobilního počítače) k autorizaci. Nativní identita klienta je ze své podstaty nejistá, což je v rozporu s důvěrnou identitou klientské aplikace. Důvěrné klientské aplikace si vyměňují tajný klíč s platformou Microsoft Identity, aby prokázaly svou identitu. Platforma identit společnosti Microsoft se rozhodla zmírnit tuto nejistotu pro spotřebitelské služby tím, že vyzve uživatele k udělení souhlasu při každém autorizaci aplikace.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
