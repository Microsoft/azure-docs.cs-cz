---
title: Rychlý Start – Azure Key Vault klientských knihoven pro .NET (verze 4)
description: Naučte se vytvářet, načítat a odstraňovat klíče z trezoru klíčů Azure pomocí klientské knihovny .NET (verze 4).
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: cecf8330b7060a4cbc4691f64571a3c7865c575c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935253"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Rychlý Start: Azure Key Vault klíč klientské knihovny pro .NET (SDK v4)

Začněte s knihovnou klienta Azure Key Vault Key pro .NET. [Azure Key Vault](../general/overview.md) je cloudová služba, která poskytuje zabezpečené úložiště pro kryptografické klíče. Můžete bezpečně ukládat kryptografické klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto rychlém startu se naučíte vytvářet, načítat a odstraňovat klíče z trezoru klíčů Azure pomocí klientské knihovny .NET Key.

Prostředky klientské knihovny Key Vault klíčů:

[Referenční dokumentace k](/dotnet/api/azure.security.keyvault.keys)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

Další informace o Key Vault a klíčích najdete v tématech:
- [Přehled Key Vault](../general/overview.md)
- [Přehled klíčů](about-keys.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/dotnet) .
* [.NET Core 3,1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* Key Vault – můžete ho vytvořit pomocí [Azure Portal](../general/quick-create-portal.md), rozhraní příkazového [řádku Azure](../general/quick-create-cli.md)nebo [Azure PowerShell](../general/quick-create-powershell.md).

Tento rychlý Start používá `dotnet` a Azure CLI

## <a name="setup"></a>Nastavení

Tento rychlý Start používá Azure identity Library s Azure CLI k ověřování uživatele ve službách Azure. Vývojáři můžou k ověřování hovorů použít taky Visual Studio nebo Visual Studio Code, další informace najdete v tématu [ověření klienta pomocí klientské knihovny Azure identity](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Spusťte příkaz `login`.

    ```azurecli-interactive
    az login
    ```

    Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.

    V opačném případě otevřete stránku prohlížeče na adrese [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadejte autorizační kód zobrazený v terminálu.

2. Přihlaste se pomocí přihlašovacích údajů vašeho účtu v prohlížeči.

#### <a name="grant-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje klíčovým oprávněním k vašemu uživatelskému účtu.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

### <a name="create-new-net-console-app"></a>Vytvořit novou konzolovou aplikaci .NET

1. V příkazovém prostředí spusťte následující příkaz, který vytvoří projekt s názvem `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Přejděte do nově vytvořeného adresáře *Key-trezor-Console-App* a spuštěním následujícího příkazu Sestavte projekt:

    ```dotnetcli
    dotnet build
    ```

    Výstup sestavení by neměl obsahovat žádná upozornění ani chyby.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Nainstalovat balíčky

Z příkazového prostředí nainstalujte klientskou knihovnu Azure Key Vault Key pro .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
```

Pro tento rychlý Start budete taky muset nainstalovat klientskou knihovnu Azure SDK pro identitu Azure:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Tato aplikace používá název trezoru klíčů jako proměnnou prostředí s názvem `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS nebo Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektový model

Klíčová knihovna klienta Azure Key Vault pro .NET umožňuje správu klíčů. V části [Příklady kódu](#code-examples) se dozvíte, jak vytvořit klienta, nastavit klíč, načíst klíč a odstranit klíč.

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidat direktivy

Do horní části *program.cs* přidejte následující direktivy:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V tomto rychlém startu se přihlášený uživatel používá k ověření v trezoru klíčů, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V níže uvedeném příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu [' DefaultAzureCredential () '](/dotnet/api/azure.identity.defaultazurecredential) z [knihovny Azure identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace o ověřování do trezoru klíčů najdete v tématu [Příručka pro vývojáře](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Uložení klíče

Pro tuto úlohu použijte metodu [CreateKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync) . Parametry metody akceptují název klíče a [typ klíče](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype).

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Pokud existuje klíč s názvem, bude výše uvedený kód vytvářet novou verzi tohoto klíče.

### <a name="retrieve-a-key"></a>Načtení klíče

Nyní můžete načíst dříve vytvořený klíč pomocí metody [GetKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync) .

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Odstranění klíče

Nakonec tento klíč odstraníme a vymažeme z vašeho trezoru klíčů pomocí metod [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) a [PurgeDeletedKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync) .

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Ukázka kódu

Upravte konzolovou aplikaci .NET Core tak, aby spolupracovala s Key Vault, a to provedením následujících kroků:

- Nahraďte kód v *program.cs* následujícím kódem:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testování a ověření

1.  Spusťte následující příkaz a sestavte projekt.

    ```dotnetcli
    dotnet build
    ```

1. Spusťte následující příkaz, který aplikaci spustí.

    ```dotnetcli
    dotnet run
    ```

1. Po zobrazení výzvy zadejte tajnou hodnotu. Například mySecretPassword.

    Zobrazí se varianta následujícího výstupu:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Trezor klíčů, uložili klíč a načetli jste tento klíč. 

Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete v následujících článcích:

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Přečtěte si [Přehled klíčů](about-keys.md) .
- Přečtěte si [kurz Key Vault přístupu z App Service aplikace](../general/tutorial-net-create-vault-azure-web-app.md) .
- Podívejte se na [kurz Key Vault přístupu z virtuálního počítače](../general/tutorial-net-virtual-machine.md) .
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Přečtěte si [Přehled zabezpečení Key Vault](../general/security-overview.md)
