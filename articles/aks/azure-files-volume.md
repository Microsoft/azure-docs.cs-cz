---
title: Ručně vytvořit sdílenou složku služby soubory Azure
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak ručně vytvořit svazek se soubory Azure pro použití s několika souběžnými lusky ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: a6e28464df2ff9c9dcc7734a127cc00f887e08dd
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246957"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Ruční vytvoření a použití svazku se sdílenou složkou Azure ve službě Azure Kubernetes Service (AKS)

Aplikace založené na kontejnerech často potřebují přístup k datům v externím datovém svazku a jejich uchovávání. Pokud více lusků potřebuje souběžný přístup ke stejnému svazku úložiště, můžete použít soubory Azure pro připojení pomocí [protokolu SMB (Server Message Block)][smb-overview]. V tomto článku se dozvíte, jak ručně vytvořit sdílenou složku služby soubory Azure a připojit ji k objektu pod v AKS.

Další informace o Kubernetes svazcích najdete v tématu [Možnosti úložiště pro aplikace v AKS][concepts-storage].

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Než budete moct používat soubory Azure jako Kubernetes svazek, musíte vytvořit účet Azure Storage a sdílenou složku. Následující příkazy vytvoří skupinu prostředků s názvem *myAKSShare*, účet úložiště a sdílenou složku s názvem *aksshare*:

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

Poznamenejte si název a klíč účtu úložiště zobrazené na konci výstupu skriptu. Tyto hodnoty jsou potřeba při vytváření svazku Kubernetes v jednom z následujících kroků.

## <a name="create-a-kubernetes-secret"></a>Vytvoření tajného klíče Kubernetes

Kubernetes potřebuje přihlašovací údaje pro přístup ke sdílené složce vytvořené v předchozím kroku. Tyto přihlašovací údaje jsou uložené v [tajném klíči Kubernetes][kubernetes-secret], na který se odkazuje při vytváření Kubernetes pod.

Pomocí `kubectl create secret` příkazu vytvořte tajný klíč. Následující příklad vytvoří sdílenou službu s názvem *Azure-Secret* a naplní *azurestorageaccountname* a *azurestorageaccountkey* z předchozího kroku. Pokud chcete použít existující účet úložiště Azure, zadejte název účtu a klíč.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Připojit sdílenou složku jako svazek

Pokud chcete sdílenou složku služby soubory Azure připojit k vašemu zařízení pod, nakonfigurujte svazek ve specifikaci kontejneru. Vytvořte nový soubor s názvem `azure-files-pod.yaml` s následujícím obsahem. Pokud jste změnili název sdílené složky nebo tajného názvu, aktualizujte název souboru *název_sdílené_položky* a název *tajného* klíče. V případě potřeby aktualizujte `mountPath` cestu, která je cesta, kde je sdílená složka souborů připojená v poli pod. V případě kontejnerů Windows serveru určete *mountPath* pomocí konvence cesty Windows, třeba *:*.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

Teď máte spuštěný pod s sdílenou složkou Azure, která je připojená na */mnt/Azure*. `kubectl describe pod mypod`K ověření, zda je sdílená složka úspěšně připojena, můžete použít. Následující zhuštěný příklad výstupu ukazuje svazek připojený do kontejneru:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

Výchozí hodnota pro *FileMode* a *dirMode* je *0755* pro Kubernetes verze 1.9.1 a vyšší. Pokud používáte cluster s Kubernetes verze 1.8.5 nebo vyšší a staticky vytváříte objekt trvalého svazku, musí být v objektu *PersistentVolume* zadány možnosti připojení. Následující příklad nastaví *0777*:

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

Pokud používáte cluster verze 1.8.0-1.8.4, je možné určit kontext zabezpečení s hodnotou *runAsUser* nastavenou na *0*. Další informace o kontextu zabezpečení najdete v tématu [Konfigurace kontextu zabezpečení][kubernetes-security-context].

Pokud chcete aktualizovat možnosti připojení, vytvořte soubor *azurefile-Mount-Options-PV. yaml* s *PersistentVolume*. Příklad:

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

Vytvořte soubor *azurefile-Mount-Options-PVC. yaml* s *PersistentVolumeClaim* , který používá *PersistentVolume*. Příklad:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

Použijte `kubectl` příkazy k vytvoření *PersistentVolume* a *PersistentVolumeClaim*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Ověřte, že je váš *PersistentVolumeClaim* vytvořený a vázaný na *PersistentVolume*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Aktualizujte své specifikace kontejneru, aby odkazovaly na *PersistentVolumeClaim* a aktualizovali pod. Příklad:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Další kroky

Související osvědčené postupy najdete [v tématu osvědčené postupy pro úložiště a zálohování v AKS][operator-best-practices-storage].

Další informace o spolupráci AKS clusterů se soubory Azure najdete v tématu [modul plug-in Kubernetes pro soubory Azure][kubernetes-files].

Parametry třídy úložiště najdete v tématu [statické zřizování (Přineste si vlastní sdílenou složku)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share).

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
