---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76308949"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Vlastnosti AuthenticationResult v MSAL.NET

Metody pro získání tokenů vrátí `AuthenticationResult`. Pro asynchronní `Task<AuthenticationResult>` metody vrátí.

V MSAL.NET, `AuthenticationResult` vystavuje:

- `AccessToken`pro webové rozhraní API pro přístup k prostředkům. Tento parametr je řetězec, obvykle Základní-64 kódované JWT. Klient by se nikdy neměl podívat do přístupového tokenu. Formát není zaručeno, že zůstane stabilní a může být šifrována pro prostředek. Psaní kódu, který závisí na obsahu přístupového tokenu v klientovi, je jedním z největších zdrojů chyb a přestávek logiky klienta. Další informace naleznete [v tématu Access tokeny](../articles/active-directory/develop/access-tokens.md).
- `IdToken`pro uživatele. Tento parametr je kódovaný JWT. Další informace naleznete v tématu [ID tokeny](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn`datum a čas, kdy vyprší platnost tokenu.
- `TenantId`obsahuje klienta, ve kterém byl nalezen uživatel. Pro uživatele typu Host ve scénářích B2B služby Azure Active Directory (Azure AD) je ID klienta klienta, nikoli jedinečným tenantem.
Při doručení tokenu pro `AuthenticationResult` uživatele obsahuje také informace o tomto uživateli. Pro důvěrné toky klientů, kde jsou požadovány tokeny bez uživatele pro aplikaci, je tato informace o uživateli null.
- Pro `Scopes` které byl vydán token.
- Jedinečné ID pro uživatele.

### <a name="iaccount"></a>IÚčet

MSAL.NET definuje pojem účtu `IAccount` prostřednictvím rozhraní. Tato změna lámání poskytuje správnou sémantiku. Stejný uživatel může mít několik účtů v různých adresářích Azure AD. také MSAL.NET poskytuje lepší informace v případě scénáře hosta, protože jsou k dispozici informace o účtu domů.
Následující diagram znázorňuje `IAccount` strukturu rozhraní.

![Struktura rozhraní IAccount](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Třída `AccountId` identifikuje účet v konkrétním tenantovi s vlastnostmi uvedenými v následující tabulce.

| Vlastnost | Popis |
|----------|-------------|
| `TenantId` | Řetězcová reprezentace identifikátoru GUID, což je ID klienta, ve kterém je účet umístěn. |
| `ObjectId` | Řetězcová reprezentace identifikátoru GUID, což je ID uživatele, který vlastní účet v tenantovi. |
| `Identifier` | Jedinečný identifikátor účtu `Identifier`je zřetězení `ObjectId` `TenantId` čárky a oddělené čárkou. Nejsou zakódované na základně 64. |

Rozhraní `IAccount` představuje informace o jednom účtu. Stejný uživatel může být přítomen v různých tenantů, což znamená, že uživatel může mít více účtů. Její členové jsou uvedeny v následující tabulce.

| Vlastnost | Popis |
|----------|-------------|
| `Username` | Řetězec, který obsahuje zobrazitelnou hodnotu ve formátu UserPrincipalName john.doe@contoso.com(UPN), například . Tento řetězec může být null, na rozdíl od HomeAccountId a HomeAccountId.Identifier, který nebude null. Tato vlastnost nahrazuje `DisplayableId` vlastnost `IUser` v předchozích verzích MSAL.NET. |
| `Environment` | Řetězec, který obsahuje zprostředkovatele identity pro `login.microsoftonline.com`tento účet, například . Tato vlastnost nahrazuje `IdentityProvider` vlastnost `IUser`, `IdentityProvider` s výjimkou, že také měl informace o tenanta, kromě cloudového prostředí. Zde je hodnota pouze hostitele. |
| `HomeAccountId` | ID účtu domácího účtu pro uživatele. Tato vlastnost jednoznačně identifikuje uživatele napříč klienty Azure AD. |

### <a name="use-the-token-to-call-a-protected-api"></a>Volání chráněného rozhraní API pomocí tokenu

Poté, co `AuthenticationResult` je vrácena MSAL v `result`, přidejte jej do hlavičky autorizace HTTP před voláním pro přístup k chráněné webové rozhraní API.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```