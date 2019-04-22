---
title: Vytvoření statické svazku pro několik podů se ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak ručně vytvořit svazek se soubory Azure pro použití s více souběžných podů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 65e94a271fc8fc72ac74d51af3cf7b717f8410b0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785240"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Ručně vytvořit a používat svazku se sdílenou složku služby soubory Azure ve službě Azure Kubernetes Service (AKS)

Kontejnerových aplikací často potřebují přístup k a zachovat data ve svazku externí data. Pokud potřebujete více podů souběžný přístup na stejný svazek úložiště, můžete používat soubory Azure a připojte se pomocí [zprávy bloku SMB (Server) protokol][smb-overview]. Tento článek ukazuje, jak ručně vytvořit sdílenou složku služby soubory Azure a připojit ho k pod ve službě AKS.

Další informace o Kubernetes svazky, naleznete v tématu [možnosti úložiště pro aplikace ve službě AKS][concepts-storage].

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Také nutné mít Azure CLI verze 2.0.59 nebo později nainstalované a nakonfigurované. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Soubory Azure můžete používat jako svazek Kubernetes, musíte vytvořit účet služby Azure Storage a sdílené složky. Následující příkazy vytvoří skupinu prostředků s názvem *myAKSShare*, účet úložiště a sdílené soubory s názvem *aksshare*:

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
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Poznamenejte si název účtu úložiště a klíč uvedené na konci výstupu skriptu. Tyto hodnoty jsou potřeba při vytváření svazku Kubernetes v jednom z následujících kroků.

## <a name="create-a-kubernetes-secret"></a>Vytvoření tajného kódu Kubernetes

Kubernetes potřebuje přihlašovací údaje pro přístup ke sdílené složce vytvořili v předchozím kroku. Tyto přihlašovací údaje jsou uložené v [tajného kódu Kubernetes][kubernetes-secret], který se odkazuje při vytváření podu Kubernetes.

Použití `kubectl create secret` příkaz pro vytvoření tajného klíče. Následující příklad vytvoří sdílenou s názvem *azure-secret* a naplní *azurestorageaccountname* a *azurestorageaccountkey* z předchozího kroku. Pokud chcete použít existující účet úložiště Azure, zadejte název účtu a klíč.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Připojení sdílené složky jako svazek

Pro připojení sdílené složky Azure Files v podu, nakonfigurujte u svazku v kontejneru specifikace. Vytvořte nový soubor s názvem `azure-files-pod.yaml` s následujícím obsahem. Pokud jste změnili název sdílené složky souborů nebo název tajného kódu, aktualizujte *shareName* a *secretName*. V případě potřeby aktualizovat `mountPath`, což je cesta kde sdílí soubory jsou připojeny pod.

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

Použití `kubectl` příkaz pro vytvoření pod.

```console
kubectl apply -f azure-files-pod.yaml
```

Teď máte spuštěné pod s do sdílené složky Azure Files připojil */mnt/azure*. Můžete použít `kubectl describe pod mypod` ověření je úspěšně připojit sdílenou složku. Následující výstup zhuštěnému příkladu ukazuje svazek připojený v kontejneru:

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

Výchozí *režimu souboru fileMode* a *dirMode* hodnoty se liší mezi verzemi Kubernetes, jak je popsáno v následující tabulce.

| version | hodnota |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 nebo novější | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 nebo novější | 0755 |

Pokud používáte cluster verze 1.8.5 nebo vyšší a staticky vytváření objektu trvalý svazek, možnosti připojení musí být zadána na *PersistentVolume* objektu.

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
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
```

Pokud používáte cluster verze 1.8.0 - 1.8.4, kontext zabezpečení je možné zadat při *Spustit_jako_uživatel* nastavena na hodnotu *0*. Další informace o kontextu zabezpečení Pod najdete v tématu [konfigurace kontext zabezpečení][kubernetes-security-context].

## <a name="next-steps"></a>Další postup

Přidružené osvědčené postupy, najdete v části [osvědčené postupy pro ukládání a zálohování ve službě AKS][operator-best-practices-storage].

Další informace o clusterech s AKS pracovat se soubory Azure, najdete v článku [modul plug-in Kubernetes pro Azure Files][kubernetes-files].

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
