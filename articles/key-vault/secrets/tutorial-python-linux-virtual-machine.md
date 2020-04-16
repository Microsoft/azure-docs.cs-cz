---
title: Výuka – použití virtuálního počítače linuxu a aplikace Pythonu k ukládání tajných kódů v úložišti klíčů Azure | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak nakonfigurovat aplikaci Pythonu pro čtení tajného klíče z Azure Key Vault.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423314"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Kurz: Ukládání tajných kódů v azure key vaultu pomocí virtuálního počítače s Linuxem a aplikace Pythonu

Azure Key Vault pomáhá chránit tajné klíče, jako jsou klíče rozhraní API a připojovací řetězce databáze potřebné pro přístup k vašim aplikacím, službám a prostředkům IT.

V tomto kurzu nastavíte webovou aplikaci Azure pro čtení informací z Azure Key Vault pomocí spravovaných identit pro prostředky Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte trezor klíčů
> * Uložení tajného klíče do trezoru klíčů
> * Vytvoření virtuálního počítače s Linuxem
> * Povolení [spravované identity](../../active-directory/managed-identities-azure-resources/overview.md) pro virtuální počítač
> * Udělit aplikaci konzoly požadovaná oprávnění ke čtení dat z trezoru klíčů
> * Načtení tajného klíče z trezoru klíčů

Než budete pokračovat, ujistěte se, že rozumíte [základním konceptům trezoru klíčů](../general/basic-concepts.md).

## <a name="prerequisites"></a>Požadavky

* [Git](https://git-scm.com/downloads).
* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
* [Azure CLI verze 2.0.4 nebo novější](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nebo Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Principy identity spravované služby

Azure Key Vault můžete bezpečně ukládat přihlašovací údaje, takže nejsou ve vašem kódu. Chcete-li je načíst, je třeba ověřit azure key vault. Chcete-li však ověřit trezor klíčů, potřebujete pověření. Je to klasický problém s bootstrapem. Prostřednictvím Azure a Azure Active Directory (Azure AD) poskytuje identitu spravované služby (MSI) identitu zaváděcí pasti, která usnadňuje nastartování věcí.

Když povolíte MSI pro službu Azure, jako jsou virtuální počítače, služba app nebo funkce, Azure vytvoří instancí instancí služby ve službě Azure AD. Vloží pověření pro instanční objekt služby do instance služby.

![MSI](../media/MSI.png)

Dále váš kód volá místní službu metadat, která je k dispozici na prostředku Azure získat přístupový token. Váš kód používá přístupový token, který získá z místního koncového bodu MSI k ověření služby Azure Key Vault.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete k Azure přihlásit pomocí azure cli, zadejte:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků `az group create` pomocí příkazu v umístění V USA v USA s následujícím kódem. Nahraďte `YourResourceGroupName` název podle vašeho výběru.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Tuto skupinu prostředků používáte v průběhu kurzu.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Dále vytvoříte trezor klíčů ve skupině prostředků, kterou jste vytvořili v předchozím kroku. Zadejte tyto informace:

* Název trezoru klíčů: Název musí být řetězec 3-24 znaků a musí obsahovat pouze 0-9, a-z, A-Z a pomlčky (-).
* Název skupiny prostředků.
* Umístění: **USA – západ**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Můžete chtít uložit připojovací řetězec SQL nebo jakékoli jiné informace, které musí být zabezpečeny a k dispozici pro vaši aplikaci.

Zadáním následujících příkazů vytvořte v trezoru klíčů tajný klíč *AppSecret*. V tomto tajném klíči bude uložená hodnota **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Vytvoření virtuálního počítače s Linuxem

Vytvořte virtuální hopomocí `az vm create` pomocí příkazu.

Následující příklad vytvoří virtuální počítač **myVM** a přidá uživatelský účet **azureuser**. Parametr `--generate-ssh-keys` automaticky vygeneruje klíč SSH a umístí jej do výchozího umístění klíče (**~/.ssh**). Chcete-li místo toho vytvořit určitou sadu klíčů, použijte `--ssh-key-value` tuto možnost.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut. Následující příklad výstupu ukazuje, že vytvoření virtuálního zařízení bylo úspěšné:

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

Poznamenejte si `publicIpAddress` své vlastní ve výstupu z virtuálního počítače. Tuto adresu použijete pro přístup k virtuálnímu virtuálnímu mněmu v pozdějších krocích.

## <a name="assign-an-identity-to-the-vm"></a>Přiřazení identity k virtuálnímu virtuálnímu mněmu

Vytvořte systémově přiřazenou identitu virtuálnímu počítači spuštěním následujícího příkazu:

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

Poznamenejte `systemAssignedIdentity`si . Použiješ to jako další krok.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Udělit identitě virtuálního virtuálního soudu oprávnění k trezoru klíčů

Nyní můžete udělit oprávnění k vytvoření trezoru klíčů. Spusťte následující příkaz:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Přihlášení k virtuálnímu virtuálnímu mísu

Přihlaste se k virtuálnímu počítači pomocí terminálu.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Instalace knihovny Pythonu na virtuální ms

Stáhněte a nainstalujte knihovnu [požadavků](https://pypi.org/project/requests/2.7.0/) Pythonu pro volání HTTP GET.

## <a name="create-edit-and-run-the-sample-python-app"></a>Vytvoření, úprava a spuštění ukázkové aplikace Python

Vytvořte soubor Pythonu s názvem **Sample.py**.

Otevřete Sample.py a upravte tak, aby obsahovala následující kód:

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

Předchozí kód provádí dvoustupňový proces:

   1. Načte token z místního koncového bodu MSI na virtuálním počítači. Koncový bod pak načte token z Azure Active Directory.
   1. Předá token do trezoru klíčů a načte váš tajný klíč.

Spusťte následující příkaz. Měl bys vidět tajnou hodnotu.

```console
python Sample.py
```

V tomto kurzu jste se naučili používat Azure Key Vault s aplikací Pythonu spuštěnou na virtuálním počítači s Linuxem.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, virtuální počítač a všechny související prostředky, když už je nepotřebujete. To uděláte tak, že vyberete skupinu prostředků pro virtuální počítač a pak vyberete **Odstranit**.

Odstraňte trezor klíčů `az keyvault delete` pomocí příkazu:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozhraní API REST trezoru klíčů Azure](https://docs.microsoft.com/rest/api/keyvault/)
