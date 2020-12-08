---
title: Rychlý Start – Azure Key Vault klientské knihovny pro certifikáty pro .NET (verze 4)
description: Naučte se vytvářet, načítat a odstraňovat certifikáty z trezoru klíčů Azure pomocí klientské knihovny .NET (verze 4).
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b40a13b84a0191b5c454d7edac2226f8f06fadcd
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780157"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-net-sdk-v4"></a>Rychlý Start: Azure Key Vault klientské knihovny certifikátů pro .NET (SDK v4)

Začněte s klientskou knihovnou certifikátů Azure Key Vault pro .NET. [Azure Key Vault](../general/overview.md) je cloudová služba, která poskytuje zabezpečené úložiště pro certifikáty. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto rychlém startu se dozvíte, jak vytvářet, načítat a odstraňovat certifikáty z trezoru klíčů Azure pomocí klientské knihovny .NET.

Prostředky klientské knihovny Key Vault:

[Referenční dokumentace k](/dotnet/api/azure.security.keyvault.certificates)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)

Další informace o Key Vault a certifikátech najdete v tématech:
- [Přehled Key Vault](../general/overview.md)
- [Přehled certifikátů](about-certificates.md).

## <a name="prerequisites"></a>Předpoklady

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

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění certifikátu vašemu uživatelskému účtu.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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

Z příkazového prostředí nainstalujte Azure Key Vault klientskou knihovnu certifikátů pro .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Certificates
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

Klientská knihovna certifikátů Azure Key Vault pro .NET umožňuje správu certifikátů. V části [Příklady kódu](#code-examples) se dozvíte, jak vytvořit klienta, nastavit certifikát, načíst certifikát a odstranit certifikát.

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidat direktivy

Do horní části *program.cs* přidejte následující direktivy:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Certificates;
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V tomto rychlém startu se přihlášený uživatel používá k ověření v trezoru klíčů, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V níže uvedeném příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu [' DefaultAzureCredential () '](/dotnet/api/azure.identity.defaultazurecredential) z [knihovny Azure identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme), která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace o ověřování do trezoru klíčů najdete v tématu [Příručka pro vývojáře](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

```csharp
string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-certificate"></a>Uložení certifikátu

V tomto příkladu můžete pro jednoduchost použít certifikát podepsaný svým držitelem s výchozími zásadami vystavování. Pro tuto úlohu použijte metodu [StartCreateCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startcreatecertificateasync) . Parametry metody akceptují název certifikátu a [zásady certifikátu](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificatepolicy).

```csharp
var operation = await client.StartCreateCertificateAsync("myCertificate", CertificatePolicy.Default);
var certificate = await operation.WaitForCompletionAsync();
```

> [!NOTE]
> Pokud název certifikátu existuje, vytvoří se nad kódem nová verze tohoto certifikátu.

### <a name="retrieve-a-certificate"></a>Načtení certifikátu

Nyní můžete načíst dříve vytvořený certifikát pomocí metody [GetCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.getcertificateasync) .

```csharp
var certificate = await client.GetCertificateAsync("myCertificate");
```

### <a name="delete-a-certificate"></a>Odstranit certifikát

Nakonec smažte a vyprázdnit certifikát z trezoru klíčů pomocí metod [StartDeleteCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startdeletecertificateasync) a [PurgeDeletedCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.purgedeletedcertificateasync)  .

```csharp
var operation = await client.StartDeleteCertificateAsync("myCertificate");

// You only need to wait for completion if you want to purge or recover the certificate.
await operation.WaitForCompletionAsync();

var certificate = operation.Value;
await client.PurgeDeletedCertificateAsync("myCertificate");
```

## <a name="sample-code"></a>Ukázka kódu

Upravte konzolovou aplikaci .NET Core tak, aby spolupracovala s Key Vault, a to provedením následujících kroků:

- Nahraďte kód v *program.cs* následujícím kódem:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Certificates;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string certificateName = "myCertificate";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a certificate in {keyVaultName} called '{certificateName}' ...");
                CertificateOperation operation = await client.StartCreateCertificateAsync(certificateName, CertificatePolicy.Default);
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
    
                Console.WriteLine($"Retrieving your certificate from {keyVaultName}.");
                var certificate = await client.GetCertificateAsync(certificateName);
                Console.WriteLine($"Your certificate version is '{certificate.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your certificate from {keyVaultName} ...");
                DeleteCertificateOperation deleteOperation = await client.StartDeleteCertificateAsync(certificateName);
                // You only need to wait for completion if you want to purge or recover the certificate.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine(" done.");

                Console.Write($"Purging your certificate from {keyVaultName} ...");
                await client.PurgeDeletedCertificateAsync(certificateName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testování a ověření

Spusťte následující příkaz a sestavte projekt.

```dotnetcli
dotnet build
```

Zobrazí se varianta následujícího výstupu:

```console
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Trezor klíčů, uložili certifikát a načetli jste tento certifikát. 

Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete v následujících článcích:

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Přečtěte si [Přehled certifikátů](about-certificates.md) .
- Přečtěte si [kurz Key Vault přístupu z App Service aplikace](../general/tutorial-net-create-vault-azure-web-app.md) .
- Podívejte se na [kurz Key Vault přístupu z virtuálního počítače](../general/tutorial-net-virtual-machine.md) .
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)
