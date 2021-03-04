---
title: Šifrování deklarací trvalých svazků pomocí klíče spravovaného zákazníkem (CMK) v Azure Red Hat OpenShift (ARO)
description: Dodejte si vlastní klíč (BYOK)/pokyny k nasazení CMK (Customer-Managed Key) pro Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: šifrování, BYOK, ARO, OpenShift, Red Hat
ms.openlocfilehash: 09e1f92a967c7b77d3bb8e27769f4cafd4fd4f53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054601"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Šifrování trvalých deklarací identity pomocí klíče spravovaného zákazníkem (CMK) v Azure Red Hat OpenShift (ARO) (Preview)

Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem, které obsahují operační systém a datové disky. Pro lepší kontrolu šifrovacích klíčů můžete poskytovat klíče spravované zákazníkem k šifrování dat v clusterech Azure Red Hat OpenShift.

> [!NOTE]
> V této fázi existuje podpora jenom pro šifrování trvalých svazků v ARO pomocí klíčů spravovaných zákazníkem. Tato funkce není aktuálně k dispozici pro disky s operačním systémem.

> [!IMPORTANT]
> Funkce ve verzi Preview v ARO jsou k dispozici na samoobslužné službě, na základě souhlasu. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. Verze Preview pro ARO jsou částečně pokryté zákaznickou podporou na základě nejlepších úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí.

## <a name="before-you-begin"></a>Než začnete
V tomto článku se předpokládá, že:

* Máte již existující cluster ARO na verzi OpenShift 4,4 (nebo vyšší).

* Máte nástroj příkazového řádku "oC" OpenShift, Base64 (součást základních nástrojů) a je nainstalováno rozhraní příkazového řádku "AZ" Azure CLI.

* K vašemu clusteru ARO jste se přihlásili pomocí *oC* jako globální uživatel správce clusteru (kubeadmin).

* K rozhraní příkazového řádku Azure jste přihlášeni pomocí příkazu *AZ* s účtem autorizovaným pro udělení přístupu přispěvatele ve stejném předplatném jako cluster ARO.

## <a name="limitations"></a>Omezení

* Dostupnost pro šifrování klíče spravovaného zákazníkem je specifické pro oblast. Pokud chcete zobrazit stav konkrétní oblasti Azure, zkontrolujte [oblasti Azure][supported-regions].
* Pokud používáte Ultra disks, povolte v předplatném, než začnete.

## <a name="create-an-azure-key-vault-instance"></a>Vytvoření instance Azure Key Vault
K uložení klíčů se musí použít instance Azure Key Vault. Vytvoří novou Key Vault s povolenou ochranou vyprázdnění a obnovitelného odstranění. Pak vytvořte nový klíč v rámci trezoru a uložte si vlastní klíč:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table
# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Vytvoření sady Azure Disk Encryption

Sada Azure Disk Encryption se používá jako referenční bod pro disky v ARO společnosti. Je připojený k Azure Key Vault, kterou jsme vytvořili v předchozím kroku, a vyžádá si z tohoto místa klíče spravované zákazníky.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $myRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Udělte sadě šifrování disku přístup k Key Vault
Použijte sadu šifrování disků, kterou jsme vytvořili v předchozích krocích, a udělte přístup k sadě Disk Encryption Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's AppId value.
desIdentity="$(az disk-encryption-set show -n $desName -g $myRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $myRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the disk encryption set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Získat další ID požadovaná pro přiřazení rolí
Je potřeba, aby měl cluster ARO k šifrování trvalých deklarací identity (PVC) v clusteru ARO použitou sadu Disk Encryption. K tomu vytvoříme novou Identita spravované služby (MSI). Také nastavíme další oprávnění pro MSI pro ARO a pro sadu Disk Encryption.
```
# First, get the application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $myRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity application ID.
aroMSIAppId="$(az identity show -n $msiName -g $myRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
myRGResourceId="$(az group show -n $myRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-byokcmk-encryption"></a>Implementace dalších přiřazení rolí vyžadovaných pro šifrování BYOK/CMK
Použijte přiřazení požadovaných rolí pomocí proměnných, které jste získali v předchozím kroku:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $myRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $myRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks-optional"></a>Vytvoření třídy úložiště k8s pro šifrované disky Premium & Ultra (volitelné)
Generovat třídy úložiště, které se mají použít pro BYOK/CMK pro Premium_LRS a UltraSSD_LRS disky:
```
# Premium Disks
cat > managed-premium-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```
### <a name="set-up-your-storage-class-configuration"></a>Nastavení vaší konfigurace třídy úložiště
Nahraďte proměnné, které jsou jedinečné pro váš cluster ARO, do dvou konfiguračních souborů třídy úložiště:
```
# Insert your current active subscription ID into the configuration
sed -i "s/subId/$subId/g" managed-premium-encrypted-byok.yaml
sed -i "s/subId/$subId/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the Resource Group which contains the disk encryption set and Key Vault
sed -i "s/myRG/$myRG/g" managed-premium-encrypted-byok.yaml
sed -i "s/myRG/$myRG/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the disk encryption set
sed -i "s/desName/$desName/g" managed-premium-encrypted-byok.yaml
sed -i "s/desName/$desName/g" managed-ultra-encrypted-byok.yaml
```
V dalším kroku spusťte toto nasazení v clusteru ARO a použijte konfiguraci třídy úložiště:
```
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-byok.yaml
oc apply -f managed-ultra-encrypted-byok.yaml
```
## <a name="test-encryption-with-customer-managed-keys"></a>Testování šifrování pomocí klíčů spravovaných zákazníkem
Pokud chcete zjistit, jestli váš cluster používá pro šifrování PVC klíč spravovaný zákazníkem, vytvoříme pomocí příslušné třídy úložiště deklaraci trvalého svazku. Následující fragment kódu vytvoří a připojí trvalou deklaraci identity pomocí standardních disků.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-byok-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-byok
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-byok-encryption
spec:
  containers:
  - name: mypod-with-byok-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-byok-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file"></a>Použít konfigurační soubor test pod
Spuštěním následujících příkazů použijte konfiguraci test pod a vraťte UID nové deklarace identity trvalého svazku. UID se použije k ověření šifrování disku pomocí BYOK/CMK.
```
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o json | jq -r '.items[0].metadata.uid')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o json |jq -r '.spec.azureDisk.diskName')"
```
### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey"></a>Ověřte, že je na disku PVC nakonfigurovaná možnost EncryptionAtRestWithCustomerKey. 
Pod ním by měla být vytvořena deklarace identity trvalého svazku, která odkazuje na třídu úložiště BYOK/CMK. Spuštěním následujícího příkazu ověříte, že je trvalý virtuální okruh nasazený podle očekávání:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $myRG -o json --query [encryption]
```

## <a name="next-steps"></a>Další kroky

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

