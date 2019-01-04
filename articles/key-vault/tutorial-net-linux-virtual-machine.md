---
title: Kurz – jak používat Azure Key Vault pomocí Azure virtuálního počítače s Linuxem v rozhraní .NET – Azure Key Vault | Dokumentace Microsoftu
description: 'Kurz: Konfigurace aplikace ASP.NET Core pro čtení tajného klíče z trezoru klíčů'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 68a788205917e87469b432de435e296dcabc350c
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54001681"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-linux-virtual-machine-in-net"></a>Kurz: Jak používat Azure Key Vault s Linuxových virtuálních počítačů Azure v .NET

Azure Key Vault pomáhá chránit tajné klíče, jako jsou klíče rozhraní API nebo databázové připojovací řetězce potřebné pro přístup k aplikacím, službám a prostředkům IT.

V tomto kurzu postupujte podle potřeby postup, jak získat konzolovou aplikaci pro čtení informací z Azure Key Vault pomocí spravované identity pro prostředky Azure. V následujících částech získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Uložení tajného klíče v trezoru klíčů
> * Načtení tajného klíče z trezoru klíčů
> * Vytvoření virtuálního počítače Azure.
> * Povolit [se identita spravované](../active-directory/managed-identities-azure-resources/overview.md) pro virtuální počítač.
> * Přidělení požadovaných oprávnění pro konzolovou aplikaci pro čtení dat ze služby key vault.
> * Načíst tajné kódy z trezoru klíčů

Než půjdeme dál, přečtěte si [základní koncepty](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Požadavky
* Všechny platformy:
  * Git ([stáhnout](https://git-scm.com/downloads)).
  * Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) verze 2.0.4 nebo novější. K dispozici pro Windows, Mac a Linux.

Tento kurz využívá identita spravované služby

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Co je Identita spravované služby a jak funguje?

Než budeme pokračovat, seznámíme se s MSI. Azure Key Vault umožňuje bezpečné ukládání přihlašovacích údajů, aby nemusely být v kódu. Abyste je však mohli načíst, musíte se ověřit ve službě Azure Key Vault. K ověření ve službě Key Vault potřebujete přihlašovací údaje. Jedná se o klasický problém. Prostřednictvím spojení Azure a Azure AD poskytuje MSI spouštěcí identitu, která výrazně usnadňuje začátek práce.

Funguje to takto. Když povolíte MSI pro službu Azure, jako je Virtual Machines, App Service nebo Functions, Azure vytvoří [instanční objekt](key-vault-whatis.md#basic-concepts) pro instanci služby v Azure Active Directory a vloží do instance služby přihlašovací údaje k instančnímu objektu. 

![MSI](media/MSI.png)

Potom váš kód zavolá místní službu Metadata Service, která je dostupná v prostředku Azure, a získá přístupový token.
Váš kód použije přístupový token, který získá z místního koncového bodu MSI_ENDPOINT, k ověření ve službě Azure Key Vault. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure pomocí rozhraní příkazového řádku Azure, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vyberte název skupiny prostředků a nahraďte zástupný text.
Následující příklad vytvoří skupinu prostředků v umístění Západní USA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

V tomto článku se používá skupina prostředků, kterou jste právě vytvořili.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Dále ve skupině prostředků, kterou jste vytvořili v předchozím kroku, vytvoříte trezor klíčů. Zadejte tyto informace:

* Název trezoru klíčů: Název musí být řetězec dlouhý 3 až 24 znaků a musí obsahovat pouze (0-9, a – z, A-Z a -).
* Název skupiny prostředků.
* Umístění: **USA – západ**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Mohli byste ukládat připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete zabezpečeně uchovávat a současně zpřístupnit vaší aplikaci.

Zadáním následujících příkazů vytvořte v trezoru klíčů tajný klíč **AppSecret**. V tomto tajném klíči bude uložená hodnota **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create).

Následující příklad vytvoří virtuální počítač *myVM* a přidá uživatelský účet *azureuser*. Parametr `--generate-ssh-keys` slouží k automatickému vygenerování klíče SSH a jeho uložení do výchozího umístění klíčů (*~/.ssh*). Pokud místo toho chcete použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut. Následující příklad ukazuje, že operace vytvoření virtuálního počítače byla úspěšná.

```
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

Poznamenejte si vlastní adresu `publicIpAddress` ve výstupu z vašeho virtuálního počítače. Tato adresa se používá pro přístup k virtuálnímu počítači v dalších krocích.

## <a name="assign-identity-to-virtual-machine"></a>Přiřazení identit k virtuálnímu počítači

V tomto kroku vytváříme systému identity přiřazené k virtuálnímu počítači spuštěním následujícího příkazu

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Všimněte si systemAssignedIdentity vidíte níže. Výstup výše uvedený příkaz by měl 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-vm-identity-permission-to-key-vault"></a>Udělení oprávnění identita virtuálního počítače do služby Key Vault

Teď můžeme poskytnout výše vytvořili oprávnění identit do služby Key Vault spuštěním následujícího příkazu

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>Přihlaste se k virtuálnímu počítači

Teď se přihlaste k virtuálnímu počítači pomocí terminálu

```
ssh azureuser@<PublicIpAddress>
```

## <a name="install-dot-net-core-on-linux"></a>Instalace Dot Net core v Linuxu

### <a name="register-the-microsoft-product-key-as-trusted-run-the-following-two-commands"></a>Registrace Microsoft Product key jako důvěryhodné. Spusťte následující dva příkazy

```
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
```

### <a name="set-up-desired-version-host-package-feed-based-on-operating-system"></a>Nastavení balíčku hostitele požadovanou verzi informačního kanálu na základě operačního systému

```
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

### <a name="install-net-core"></a>Instalace .NET Core

A zkontrolujte verze rozhraní .NET

```
sudo apt-get install dotnet-sdk-2.1.4
dotnet --version
```

## <a name="create-and-run-sample-dot-net-app"></a>Vytvoření a spuštění ukázkové aplikace .NET tečka

Spuštěním následujících příkazů, měli byste vidět "Hello World" Tisk do konzoly

```
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-console-app"></a>Upravit aplikaci konzoly

Otevřete soubor Program.cs a přidejte tyto balíčky
```
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Změňte soubor třídy tak, aby obsahovala níže uvedeného kódu. Je dvoustupňový proces.

1. Načíst token z koncového bodu místní instalační služby MSI ve virtuálním počítači, která pak načte token ze služby Azure Active Directory
2. Předat token do služby Key Vault a načtou se vaše tajný klíč 

```
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

Výše uvedený kód ukazuje, jak provádět operace se službou Azure Key Vault v Azure virtuálního počítače s Linuxem. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní REST API služby Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
