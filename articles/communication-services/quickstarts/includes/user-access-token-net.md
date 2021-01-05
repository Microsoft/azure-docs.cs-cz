---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 4c05b654b6714c5317e1334d3a3ea5c327a5ff18
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770809"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nejnovější verze [klientské knihovny .NET Core](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém.
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../create-communication-resource.md).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `AccessTokensQuickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: **program.cs**.

```console
dotnet new console -o AccessTokensQuickstart
```

Změňte adresář na nově vytvořenou složku aplikace a použijte `dotnet build` příkaz pro zkompilování aplikace.

```console
cd AccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalace balíčku

Stále v adresáři aplikace nainstalujte knihovnu pro správu služby Azure Communication Services pro balíček .NET pomocí `dotnet add package` příkazu.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.3
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Otevřít soubor **program.cs** v textovém editoru
1. Přidat `using` direktivu, která zahrnuje `Azure.Communication.Administration` obor názvů
1. Aktualizujte `Main` deklaraci metody pro podporu asynchronního kódu.

Pro začátek použijte následující kód:

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Administration;

namespace AccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```
## <a name="authenticate-the-client"></a>Ověření klienta

Inicializujte `CommunicationIdentityClient` pomocí připojovacího řetězce. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../create-communication-resource.md#store-your-connection-string).

Do metody `Main` přidejte následující kód:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>Vytvoření identity

Komunikační služby Azure udržují zjednodušený adresář identit. Použijte `createUser` metodu k vytvoření nové položky v adresáři s jedinečným objektem `Id` . Uložte si získanou identitu s mapováním na uživatele vaší aplikace. Například uložením do databáze aplikačního serveru. Identita se vyžaduje později pro vydávání přístupových tokenů.

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>Vydávání tokenů přístupu k identitám

Použijte `issueToken` metodu pro vydání přístupového tokenu pro již existující identitu komunikačních služeb. Parametr `scopes` definuje sadu primitivních hodnot, které budou autorizovat tento přístupový token. Podívejte se na [seznam podporovaných akcí](../../concepts/authentication.md). Nová instance parametru `communicationUser` se dá sestavit na základě řetězcové reprezentace identity komunikační služby Azure.

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Přístupové tokeny jsou krátkodobé přihlašovací údaje, které je potřeba znovu vydat. V takovém případě může dojít k přerušení činnosti uživatelů vaší aplikace. `expiresOn`Vlastnost Response označuje dobu života přístupového tokenu. 

## <a name="refresh-access-tokens"></a>Obnovení přístupových tokenů

Chcete-li aktualizovat přístupový token, předejte instanci `CommunicationUser` objektu do `IssueTokenAsync` . Pokud jste to uložili `Id` a potřebujete vytvořit nový `CommunicationUser` , můžete tak učinit předáním uloženého `Id` do `CommunicationUser` konstruktoru následujícím způsobem:

```csharp  
// In this example, userId is a string containing the Id property of a previously-created CommunicationUser
identityToRefresh = new CommunicationUser(userId);
tokenResponse = await client.IssueTokenAsync(identityToRefresh, scopes: new [] { CommunicationTokenScope.VoIP });
```

## <a name="revoke-access-tokens"></a>Odvolat přístupové tokeny

V některých případech je možné explicitně odvolat přístupové tokeny. Například když uživatel aplikace změní heslo, které používá k ověření vaší služby. Metoda `RevokeTokensAsync` zruší platnost všech aktivních přístupových tokenů, které byly vystaveny identitě.

```csharp  
await client.RevokeTokensAsync(identity);
Console.WriteLine($"\nSuccessfully revoked all access tokens for identity with ID: {identity.Id}");
```

## <a name="delete-an-identity"></a>Odstranění identity

Odstranění identity odvolá všechny aktivní přístupové tokeny a zabrání vám v vydávání přístupových tokenů pro tyto identity. Zároveň se tím odebere veškerý trvalý obsah přidružený k identitě.

```csharp
await client.DeleteUserAsync(identity);
Console.WriteLine($"\nDeleted the identity with ID: {identity.Id}");
```

## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `dotnet run` příkazu.

```console
dotnet run
```
