---
title: Výuka – použití trezoru klíčů Azure s virtuálním počítačem windows v Pythonu | Dokumenty společnosti Microsoft
description: V tomto kurzu nakonfigurujete základní aplikaci ASP.NET pro čtení tajného klíče z trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: c0bb391348548ecca595fd1a6472bafcb22ed4ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472652"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Kurz: Použití Azure Key Vault u virtuálního počítače s Windows v Pythonu

Azure Key Vault vám pomůže chránit tajné klíče, jako jsou klíče rozhraní API, připojovací řetězce databáze, které potřebujete pro přístup k vašim aplikacím, službám a prostředkům IT.

V tomto kurzu se dozvíte, jak získat konzolové aplikace pro čtení informací z Azure Key Vault. Chcete-li tak učinit, použijte spravované identity pro prostředky Azure. 

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Přidejte tajný klíč do trezoru klíčů.
> * Načtení tajného klíče z trezoru klíčů
> * Vytvořte virtuální počítač Azure.
> * Povolte spravovanou identitu.
> * Přiřaďte oprávnění k identitě virtuálního aplikace.

Než začnete, přečtěte si [základní koncepty trezoru klíčů](basic-concepts.md). 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Pro Windows, Mac a Linux:
  * [Git](https://git-scm.com/downloads)
  * Tento kurz vyžaduje, abyste místní spouštění příkazového příkazového odložení příkazového odlokala. Musíte mít nainstalovanou verzi Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informace o funkci Identita spravované služby

Azure Key Vault ukládá přihlašovací údaje bezpečně, takže nejsou zobrazeny ve vašem kódu. Je však nutné ověřit azure key vault načíst klíče. K ověření trezoru klíčů potřebujete pověření. Je to klasické dilema bootstrap. Identita spravované služby (MSI) řeší tento problém poskytnutím _identity zaváděcí pasti,_ která zjednodušuje proces.

Když povolíte MSI pro službu Azure, jako jsou virtuální počítače Azure, Azure App Service nebo Azure Functions, Azure vytvoří [instanční objekt .](basic-concepts.md) MSI to pro instanci služby ve službě Azure Active Directory (Azure AD) a vloží pověření instancí instancí. 

![MSI](media/MSI.png)

Dále chcete-li získat přístupový token, váš kód volá místní službu metadat, která je k dispozici na prostředek Azure. K ověření služby Azure Key Vault váš kód používá přístupový token, který získá z místního koncového bodu MSI. 

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). 

Vyberte název skupiny prostředků a nahraďte zástupný text. Následující příklad vytvoří skupinu prostředků v umístění USA – západ:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Nově vytvořenou skupinu prostředků používáte v průběhu tohoto kurzu.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Chcete-li vytvořit trezor klíčů ve skupině prostředků, kterou jste vytvořili v předchozím kroku, zadejte následující informace:

* Název trezoru klíčů: řetězec 3 až 24 znaků, který může obsahovat pouze čísla (0-9), písmena (a-z, A-Z) a pomlčky (-)
* Název skupiny prostředků
* Umístění: **Západní USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
V tomto okamžiku je váš účet Azure jediný, který je oprávněn provádět operace v tomto novém trezoru klíčů.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Tajný klíč může být připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete zachovat bezpečné a dostupné pro vaši aplikaci.

Chcete-li vytvořit tajný klíč v trezoru klíčů s názvem **AppSecret**, zadejte následující příkaz:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Tento tajný klíč ukládá hodnotu **MySecret**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Virtuální počítač můžete vytvořit pomocí jedné z následujících metod:

* [The Azure CLI](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [Portál Azure](../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Přiřazení identity k virtuálnímu virtuálnímu mněmu
V tomto kroku vytvoříte systémově přiřazenou identitu pro virtuální počítač spuštěním následujícího příkazu v příkazu k onomu Azure:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Všimněte si systémově přiřazené identity, která je zobrazena v následujícím kódu. Výstup předchozího příkazu by byl: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Přiřazení oprávnění k identitě virtuálního aplikace
Nyní můžete k úložišti klíčů přiřadit dříve vytvořená oprávnění identity spuštěním následujícího příkazu:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Přihlášení k virtuálnímu počítači

Pokud se chcete přihlásit k virtuálnímu počítači, postupujte podle pokynů v části [Připojit a přihlaste se k virtuálnímu počítači Azure se systémem Windows](../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Vytvoření a spuštění ukázkové aplikace Pythonu

V další části je ukázkový soubor s názvem *Sample.py*. Používá knihovnu [požadavků](https://2.python-requests.org/en/master/) k volání HTTP GET.

## <a name="edit-samplepy"></a>Upravit Sample.py

Po vytvoření *Sample.py*otevřete soubor a zkopírujte kód v této části. 

Kód představuje dvoustupňový proces:
1. Načíst token z místního koncového bodu MSI na virtuálním počítači.  
  Tím také načte token z Azure AD.
1. Předejte token do trezoru klíčů a pak přineste svůj tajný klíč. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Hodnotu tajného klíče můžete zobrazit spuštěním následujícího kódu: 

```console
python Sample.py
```

Předchozí kód ukazuje, jak provést operace s Azure Key Vault ve virtuálním počítači windows. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nejsou potřeba, odstraňte virtuální počítač a trezor klíčů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozhraní API REST trezoru klíčů Azure](https://docs.microsoft.com/rest/api/keyvault/)
