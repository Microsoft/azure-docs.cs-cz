---
title: Škálování clusteru Azure Kubernetes Service (AKS)
description: Naučte se škálovat počet uzlů v clusteru Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368413"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Škálování počtu uzlů v clusteru Azure Kubernetes Service (AKS)

Pokud se potřeby prostředků vašich aplikací změní, můžete cluster AKS ručně škálovat tak, aby běžel jiný počet uzlů. Při horizontálním navýšení kapacity jsou uzly pečlivě [uzavřené a vyprázdněné][kubernetes-drain] , aby se minimalizovalo přerušení spuštěných aplikací. Při horizontálním navýšení kapacity AKS počká, dokud nebudou uzly označeny `Ready` clusterem Kubernetes předtím, než je v nich naplánováno lusky.

## <a name="scale-the-cluster-nodes"></a>Škálování uzlů clusteru

Nejprve získejte *název* fondu uzlů pomocí příkazu [AZ AKS show][az-aks-show] . Následující příklad získá název fondu uzlů pro cluster s názvem *myAKSCluster* ve skupině prostředků *myResourceGroup* :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Následující příklad výstupu ukazuje, že *název* je *nodepool1*:

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Pomocí příkazu [AZ AKS Scale][az-aks-scale] můžete škálovat uzly clusteru. Následující příklad škáluje cluster s názvem *myAKSCluster* na jeden uzel. Zadejte vlastní *--nodepool-Name* z předchozího příkazu, jako je například *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Následující příklad výstupu ukazuje, že se cluster úspěšně škálovat na jeden uzel, jak je znázorněno v části *agentPoolProfiles* :

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste ručně nazměnili velikost clusteru AKS na zvýšení nebo snížení počtu uzlů. K automatickému škálování clusteru můžete použít taky automatický [škálování clusteru][cluster-autoscaler] .

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
