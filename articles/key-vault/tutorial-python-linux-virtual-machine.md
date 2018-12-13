---
title: Kurz – použití Azure Key Vault s Linuxových virtuálních počítačů Azure v Pythonu | Dokumentace Microsoftu
description: 'Kurz: Konfigurace aplikace ASP.NET Core pro čtení tajného klíče z trezoru klíčů'
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
ms.openlocfilehash: acc926151e5abd1d6f9d0992591575198d1fdf44
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890530"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-linux-virtual-machine-in-python"></a>Kurz: Jak používat Azure Key Vault s Linuxových virtuálních počítačů Azure v Pythonu

Azure Key Vault pomáhá chránit tajné klíče, jako jsou klíče rozhraní API nebo databázové připojovací řetězce potřebné pro přístup k aplikacím, službám a prostředkům IT.

V tomto kurzu si projdete nezbytné kroky pro využití webové aplikace Azure k načtení informací ze služby Azure Key Vault pomocí spravovaných identit pro prostředky Azure. Tento kurz je založený na službě [Azure Web Apps](../app-service/app-service-web-overview.md). V následujících částech získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Uložení tajného klíče v trezoru klíčů
> * Načtení tajného klíče z trezoru klíčů
> * Vytvoření virtuálního počítače Azure.
> * Povolit [se identita spravované](../active-directory/managed-identities-azure-resources/overview.md) pro virtuální počítač.
> * Přidělení požadovaných oprávnění pro konzolovou aplikaci pro čtení dat ze služby key vault.
> * Načíst tajné kódy z trezoru klíčů

Než budeme pokračovat, přečtěte si [základní koncepty](key-vault-whatis.md#basic-concepts).

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

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vyberte název skupiny prostředků a nahraďte zástupný text.
Následující příklad vytvoří skupinu prostředků v umístění USA – západ:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

V tomto článku se používá skupina prostředků, kterou jste právě vytvořili.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Dále ve skupině prostředků, kterou jste vytvořili v předchozím kroku, vytvoříte trezor klíčů. Zadejte tyto informace:

* Název trezoru klíčů: Název musí být řetězec dlouhý 3 až 24 znaků a může obsahovat pouze následující znaky: 0–9, a–z, A–Z a spojovník (-).
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
V tomto kroku vytváříme systému identity přiřazené k virtuálnímu počítači spuštěním následujícího příkazu v rozhraní příkazového řádku Azure

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Mějte prosím na paměti systemAssignedIdentity vidíte níže. Výstup výše uvedený příkaz by měl 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-virtual-machine-identity-permission-to-key-vault"></a>Udělení oprávnění identitu virtuálního počítače do služby Key Vault
Teď můžeme poskytnout výše vytvořili oprávnění identit do služby Key Vault spuštěním následujícího příkazu

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="login-to-the-virtual-machine"></a>Přihlášení k virtuálnímu počítači

Můžete použít tento [kurz](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)

## <a name="create-and-run-sample-python-app"></a>Vytvořit a spustit ukázkovou aplikaci v Pythonu

Níže je právě ukázkového souboru s názvem "Sample.py". Používá [požadavky](http://docs.python-requests.org/master/) knihovny pro volání HTTP GET.

## <a name="edit-samplepy"></a>Upravit Sample.py
Po vytvoření Sample.py otevřete soubor a zkopírujte níže uvedeného kódu

Níže je o 2 krocích. 
1. Načíst token z koncového bodu místní instalační služby MSI ve virtuálním počítači, který pak načte token ze služby Azure Active Directory
2. Předat token do služby Key Vault a načtou se vaše tajný klíč 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets a access_token from Azure Active Directory using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to Key Vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Spuštěním následující příkaz, měli byste vidět tajná hodnota 

```
python Sample.py
```

Výše uvedený kód ukazuje, jak provádět operace se službou Azure Key Vault ve virtuálním počítači Windows Azure. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozhraní REST API služby Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
