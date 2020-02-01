---
title: Použití klíče spravovaného zákazníkem k šifrování disků Azure ve službě Azure Kubernetes Service (AKS)
description: Přineste si vlastní klíče (BYOK) k šifrování AKS operačního systému a datových disků.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 831a867d1084f7396db0f8aa4c2e2e665eb98732
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898816"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Přineste si vlastní klíče (BYOK) s disky Azure ve službě Azure Kubernetes Service (AKS).

Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete zadat [klíče spravované zákazníkem][customer-managed-keys] , které se použijí pro šifrování operačního systému i datových disků pro clustery AKS.

> [!NOTE]
> Clustery AKS s BYOK Linux a Windows jsou dostupné v [oblastech Azure][supported-regions] , které podporují šifrování na straně serveru Azure Managed disks.

## <a name="before-you-begin"></a>Než začnete

* V tomto článku se předpokládá, že vytváříte *nový cluster AKS*.

* Pokud používáte Key Vault k šifrování spravovaných disků, musíte povolit ochranu pomocí obnovitelného odstranění a vyprázdnění pro *Azure Key Vault* .

* Potřebujete Azure CLI verze 2.0.79 nebo novější a rozšíření 0.4.26 AKS-Preview.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace o tom, jak se zaregistrují, najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS](support-policies.md)
> * [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Nainstalovat nejnovější rozšíření AKS CLI Preview

Pokud chcete používat klíče spravované zákazníkem, potřebujete rozšíření *AKS-Preview* CLI verze 0.4.26 nebo vyšší. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] a potom zkontrolujte, jestli nejsou dostupné aktualizace, pomocí příkazu [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

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
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Udělení přístupu DiskEncryptionSet k trezoru klíčů

Použijte DiskEncryptionSet a skupiny prostředků, které jste vytvořili v předchozích krocích, a udělte přístup k Azure Key Vault prostředkům DiskEncryptionSet.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Vytvořte nový cluster AKS a Zašifrujte disk s operačním systémem.

Vytvořte **novou skupinu prostředků** a cluster AKS a pak použijte svůj klíč k zašifrování disku s operačním systémem. Klíče spravované zákazníkem jsou podporovány pouze ve verzích Kubernetes vyšších než 1,17. 

> [!IMPORTANT]
> Ujistěte se, že jste pro svůj cluster AKS vytvořili novou skupinu prostředků.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Když se do clusteru vytvořeného výše přidá nové fondy uzlů, klíč spravovaný zákazníkem, který se poskytuje během vytváření, se použije k zašifrování disku s operačním systémem.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Šifrování datového disku clusteru AKS

Datové disky AKS můžete také šifrovat pomocí vlastních klíčů.

> [!IMPORTANT]
> Ujistěte se, že máte správné přihlašovací údaje AKS. Instanční objekt bude muset mít přístup přispěvatele ke skupině prostředků, ve které je nasazená služba diskencryptionset. V opačném případě se zobrazí chyba naznačující, že objekt služby nemá oprávnění.

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

## <a name="limitations"></a>Omezení

* BYOK je aktuálně dostupná jenom v GA a ve verzi Preview v určitých [oblastech Azure][supported-regions] .
* Šifrování disku s operačním systémem s podporou Kubernetes verze 1,17 a vyšší   
* K dispozici pouze v oblastech, kde je podporována podpora BYOK
* Šifrování pomocí klíčů spravovaných zákazníkem se momentálně používá jenom pro nové clustery AKS. stávající clustery nejde upgradovat.
* AKS Virtual Machine Scale Sets cluster se vyžaduje, není dostupná žádná podpora pro sady dostupnosti virtuálních počítačů.


## <a name="next-steps"></a>Další kroky

Kontrola [osvědčených postupů pro zabezpečení clusteru AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
