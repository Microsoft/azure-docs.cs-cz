---
title: Rychlý Start – Azure Key Vault Klientská knihovna pro .NET (sada SDK V3)
description: Naučte se vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny .NET (V3).
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 816cf8d385c12046a5363cf94ab5fa60e5d77e48
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078863"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Rychlý Start: Klientská knihovna Azure Key Vault pro .NET (sada SDK V3)

Začněte s klientskou knihovnou Azure Key Vault pro .NET. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

> [!NOTE]
> V tomto rychlém startu se používá verze klientské knihovny Microsoft. Azure. 3.0.4 ve verzi v. Chcete-li použít nejaktuálnější verzi klientské knihovny Key Vault, přečtěte si téma [Azure Key Vault Klientská knihovna pro .NET (sada SDK v4)](quick-create-net.md). 

[Referenční dokumentace k](https://docs.microsoft.com/dotnet/api/overview/azure/keyvault/v3)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.KeyVault)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Každý Trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <název trezoru klíčů jedinečných> s názvem vašeho trezoru klíčů.


## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3,1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli)

Tento rychlý Start používá `dotnet` a Azure CLI

## <a name="setting-up"></a>Nastavení

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Spusťte příkaz `login`.

    ```azurecli-interactive
    az login
    ```

    Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.

    V opačném případě otevřete stránku prohlížeče na adrese [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadejte autorizační kód zobrazený v terminálu.

2. Přihlaste se pomocí přihlašovacích údajů vašeho účtu v prohlížeči.

### <a name="create-new-net-console-app"></a>Vytvořit novou konzolovou aplikaci .NET

V okně konzoly pomocí `dotnet new` příkazu vytvořte novou konzolovou aplikaci .NET s názvem `akv-dotnet` .


```console
dotnet new console -n akvdotnet
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```console
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Instalace balíčku

V okně konzoly nainstalujte Azure Key Vault klientskou knihovnu pro rozhraní .NET a knihovnu [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) :

```console
dotnet add package Microsoft.Azure.KeyVault
dotnet add package Microsoft.Azure.Services.AppAuthentication
```

Pro tento rychlý Start budete muset nainstalovat i tyto balíčky:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění tajného kódu vašemu uživatelskému účtu.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

## <a name="object-model"></a>Objektový model

Klientská knihovna Azure Key Vault pro .NET umožňuje správu klíčů a souvisejících prostředků, jako jsou certifikáty a tajné klíče. Následující ukázka kódu vám ukáže, jak nastavit tajný klíč a načíst tajný kód.

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidat direktivy

Do horní části kódu přidejte následující direktivy:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Azure.KeyVault;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Management.ResourceManager.Fluent;
using Microsoft.Azure.Management.ResourceManager.Fluent.Authentication;
using Microsoft.Azure.Services.AppAuthentication;
```

### <a name="authenticate-to-your-key-vault"></a>Ověřování pro váš Trezor klíčů

Nakonfigurujte ověřování pro vaši aplikaci ve [třídě KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient). V našem příkladu použije token poskytnutý `AzureServiceTokenProvider` z aktuálně přihlášeného uživatele:

```csharp
var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));
```

Další informace o najdete `AzureServiceTokenProvider` v tématu [ověřování ve službách Azure](https://docs.microsoft.com/azure/key-vault/general/service-to-service-authentication#authenticating-to-azure-services) .

### <a name="save-a-secret"></a>Uložení tajného klíče

Teď, když je vaše aplikace ověřená, můžete do trezoru klíčů vložit tajný klíč pomocí [metody SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) , která vyžaduje adresu URL vašeho trezoru klíčů, který je ve formuláři `https://<your-unique-keyvault-name>.vault.azure.net/secrets/` . Také vyžaduje název tajného kódu – používáme "mySecret". 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Můžete ověřit, jestli je tajný kód nastavený pomocí příkazu [AZ klíčů trezor tajného zobrazení](/cli/azure/keyvault/secret?#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Načtení tajného kódu

Nyní můžete načíst dříve nastavenou hodnotu pomocí [metody GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Váš tajný kód se teď uloží jako `keyvaultSecret.Value;` .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání trezoru klíčů a odpovídající skupiny prostředků použít Azure CLI nebo Azure PowerShell.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Ukázka kódu

Upravte konzolovou aplikaci .NET Core tak, aby spolupracovala s Key Vault, a to provedením následujících kroků:

1. Nahraďte kód v *program.cs* následujícím kódem:
```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Azure.KeyVault;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Management.ResourceManager.Fluent;
using Microsoft.Azure.Management.ResourceManager.Fluent.Authentication;
// </directives>

namespace akvdotnet
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Program P = new Program();
            string secretName = "mySecret";

            // kvURL must be updated to the URL of your key vault
            string kvURL = "https://myKV.vault.azure.net";

            // <authentication>

            var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
            var kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));        
            // </authentication>


            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.Write("Saving the value of your secret to your key vault ...");

            //set secret
            await kvClient.SetSecretAsync($"{kvURL}", secretName, secretValue);

            Console.WriteLine("done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");
            Console.WriteLine("Retrieving your secret from key vault.");

            //retrieve secret
            var keyvaultSecret = await kvClient.GetSecretAsync($"{kvURL}", secretName).ConfigureAwait(false);

            secretValue = keyvaultSecret.Result;
            Console.WriteLine("Your secret is " + secretValue);
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Implementace [ověřování služby-služba pro Azure Key Vault pomocí .NET](../general/service-to-service-authentication.md)
- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)
