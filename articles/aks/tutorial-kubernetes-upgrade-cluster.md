---
title: Kurz Kubernetes v Azure – Aktualizace clusteru
description: Kurz Kubernetes v Azure – Aktualizace clusteru
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d66197b69a0804a49fabb72e9b97c77e000bdf88
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131639"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Kurz: Upgrade Kubernetes ve službě Azure Kubernetes Service (AKS)

Cluster Azure Kubernetes Service (AKS) je možné upgradovat pomocí Azure CLI. Aby se minimalizovalo přerušení spuštěných aplikací, jsou uzly Kubernetes během procesu upgradu pečlivě [uzavřené a vyprázdněné][kubernetes-drain].

V tomto kurzu, který je sedmou částí sedmidílné série, se upgraduje cluster Kubernetes. Úlohy, které provedete:

> [!div class="checklist"]
> * Identifikace aktuální verze a dostupných verzí Kubernetes
> * Upgrade uzlů Kubernetes
> * Ověření úspěšného upgradu

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do image kontejneru, tato image se odeslala do Azure Container Registry a vytvořil se cluster Kubernetes. Aplikace se potom spustila v tomto clusteru Kubernetes.

Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru][aks-tutorial-prepare-app].

## <a name="get-cluster-versions"></a>Získání verzí clusteru

Před upgradem clusteru pomocí příkazu [az aks get-upgrades][] zkontrolujte, které vydané verze Kubernetes jsou pro upgrade k dispozici.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

V následujícím příkladu je aktuální verze uzlu *1.9.6* a dostupné verze se zobrazují ve sloupci *Upgrades*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------
default  myResourceGroup  1.9.6            1.9.6              1.10.3
```

## <a name="upgrade-cluster"></a>Upgrade clusteru

Uzly clusteru můžete upgradovat pomocí příkazu [az aks upgrade][]. Následující příklad aktualizuje cluster na verzi *1.10.3*.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.10.3
```

Následující zkrácený příklad výstupu ukazuje *kubernetesVersion* nyní s hodnotou *1.10.3*:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.3",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Ověření upgradu

Ověřte úspěšné provedení upgradu pomocí příkazu [az aks show][].

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Výstup:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.3               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste upgradovali Kubernetes v clusteru AKS. Dokončili jste následující úlohy:

> [!div class="checklist"]
> * Identifikace aktuální verze a dostupných verzí Kubernetes
> * Upgrade uzlů Kubernetes
> * Ověření úspěšného upgradu

Další informace o službě AKS najdete na následujícím odkazu.

> [!div class="nextstepaction"]
> [Přehled služby AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade