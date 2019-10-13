---
title: Integrace Azure Container Registry se službou Azure Kubernetes
description: Naučte se integrovat službu Azure Kubernetes Service (AKS) s využitím Azure Container Registry (ACR).
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 09/17/2018
ms.author: mlearned
ms.openlocfilehash: bbd08e49256886a1df334cbf36e6e468bb8f3895
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286784"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Ověření pomocí Azure Container Registry služby Azure Kubernetes

Pokud používáte Azure Container Registry (ACR) se službou Azure Kubernetes Service (AKS), je nutné vytvořit ověřovací mechanismus. Tento článek popisuje příklady konfigurace ověřování mezi těmito dvěma službami Azure.

AKS můžete nastavit na integraci ACR v několika jednoduchých příkazech pomocí Azure CLI.

## <a name="before-you-begin"></a>Než začnete

Tyto příklady vyžadují:

* Role **vlastníka** nebo **správce účtu Azure** v **předplatném Azure**
* Azure CLI verze 2.0.73 nebo novější

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Vytvoření nového clusteru AKS s integrací ACR

Během počátečního vytváření clusteru AKS můžete nastavit integraci AKS a ACR.  Pokud chcete, aby cluster AKS spolupracoval s ACR, použije se Azure Active Directory **instanční objekt** . Následující příkaz rozhraní příkazového řádku umožňuje autorizovat stávající ACR ve vašem předplatném a nakonfiguruje příslušnou roli **ACRPull** pro instanční objekt. Zadejte platné hodnoty pro následující parametry.  Parametry v hranatých závorkách jsou volitelné.
```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --attach-acr $MYACR

```
Případně můžete zadat název ACR pomocí ID prostředku ACR, který má následující formát:

/Subscriptions/\<subscription-ID @ no__t-1/resourceGroups/\<resource-Group-Name @ no__t-3/Providers/Microsoft. ContainerRegistry/Registry/\<name @ no__t-5 
 
```azurecli
az aks create -n myAKSCluster -g myResourceGroup --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Dokončení tohoto kroku může trvat několik minut.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurace integrace ACR pro existující clustery AKS

Integrujte stávající ACR s existujícími clustery AKS zadáním platných hodnot pro **ACR-Name** nebo **ACR-Resource-ID** , jak je uvedeno níže.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```
Ani
```
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Integraci mezi ACR a clusterem AKS taky můžete odebrat pomocí následujících kroků:
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```
nebo
```
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Práce s ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Import obrázku do ACR

Naimportujte image z Docker Hub do svého ACR spuštěním následujícího postupu:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Nasazení ukázkové image z ACR do AKS

Ujistěte se, že máte správné přihlašovací údaje AKS.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Vytvořte soubor s názvem **ACR-Nginx. yaml** , který obsahuje následující:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80
```

V dalším kroku spusťte toto nasazení v clusteru AKS:
```
kubectl apply -f acr-nginx.yaml
```

Nasazení můžete monitorovat spuštěním:
```
kubectl get pods
```
Měli byste mít dvě běžící lusky.
```
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
