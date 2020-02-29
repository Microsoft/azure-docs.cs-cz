---
title: Kurz – použití virtuálního počítače se systémem Linux a konzolové aplikace ASP.NET k ukládání tajných kódů v Azure Key Vault | Microsoft Docs
description: V tomto kurzu se naučíte konfigurovat aplikaci ASP.NET Core pro čtení tajného klíče z trezoru klíčů Azure.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8c5b3fcc1cb2ac481be0b435c48ce213c716edde
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198163"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Kurz: použití virtuálního počítače se systémem Linux a aplikace .NET k ukládání tajných kódů v Azure Key Vault

Azure Key Vault pomáhá chránit tajné kódy, jako jsou klíče rozhraní API a databázové připojovací řetězce, které jsou potřeba pro přístup k aplikacím, službám a prostředkům IT.

V tomto kurzu nastavíte konzolovou aplikaci .NET pro čtení informací z Azure Key Vault pomocí spravovaných identit pro prostředky Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte trezor klíčů
> * Uložení tajného kódu v Key Vault
> * Vytvoření virtuálního počítače se systémem Azure Linux
> * Povolit [spravovanou identitu](../active-directory/managed-identities-azure-resources/overview.md) pro virtuální počítač
> * Udělte aplikaci konzoly požadovaná oprávnění ke čtení dat z Key Vault
> * Načtení tajného kódu z Key Vault

Než budeme pokračovat, přečtěte si o [základních konceptech trezoru klíčů](basic-concepts.md).

## <a name="prerequisites"></a>Předpoklady

* [Git](https://git-scm.com/downloads)
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* [Azure CLI 2,0 nebo novější](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nebo Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Pochopení Identita spravované služby

Azure Key Vault umožňuje bezpečné ukládání přihlašovacích údajů, aby nemusely být v kódu. Abyste je však mohli načíst, musíte se ověřit ve službě Azure Key Vault. K ověření Key Vault ale potřebujete přihlašovací údaje. Jedná se o klasický problém při zavádění. Pomocí Azure a Azure Active Directory (Azure AD) může Identita spravované služby (MSI) poskytovat identitu pro spuštění, která výrazně zjednodušuje zprovoznění vašich věcí.

Když povolíte MSI pro službu Azure, jako je Virtual Machines, App Service nebo Functions, Azure vytvoří instanční objekt pro instanci služby v Azure Active Directory. Vloží pověření pro instanční objekt do instance služby.

![MSI](media/MSI.png)

Dál váš kód volá místní službu metadat dostupnou v prostředku Azure a získá přístupový token.
Váš kód použije přístupový token, který získá z místního koncového bodu MSI_ENDPOINT, k ověření ve službě Azure Key Vault.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku Azure, zadejte:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu `az group create`. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků v umístění Západní USA. Vyberte název vaší skupiny prostředků a v následujícím příkladu nahraďte `YourResourceGroupName`:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Tuto skupinu prostředků použijete v průběhu tohoto kurzu.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Potom ve skupině prostředků vytvořte Trezor klíčů. Zadejte tyto informace:

* Název trezoru klíčů: řetězec na 3 až 24 znaků, který může obsahovat jenom čísla, písmena a spojovníky (0-9, a-z, a-Z a \-).
* Název skupiny prostředků
* Umístění: **západní USA**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

V tuto chvíli je k provádění operací s tímto novým trezorem autorizován pouze účet Azure.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Nyní přidáte tajný klíč. V reálném scénáři můžete uložit připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete, abyste mohli bezpečně udržovat, ale zpřístupnit aplikaci.

Pro tento kurz zadejte následující příkazy pro vytvoření tajného klíče v trezoru klíčů. Tajný kód se nazývá **AppSecret** a jeho hodnota je **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Vytvoření virtuálního počítače s Linuxem

Vytvořte virtuální počítač pomocí příkazu `az vm create`.

Následující příklad vytvoří virtuální počítač **myVM** a přidá uživatelský účet **azureuser**. `--generate-ssh-keys` parametr US se používá k automatickému vygenerování klíče SSH a jeho vložení do výchozího umístění klíče ( **~/.ssh**). Pokud místo toho chcete použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut. Následující příklad výstupu ukazuje, že operace vytvoření virtuálního počítače byla úspěšná.

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

Poznamenejte si `publicIpAddress` ve výstupu z virtuálního počítače. Tuto adresu použijete pro přístup k virtuálnímu počítači v pozdějších krocích.

## <a name="assign-an-identity-to-the-vm"></a>Přiřazení identity k virtuálnímu počítači

Spuštěním následujícího příkazu vytvořte identitu přiřazenou systémem k virtuálnímu počítači:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Výstup příkazu by měl být následující:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Poznamenejte si `systemAssignedIdentity`. Použijete ho v dalším kroku.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Udělte identitě virtuálního počítače oprávnění k Key Vault

Nyní můžete pro vytvořenou identitu udělit Key Vault oprávnění. Spusťte následující příkaz:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Přihlaste se k virtuálnímu počítači.

Přihlaste se k virtuálnímu počítači pomocí terminálu.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Nainstalovat .NET Core v systému Linux

Na VIRTUÁLNÍm počítači se systémem Linux:

Zaregistrujte kód Product Key společnosti Microsoft jako důvěryhodný spuštěním následujících příkazů:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Nastavte požadovaný kanál balíčku hostitele verze podle operačního systému:

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

Nainstalujte .NET a podívejte se na verzi:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Vytvoření a spuštění ukázkové aplikace .NET

Spusťte následující příkazy. Měla by se zobrazit zpráva "Hello World" vytištěna v konzole nástroje.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Upravit konzolovou aplikaci pro načtení tajného klíče

Otevřete soubor Program.cs a přidejte tyto balíčky:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Jedná se o proces se dvěma kroky ke změně souboru třídy, aby aplikace mohla přistupovat ke tajnému kódu v trezoru klíčů.

1. Načtěte token z místního koncového bodu MSI na virtuálním počítači, který zase načte token z Azure Active Directory.
1. Předání tokenu pro Key Vault a načtení tajného kódu.

   Upravte soubor třídy tak, aby obsahoval následující kód:

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

Nyní jste se naučili, jak provádět operace s Azure Key Vault v aplikaci .NET běžící na virtuálním počítači Azure Linux.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, virtuální počítač a všechny související prostředky, pokud je už nepotřebujete. Provedete to tak, že vyberete skupinu prostředků pro virtuální počítač a vyberete **Odstranit**.

Odstraňte Trezor klíčů pomocí příkazu `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
