---
title: Použít Azure soubor s AKS
description: Disky systému Azure pomocí AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 25ae0e508223b50219e40245cc9dfbc407b96bba
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096692"
---
# <a name="volumes-with-azure-files"></a>Svazky s soubory Azure

Aplikace založené na kontejneru často potřebují přístup a uchovávat data v svazku externí data. Soubory Azure můžete použít jako tohle úložiště dat externí. Tento článek údaje pomocí Azure souborů jako svazek Kubernetes ve službě Azure Kubernetes Service.

Další informace o svazcích Kubernetes najdete v tématu [Kubernetes svazky][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Před použitím sdílení souborů Azure jako svazek Kubernetes, je nutné vytvořit účet úložiště Azure a sdílené složky. K dokončení těchto úloh můžete použít následující skript. Všimněte si nebo aktualizujte hodnoty parametru, některé z nich je třeba při vytváření svazku Kubernetes.

```azurecli-interactive
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
echo Storgae account key: $STORAGE_KEY
```

## <a name="create-kubernetes-secret"></a>Vytvoření tajného klíče Kubernetes

Kubernetes potřebuje přihlašovací údaje pro přístup ke sdílené složce. Tyto přihlašovací údaje jsou uložené v [tajný klíč Kubernetes][kubernetes-secret], který se odkazuje při vytváření Kubernetes pod.

Použijte následující příkaz k vytvoření tajný klíč. Nahraďte `STORAGE_ACCOUNT_NAME` s názvem svého účtu úložiště a `STORAGE_ACCOUNT_KEY` s klíče účtu úložiště.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>Připojení sdílené složky jako svazek

Připojte do sdílené složky Azure soubory do vaší pod nakonfigurováním svazek v jeho specifikace. Vytvořte nový soubor s názvem `azure-files-pod.yaml` s tímto obsahem. Aktualizace `aksshare` s názvem uvedeným k Azure Files sdílet.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
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

K vytvoření pod použijte kubectl.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

Nyní máte kontejner spuštěné s Azure sdílené složky připojené `/mnt/azure` adresáře.  Zobrazí připojení při kontrole vaší pod prostřednictvím svazku `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Další postup

Další informace o Kubernetes svazky s využitím Azure Files.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro Azure Files][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
