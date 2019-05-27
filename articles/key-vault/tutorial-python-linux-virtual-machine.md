---
title: Kurz – použití virtuálního počítače s Linuxem a aplikace v Pythonu ukládat tajné kódy ve službě Azure Key Vault | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak nakonfigurovat aplikaci Python k čtení tajného klíče ze služby Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: e5fbb4e6f6599d8f9560561fc219dbf57ac0cee1
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "66147757"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Kurz: Tajné kódy ukládat v Azure Key Vault pomocí virtuálního počítače s Linuxem a aplikace v Pythonu

Služba Azure Key Vault pomáhá chránit tajné kódy jako jsou klíče rozhraní API a databázové připojovací řetězce, které jsou potřebné pro přístup k aplikacím, služby a prostředky IT.

V tomto kurzu se nastavení webové aplikace Azure ke čtení informací z Azure Key Vault pomocí spravované identity pro prostředky Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte trezor klíčů
> * Store tajný klíč v trezoru klíčů
> * Vytvořit virtuální počítač s Linuxem
> * Povolit [se identita spravované](../active-directory/managed-identities-azure-resources/overview.md) pro virtuální počítač
> * Přidělení požadovaných oprávnění pro konzolovou aplikaci pro čtení dat ze služby key vault
> * Získání tajného klíče z trezoru klíčů

Před pokračováním, ujistěte se, že rozumíte [základnímu konceptu služby Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Požadavky

* [Git](https://git-scm.com/downloads)
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* [Azure CLI verze 2.0.4 nebo novější](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nebo Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Vysvětlení služby Managed Service Identity

Služba Azure Key Vault můžete bezpečně ukládat přihlašovací údaje, proto nejsou ve vašem kódu. Je načítat, musíte pro ověření do služby Azure Key Vault. Však k ověření do služby Key Vault, budete potřebovat přihlašovací údaje. Je classic bootstrap problém. Pomocí Azure a Azure Active Directory (Azure AD) Identity spravované služby (MSI) poskytuje bootstrap identitou, která zjednodušuje začněte tím.

Při povolení MSI pro službu Azure, jako jsou virtuální počítače, služby App Service nebo funkce, Azure vytvoří službu instanční objekt pro instanci služby ve službě Azure AD. Přihlašovací údaje pro instanční objekt se vkládá do instance služby.

![MSI](media/MSI.png)

V dalším kroku váš kód volá místních metadat služba k dispozici u prostředku Azure získat přístupový token. Váš kód používá přístupový token, který získá z místního koncového bodu MSI pro ověření do služby Azure Key Vault.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k Azure pomocí rozhraní příkazového řádku Azure, zadejte:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků s použitím `az group create` v umístění západní USA s následujícím kódem. Nahraďte `YourResourceGroupName` s názvem podle vašeho výběru.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Tato skupina prostředků v rámci tohoto kurzu použijete.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

V dalším kroku vytvoříte trezor klíčů ve skupině prostředků, kterou jste vytvořili v předchozím kroku. Zadejte tyto informace:

* Název trezoru klíčů: Název musí být řetězec dlouhý 3 až 24 znaků a musí obsahovat pouze 0-9, a-z, A-Z a pomlčky (-).
* Název skupiny prostředků.
* Umístění: **USA – západ**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Můžete chtít uložit připojovací řetězec SQL nebo nějakých jiných informací, které je potřeba nastavit na obojí udržovat zabezpečených a dostupných pro vaši aplikaci.

Zadáním následujících příkazů vytvořte v trezoru klíčů tajný klíč *AppSecret*. V tomto tajném klíči bude uložená hodnota **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Vytvořit virtuální počítač s Linuxem

Vytvoření virtuálního počítače pomocí `az vm create` příkazu.

Následující příklad vytvoří virtuální počítač **myVM** a přidá uživatelský účet **azureuser**. `--generate-ssh-keys` Parametr automaticky vygeneruje klíče SSH a vloží je ve výchozím umístění klíčů (**~/.ssh**). Chcete-li místo toho vytvořit konkrétní sadu klíčů, použijte `--ssh-key-value` možnost.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut. Následující příklad výstupu ukazuje, že vytvoření virtuálního počítače bylo úspěšné:

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

Poznamenejte si vlastní `publicIpAddress` ve výstupu z vašeho virtuálního počítače. Tuto adresu budete používat pro přístup k virtuálnímu počítači v dalších krocích.

## <a name="assign-an-identity-to-the-vm"></a>Přiřadit identitu virtuálního počítače

Vytvoření identity systém přiřadil k virtuálnímu počítači spuštěním následujícího příkazu:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Výstup příkazu je následujícím způsobem.

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Poznamenejte si, `systemAssignedIdentity`. Můžete ji použít na další krok.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Udělit oprávnění identity virtuálního počítače do služby Key Vault

Nyní můžete udělit oprávnění služby Key Vault k identitě, kterou jste vytvořili. Spusťte následující příkaz:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Přihlaste se k virtuálnímu počítači

Přihlaste se k virtuálnímu počítači pomocí terminálu.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Nainstalujte knihovnu Pythonu ve virtuálním počítači

Stáhněte a nainstalujte [požadavky](https://pypi.org/project/requests/2.7.0/) knihovna Python pro volání HTTP GET.

## <a name="create-edit-and-run-the-sample-python-app"></a>Vytvořit, upravit a spustit ukázkovou aplikaci v Pythonu

Vytvořte soubor Pythonu s názvem **Sample.py**.

Sample.py otevřít a upravit ji tak, aby obsahovala následující kód:

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

Předchozí kód provede dvoustupňový proces:

   1. Načte token z koncového bodu místní MSI ve virtuálním počítači. Koncový bod potom načte token ze služby Azure Active Directory.
   1. Token, který předá do služby key vault a načte váš tajný klíč.

Spusťte následující příkaz. Měli byste vidět tajná hodnota.

```console
python Sample.py
```

V tomto kurzu jste zjistili, jak používat Azure Key Vault pomocí aplikace v Pythonu běží na virtuálním počítači s Linuxem.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už je budete potřebovat, odstraňte skupinu prostředků, virtuálního počítače a všech souvisejících prostředků. Uděláte to tak, vyberte skupinu prostředků příslušného virtuálního počítače a vyberte **odstranit**.

Odstranění trezoru klíčů pomocí `az keyvault delete` příkaz:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní REST API služby Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
