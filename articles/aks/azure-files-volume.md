---
title: Vytvoření statické svazku pro několik podů se ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak ručně vytvořit svazek se soubory Azure pro použití s více souběžných podů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 1a8609dbf5fa1c1e7d5f4e35b081ecaa09994eb6
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068073"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Ručně vytvořit a používat svazku se sdílenou složku služby soubory Azure ve službě Azure Kubernetes Service (AKS)

Kontejnerových aplikací často potřebují přístup k a zachovat data ve svazku externí data. Pokud potřebujete více podů souběžný přístup na stejný svazek úložiště, můžete používat soubory Azure a připojte se pomocí [zprávy bloku SMB (Server) protokol][smb-overview]. Tento článek ukazuje, jak ručně vytvořit sdílenou složku služby soubory Azure a připojit ho k pod ve službě AKS.

Další informace o Kubernetes svazky, naleznete v tématu [Kubernetes svazky][kubernetes-volumes].

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Také nutné mít Azure CLI verze 2.0.46 nebo později nainstalované a nakonfigurované. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Soubory Azure můžete používat jako svazek Kubernetes, musíte vytvořit účet služby Azure Storage a sdílené složky. Tento skript vytvoří skupinu prostředků s názvem *myAKSShare*, účet úložiště a sdílené soubory s názvem *aksshare*:

```azurecli
#!/bin/bash

# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

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

```azurecli-interactive
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
      Started:      Mon, 08 Oct 2018 19:28:34 +0000
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

## <a name="next-steps"></a>Další postup

Další informace o clusterech s AKS pracovat se soubory Azure, najdete v článku [modul plug-in Kubernetes pro Azure Files][kubernetes-files].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
