---
ms.openlocfilehash: 0bfb23977f6553568da24df614621bdf1eb9d06d
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113174"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nejnovější verze [klientské knihovny .NET Core](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém.
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

- V terminálu nebo příkazovém okně spusťte `dotnet` příkaz a ověřte, zda je nainstalována knihovna klienta .NET.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `PhoneNumbersQuickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: **program. cs**.

```console
dotnet new console -o PhoneNumbersQuickstart
```

Změňte adresář na nově vytvořenou složku aplikace a použijte `dotnet build` příkaz pro zkompilování aplikace.

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalace balíčku

Ještě pořád v adresáři aplikace nainstalujte klientskou knihovnu Azure Communication PhoneNumbers pro balíček .NET pomocí `dotnet add package` příkazu.

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0-beta.6
```

Přidejte do `using` horní části **programu. cs** direktivu, aby zahrnovala obory názvů.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
```

Aktualizujte `Main` signaturu funkce tak, aby byla asynchronní.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>Ověření klienta

Telefonní číslo klientů lze ověřit pomocí připojovacího řetězce získaného z komunikačních prostředků Azure v [Azure Portal] [azure_portal].

```csharp
// Get a connection string to our Azure Communication resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

Klienti telefonního čísla mají taky možnost ověřování pomocí ověřování Azure Active Directory. S touto možností, `AZURE_CLIENT_SECRET` `AZURE_CLIENT_ID` a `AZURE_TENANT_ID` proměnné prostředí musí být nastavené pro ověřování.

```csharp
// Get an endpoint to our Azure Communication resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>Správa telefonních čísel

### <a name="search-for-available-phone-numbers"></a>Vyhledat dostupná telefonní čísla

Aby bylo možné koupit telefonní čísla, je třeba nejprve vyhledat dostupná telefonní čísla. Chcete-li vyhledat telefonní čísla, zadejte směrové číslo oblasti, typ přiřazení, [Možnosti telefonního čísla](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [typ telefonního čísla](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)a množství. Všimněte si, že pro typ telefonního čísla bez poplatků je zadání kódu oblasti volitelné.

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>Koupit telefonní čísla

Výsledek hledání telefonních čísel je `PhoneNumberSearchResult` . Obsahuje, `SearchId` který se dá předávat do rozhraní API nákupních čísel, aby se získaly čísla ve vyhledávání. Všimněte si, že volání rozhraní API pro telefonní čísla nákupu bude mít za následek poplatek za váš účet Azure.

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionResponseAsync();
```

### <a name="get-phone-numbers"></a>Získat telefonní čísla

Po nákupním čísle ho můžete načíst z klienta.
```csharp
var getPhoneNumberResponse = await client.GetPurchasedPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

Můžete také načíst všechna zakoupená telefonní čísla.
``` csharp
var purchasedPhoneNumbers = client.GetPurchasedPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>Aktualizace možností telefonního čísla

Po zakoupeném čísle můžete možnosti aktualizovat.

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>Telefonní číslo verze

Můžete vydat zakoupené telefonní číslo.

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionResponseAsync();
````

## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `dotnet run` příkazu.

```console
dotnet run
```

## <a name="sample-code"></a>Příklad kódu

Ukázkovou aplikaci si můžete stáhnout z [GitHubu](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers) .
