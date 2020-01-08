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
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423758"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Vlastnosti AuthenticationResult v MSAL.NET

Metody získání tokenů vrací `AuthenticationResult` (nebo pro asynchronní metody `Task<AuthenticationResult>`.

V MSAL.NET `AuthenticationResult` zveřejňuje:

- `AccessToken` pro přístup k prostředkům webového rozhraní API. Tento parametr je řetězec, obvykle token JWT kódovaného ve formátu base64, ale klient by nikdy neměl být v přístupovém tokenu. Formát není zaručený, aby zůstal stabilní a mohl by být zašifrovaný pro daný prostředek. Lidé, kteří vytvářejí kód v závislosti na obsahu přístupového tokenu na klientovi, je jedním z největších zdrojů chyb a konců klientských logik. Viz také [přístupové tokeny](../articles/active-directory/develop/access-tokens.md) .
- `IdToken` pro uživatele (Tento parametr je kódovaným tokenem JWT). Zobrazit [tokeny ID](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` oznamuje datum a čas, kdy vyprší platnost tokenu.
- `TenantId` obsahuje tenanta, ve kterém se uživatel našel. Pro uživatele typu Host (scénáře Azure AD B2B) je ID tenanta tenant hosta, nikoli jedinečný tenant.
Po doručení tokenu uživateli `AuthenticationResult` také obsahuje informace o tomto uživateli. U důvěrných toků klienta, kde jsou požadovány tokeny bez uživatele (pro aplikaci), jsou informace o uživateli null.
- `Scopes`, pro který byl token vydán.
- Jedinečné ID uživatele

### <a name="iaccount"></a>IAccount

MSAL.NET definuje pojem účtu (prostřednictvím rozhraní `IAccount`). Tato zásadní změna poskytuje správnou sémantiku: skutečnost, že stejný uživatel může mít několik účtů v různých adresářích Azure AD. MSAL.NET také poskytuje lepší informace v případě hostujících scénářů, protože jsou k dispozici informace o domácím účtu.
Následující diagram znázorňuje strukturu `IAccount` rozhraní:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Třída `AccountId` identifikuje účet v konkrétním tenantovi. Má následující vlastnosti:

| Vlastnost | Popis |
|----------|-------------|
| `TenantId` | Řetězcová reprezentace pro identifikátor GUID, což je ID tenanta, ve kterém se účet nachází. |
| `ObjectId` | Řetězcová reprezentace pro identifikátor GUID, což je ID uživatele, který je vlastníkem účtu v tenantovi. |
| `Identifier` | Jedinečný identifikátor účtu `Identifier` je zřetězení `ObjectId` a `TenantId` oddělené čárkou a nejsou kódovány pomocí kódování Base64. |

Rozhraní `IAccount` představuje informace o jednom účtu. Stejný uživatel může být přítomen v různých klientech, tedy uživatel může mít více účtů. Její členové jsou:

| Vlastnost | Popis |
|----------|-------------|
| `Username` | Řetězec obsahující zobrazitelnou hodnotu ve formátu UserPrincipalName (UPN), například john.doe@contoso.com. Tento řetězec může mít hodnotu null, zatímco HomeAccountId a HomeAccountId. Identifier nebudou mít hodnotu null. Tato vlastnost nahrazuje vlastnost `DisplayableId` `IUser` v předchozích verzích systému MSAL.NET. |
| `Environment` | Řetězec obsahující zprostředkovatele identity pro tento účet, například `login.microsoftonline.com`. Tato vlastnost nahrazuje vlastnost `IdentityProvider` `IUser`, s výjimkou toho, že `IdentityProvider` také obsahovala informace o tenantovi (kromě cloudového prostředí), zatímco tato hodnota je pouze hostitelem. |
| `HomeAccountId` | ID účtu domovského účtu uživatele Tato vlastnost jednoznačně identifikuje uživatele napříč klienty Azure AD. |

### <a name="using-the-token-to-call-a-protected-api"></a>Použití tokenu k volání chráněného rozhraní API

Jakmile `AuthenticationResult` vrátila služba MSAL (v `result`), musíte ji přidat do hlavičky autorizace protokolu HTTP a teprve potom provést volání pro přístup k chráněnému webovému rozhraní API.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```