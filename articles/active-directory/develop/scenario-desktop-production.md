---
title: Aplikace klasické pracovní plochy, že volání webových rozhraní API (Přesun do produkčního prostředí) – platforma identit Microsoft
description: Zjistěte, jak vytvářet aplikace klasické pracovní plochy, že volání webových rozhraní API (Přesun do produkčního prostředí)
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
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ca66a41f26c54bf04273682d14889a36b688c70
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075127"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Aplikace klasické pracovní plochy, která volá webové rozhraní API – Přesun do produkčního prostředí

Tento článek obsahuje podrobnosti o vylepšovat vaši aplikaci dál a přesuňte ho do produkčního prostředí.

## <a name="handling-errors-in-desktop-applications"></a>Zpracování chyb v klientských aplikacích

Různé toky jste zjistili, jak pro zpracování chyb pro tichou toků (jak je uvedeno ve fragmentech kódu). Seznámili jste se také, že existují případy, kde je interakce potřebné (přírůstkové souhlasu a podmíněný přístup).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Jak vám má souhlasu uživatele předem pro několik prostředků

> [!NOTE]
> Získávání souhlas pro několik prostředků funguje platforma identit Microsoft, ale ne pro Azure Active Directory (Azure AD) B2C. Azure AD B2C podporuje pouze souhlas správce, není souhlasu uživatele.

Koncový bod Microsoft identity platform (v2.0) neumožňuje získání tokenu pro několik prostředků najednou. Proto `scopes` parametr může obsahovat pouze obory pro jeden prostředek. Můžete zajistit, že uživatel předem souhlasí s několika prostředky pomocí `extraScopesToConsent` parametru.

Například pokud máte dva prostředky, které mají dvě obory každé:

- `https://mytenant.onmicrosoft.com/customerapi` -s 2 obory `customer.read` a `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` -s 2 obory `vendor.read` a `vendor.write`

Měli byste použít `.WithAdditionalPromptToConsent` modifikátor, který má `extraScopesToConsent` parametru.

Příklad:

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

Toto volání se získat přístupový token pro první webové rozhraní API.

Když budete potřebovat volání druhého webového rozhraní API, můžete volat:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Osobní účet Microsoft vyžaduje reconsenting při každém spuštění aplikace

Reprompting pro vyjádření souhlasu pro všechna volání nativního klienta (desktop a mobilní aplikace) k autorizaci uživatele osobní účty Microsoft, je zamýšlené chování. Nativní klient systému identity je ze své podstaty nezabezpečené (rozporu s důvěrnými klientské aplikace, které Výměna tajného kódu s platformou Microsoft Identity k prokázání své identity). Platforma identit Microsoft jste zvolili pro zmírnění této zajišťování pro službami pro veřejnost podle výzvy k zadání souhlasu, pokaždé, když je tato aplikace oprávnění.

## <a name="next-steps"></a>Další postup

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
