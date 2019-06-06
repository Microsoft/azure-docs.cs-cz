---
title: Škálování clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak škálovat počet uzlů v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfoulds
ms.openlocfilehash: de3f8613c93715aecf7e9e066a8ad1d82e4379e3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475133"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Škálovat počet uzlů v clusteru služby Azure Kubernetes Service (AKS)

Pokud prostředek požadavkům vašich aplikací změnit, můžete ručně škálovat cluster AKS ke spuštění jiný počet uzlů. Když vertikálně snížit kapacitu, jsou uzly pečlivě [uzavřené a Vyprázdněné] [ kubernetes-drain] aby se minimalizovalo přerušení spuštěných aplikací. Když vertikálně navýšit kapacitu, AKS čeká na označení uzlů `Ready` clusterem Kubernetes před podů jsou naplánovány na ně.

## <a name="scale-the-cluster-nodes"></a>Škálování uzlů clusteru

Nejprve získejte *název* použití fondu uzlů [az aks zobrazit] [ az-aks-show] příkazu. Následující příklad získá název uzlu fondu pro cluster s názvem *myAKSCluster* v *myResourceGroup* skupina prostředků:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Následující příklad výstupu ukazuje, že *název* je *nodepool1*:

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

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

Použití [az aks škálování] [ az-aks-scale] příkazu škálování uzlů clusteru. Následující příklad škáluje cluster *myAKSCluster* do jednoho uzlu. Zadejte vlastní *nodepool – název* z předchozího příkazu, jako například *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Následující příklad výstupu ukazuje clusteru má byla úspěšně škálována na jeden uzel, jak je znázorněno *agentPoolProfiles* části:

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

## <a name="next-steps"></a>Další postup

V tomto článku můžete ručně škálovat cluster AKS chcete zvýšit nebo snížit počet uzlů. Můžete také použít [clusteru bylo] [ cluster-autoscaler] (aktuálně ve verzi preview ve službě AKS) pro automatické škálování clusteru.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
