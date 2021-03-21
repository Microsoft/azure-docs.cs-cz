---
title: Škálování clusteru Azure Kubernetes Service (AKS)
description: Naučte se škálovat počet uzlů v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: fdb61bf090351894329c24eb1a3c73d627e622e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173762"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Škálování počtu uzlů ve clusteru Azure Kubernetes Service (AKS)

Pokud se potřeby prostředků vašich aplikací změní, můžete cluster AKS ručně škálovat tak, aby běžel jiný počet uzlů. Při horizontálním navýšení kapacity jsou uzly pečlivě [uzavřené a vyprázdněné][kubernetes-drain] , aby se minimalizovalo přerušení spuštěných aplikací. Při horizontálním navýšení kapacity AKS počká, dokud nebudou uzly označeny `Ready` clusterem Kubernetes, než je v nich naplánováno lusky.

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

Pomocí příkazu [AZ AKS Scale][az-aks-scale] můžete škálovat uzly clusteru. Následující příklad škáluje cluster s názvem *myAKSCluster* na jeden uzel. Zadejte vlastní `--nodepool-name` z předchozího příkazu, jako je například *nodepool1*:

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


## <a name="scale-user-node-pools-to-0"></a>Škálovat `User` fondy uzlů na 0

Na rozdíl od `System` fondů uzlů, které vždy vyžadují spuštěné uzly, `User` fondy uzlů umožňují škálování na 0. Další informace o rozdílech mezi fondy systémových a uživatelských uzlů najdete v tématu [fondy systémových a uživatelských](use-system-pools.md)uzlů.

Pokud chcete škálovat fond uživatelů na 0, můžete použít možnost [AZ AKS nodepool Scale][az-aks-nodepool-scale] v alternativním příkazu k výše uvedenému `az aks scale` příkazu a jako počet uzlů nastavit 0.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

Je také možné automatické škálování `User` fondů uzlů na 0 uzlů nastavením `--min-count` parametru [automatického škálování clusteru](cluster-autoscaler.md) na hodnotu 0.

## <a name="next-steps"></a>Další kroky

V tomto článku jste ručně nazměnili velikost clusteru AKS na zvýšení nebo snížení počtu uzlů. K automatickému škálování clusteru můžete použít taky automatický [škálování clusteru][cluster-autoscaler] .

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale