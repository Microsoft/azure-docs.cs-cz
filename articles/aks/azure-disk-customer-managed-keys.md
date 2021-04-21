---
title: Použití klíče spravovaného zákazníkem k šifrování disků Azure ve službě Azure Kubernetes Service (AKS)
description: Přineste si vlastní klíče (BYOK) k šifrování AKS operačního systému a datových disků.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: c5c555d7eb5142f5f41f65b24f754c65450a2713
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776186"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Přineste si vlastní klíče (BYOK) s disky Azure ve službě Azure Kubernetes Service (AKS).

Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete zadat klíče spravované zákazníkem, které se použijí pro šifrování v klidovém prostředí pro AKS clustery i pro datové disky. Přečtěte si další informace o klíčích spravovaných zákazníkem v systémech [Linux][customer-managed-keys-linux] a [Windows][customer-managed-keys-windows].

## <a name="limitations"></a>Omezení
* Podpora šifrování datových disků je omezená na AKS clustery se systémem Kubernetes verze 1,17 a vyšší.
* Šifrování operačního systému a datového disku pomocí klíčů spravovaných zákazníkem se dá povolit jenom při vytváření clusteru AKS.

## <a name="prerequisites"></a>Požadavky
* Pokud používáte Key Vault k šifrování spravovaných disků, musíte povolit ochranu pomocí obnovitelného odstranění a vyprázdnění pro *Azure Key Vault* .
* Potřebujete Azure CLI verze 2.11.1 nebo novější.

## <a name="create-an-azure-key-vault-instance"></a>Vytvoření instance Azure Key Vault

Použijte instanci Azure Key Vault k uložení klíčů.  Volitelně můžete použít Azure Portal ke [konfiguraci klíčů spravovaných zákazníkem Azure Key Vault][byok-azure-portal]

Vytvořte novou *skupinu prostředků*, vytvořte novou instanci *Key Vault* a povolte ochranu pomocí obnovitelného odstranění a vyprázdnění.  Ujistěte se, že pro každý příkaz používáte stejnou oblast a názvy skupin prostředků.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Vytvoření instance objektu DiskEncryptionSet

Nahraďte *myKeyVaultName* názvem vašeho trezoru klíčů.  Budete také potřebovat *klíč* uložený v Azure Key Vault k provedení následujících kroků.  Uložte svůj existující klíč do Key Vault, který jste vytvořili v předchozích krocích, nebo [Vygenerujte nový klíč][key-vault-generate] a nahraďte *myKeyName* názvem vašeho klíče.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Udělení přístupu DiskEncryptionSet k trezoru klíčů

Použijte DiskEncryptionSet a skupiny prostředků, které jste vytvořili v předchozích krocích, a udělte přístup k Azure Key Vault prostředkům DiskEncryptionSet.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Vytvořte nový cluster AKS a Zašifrujte disk s operačním systémem.

Vytvořte **novou skupinu prostředků** a cluster AKS a pak použijte svůj klíč k zašifrování disku s operačním systémem. Klíče spravované zákazníkem jsou podporovány pouze ve verzích Kubernetes vyšších než 1,17. 

> [!IMPORTANT]
> Ujistěte se, že jste pro svůj cluster AKS vytvořili novou skupinu prostředků.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Když se do clusteru vytvořeného výše přidá nové fondy uzlů, klíč spravovaný zákazníkem, který se poskytuje během vytváření, se použije k zašifrování disku s operačním systémem.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Šifrování datového disku clusteru AKS (volitelné)
Šifrovací klíč disku s operačním systémem se použije k zašifrování datového disku, pokud se pro datový disk z 1.17.2 neposkytne klíč, a můžete taky šifrovat datové disky AKS s ostatními klíči.

> [!IMPORTANT]
> Ujistěte se, že máte správné přihlašovací údaje AKS. Spravovaná identita bude potřebovat přístup přispěvatele ke skupině prostředků, ve které je nasazený diskencryptionset. V opačném případě se zobrazí chyba naznačující, že spravovaná identita nemá oprávnění.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Vytvořte soubor s názvem **BYOK-Azure-disk. yaml** , který obsahuje následující informace.  Nahraďte myAzureSubscriptionId, myResourceGroup a myDiskEncrptionSetName hodnotami a použijte YAML.  Ujistěte se, že používáte skupinu prostředků, ve které je nasazená vaše DiskEncryptionSet.  Pokud Azure Cloud Shell použijete, můžete tento soubor vytvořit pomocí VI nebo nano jako při práci na virtuálním nebo fyzickém systému:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
V dalším kroku spusťte toto nasazení v clusteru AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Další kroky

Kontrola [osvědčených postupů pro zabezpečení clusteru AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions