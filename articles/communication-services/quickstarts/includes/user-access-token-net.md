---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 5c9066f369183de3b4cfe19cc5635e8f1b4a94a2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779117"
---
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nejnovější verze [klientské knihovny .NET Core](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém.
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../create-communication-resource.md).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `UserAccessTokensQuickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: **program.cs**.

```console
dotnet new console -o UserAccessTokensQuickstart
```

Změňte adresář na nově vytvořenou složku aplikace a použijte `dotnet build` příkaz pro zkompilování aplikace.

```console
cd UserAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalace balíčku

Stále v adresáři aplikace nainstalujte knihovnu pro správu služby Azure Communication Services pro balíček .NET pomocí `dotnet add package` příkazu.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.2
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Otevřít soubor **program.cs** v textovém editoru
1. Přidat `using` direktivu, která zahrnuje `Azure.Communication.Administration` obor názvů
1. Aktualizujte `Main` deklaraci metody pro podporu asynchronního kódu.

Pro začátek použijte následující kód:

```csharp
using System;
using Azure.Communication.Administration;

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Ověření klienta

Inicializujte `CommunicationIdentityClient` pomocí připojovacího řetězce. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../create-communication-resource.md#store-your-connection-string).

Do metody `Main` přidejte následující kód:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>Vytvoření uživatele

Komunikační služby Azure udržují zjednodušený adresář identit. Použijte `createUser` metodu k vytvoření nové položky v adresáři s jedinečným objektem `Id` . Měli byste udržovat mapování mezi uživateli vaší aplikace a komunikačními službami, které vygenerovaly identity (například jejich uložením do databáze aplikačního serveru).

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>Vystavení přístupových tokenů uživatele

Použijte `issueToken` metodu pro vydání přístupového tokenu pro uživatele komunikačních služeb. Pokud nezadáte volitelný parametr, vytvoří se `user` Nový uživatel, který se vrátí s tokenem.

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Tokeny přístupu uživatele jsou krátkodobé přihlašovací údaje, které je potřeba znovu vystavit, aby nedocházelo k přerušení služeb uživatelů. `expiresOn`Vlastnost Response označuje dobu života tokenu.

## <a name="revoke-user-access-tokens"></a>Odvolat tokeny přístupu uživatele

V některých případech může být nutné explicitně odvolat tokeny přístupu uživatele, například když uživatel změní heslo, které používá k ověření vaší služby. Tato funkce je k dispozici prostřednictvím klientské knihovny pro správu služby Azure Communication Services.

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>Odstranění uživatele

Odstranění identity odvolá všechny aktivní tokeny a zabrání vystavení následných tokenů pro identity. Zároveň se tím odebere veškerý trvalý obsah přidružený k uživateli.

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `dotnet run` příkazu.

```console
dotnet run
```
