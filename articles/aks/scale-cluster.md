---
title: Škálování clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak škálovat počet uzlů v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/10/2019
ms.author: iainfoulds
ms.openlocfilehash: 558a3b6dc15293ab9a0895aa4f9f709ba2d0a51f
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214619"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Škálovat počet uzlů v clusteru služby Azure Kubernetes Service (AKS)

Pokud prostředek požadavkům vašich aplikací změnit, můžete ručně škálovat cluster AKS ke spuštění jiný počet uzlů. Když vertikálně snížit kapacitu, jsou uzly pečlivě [uzavřené a Vyprázdněné] [ kubernetes-drain] aby se minimalizovalo přerušení spuštěných aplikací. Když vertikálně navýšit kapacitu, `az` příkaz čeká na označení uzlů `Ready` clusterem Kubernetes.

## <a name="scale-the-cluster-nodes"></a>Škálování uzlů clusteru

Nejprve získejte *název* nodepool pomocí [az aks zobrazit] [ az-aks-show] příkazu. Následující příklad získá nodepool název pro cluster s názvem *myAKSCluster* v *myResourceGroup* skupina prostředků:

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

Uzly clusteru můžete škálovat pomocí příkazu `az aks scale`. Následující příklad škáluje cluster *myAKSCluster* do jednoho uzlu. Zadejte vlastní *nodepool – název* z předchozího příkazu, jako například *nodepool1*:

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
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": true,
  "fqdn": "myaksclust-myresourcegroup-19da35-0d60b16a.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.9.11",
  "linuxProfile": {
    "adminUsername": "azureuser",
    "ssh": {
      "publicKeys": [
        {
          "keyData": "[...]"
        }
      ]
    }
  },
  "location": "eastus",
  "name": "myAKSCluster",
  "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "networkPlugin": "kubenet",
    "networkPolicy": null,
    "podCidr": "10.244.0.0/16",
    "serviceCidr": "10.0.0.0/16"
  },
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_eastus",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "servicePrincipalProfile": {
    "clientId": "[...]",
    "secret": null
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="next-steps"></a>Další postup

Další informace o nasazení a správě AKS najdete v kurzech AKS.

> [!div class="nextstepaction"]
> [Kurz AKS][aks-tutorial]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
