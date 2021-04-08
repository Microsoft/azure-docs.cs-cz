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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "76308949"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Vlastnosti AuthenticationResult v MSAL.NET

Metody získání tokenů pro vrácení `AuthenticationResult` . Pro asynchronní metody `Task<AuthenticationResult>` vrátí.

V MSAL.NET `AuthenticationResult` zpřístupňuje:

- `AccessToken` pro webové rozhraní API získáte přístup k prostředkům. Tento parametr je řetězec, obvykle token JWT s kódováním Base-64. Klient by nikdy neměl Hledat v přístupovém tokenu. Formát není zaručený, aby zůstal stabilní a mohl by být zašifrovaný pro daný prostředek. Psaní kódu, který závisí na obsahu přístupového tokenu na klientovi, je jedním z největších zdrojů chyb a konců klientských logik. Další informace najdete v tématu [přístupové tokeny](../articles/active-directory/develop/access-tokens.md).
- `IdToken` pro uživatele. Tento parametr je kódovaným tokenem JWT. Další informace najdete v tématu [tokeny ID](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn` Určuje datum a čas, kdy vyprší platnost tokenu.
- `TenantId` obsahuje tenanta, ve kterém se uživatel našel. Pro uživatele typu Host ve scénářích Azure Active Directory (Azure AD) B2B se ID tenanta hostuje jako tenant, nikoli jedinečný tenant.
Když se token doručí uživateli, `AuthenticationResult` obsahuje taky informace o tomto uživateli. U důvěrných toků klienta, kde jsou požadovány tokeny bez uživatele pro aplikaci, jsou informace o uživateli null.
- `Scopes`Pro který byl token vydán.
- Jedinečné ID uživatele

### <a name="iaccount"></a>IAccount

MSAL.NET definuje pojem účtu přes `IAccount` rozhraní. Tato Průlomová změna poskytuje správnou sémantiku. Stejný uživatel může mít několik účtů v různých adresářích Azure AD. MSAL.NET také poskytuje lepší informace v případě hostujících scénářů, protože jsou k dispozici informace o domácím účtu.
Následující diagram znázorňuje strukturu `IAccount` rozhraní.

![Struktura rozhraní IAccount](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId`Třída identifikuje účet v konkrétním tenantovi s vlastnostmi, které jsou uvedeny v následující tabulce.

| Vlastnost | Popis |
|----------|-------------|
| `TenantId` | Řetězcová reprezentace pro identifikátor GUID, což je ID tenanta, ve kterém se účet nachází. |
| `ObjectId` | Řetězcová reprezentace pro identifikátor GUID, což je ID uživatele, který je vlastníkem účtu v tenantovi. |
| `Identifier` | Jedinečný identifikátor účtu `Identifier` je zřetězení `ObjectId` a `TenantId` oddělené čárkou. Nejsou zakódované v kódování Base 64. |

`IAccount`Rozhraní představuje informace o jednom účtu. Stejný uživatel může být přítomen v různých klientech, což znamená, že uživatel může mít více účtů. Její členové jsou uvedeni v následující tabulce.

| Vlastnost | Popis |
|----------|-------------|
| `Username` | Řetězec, který obsahuje zobrazitelnou hodnotu ve formátu UserPrincipalName (UPN), například john.doe@contoso.com . Tento řetězec může mít hodnotu null, na rozdíl od HomeAccountId a HomeAccountId. Identifier, což nebude mít hodnotu null. Tato vlastnost nahrazuje `DisplayableId` vlastnost `IUser` v předchozích verzích MSAL.NET. |
| `Environment` | Řetězec, který obsahuje zprostředkovatele identity pro tento účet, například `login.microsoftonline.com` . Tato vlastnost nahrazuje `IdentityProvider` vlastnost třídy `IUser` , kromě toho, že `IdentityProvider` kromě cloudového prostředí obsahovala také informace o tenantovi. Zde je hodnota pouze hostitel. |
| `HomeAccountId` | ID účtu domovského účtu uživatele Tato vlastnost jednoznačně identifikuje uživatele napříč klienty Azure AD. |

### <a name="use-the-token-to-call-a-protected-api"></a>Použití tokenu k volání chráněného rozhraní API

Poté `AuthenticationResult` , co se vrátí pomocí MSAL v `result` , přidejte ho do hlavičky Authorization protokolu HTTP a potom zajistěte, aby bylo volání přístupu k chráněnému webovému rozhraní API.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```