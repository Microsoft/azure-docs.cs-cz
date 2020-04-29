---
title: Kurz – použití virtuálního počítače se systémem Linux a aplikace Python k ukládání tajných kódů v Azure Key Vault | Microsoft Docs
description: V tomto kurzu se naučíte konfigurovat aplikaci v Pythonu pro čtení tajného klíče z Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: df089f0338a177c08f4d9e88d55b501fd12f88f2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81423314"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Kurz: použití virtuálního počítače se systémem Linux a aplikace Python k ukládání tajných kódů v Azure Key Vault

Azure Key Vault vám pomůže s ochranou tajných kódů, jako jsou klíče rozhraní API a databázové připojovací řetězce potřebné pro přístup k aplikacím, službám a prostředkům IT.

V tomto kurzu nastavíte webovou aplikaci Azure pro čtení informací z Azure Key Vault pomocí spravovaných identit pro prostředky Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte trezor klíčů
> * Uložení tajného klíče do trezoru klíčů
> * Vytvoření virtuálního počítače s Linuxem
> * Povolit [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) pro virtuální počítač
> * Udělte aplikaci konzoly požadovaná oprávnění ke čtení dat z trezoru klíčů.
> * Načtení tajného kódu z trezoru klíčů

Než budete pokračovat, ujistěte se, že rozumíte [základním konceptům o Key Vault](../general/basic-concepts.md).

## <a name="prerequisites"></a>Požadavky

* [Git](https://git-scm.com/downloads).
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* [Azure CLI verze 2.0.4 nebo novější](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nebo Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Pochopení Identita spravované služby

Azure Key Vault můžou přihlašovací údaje ukládat bezpečně, takže nejsou ve vašem kódu. Pokud je chcete načíst, je nutné ověřit, že Azure Key Vault. K ověření Key Vault ale potřebujete přihlašovací údaje. Jedná se o klasický problém při zavádění. Prostřednictvím Azure a Azure Active Directory (Azure AD) poskytuje Identita spravované služby (MSI) identitu pro spuštění, která usnadňuje zprovoznění vašich věcí.

Když povolíte MSI pro službu Azure, například Virtual Machines, App Service nebo Functions, Azure vytvoří instanční objekt pro instanci služby ve službě Azure AD. Vloží pověření pro instanční objekt do instance služby.

![MSI](../media/MSI.png)

Dál váš kód volá místní službu metadat dostupnou v prostředku Azure a získá přístupový token. Váš kód používá přístupový token, který získává z místního koncového bodu MSI k ověření pro službu Azure Key Vault.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku Azure, zadejte:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí `az group create` příkazu v umístění západní USA s následujícím kódem. Nahraďte `YourResourceGroupName` názvem, který jste si zvolili.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Tuto skupinu prostředků použijete v průběhu tohoto kurzu.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Potom ve skupině prostředků, kterou jste vytvořili v předchozím kroku, vytvoříte Trezor klíčů. Zadejte tyto informace:

* Název trezoru klíčů: název musí být řetězec 3-24 znaků a musí obsahovat pouze 0-9, a-z, A-Z a spojovníky (-).
* Název skupiny prostředků.
* Umístění: **USA – západ**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Je možné, že budete chtít uložit připojovací řetězec SQL nebo jakékoli jiné informace, které musí být udržovány v bezpečí a k dispozici pro vaši aplikaci.

Zadáním následujících příkazů vytvořte v trezoru klíčů tajný klíč *AppSecret*. V tomto tajném klíči bude uložená hodnota **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Vytvoření virtuálního počítače s Linuxem

Vytvořte virtuální počítač pomocí `az vm create` příkazu.

Následující příklad vytvoří virtuální počítač **myVM** a přidá uživatelský účet **azureuser**. `--generate-ssh-keys` Parametr automaticky vygeneruje klíč SSH a umístí ho do výchozího umístění klíče (**~/.ssh**). Pokud chcete místo toho vytvořit konkrétní sadu klíčů, použijte `--ssh-key-value` možnost.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut. Následující příklad výstupu ukazuje, že vytvoření virtuálního počítače bylo úspěšné:

```output
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

Poznamenejte si si vlastní `publicIpAddress` výstup z virtuálního počítače. Tuto adresu použijete pro přístup k virtuálnímu počítači v pozdějších krocích.

## <a name="assign-an-identity-to-the-vm"></a>Přiřazení identity k virtuálnímu počítači

Spuštěním následujícího příkazu vytvořte identitu přiřazenou systémem k virtuálnímu počítači:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Výstup příkazu je následující.

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Poznamenejte si `systemAssignedIdentity`. Použijete ho v dalším kroku.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Udělte identitě virtuálního počítače oprávnění k Key Vault

Nyní můžete pro vytvořenou identitu udělit Key Vault oprávnění. Spusťte následující příkaz:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Přihlaste se k virtuálnímu počítači.

Přihlaste se k virtuálnímu počítači pomocí terminálu.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Instalace knihovny Pythonu na virtuálním počítači

Stáhněte a nainstalujte knihovnu Pythonu [žádosti](https://pypi.org/project/requests/2.7.0/) , abyste mohli volat volání http.

## <a name="create-edit-and-run-the-sample-python-app"></a>Vytvoření, úprava a spuštění ukázkové aplikace v Pythonu

Vytvořte soubor Pythonu s názvem **Sample.py**.

Otevřete Sample.py a upravte jej tak, aby obsahoval následující kód:

```python
# importing the requests library
import requests

# Step 1: Fetch an access token from an MSI-enabled Azure resource      
# Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
# This request gets an access token from Azure Active Directory by using the local MSI endpoint
data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

Předchozí kód provádí proces se dvěma kroky:

   1. Načte token z místního koncového bodu MSI na virtuálním počítači. Koncový bod potom načte token z Azure Active Directory.
   1. Předá token do trezoru klíčů a načte váš tajný klíč.

Spusťte následující příkaz. Měla by se zobrazit tajná hodnota.

```console
python Sample.py
```

V tomto kurzu jste zjistili, jak používat Azure Key Vault s aplikací Python běžícím na virtuálním počítači se systémem Linux.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, virtuální počítač a všechny související prostředky, pokud je už nepotřebujete. To uděláte tak, že vyberete skupinu prostředků pro virtuální počítač a pak vyberete **Odstranit**.

Odstraňte Trezor klíčů pomocí `az keyvault delete` příkazu:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
