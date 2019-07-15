---
title: Kurz – použití virtuálního počítače s Linuxem a technologie ASP.NET konzolovou aplikaci pro ukládání tajných klíčů ve službě Azure Key Vault | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak nakonfigurovat aplikaci ASP.NET Core pro čtení tajného klíče ze služby Azure Key vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8c142b4c1663a7ccd462836e9aac4ee80accaae4
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874833"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Kurz: Tajné kódy ukládat v Azure Key Vault pomocí virtuálního počítače s Linuxem a aplikace .NET

Azure Key Vault pomáhá chránit tajné kódy jako jsou klíče rozhraní API a databázové připojovací řetězce, které jsou potřeba pro přístup k aplikacím, služby a prostředky IT.

V tomto kurzu nastavíte konzolovou aplikaci .NET pro čtení informací z Azure Key Vault pomocí spravované identity pro prostředky Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte trezor klíčů
> * Store tajného klíče ve službě Key Vault
> * Vytvoření virtuálního počítače Azure s Linuxem
> * Povolit [se identita spravované](../active-directory/managed-identities-azure-resources/overview.md) pro virtuální počítač
> * Přidělení požadovaných oprávnění pro konzolovou aplikaci pro čtení dat ze služby Key Vault
> * Získání tajného klíče ze služby Key Vault

Než půjdeme dál, přečtěte si o [klíče trezoru základní koncepty](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Požadavky

* [Git](https://git-scm.com/downloads)
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* [Azure CLI 2.0 nebo novější](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nebo Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Vysvětlení služby Managed Service Identity

Azure Key Vault umožňuje bezpečné ukládání přihlašovacích údajů, aby nemusely být v kódu. Abyste je však mohli načíst, musíte se ověřit ve službě Azure Key Vault. Však k ověření do služby Key Vault, budete potřebovat přihlašovací údaje. Je classic bootstrap problém. S Azure a Azure Active Directory (Azure AD) Identity spravované služby (MSI) můžete poskytovat bootstrap identitou, která umožňuje mnohem jednodušší, začněte tím.

Při povolení MSI pro službu Azure, jako jsou virtuální počítače, služby App Service nebo funkce, Azure vytvoří službu instanční objekt pro instanci služby v Azure Active Directory. Přihlašovací údaje pro instanční objekt se vkládá do instance služby.

![MSI](media/MSI.png)

V dalším kroku váš kód volá místních metadat služba k dispozici u prostředku Azure získat přístupový token.
Váš kód použije přístupový token, který získá z místního koncového bodu MSI_ENDPOINT, k ověření ve službě Azure Key Vault.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure pomocí rozhraní příkazového řádku Azure, zadejte:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků s použitím `az group create` příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků v umístění západní USA. Vyberte název vaší skupiny prostředků a nahraďte `YourResourceGroupName` v následujícím příkladu:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Tato skupina prostředků v rámci tohoto kurzu použijete.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

V dalším kroku vytvoření služby key vault ve vaší skupině prostředků. Zadejte tyto informace:

* Název trezoru klíčů: řetězec dlouhý 3 až 24 znaků, které mohou obsahovat pouze číslice, písmena a pomlčky (0-9, a – z, A-Z, a \- ).
* Název skupiny prostředků
* Umístění: **Západní USA**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

V tomto okamžiku pouze váš účet Azure je oprávněn provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Teď můžete přidat tajný klíč. Ve skutečném scénáři může být ukládání připojovací řetězec SQL nebo nějakých jiných informací, které potřebujete zachovat bezpečně, ale je k dispozici pro vaši aplikaci.

Pro účely tohoto kurzu zadejte následující příkazy k vytvoření tajného klíče v trezoru klíčů. Tajný kód se nazývá **AppSecret** a jeho hodnota může být **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Vytvoření virtuálního počítače s Linuxem

Vytvoření virtuálního počítače s `az vm create` příkazu.

Následující příklad vytvoří virtuální počítač **myVM** a přidá uživatelský účet **azureuser**. `--generate-ssh-keys` Parametr nám použít k automatickému generování SSH klíče a vložit ho do výchozím umístění klíčů ( **~/.ssh**). Pokud místo toho chcete použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut. Následující příklad výstupu ukazuje, že operace je vytvoření virtuálního počítače bylo úspěšné.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Poznamenejte si vaše `publicIpAddress` ve výstupu z vašeho virtuálního počítače. Tuto adresu budete používat pro přístup k virtuálnímu počítači v dalších krocích.

## <a name="assign-an-identity-to-the-vm"></a>Přiřadit identitu virtuálního počítače

Vytvoření identity systém přiřadil k virtuálnímu počítači spuštěním následujícího příkazu:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Výstup příkazu by měl být:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Poznamenejte si, `systemAssignedIdentity`. Použijete ho v dalším kroku.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Udělit oprávnění identity virtuálního počítače do služby Key Vault

Nyní můžete udělit oprávnění služby Key Vault k identitě, kterou jste vytvořili. Spusťte následující příkaz:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Přihlaste se k virtuálnímu počítači

Přihlaste se k virtuálnímu počítači pomocí terminálu.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Instalace .NET Core v Linuxu

Na virtuální počítač s Linuxem:

Zaregistrujte se kód product key společnosti Microsoft jako důvěryhodný spuštěním následujících příkazů:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Nastavte požadovanou verzi balíčku hostitele informačního kanálu na základě operačního systému:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Instalace rozhraní .NET a zkontrolujte verzi:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Vytvoření a spuštění ukázkové aplikace v .NET

Spusťte následující příkazy. Měli byste vidět, že "Hello World" Tisk do konzoly.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Upravit aplikaci konzoly pro načtení váš tajný klíč

Otevřete soubor Program.cs a přidejte tyto balíčky:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Je dvoustupňový proces, chcete-li změnit soubor třídy k povolení aplikace pro přístup k tajným kódem v trezoru klíčů.

1. Načtěte token z koncového bodu místní instalační služby MSI ve virtuálním počítači, která pak načte token ze služby Azure Active Directory.
1. Předat token do služby Key Vault a načtou se vaše tajný klíč.

   Upravte soubor třídy tak, aby obsahovala následující kód:

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
               System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
           }

           static string GetToken()
           {
               WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
               request.Headers.Add("Metadata", "true");
               WebResponse response = request.GetResponse();
               return ParseWebResponse(response, "access_token");
           }

           static string FetchSecretValueFromKeyVault(string token)
           {
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
               kvRequest.Headers.Add("Authorization", "Bearer "+  token);
               WebResponse kvResponse = kvRequest.GetResponse();
               return ParseWebResponse(kvResponse, "value");
           }

           private static string ParseWebResponse(WebResponse response, string tokenName)
           {
               string token = String.Empty;
               using (Stream stream = response.GetResponseStream())
               {
                   StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                   String responseString = reader.ReadToEnd();

                   JObject joResponse = JObject.Parse(responseString);
                   JValue ojObject = (JValue)joResponse[tokenName];
                   token = ojObject.Value.ToString();
               }
               return token;
           }
       }
   ```

Nyní jste zjistili, jak k provádění operací s Azure Key Vault do aplikace .NET běžící na virtuálním počítači s Linuxem v Azure.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už je budete potřebovat, odstraňte skupinu prostředků, virtuálního počítače a všech souvisejících prostředků. Uděláte to tak, vyberte skupinu prostředků příslušného virtuálního počítače a vyberte **odstranit**.

Odstranění trezoru klíčů pomocí `az keyvault delete` příkaz:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní REST API služby Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
