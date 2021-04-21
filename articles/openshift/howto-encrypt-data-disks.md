---
title: Šifrování deklarací trvalých svazků pomocí klíče spravovaného zákazníkem (CMK) v Azure Red Hat OpenShift (ARO)
description: Dodejte si vlastní klíč (BYOK)/pokyny k nasazení CMK (Customer-Managed Key) pro Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: šifrování, BYOK, ARO, CMK, OpenShift, Red Hat
ms.openlocfilehash: f6c80bab6f821dc7c85352bf57ebe255ae712d43
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783516"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Šifrování trvalých deklarací identity pomocí klíče spravovaného zákazníkem (CMK) v Azure Red Hat OpenShift (ARO) (Preview)

Azure Storage používá šifrování na straně serveru (SSE) k automatickému [šifrování](../storage/common/storage-service-encryption.md) vašich dat při trvalém ukládání do cloudu. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných platformou Microsoft. Pro další kontrolu nad šifrovacími klíči můžete poskytovat vlastní klíče spravované zákazníkem k šifrování dat v clusterech Azure Red Hat OpenShift.

> [!NOTE]
> V této fázi existuje podpora jenom pro šifrování trvalých svazků v ARO pomocí klíčů spravovaných zákazníkem. Tato funkce není aktuálně k dispozici pro disky s operačním systémem hlavní nebo pracovní uzel.

> [!IMPORTANT]
> Funkce ve verzi Preview v ARO jsou k dispozici na samoobslužné službě, na základě souhlasu. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. Verze Preview pro ARO jsou částečně pokryté zákaznickou podporou na základě nejlepších úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí.

## <a name="before-you-begin"></a>Než začnete
V tomto článku se předpokládá, že:

* Máte již existující cluster ARO na verzi OpenShift 4,4 (nebo vyšší).

* Máte k dispozici nástroj pro příkazový řádek **oC** OpenShift, Base64 (součást coreutils) a nainstalovanou **AZ** Azure CLI.

* K vašemu clusteru ARO jste přihlášení pomocí **oC** jako globální uživatel správce clusteru (kubeadmin).

* K rozhraní příkazového řádku Azure jste přihlášeni pomocí příkazu **AZ** s účtem autorizovaným pro udělení přístupu přispěvatele ve stejném předplatném jako cluster ARO.

## <a name="limitations"></a>Omezení

* Dostupnost pro šifrování klíče spravovaného zákazníkem je specifické pro oblast. Pokud chcete zobrazit stav konkrétní oblasti Azure, zkontrolujte [oblasti Azure][supported-regions].
* Pokud chcete použít disky Ultra, musíte je nejdřív povolit ve svém předplatném, než začnete.

## <a name="declare-cluster--encryption-variables"></a>Deklarovat & proměnných šifrování clusteru
Níže uvedené proměnné byste měli nakonfigurovat tak, aby mohly být vhodné pro vás cluster ARO, ve kterém chcete povolit šifrovací klíče spravované zákazníkem:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Získat ID vašeho předplatného
ID vašeho předplatného Azure se v konfiguraci CMK používá několikrát. Získejte ho a uložte ho jako proměnnou:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Vytvoření instance Azure Key Vault
K uložení klíčů se musí použít instance Azure Key Vault. Vytvoří novou Key Vault s povolenou ochranou vyprázdnění. Pak vytvořte nový klíč v rámci trezoru a uložte si vlastní klíč:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Vytvoření sady Azure Disk Encryption

Azure Disk Encryption sada se používá jako referenční bod pro disky v ARO společnosti. Je připojený k Azure Key Vault, kterou jsme vytvořili v předchozím kroku, a vyžádá si z tohoto místa klíče spravované zákazníky.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Udělte sadě šifrování disku přístup k Key Vault
Použijte sadu šifrování disků, kterou jsme vytvořili v předchozích krocích, a udělte přístup k sadě Disk Encryption Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Získat další ID požadovaná pro přiřazení rolí
Je potřeba, aby měl cluster ARO k šifrování trvalých deklarací identity (PVC) v clusteru ARO použitou sadu Disk Encryption. K tomu vytvoříme novou Identita spravované služby (MSI). Také nastavíme další oprávnění pro MSI pro ARO a pro sadu Disk Encryption.

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>Implementace dalších přiřazení rolí vyžadovaných pro šifrování CMK
Použijte přiřazení požadovaných rolí pomocí proměnných, které jste získali v předchozím kroku:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Vytvoření třídy úložiště k8s pro šifrované disky Premium & Ultra
Generovat třídy úložiště, které se mají použít pro CMK pro Premium_LRS a UltraSSD_LRS disky:

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

V dalším kroku spusťte toto nasazení v clusteru ARO a použijte konfiguraci třídy úložiště:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Testování šifrování pomocí klíčů spravovaných zákazníkem (volitelné)
Pokud chcete zjistit, jestli váš cluster používá pro šifrování PVC klíč spravovaný zákazníkem, vytvoříme pomocí nové třídy úložiště deklaraci trvalého svazku. Následující fragment kódu vytvoří a připojí trvalou deklaraci identity pomocí prémiových disků.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
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
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>Použít konfigurační soubor test pod (volitelné)
Spuštěním následujících příkazů použijte konfiguraci test pod a vraťte UID nové deklarace identity trvalého svazku. UID se použije k ověření šifrování disku pomocí CMK.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> V některých případech může při použití přiřazení rolí v rámci Azure Active Directory dojít k mírné prodlevě. V závislosti na rychlosti spuštění těchto instrukcí se nemusí zdařit příkaz "určení úplného názvu disku Azure". Pokud k tomu dojde, zkontrolujte výstup **oC mypod-with-CMK-Encryption-PVC** a ujistěte se, že se disk úspěšně zřídil. Pokud se rozšíření přiřazení role nedokončilo, může být nutné *Odstranit* a znovu *použít* & PVC YAML.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Ověřte, že je na disku PVC nakonfigurovaná možnost EncryptionAtRestWithCustomerKey (volitelné).
Pod ním by měla být vytvořena deklarace identity trvalého svazku, která odkazuje na třídu úložiště CMK. Spuštěním následujícího příkazu ověříte, že je trvalý virtuální okruh nasazený podle očekávání:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions