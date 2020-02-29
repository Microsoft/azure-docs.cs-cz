---
title: Kurz – použití Azure Key Vault s virtuálním počítačem s Windows v Pythonu | Microsoft Docs
description: V tomto kurzu nakonfigurujete aplikaci ASP.NET Core pro čtení tajného klíče z vašeho trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 3b965ab7dfafd6c78c801cf3692463efe366c852
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198078"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Kurz: použití Azure Key Vault s virtuálním počítačem s Windows v Pythonu

Azure Key Vault pomáhá chránit tajné kódy, jako jsou klíče rozhraní API, databázové připojovací řetězce, které potřebujete pro přístup k aplikacím, službám a prostředkům IT.

V tomto kurzu se naučíte, jak získat konzolovou aplikaci pro čtení informací z Azure Key Vault. Uděláte to tak, že použijete spravované identity pro prostředky Azure. 

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Přidejte tajný klíč do trezoru klíčů.
> * Načtení tajného klíče z trezoru klíčů
> * Vytvořte virtuální počítač Azure.
> * Povolte spravovanou identitu.
> * Přiřaďte oprávnění k identitě virtuálního počítače.

Než začnete, přečtěte si téma [Key Vault Basic koncepty](basic-concepts.md). 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

Pro Windows, Mac a Linux:
  * [Git](https://git-scm.com/downloads)
  * Tento kurz vyžaduje, abyste spouštěli Azure CLI místně. Musíte mít nainstalovanou verzi Azure CLI 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informace o funkci Identita spravované služby

Azure Key Vault ukládá pověření bezpečně, takže se ve vašem kódu nezobrazují. K načtení klíčů ale musíte ověřit, Azure Key Vault. K ověření Key Vault budete potřebovat přihlašovací údaje. Je to klasický spouštěcí dilematem. Identita spravované služby (MSI) Tento problém řeší tím, že poskytuje _identitu_ spuštění, která zjednodušuje proces.

Když povolíte MSI pro službu Azure, například Azure Virtual Machines, Azure App Service nebo Azure Functions, vytvoří [Služba Azure instanční objekt](basic-concepts.md). Služba MSI to provede pro instanci služby v Azure Active Directory (Azure AD) a vloží do této instance přihlašovací údaje instančního objektu. 

![MSI](media/MSI.png)

Pokud chcete získat přístupový token, váš kód volá místní službu metadat, která je k dispozici v prostředku Azure. K ověření u Azure Key Vault služby používá váš kód přístupový token, který získá z místního koncového bodu MSI. 

## <a name="log-in-to-azure"></a>Přihlášení k Azure

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

V tomto kurzu použijete nově vytvořenou skupinu prostředků.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Pokud chcete vytvořit Trezor klíčů ve skupině prostředků, kterou jste vytvořili v předchozím kroku, zadejte následující informace:

* Název trezoru klíčů: řetězec na 3 až 24 znaků, který může obsahovat jenom číslice (0-9), písmena (a-z, A-Z) a spojovníky (-).
* Název skupiny prostředků
* Umístění: **západní USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
V tomto okamžiku je váš účet Azure jediným z nich, který je autorizovaný k provádění operací s tímto novým trezorem klíčů.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Tajný klíč může být připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete pro zajištění zabezpečení i k dispozici pro vaši aplikaci.

Pokud chcete v trezoru klíčů s názvem **AppSecret**vytvořit tajný klíč, zadejte následující příkaz:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Tento tajný kód ukládá hodnotu **MySecret**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Virtuální počítač můžete vytvořit pomocí jedné z následujících metod:

* [Rozhraní příkazového řádku Azure](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Přiřazení identity k virtuálnímu počítači
V tomto kroku vytvoříte identitu přiřazenou systémem pro virtuální počítač spuštěním následujícího příkazu v rozhraní příkazového řádku Azure CLI:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Poznamenejte si identitu přiřazenou systémem, která se zobrazí v následujícím kódu. Výstup předchozího příkazu by byl: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Přiřazení oprávnění k identitě virtuálního počítače
Nyní můžete k trezoru klíčů přiřadit dříve vytvořená oprávnění identity spuštěním následujícího příkazu:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Přihlášení k virtuálnímu počítači

Pokud se chcete přihlásit k virtuálnímu počítači, postupujte podle pokynů v tématu [připojení a přihlášení k virtuálnímu počítači Azure s Windows](../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Vytvoření a spuštění ukázkové aplikace v Pythonu

V další části je příklad souboru s názvem *Sample.py*. Používá knihovnu [požadavků](https://2.python-requests.org/en/master/) k tomu, aby volání HTTP GET.

## <a name="edit-samplepy"></a>Upravit Sample.py

Po vytvoření *Sample.py*otevřete soubor a zkopírujte kód v této části. 

Kód představuje proces se dvěma kroky:
1. Načte token z místního koncového bodu MSI na virtuálním počítači.  
  Tím se taky načte token z Azure AD.
1. Předejte token do trezoru klíčů a potom tento klíč načtěte. 

```
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

Tajnou hodnotu můžete zobrazit spuštěním následujícího kódu: 

```
python Sample.py
```

Předchozí kód ukazuje, jak provádět operace s Azure Key Vault na virtuálním počítači s Windows. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte virtuální počítač a trezor klíčů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
