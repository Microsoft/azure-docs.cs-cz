---
title: Použití klíče spravovaného zákazníkem k šifrování disků Azure ve službě Azure Kubernetes Service (AKS)
description: Přineste si vlastní klíče (BYOK) k šifrování AKS operačního systému a datových disků.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/09/2020
ms.author: mlearned
ms.openlocfilehash: 3efb7a6005d862b15beec0f979b67a701a26ba74
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903961"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Přineste si vlastní klíče (BYOK) s disky Azure ve službě Azure Kubernetes Service (AKS).

Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete zadat [klíče spravované zákazníkem][customer-managed-keys] , které se použijí pro šifrování operačního systému i datových disků pro clustery AKS.

> [!NOTE]
> Clustery AKS založené na systému Linux a Windows jsou podporované.

## <a name="before-you-begin"></a>Než začnete

* V tomto článku se předpokládá, že vytváříte *nový cluster AKS*.  K ukládání šifrovacích klíčů budete taky muset použít nebo vytvořit instanci Azure Key Vault.

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

## <a name="create-an-azure-key-vault-instance-to-store-your-keys"></a>Vytvoření instance Azure Key Vault pro uložení klíčů

Volitelně můžete použít Azure Portal ke [konfiguraci klíčů spravovaných zákazníkem Azure Key Vault][byok-azure-portal]

Vytvořte novou *skupinu prostředků*, vytvořte novou instanci *Key Vault* a povolte ochranu pomocí obnovitelného odstranění a vyprázdnění.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations

# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup-l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Vytvoření instance objektu DiskEncryptionSet

K provedení následujících kroků budete potřebovat *klíč* uložený v Azure Key Vault.  Uložte si existující klíč do Key Vault, který jste vytvořili, nebo [vygenerujte klíč][key-vault-generate] .
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup--source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-resource-access-to-the-key-vault"></a>Udělení přístupu k DiskEncryptionSet prostředku do trezoru klíčů

Použijte DiskEncryptionSet a skupiny prostředků, které jste vytvořili v předchozích krocích, a udělte přístup k Azure Key Vault prostředkům DiskEncryptionSet.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup--query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup--object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk-with-a-customer-manged-key"></a>Vytvořte nový cluster AKS a Zašifrujte disk s operačním systémem pomocí klíčového spravovaných zákazníka.

Vytvořte novou skupinu prostředků a cluster AKS a pak použijte svůj klíč k zašifrování disku s operačním systémem.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n $diskEncryptionSetName -g ssecmktesting --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup-l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionsetid diskEncryptionId
```

## <a name="add-a-node-pool-to-an-existing-aks-cluster-and-encrypt-the-os-disk-with-a-customer-managed-key"></a>Přidání fondu uzlů do existujícího clusteru AKS a šifrování disku s operačním systémem pomocí klíče spravovaného zákazníkem

Nové nodepools ve výchozím nastavení nepoužívají šifrované disky.  Nový fond uzlů můžete přidat do existujícího clusteru a pomocí následujícího příkazu zašifrovat disk s operačním systémem pomocí vlastního klíče.

```azurecli-interactive
# Add a nodepool to an existing cluster with BYOK encryption
nodepool add –-cluster-name myAKSCluster -n myNodePoolName -g myResourceGroup --node-osdisk-diskencryptionsetid diskEncryptionId  
```

## <a name="encrypt-your-aks-cluster-data-disk-with-a-customer-managed-key"></a>Šifrování datového disku clusteru AKS pomocí klíče spravovaného zákazníkem

Datové disky AKS můžete také šifrovat pomocí vlastních klíčů.  Nahraďte myResourceGroup a myDiskEncryptionSetName skutečnými hodnotami a použijte YAML.

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Nasazení ukázkové image z ACR do AKS

Ujistěte se, že máte správné přihlašovací údaje AKS.

Vytvořte soubor s názvem **BYOK-Azure-disk. yaml** , který obsahuje následující informace.  Nahraďte myResourceGroup a myDiskEncrptionSetName hodnotami.

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{subs-id}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
V dalším kroku spusťte toto nasazení v clusteru AKS:
```azurecli-interactive
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Omezení

* Šifrování disku s operačním systémem s podporou Kubernetes verze 1,17 a vyšší   
* K dispozici pouze v oblastech, kde je podporována podpora BYOK
* V současné době platí jenom pro nové clustery AKS. existující clustery nejde upgradovat.
* AKS Virtual Machine Scale Sets cluster se vyžaduje, není dostupná žádná podpora pro sady dostupnosti virtuálních počítačů.


## <a name="next-steps"></a>Další kroky

Kontrola [osvědčených postupů pro zabezpečení clusteru AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys-public-preview
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
