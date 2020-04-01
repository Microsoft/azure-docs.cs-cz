---
title: Úvodní příručka – klientská knihovna Azure Key Vault pro rozhraní .NET (v4)
description: Zjistěte, jak vytvořit, načíst a odstranit tajné klíče z trezoru klíčů Azure pomocí knihovny klienta .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: a94717c7bed3ba25a4682896053672fe100dc43a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398386"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Úvodní příručka: Klientská knihovna Azure Key Vault pro rozhraní .NET (SDK v4)

Začínáme s klientskou knihovnou Azure Key Vault pro rozhraní .NET. Podle následujících kroků nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.

Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí klientské knihovny Trezor klíčů pro rozhraní .NET můžete:

- Zvyšte zabezpečení a kontrolu nad klíči a hesly.
- Vytvořte a importujte šifrovací klíče během několika minut.
- Snižte latenci díky cloudovému škálování a globální redundanci.
- Zjednodušte a automatizujte úlohy pro certifikáty TLS/SSL.
- Použijte fips 140-2 úroveň 2 ověřené hmeony.

[Referenční dokumentace](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | rozhraní[API Balíček zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [knihovny (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Sada [.NET Core 2.1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/overview)

Tento rychlý start předpokládá, `dotnet`že používáte příkazy [, Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)a Windows v terminálu Windows (například [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), Windows [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)nebo [Azure Cloud Shell).](https://shell.azure.com/)

## <a name="setting-up"></a>Nastavení

### <a name="create-new-net-console-app"></a>Vytvoření nové aplikace konzoly ROZHRANÍ .NET

V okně konzoly `dotnet new` vytvořte pomocí příkazu novou konzolovou `key-vault-console-app`aplikaci .NET s názvem .

```console
dotnet new console -n key-vault-console-app
```

Změňte adresář do nově vytvořené složky aplikace. Aplikaci můžete sestavit pomocí:

```console
dotnet build
```

Výstup sestavení by měl obsahovat žádná upozornění nebo chyby.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Instalace balíčku

V okně konzoly nainstalujte klientskou knihovnu Azure Key Vault pro rozhraní .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Pro tento rychlý start budete muset nainstalovat také následující balíčky:

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

Tento rychlý start používá předem vytvořený trezor klíčů Azure. Trezor klíčů můžete vytvořit podle kroků v [rychlém startu Azure CLI](quick-create-cli.md), [rychlém startu Azure PowerShellu](quick-create-powershell.md)nebo [rychlém startu na webu Azure Portal](quick-create-portal.md). Případně můžete jednoduše spustit příkazy Azure CLI níže.

> [!Important]
> Každý trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <> názvu trezoru s jedinečným klíčem.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location EastUS
```

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Nejjednodušší způsob ověření aplikace .NET na principu shluků je se spravovanou identitou. podrobnosti najdete [v tématu Použití spravované identity služby App Service pro přístup k azure key vault](managed-identity.md) pro podrobnosti. Z důvodu jednoduchosti však tento rychlý start vytvoří aplikaci konzoly .NET. Ověření desktopové aplikace pomocí Azure vyžaduje použití instančního objektu a zásad řízení přístupu.

Vytvořte princip služby pomocí příkazu Azure CLI [az az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Tato operace vrátí řadu párů klíč / hodnota. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Vytvořte instanční objekt pomocí příkazu Azure PowerShell [New-AzADServicePrincipal:](/powershell/module/az.resources/new-azadserviceprincipal)

```azurepowershell
# Create a new service principal
$spn = New-AzADServicePrincipal -DisplayName "http://mySP"

# Get the tenant ID and subscription ID of the service principal
$tenantId = (Get-AzContext).Tenant.Id
$subscriptionId = (Get-AzContext).Subscription.Id

# Get the client ID
$clientId = $spn.ApplicationId

# Get the client Secret
$bstr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($spn.Secret)
$clientSecret = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
```

Další podrobnosti o instančním objektu s Azure PowerShell, najdete v části [Vytvoření hlavního účtu služby Azure s Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Poznamenejte si clientId, clientSecret a tenantId, jak budeme používat v následujících krocích.


#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Poskytnutí přístupu k objektu služeb trezoru klíčů

Vytvořte zásady přístupu pro trezor klíčů, který uděluje oprávnění k instančnímu objektu klienta příkazu [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Udělte instančníobjekt get, list a nastavit oprávnění pro klíče i tajné klíče.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions list get set delete purge
```

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName <your-unique-keyvault-name> -ServicePrincipalName <clientId-of-your-service-principal> -PermissionsToSecrets list,get,set,delete,purge
```

#### <a name="set-environmental-variables"></a>Nastavení proměnných prostředí

Metoda DefaultAzureCredential v naší aplikaci závisí na `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET`třech `AZURE_TENANT_ID`proměnných prostředí: , , a . použijte nastavit tyto proměnné clientId, clientSecret a tenantId hodnoty, které jste si poznamenali v [vytvořit instanční objekt](#create-a-service-principal) krok výše.

Budete také muset uložit název trezoru klíčů `KEY_VAULT_NAME`jako proměnnou prostředí nazvanou ;

```console
setx AZURE_CLIENT_ID <your-clientID>

setx AZURE_CLIENT_SECRET <your-clientSecret>

setx AZURE_TENANT_ID <your-tenantId>

setx KEY_VAULT_NAME <your-key-vault-name>
````

Pokaždé, když `setx`zavoláte , měli byste dostat odpověď na "ÚSPĚCH: Zadaná hodnota byla uložena."

```shell
AZURE_CLIENT_ID=<your-clientID>

AZURE_CLIENT_SECRET=<your-clientSecret>

AZURE_TENANT_ID=<your-tenantId>

KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektový model

Klientská knihovna Azure Key Vault pro rozhraní .NET umožňuje spravovat klíče a související prostředky, jako jsou certifikáty a tajné klíče. Ukázky kódu níže vám ukáže, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

Celá konzolová aplikace https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-appje k dispozici na adrese .

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidání směrnic

Na začátek kódu přidejte následující direktivy:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

Ověření do trezoru klíčů a vytvoření klienta trezoru klíčů závisí na proměnných prostředí v kroku [Nastavit proměnné prostředí](#set-environmental-variables) výše. Název trezoru klíčů je rozšířen na identifikátor URI trezoru\<klíčů ve formátu\>"https:// název trezoru klíčů .vault.azure.net".

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Uložení tajného klíče

Nyní, když je aplikace ověřena, můžete vložit tajný klíč do trezoru klíčů pomocí [klienta. SetSecret metoda](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) To vyžaduje název pro tajný klíč -- používáme "mySecret" v této ukázce.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Pomocí příkazu [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) můžete ověřit, zda byl tajný klíč nastaven:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Načtení tajného klíče

Nyní můžete načíst dříve nastavenou hodnotu s [klientem. GetSecret metoda](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Vaše tajemství je `secret.Value`nyní uloženo jako .

### <a name="delete-a-secret"></a>Odstranění tajného klíče

Nakonec odstraníme tajný klíč z trezoru klíčů s [klientem. DeleteSecret metoda](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Můžete ověřit, že tajný klíč je pryč s [az keyvault tajné show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) příkazu:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete použít Azure CLI nebo Azure PowerShell odebrat trezor klíčů a odpovídající skupinu prostředků.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Ukázka kódu

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
}
```


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor klíčů, uložili tajný klíč a získali tento tajný klíč. Podívejte se na [celou konzolovou aplikaci na GitHubu](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Chcete-li se dozvědět více o trezoru klíčů a o tom, jak jej integrovat s aplikacemi, pokračujte v následujících článcích.

- Implementace [ověřování mezi službami do trezoru klíčů Azure pomocí rozhraní .NET](service-to-service-authentication.md)
- Přečtěte si [přehled trezoru klíčů Azure](key-vault-overview.md)
- Podívejte se na [průvodce vývojářem azure key vaultu](key-vault-developers-guide.md)
- Informace o [klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
- Kontrola [doporučených postupů azure key vaultu](key-vault-best-practices.md)
