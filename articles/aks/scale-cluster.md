---
title: Škálování clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak škálovat počet uzlů v clusteru Služby Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368413"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Škálování počtu uzlů ve clusteru Azure Kubernetes Service (AKS)

Pokud se změní potřeby prostředků vašich aplikací, můžete ručně škálovat cluster AKS a spustit jiný počet uzlů. Při škálování dolů uzly jsou pečlivě [uzavřeny a vyprázdněny,][kubernetes-drain] aby se minimalizovalo narušení spuštěných aplikací. Při škálování navertí, AKS čeká, dokud uzly jsou označeny `Ready` clusteru Kubernetes před pods jsou naplánovány na ně.

## <a name="scale-the-cluster-nodes"></a>Škálování uzlů clusteru

Nejprve získejte *název* fondu uzlů pomocí příkazu [az aks show.][az-aks-show] Následující příklad získá název fondu uzlů pro cluster s názvem *myAKSCluster* ve skupině prostředků *myResourceGroup:*

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

Pomocí příkazu [měřítko az aks][az-aks-scale] můžete škálovat uzly clusteru. Následující příklad škáluje cluster s názvem *myAKSCluster* na jeden uzel. Zadejte vlastní *název --nodepool* z předchozího příkazu, například *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Následující ukázkový výstup ukazuje, že cluster byl úspěšně škálován na jeden uzel, jak je znázorněno v části *agentPoolProfiles:*

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

V tomto článku ručně škálovat clustera AKS zvýšit nebo snížit počet uzlů. Pomocí [automatického škálování clusteru][cluster-autoscaler] můžete také automaticky škálovat cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
