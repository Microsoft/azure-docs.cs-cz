---
title: Kurz – použití Azure Key Vault s Windows virtuální počítač Azure v Pythonu | Dokumentace Microsoftu
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
ms.openlocfilehash: cced3d363f9eb7418d6f453eccb1bf1d7ac20ead
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972341"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-windows-virtual-machine-in-python"></a>Kurz: Jak používat Azure Key Vault s Windows virtuální počítač Azure v Pythonu

Azure Key Vault pomáhá chránit tajné klíče, jako jsou klíče rozhraní API nebo databázové připojovací řetězce potřebné pro přístup k aplikacím, službám a prostředkům IT.

V tomto kurzu si projdete nezbytné kroky pro využití webové aplikace Azure k načtení informací ze služby Azure Key Vault pomocí spravovaných identit pro prostředky Azure. V následujících částech získáte informace o těchto tématech:

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
Postupujte podle následujících odkazech k vytvoření virtuálního počítače s Windows

[Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli) 

[Powershell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)

[Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

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

Níže je právě ukázkového souboru s názvem "Sample.py". Používá [požadavky](http://docs.python-requests.org/en/master/) knihovny pro volání HTTP GET.

## <a name="edit-samplepy"></a>Upravit Sample.py
Po vytvoření Sample.py otevřete soubor a zkopírujte níže uvedeného kódu

```
The below is a 2 step process. 
1. Fetch a token from the local MSI endpoint on the VM which in turn fetches a token from Azure Active Directory
2. Pass the token to Key Vault and fetch your secret 
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

By running you should see the secret value 
```
Python Sample.py
```

The above code shows you how to do operations with Azure Key Vault in an Azure Windows Virtual Machine. 

## Next steps

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
