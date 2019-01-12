---
title: Kurz – použití Azure Key Vault se virtuální počítač Azure v Pythonu | Dokumentace Microsoftu
description: V tomto kurzu nakonfigurujete aplikace v Pythonu pro čtení tajného klíče ze služby key vault
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 8c816d17807432d75b6102190fc37d25a525d7cf
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244167"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-virtual-machine-in-python"></a>Kurz: Virtuální počítač Azure v Pythonu pomocí služby Azure Key Vault

Služba Azure Key Vault pomáhá chránit tajné kódy jako jsou klíče rozhraní API a databázové připojovací řetězce, které jsou potřebné pro přístup k aplikacím, služby a prostředky IT.

V tomto kurzu postupujte podle pokynů a webové aplikace Azure ke čtení informací z Azure Key Vault pomocí spravované identity pro prostředky Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Uložení tajného klíče v trezoru klíčů
> * Vytvoření virtuálního počítače Azure.
> * Povolit [se identita spravované](../active-directory/managed-identities-azure-resources/overview.md) pro virtuální počítač.
> * Přidělení požadovaných oprávnění pro konzolovou aplikaci pro čtení dat ze služby key vault.
> * Načtení tajného klíče z trezoru klíčů

Před pokračováním, přečtěte si prosím [základnímu konceptu služby Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Požadavky
Pro všechny platformy budete potřebovat:

* Git ([stáhnout](https://git-scm.com/downloads)).
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) verze 2.0.4 nebo novější. Je dostupná pro Windows, Mac a Linux.

### <a name="managed-service-identity-and-how-it-works"></a>Identita spravované služby a jak to funguje
Tento kurz využívá z Identity spravované služby (MSI).

Služba Azure Key Vault můžete bezpečně ukládat přihlašovací údaje, proto nejsou ve vašem kódu. Je načítat, musíte pro ověření do služby Key Vault. K ověření do služby Key Vault, budete potřebovat přihlašovací údaje. To je classic bootstrap problém. Pomocí Azure a Azure Active Directory (Azure AD) poskytuje MSI "bootstrap identitou", která zjednodušuje začněte tím.

Při povolení MSI pro službu Azure, jako jsou virtuální počítače, služby App Service nebo funkce, Azure vytvoří [instanční objekt služby](key-vault-whatis.md#basic-concepts) pro instanci služby ve službě Azure AD. Azure vkládá přihlašovací údaje pro instanční objekt do instance služby. 

![MSI](media/MSI.png)

V dalším kroku váš kód volá služba místních metadat, která je k dispozici u prostředku Azure získat přístupový token.
Váš kód používá přístupový token, který získá z místního koncového bodu MSI pro ověření do služby Azure Key Vault. 

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

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

* Název trezoru klíčů: Název musí být řetězec dlouhý 3 až 24 znaků a musí obsahovat pouze 0-9, a-z, A-Z a pomlčky (-).
* Název skupiny prostředků.
* Umístění: **USA – západ**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Mohli byste ukládat připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete zabezpečeně uchovávat a současně zpřístupnit vaší aplikaci.

Zadáním následujících příkazů vytvořte v trezoru klíčů tajný klíč *AppSecret*. V tomto tajném klíči bude uložená hodnota **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače pomocí [az vm vytvořit](/cli/azure/vm#az_vm_create) příkazu.

Následující příklad vytvoří virtuální počítač *myVM* a přidá uživatelský účet *azureuser*. `--generate-ssh-keys` Parametr automaticky vygeneruje klíče SSH a vloží je ve výchozím umístění klíčů (*~/.ssh*). Pokud místo toho chcete použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut. Následující příklad výstupu ukazuje, že vytvoření virtuálního počítače bylo úspěšné:

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

Mějte na paměti vlastní `publicIpAddress` hodnota ve výstupu z vašeho virtuálního počítače. Tato adresa se používá pro přístup k virtuálnímu počítači v dalších krocích.

## <a name="assign-an-identity-to-the-virtual-machine"></a>Přiřadit identitu virtuálního počítače
V tomto kroku vytváříme systém přiřadil identitou pro virtuální počítač. V Azure CLI, spusťte následující příkaz:

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Výstup příkazu je následujícím způsobem. Poznamenejte si hodnotu **systemAssignedIdentity**. 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-the-virtual-machine-identity-permission-to-the-key-vault"></a>Udělit oprávnění identity virtuálního počítače k trezoru klíčů
Teď můžeme poskytnout identitu oprávnění k trezoru klíčů. Spusťte následující příkaz:

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-virtual-machine"></a>Přihlaste se k virtuálnímu počítači

Přihlaste se k virtuálnímu počítači pomocí následujících [v tomto kurzu](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-the-sample-python-app"></a>Vytvořit a spustit ukázkovou aplikaci v Pythonu

Následující příklad souboru jmenuje *Sample.py*. Používá [požadavky](https://pypi.org/project/requests/2.7.0/) knihovny pro volání HTTP GET.

## <a name="edit-samplepy"></a>Upravit Sample.py
Po vytvoření Sample.py otevřete soubor a zkopírujte následující kód. Kód je dvoustupňový proces: 
1. Načtěte token z koncového bodu místní instalační služby MSI ve virtuálním počítači. Koncový bod potom načte token ze služby Azure Active Directory.
2. Předat token k trezoru klíčů a načtou se vaše tajný klíč. 

```
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

Spuštěním následujícího příkazu byste měli vidět tajnou hodnotu: 

```
python Sample.py
```

Předchozí kód ukazuje, jak provádět operace se službou Azure Key Vault do virtuálního počítače s Windows. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní REST API služby Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
