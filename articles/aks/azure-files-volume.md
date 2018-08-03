---
title: Použijte Azure soubor s AKS
description: Použití disků v Azure s AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b35d0e33009f76e0b2d6f90c52c98ce5f317792d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436756"
---
# <a name="volumes-with-azure-files"></a>Svazky se soubory Azure

Kontejnerových aplikací často potřebují přístup k a zachovat data ve svazku externí data. Služba soubory Azure může sloužit jako úložiště tuto externí data. Tento článek podrobně popisuje použití Azure files jako svazek Kubernetes ve službě Azure Kubernetes Service.

Další informace o Kubernetes svazky, naleznete v tématu [Kubernetes svazky][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Před použitím sdílení souborů Azure jako svazek Kubernetes, musíte vytvořit účet služby Azure Storage a sdílené složky. Následující skript můžete použít k provedení těchto úloh. Všimněte si nebo aktualizujte hodnoty parametrů, některé z nich jsou potřeba při vytváření svazku Kubernetes.

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
echo Storage account key: $STORAGE_KEY
```

## <a name="create-kubernetes-secret"></a>Vytvoření tajného kódu Kubernetes

Kubernetes potřebuje přihlašovací údaje pro přístup ke sdílené složce. Tyto přihlašovací údaje jsou uložené v [tajného kódu Kubernetes][kubernetes-secret], který se odkazuje při vytváření podu Kubernetes.

Následujícím příkazem vytvořte tajný kód. Nahraďte `STORAGE_ACCOUNT_NAME` s názvem vašeho účtu úložiště a `STORAGE_ACCOUNT_KEY` se klíče účtu úložiště.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>Připojení sdílené složky jako svazek

Připojení vaší sdílenou složku služby soubory Azure do podu pomocí konfigurace svazku v jeho specifikace. Vytvořte nový soubor s názvem `azure-files-pod.yaml` s následujícím obsahem. Aktualizace `aksshare` název použitý pro soubory Azure sdílet.

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

Můžete vytvořit pod kubectl.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

Teď máte spuštěný kontejner se sdílenou složkou Azure připojené `/mnt/azure` adresáře.  Vidíte připojení při kontrole podu prostřednictvím svazku `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Další postup

Další informace o svazcích Kubernetes pomocí služby soubory Azure.

> [!div class="nextstepaction"]
> [Modul plug-in Kubernetes pro Azure Files][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
