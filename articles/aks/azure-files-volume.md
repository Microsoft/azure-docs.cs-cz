---
title: Vytvoření statického svazku pro více podů ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak ručně vytvořit svazek pomocí souborů Azure pro použití s více souběžnými pody ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 084ab5cd6736c9148bcab1faf048d3d9081855d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596398"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Ruční vytvoření a použití svazku se službou Azure Files ve službě Azure Kubernetes Service (AKS)

Aplikace založené na kontejnerech často potřebují přístup k datům a jejich uchovávání v externím datovém svazku. Pokud více podů potřebují souběžný přístup ke stejnému svazku úložiště, můžete použít soubory Azure pro připojení pomocí [protokolu Server Message Block (SMB).][smb-overview] Tento článek ukazuje, jak ručně vytvořit sdílené složky Azure Files a připojit ji k podu v AKS.

Další informace o svazcích Kubernetes najdete [v tématu Možnosti úložiště pro aplikace v AKS][concepts-storage].

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované verze Azure CLI verze 2.0.59 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Než budete moci použít Soubory Azure jako svazek Kubernetes, musíte vytvořit účet Azure Storage a sdílenou složku. Následující příkazy vytvoří skupinu prostředků s názvem *myAKSShare*, účet úložiště a sdílenou složku Soubory s názvem *aksshare*:

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Poznamenejte si název účtu úložiště a klíč zobrazený na konci výstupu skriptu. Tyto hodnoty jsou potřeba při vytváření svazku Kubernetes v jednom z následujících kroků.

## <a name="create-a-kubernetes-secret"></a>Vytvoření tajného klíče Kubernetes

Kubernetes potřebuje přihlašovací údaje pro přístup ke sdílené složce vytvořené v předchozím kroku. Tato pověření jsou uložena v [tajném klíči Kubernetes][kubernetes-secret], na který se odkazuje při vytváření podu Kubernetes.

Pomocí `kubectl create secret` příkazu vytvořte tajný klíč. Následující příklad vytvoří sdílený pojmenovaný *azure-secret* a naplní *azurestorageaccountname* a *azurestorageaccountkey* z předchozího kroku. Pokud chcete použít existující účet úložiště Azure, zadejte název účtu a klíč.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Připojení sdílené složky jako svazku

Chcete-li připojit sdílené složky Azure Files do podu, nakonfigurujte svazek ve specifikaci kontejneru. `azure-files-pod.yaml` Pokud jste změnili název sdílené složky nebo tajného názvu souborů, aktualizujte *název sharename* a *secretName*. V případě potřeby `mountPath`aktualizujte , což je cesta, kde je sdílená složka Soubory připojena v podu. Pro kontejnery Windows Serveru (aktuálně ve verzi preview v AKS) zadejte *mountPath* pomocí konvence cesty systému Windows, například *"D:"*.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Pomocí `kubectl` příkazu vytvořte pod.

```console
kubectl apply -f azure-files-pod.yaml
```

Teď máte spuštěný pod se sdílenou spores Azure Files připojenou na */mnt/azure*. Můžete použít `kubectl describe pod mypod` k ověření sdílené složky je úspěšně připojen. Následující kondenzovaný příklad výstupu ukazuje objem namontovaný v kontejneru:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-options"></a>Možnosti připojení

Výchozí hodnota pro *fileMode* a *dirMode* je *0755* pro Kubernetes verze 1.9.1 a vyšší. Pokud používáte cluster s Kuberetes verze 1.8.5 nebo vyšší a staticky vytváří trvalý objekt svazku, je třeba zadat možnosti připojení na *objektpersistentVolume.* Následující příklad nastaví *0777*:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Pokud používáte cluster verze 1.8.0 - 1.8.4, lze zadat kontext zabezpečení s hodnotou *runAsUser* nastavenou na *hodnotu 0*. Další informace o kontextu zabezpečení podu naleznete [v tématu Konfigurace kontextu zabezpečení][kubernetes-security-context].

Chcete-li aktualizovat možnosti připojení, vytvořte soubor *azurefile-mount-options-pv.yaml* s *persistentvolume*. Například:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Vytvořte soubor *azurefile-mount-options-pvc.yaml* s *persistentVolumeclaim,* který používá *PersistentVolume*. Například:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Pomocí `kubectl` příkazů vytvořte *příkazy PersistentVolume* a *PersistentVolumeClaim*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Ověřte, zda je *deklarace persistentVolumeClaim* vytvořena a vázána na *trvalý svazek*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Aktualizujte specifikace kontejneru tak, aby odkazoval na *váš PersistentVolumeClaim* a aktualizoval pod. Například:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Další kroky

Doporučené postupy najdete v [tématu Doporučené postupy pro ukládání a zálohování v AKS][operator-best-practices-storage].

Další informace o clusterech AKS, které interagují se soubory Azure, najdete v [tématu Plugin Kubernetes pro Soubory Azure][kubernetes-files].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
