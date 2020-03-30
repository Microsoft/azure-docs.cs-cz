---
title: Šifrování disků Azure ve službě Azure Kubernetes Service (AKS) pomocí klíče spravovaného zákazníkem
description: Přineste si vlastní klíče (BYOK) pro šifrování AKS OS a datové disky.
services: container-service
ms.topic: article
ms.date: 01/12/2020
ms.openlocfilehash: bb6ba5e6dd4ace9e33043079c0f435c10baf5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596500"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Přineste si vlastní klíče (BYOK) s disky Azure ve službě Azure Kubernetes Service (AKS)

Azure Storage šifruje všechna data v účtu úložiště v klidovém stavu. Ve výchozím nastavení jsou data šifrována pomocí klíčů spravovaných společností Microsoft. Pro další kontrolu nad šifrovacími klíči můžete zadat [klíče spravované zákazníkem,][customer-managed-keys] které se použijí pro šifrování v klidovém stavu pro operační systém i datové disky pro clustery AKS.

> [!NOTE]
> Clustery AKS založené na BYOK Linuxu a Windows jsou dostupné v [oblastech Azure,][supported-regions] které podporují šifrování spravovaných disků Azure na straně serveru.

## <a name="before-you-begin"></a>Než začnete

* Tento článek předpokládá, že vytváříte *nový cluster AKS*.

* Při šifrování spravovaných disků je nutné povolit ochranu proti odstranění a vymazání úložiště klíčů Azure Key *Vault.*

* Potřebujete azure CLI verze 2.0.79 nebo novější a rozšíření aks-preview 0.4.26

> [!IMPORTANT]
> Funkce AKS preview jsou samoobslužné opt-in. Náhledy jsou poskytovány "tak, jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS Previews jsou částečně pokryty zákaznickou podporou na základě maximálního úsilí. Jako takové tyto funkce nejsou určeny pro produkční použití. Další infromace naleznete v následujících článcích podpory:
>
> * [Zásady podpory AKS](support-policies.md)
> * [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>Instalace nejnovějšího rozšíření náhledu AKS CLI

Chcete-li používat klíče spravované zákazníkem, potřebujete rozšíření cli *aks-preview* verze 0.4.26 nebo vyšší. Nainstalujte rozšíření *AKS-preview* Azure CLI pomocí příkazu [az extension add][az-extension-add] a pak zkontrolujte všechny dostupné aktualizace pomocí příkazu [aktualizace rozšíření az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Vytvoření instance trezoru klíčů Azure

K ukládání klíčů použijte instanci Azure Key Vault.  Na webu Azure Portal můžete volitelně použít [ke konfiguraci klíčů spravovaných zákazníky pomocí azure key vaultu.][byok-azure-portal]

Vytvořte novou *skupinu prostředků*, vytvořte novou instanci *trezoru klíčů* a povolte ochranu proti měkkému odstranění a vymazání.  Ujistěte se, že pro každý příkaz používáte stejné názvy oblastí a skupin prostředků.

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

## <a name="create-an-instance-of-a-diskencryptionset"></a>Vytvoření instance sady DiskEncryptionSet

Nahraďte *název myKeyVaultName* názvem trezoru klíčů.  Budete také potřebovat *klíč* uložený v azure key vault k dokončení následujících kroků.  Buď uložte existující klíč do trezoru klíčů, který jste vytvořili v předchozích krocích, nebo [vygenerujte nový klíč][key-vault-generate] a nahraďte *myKeyName* níže názvem klíče.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Udělení přístupu programu DiskEncryptionSet k trezoru klíčů

Použijte DiskEncryptionSet a skupiny prostředků, které jste vytvořili v předchozích krocích a udělit DiskEncryptionSet přístup k prostředku Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Vytvoření nového clusteru AKS a šifrování disku operačního systému

Vytvořte **novou skupinu prostředků** a cluster AKS a potom pomocí klíče zašifrujte disk operačního systému. Klíče spravované zákazníkem jsou podporovány pouze ve verzích Kubernetes větších než 1.17. 

> [!IMPORTANT]
> Ujistěte se, že vytvoříte novou skupinu resoklid pro váš cluster AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

Při přidání nových fondů uzlů do clusteru vytvořeného výše se klíč spravovaný zákazníkem poskytnutý během vytváření použije k šifrování disku operačního systému.

## <a name="encrypt-your-aks-cluster-data-disk"></a>Šifrování datového disku clusteru AKS

Datové disky AKS můžete také šifrovat pomocí vlastních klíčů.

> [!IMPORTANT]
> Ujistěte se, že máte správné pověření AKS. Instanční objekt bude muset mít přístup přispěvatele ke skupině prostředků, kde je nasazena sada šifrování disku. V opačném případě se zobrazí chyba oznamovat, že instanční objekt nemá oprávnění.

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

Vytvořte soubor s názvem **byok-azure-disk.yaml,** který obsahuje následující informace.  Nahraďte myAzureSubscriptionId, myResourceGroup a myDiskEncrptionSetName svými hodnotami a použijte yaml.  Ujistěte se, že používáte skupinu prostředků, kde je nasazena sada DiskEncryptionSet.  Pokud používáte Azure Cloud Shell, tento soubor lze vytvořit pomocí vi nebo nano, jako by pracoval na virtuální nebo fyzický systém:

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
Dále spusťte toto nasazení v clusteru AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>Omezení

* BYOK je momentálně dostupný jenom v GA a Preview v určitých [oblastech Azure.][supported-regions]
* Šifrování disku operačního systému podporované kubernetes verze 1.17 a vyšší   
* K dispozici pouze v oblastech, kde je podporován BYOK
* Šifrování pomocí klíčů spravovaných zákazníkem je v současné době pouze pro nové clustery AKS, existující clustery nelze upgradovat.
* Cluster AKS používající škálovací sady virtuálních strojů je povinný, žádná podpora pro sady dostupnosti virtuálních strojů


## <a name="next-steps"></a>Další kroky

Projděte si [doporučené postupy pro zabezpečení clusteru AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
