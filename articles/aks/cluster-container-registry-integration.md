---
title: Integrace Azure Container Registry se službou Azure Kubernetes
description: Naučte se integrovat službu Azure Kubernetes Service (AKS) s využitím Azure Container Registry (ACR).
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: mlearned
ms.openlocfilehash: 3c11367945b74db9be20ade86c7bc26901440e4d
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305151"
---
# <a name="preview---authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Preview – ověření pomocí Azure Container Registry služby Azure Kubernetes

Pokud používáte Azure Container Registry (ACR) se službou Azure Kubernetes Service (AKS), je nutné vytvořit ověřovací mechanismus. Tento článek podrobně popisuje doporučené konfigurace pro ověřování mezi těmito dvěma službami Azure.

AKS můžete nastavit na integraci ACR v několika jednoduchých příkazech pomocí Azure CLI.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace o tom, jak se zaregistrují, najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS](support-policies.md)
> * [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="before-you-begin"></a>Před zahájením

Musíte mít následující:

* Role **vlastníka** nebo **správce účtu Azure** v **předplatném Azure**
* Budete také potřebovat Azure CLI verze 2.0.70 nebo novější a rozšíření 0.4.8 AKS-Preview.
* Potřebujete do svého klienta [nainstalovaného Docker](https://docs.docker.com/install/) a potřebujete přístup k [dokovacímu centru](https://hub.docker.com/) .

## <a name="install-latest-aks-cli-preview-extension"></a>Nainstalovat nejnovější rozšíření AKS CLI Preview

Potřebujete rozšíření **AKS-Preview 0.4.13** nebo novější.

```azurecli
az extension remove --name aks-preview 
az extension add -y --name aks-preview
```

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Vytvoření nového clusteru AKS s integrací ACR

Během počátečního vytváření clusteru AKS můžete nastavit integraci AKS a ACR.  Pokud chcete, aby cluster AKS spolupracoval s ACR, použije se Azure Active Directory **instanční objekt** . Následující příkaz rozhraní příkazového řádku umožňuje autorizovat stávající ACR ve vašem předplatném a nakonfiguruje příslušnou roli **ACRPull** pro instanční objekt. Zadejte platné hodnoty pro následující parametry.  Parametry v hranatých závorkách jsou volitelné.
```azurecli
az login
az acr create -n myContainerRegistry -g myContainerRegistryResourceGroup --sku basic [in case you do not have an existing ACR]
az aks create -n myAKSCluster -g myResourceGroup --attach-acr <acr-name-or-resource-id>
```
\* * ID prostředku ACR má následující formát: 

/Subscriptions/< předplatné-d >/resourceGroups/< Resource-Group-Name >/providers/Microsoft.ContainerRegistry/registries/{name} 
  
Dokončení tohoto kroku může trvat několik minut.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurace integrace ACR pro existující clustery AKS

Integrujte stávající ACR s existujícími clustery AKS zadáním platných hodnot pro **ACR-Name** nebo **ACR-Resource-ID** , jak je uvedeno níže.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Integraci mezi ACR a clusterem AKS taky můžete odebrat pomocí následujících kroků:
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```


## <a name="log-in-to-your-acr"></a>Přihlaste se k ACR

K přihlášení do ACR použijte následující příkaz.  Nahraďte <acrname> parametr názvem ACR.  Výchozí hodnota je například **aks < > ACR**.

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>Stažení obrázku z Docker Hub a vložení do ACR

Nahrajte image z Docker Hub, označte image a vložte ji do svého ACRu.

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>Aktualizovat stav a ověřit lusky

Provedením následujících kroků ověříte nasazení.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Zobrazte soubor YAML a upravte vlastnost image tak, že nahradíte hodnotu pomocí přihlašovacího serveru ACR, obrázku a značky.

```
$ cat acr-nginx.yaml

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

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
