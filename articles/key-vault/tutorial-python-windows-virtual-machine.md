---
title: Kurz – použití Azure Key Vault se virtuální počítač s Windows v Pythonu | Dokumentace Microsoftu
description: V tomto kurzu nakonfigurujete aplikaci ASP.NET core pro čtení tajného klíče z trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: cdc540f2f6fa834a97c4c405276414f29672e5c7
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876674"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Kurz: Virtuální počítač s Windows v Pythonu pomocí služby Azure Key Vault

Služba Azure Key Vault pomáhá chránit tajné kódy jako jsou klíče rozhraní API, databázové připojovací řetězce, potřebujete přístup k aplikacím, služby a prostředky IT.

V tomto kurzu se dozvíte, jak získat konzolovou aplikaci pro čtení informací z Azure Key Vault. To provedete tak, pomocí spravované identity pro prostředky Azure. 

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Přidání tajného klíče do trezoru klíčů.
> * Načtení tajného klíče z trezoru klíčů
> * Vytvoření virtuálního počítače Azure.
> * Povolte spravované identity.
> * Přiřadíte oprávnění na identitu virtuálního počítače.

Než začnete, přečtěte si [základní koncepty služby Key Vault](key-vault-whatis.md#basic-concepts). 

Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Pro Windows, Mac a Linux:
  * [Git](https://git-scm.com/downloads)
  * Tento kurz vyžaduje, abyste spustili Azure CLI místně. Musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informace o funkci Identita spravované služby

Azure Key Vault ukládá přihlašovací údaje bezpečně, takže nejsou zobrazeny ve vašem kódu. Však musíte pro ověření do služby Azure Key Vault k načtení klíče. K ověření do služby Key Vault, budete potřebovat přihlašovací údaje. Je classic dilema bootstrap. Identita spravované služby (MSI) řeší tento problém tím, že poskytuje _bootstrap identity_ , která zjednodušuje proces.

Při povolení MSI pro službu Azure, jako jsou Azure Virtual Machines, Azure App Service nebo Azure Functions, Azure vytvoří [instanční objekt služby](key-vault-whatis.md#basic-concepts). MSI se k tomu pro instanci služby v Azure Active Directory (Azure AD) a vloží pověření instančního objektu do této instance. 

![MSI](media/MSI.png)

V dalším kroku k získání přístupového tokenu, váš kód volá služba místních metadat, která je k dispozici u prostředku Azure. K ověření do služby Azure Key Vault, váš kód používá přístupový token, který získá z místního koncového bodu MSI. 

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). 

Vyberte název skupiny prostředků a nahraďte zástupný text. Následující příklad vytvoří skupinu prostředků v umístění Západní USA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Vaše nově vytvořené skupiny prostředků v celém tomto kurzu použijete.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Chcete-li vytvořit trezor klíčů ve skupině prostředků, kterou jste vytvořili v předchozím kroku, zadejte následující informace:

* Název trezoru klíčů: řetězec dlouhý 3 až 24 znaků, které mohou obsahovat pouze číslice (0 – 9), písmena (a – z, A-Z) a pomlčky (-)
* Název skupiny prostředků
* Umístění: **Západní USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Váš účet Azure v tuto chvíli je pouze jeden, který má oprávnění k provádění operací s tímto novým trezorem klíčů.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Tajný kód může být připojovací řetězec SQL nebo nějakých jiných informací, které potřebujete k zabezpečení a k dispozici pro vaši aplikaci.

Vytvoření tajného klíče v trezoru klíčů s názvem **AppSecret**, zadejte následující příkaz:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Tento tajný kód ukládá hodnotu **MySecret**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Vytvoření virtuálního počítače pomocí jedné z následujících metod:

* [Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-an-identity-to-the-vm"></a>Přiřadit identitu virtuálního počítače
V tomto kroku vytvoření systém přiřadil identity pro virtuální počítač spuštěním následujícího příkazu v Azure CLI:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Všimněte si systém přiřadil identity, který se zobrazí v následujícím kódu. Výstup ve výstupu předchozího příkazu by měl: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Přiřazení oprávnění na identitu virtuálního počítače
Nyní můžete přiřadit oprávnění identity dříve vytvořeného trezoru klíčů spuštěním následujícího příkazu:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Přihlášení k virtuálnímu počítači

Přihlaste se k virtuálnímu počítači, postupujte podle pokynů v [připojit a přihlásit se k virtuálnímu počítači Azure s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-a-sample-python-app"></a>Vytvoření a spuštění ukázkové aplikace v Pythonu

V další části je ukázkový soubor s názvem *Sample.py*. Použije [požadavky](http://docs.python-requests.org/en/master/) knihovny pro volání HTTP GET.

## <a name="edit-samplepy"></a>Upravit Sample.py

Po vytvoření *Sample.py*, otevřete soubor a potom zkopírujte kód v této části. 

Kód představuje ve dvou krocích:
1. Načtěte token z koncového bodu místní instalační služby MSI ve virtuálním počítači.  
  Tím také načte token ze služby Azure AD.
1. Předat token trezoru klíčů a pak načíst váš tajný klíč. 

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

Hodnota tajného klíče můžete zobrazit spuštěním následujícího kódu: 

```
python Sample.py
```

Předchozí kód ukazuje, jak provádět operace se službou Azure Key Vault do virtuálního počítače s Windows. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte virtuální počítač a trezor klíčů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozhraní REST API služby Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
