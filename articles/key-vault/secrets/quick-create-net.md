---
title: Rychlý Start-Azure Key Vault Klientská knihovna pro .NET (verze 4)
description: Naučte se vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny .NET (verze 4).
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: dcf7c8db955b2e85ad7d1c047c714eb2c5968455
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780803"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Rychlý Start: Klientská knihovna Azure Key Vault tajných klíčů pro .NET (SDK v4)

Začínáme s klientskou knihovnou Azure Key Vault tajných klíčů pro .NET [Azure Key Vault](../general/overview.md) je cloudová služba, která poskytuje zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto rychlém startu se naučíte vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny .NET.

Prostředky klientské knihovny Key Vault:

[Referenční dokumentace k](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet&preserve-view=true)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Další informace o Key Vault a tajných klíčích najdete v tématech:
- [Přehled Key Vault](../general/overview.md)
- [Přehled tajných klíčů](about-secrets.md)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/dotnet) .
* [.NET Core 3,1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* Key Vault – můžete ho vytvořit pomocí [Azure Portal](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)nebo [Azure PowerShell](../general/quick-create-powershell.md)

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

### <a name="grant-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění tajnosti vašemu uživatelskému účtu.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
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

V příkazovém prostředí nainstalujte klientskou knihovnu Azure Key Vault tajných klíčů pro .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
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
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS nebo Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektový model

Klientská knihovna Azure Key Vault tajných klíčů pro .NET umožňuje správu tajných kódů. V části [Příklady kódu](#code-examples) se dozvíte, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidat direktivy

Do horní části *program.cs* přidejte následující direktivy:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V tomto rychlém startu se přihlášený uživatel používá k ověření v trezoru klíčů, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V níže uvedeném příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu [' DefaultAzureCredential () '](/dotnet/api/azure.identity.defaultazurecredential) z [knihovny Azure identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace o ověřování do trezoru klíčů najdete v tématu [Příručka pro vývojáře](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Uložení tajného klíče

Teď, když je aplikace konzoly ověřená, přidejte tajný klíč do trezoru klíčů. Pro tuto úlohu použijte metodu [SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) . První parametr metody přijímá název tajného klíče &mdash; "mySecret" v této ukázce.

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Pokud název tajného klíče existuje, vytvoří se v kódu nad kódem nová verze tohoto tajného klíče.


### <a name="retrieve-a-secret"></a>Načtení tajného kódu

Nyní můžete načíst dříve nastavenou hodnotu pomocí metody [GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync) .

```csharp
var secret = await client.GetSecretAsync(secretName);
```

Váš tajný kód se teď uloží jako `secret.Value` .

### <a name="delete-a-secret"></a>Odstranění tajného klíče

Nakonec smažte tajný klíč z vašeho trezoru klíčů pomocí metod [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) a [PurgeDeletedSecretAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedsecretasync) .

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>Ukázka kódu

Upravte konzolovou aplikaci .NET Core tak, aby spolupracovala s Key Vault, a to provedením následujících kroků:

1. Nahraďte kód v *program.cs* následujícím kódem:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testování a ověření

1. Spusťte následující příkaz, který aplikaci spustí.

    ```dotnetcli
    dotnet run
    ```

1. Po zobrazení výzvy zadejte tajnou hodnotu. Například mySecretPassword.

Zobrazí se varianta následujícího výstupu:

```console
Input the value of your secret > mySecretPassword
Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
Forgetting your secret.
Your secret is ''.
Retrieving your secret from <your-unique-keyvault-name>.
Your secret is 'mySecretPassword'.
Deleting your secret from <your-unique-keyvault-name> ... done.    
Purging your secret from <your-unique-keyvault-name> ... done.
```

## <a name="next-steps"></a>Další kroky

Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete v následujících článcích:

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Přečtěte si [kurz Key Vault přístupu z App Service aplikace](../general/tutorial-net-create-vault-azure-web-app.md) .
- Podívejte se na [kurz Key Vault přístupu z virtuálního počítače](../general/tutorial-net-virtual-machine.md) .
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)