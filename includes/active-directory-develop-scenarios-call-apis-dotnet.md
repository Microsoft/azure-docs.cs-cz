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
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121862"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Vlastnosti AuthenticationResult v MSAL.NET

Vrátí metody k získání tokenů `AuthenticationResult` (nebo pro asynchronní metody `Task<AuthenticationResult>`.

V MSAL.NET `AuthenticationResult` poskytuje:

- `AccessToken` pro webové rozhraní API pro přístup k prostředkům. Tento parametr je řetězec, obvykle kódováním Base 64 tokenů JWT ale klient by měl vypadat nikdy uvnitř tokenu přístupu. Není zaručeno, že formát zůstat a může být šifrována pro prostředek. Uživatelé psaní kódu v závislosti na token přístup k obsahu na straně klienta je jedním z největších zdroje chyb a zalomení logiky klienta. Viz také [přístupové tokeny](../articles/active-directory/develop/access-tokens.md)
- `IdToken` pro uživatele (Tento parametr je kódovaný JWT). Zobrazit [tokeny typu ID](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` Určuje datum a čas, kdy vyprší platnost tokenu
- `TenantId` obsahuje tenanta, ve kterém byl uživatel nalezen. Pro uživatele typu Host (scénáře B2B ve službě Azure AD) je ID Tenanta tenanta hosta, není jedinečný tenanta.
Při doručení se token pro uživatele, `AuthenticationResult` také obsahuje informace o uživateli. Pro toky důvěrnému klientovi, kde jsou požadovány tokeny bez uživatele (aplikace) tyto informace o uživatelích má hodnotu null.
- `Scopes` Pro token vydala.
- Jedinečné Id pro uživatele.

### <a name="iaccount"></a>IAccount

Definuje pojem účet MSAL.NET (až `IAccount` rozhraní). Tento narušující změně poskytne tak sémantiku vpravo: skutečnost, že stejný uživatel může mít několik účtů v různých Azure AD adresáře. MSAL.NET také poskytuje lepší informace v případě scénáře typu Host, získáte informace o domácí účtu.
Následující diagram znázorňuje strukturu `IAccount` rozhraní:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` Třídy identifikuje účet v konkrétního tenanta. Má následující vlastnosti:

| Vlastnost | Popis |
|----------|-------------|
| `TenantId` | Řetězcové vyjádření identifikátoru GUID, který je ID tenanta, ve kterém je tento účet umístěný. |
| `ObjectId` | Řetězcové vyjádření identifikátoru GUID, který je ID uživatele, který vlastní tento účet v tenantovi. |
| `Identifier` | Jedinečný identifikátor pro tento účet. `Identifier` je zřetězení `ObjectId` a `TenantId` oddělená čárkami a jsou není kódování base64. |

`IAccount` Rozhraní představuje informace o jednom účtu. Stejný uživatel může být k dispozici v různých tenantech, to znamená, že uživatel může mít několik účtů. Její členy jsou:

| Vlastnost | Popis |
|----------|-------------|
| `Username` | Řetězec obsahující hodnotu zobrazitelné ve formátu UserPrincipalName (UPN), například john.doe@contoso.com. Tento řetězec může být null, zatímco HomeAccountId a HomeAccountId.Identifier nesmí mít hodnotu null. Nahradí tato vlastnost `DisplayableId` vlastnost `IUser` v předchozích verzích MSAL.NET. |
| `Environment` | Řetězec obsahující zprostředkovatele identity pro tento účet, například `login.microsoftonline.com`. Nahradí tato vlastnost `IdentityProvider` vlastnost `IUser`, s tím rozdílem, že `IdentityProvider` také měli informace o klientovi (kromě cloudovém prostředí), že tady hodnota je jenom hostitele. |
| `HomeAccountId` | AccountId domácí účtem pro uživatele. Tato vlastnost jednoznačně identifikuje uživatele mezi tenanty Azure AD. |

### <a name="using-the-token-to-call-a-protected-api"></a>Pomocí tokenu volat chráněné rozhraní API

Jednou `AuthenticationResult` byla vrácena MSAL (v `result`), je třeba přidat do hlavičky autorizace HTTP před uskutečněním hovoru pro přístup k chráněné webové rozhraní API.

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```