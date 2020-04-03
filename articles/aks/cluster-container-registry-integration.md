---
title: Integrace registru kontejnerů Azure se službou Azure Kubernetes
description: Zjistěte, jak integrovat službu Azure Kubernetes Service (AKS) s Azure Container Registry (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 514cc25e1959145c65fe60cd3054cec4ed28f44d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617428"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Ověření pomocí Azure Container Registry ze služby Azure Kubernetes Service

Když používáte Azure Container Registry (ACR) se službou Azure Kubernetes Service (AKS), je třeba vytvořit mechanismus ověřování. Tento článek obsahuje příklady konfigurace ověřování mezi těmito dvěma službami Azure. 

Integraci AKS do ACR můžete nastavit v několika jednoduchých příkazech pomocí azure CLI. Tato integrace přiřadí roli AcrPull instančnímu objektu přidruženému k clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

Tyto příklady vyžadují:

* Role **vlastníka** nebo **správce účtu Azure** v **předplatném Azure**
* Azure CLI verze 2.0.73 nebo novější

Chcete-li se vyhnout nutnosti role **vlastníka** nebo **správce účtu Azure,** můžete ručně nakonfigurovat instanční objekt nebo použít existující instanční objekt k ověření ACR z AKS. Další informace naleznete v [tématu Ověřování ACR s objekty zabezpečení služby](../container-registry/container-registry-auth-service-principal.md) nebo [Ověření z Kubernetes s tajným klíčem pro vyžádat](../container-registry/container-registry-auth-kubernetes.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Vytvoření nového clusteru AKS s integrací ACR

Integrací AKS a ACR můžete nastavit při počátečním vytvoření clusteru AKS.  Chcete-li povolit clusteru AKS pro interakci s ACR, používá se **zaregistrovaný objekt služby** Azure Active Directory. Následující příkaz rozhraní příkazového příkazu umožňuje autorizovat existující ACR ve vašem předplatném a nakonfiguruje příslušnou roli **ACRPull** pro instanční objekt. Zadejte platné hodnoty pro vaše parametry níže.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
$MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Případně můžete zadat název ACR pomocí ID prostředku ACR, které má následující formát:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Tento krok může trvat několik minut.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurace integrace ACR pro existující clustery AKS

Integrujte existující ACR s existujícími clustery AKS zadáním platných hodnot pro **název acr** nebo **acr-resource-id,** jak je uvedeno níže.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```

Nebo

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Můžete také odebrat integraci mezi acr a clusterem AKS s následujícími

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

– nebo –

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Práce s ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Import obrazu do acr

Importujte bitovou kopii z centra dockeru do acr spuštěním následujícího:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Nasazení ukázkové bitové kopie z ACR do AKS

Ujistěte se, že máte správné přihlašovací údaje AKS

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Vytvořte soubor s názvem **acr-nginx.yaml,** který obsahuje následující:

```yaml
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

Dále spusťte toto nasazení v clusteru AKS:

```console
kubectl apply -f acr-nginx.yaml
```

Nasazení můžete sledovat spuštěním:

```console
kubectl get pods
```

Měli byste mít dva běžící moduly.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
