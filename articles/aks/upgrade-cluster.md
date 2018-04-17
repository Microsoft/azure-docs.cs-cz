---
title: Upgrade clusteru Azure Container Service (AKS)
description: Upgrade clusteru Azure Container Service (AKS)
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/05/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 5f5cfc49bd7b5e1c06adf97bc6f6ba88fc81f77a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Upgrade clusteru Azure Container Service (AKS)

Služba Azure Container Service (AKS) usnadňuje provádění běžných úloh správy, včetně upgradu clusterů Kubernetes.

## <a name="upgrade-an-aks-cluster"></a>Upgrade clusteru AKS

Před upgradem clusteru pomocí příkazu `az aks get-upgrades` zkontrolujte, které vydané verze Kubernetes jsou pro upgrade k dispozici.

```azurecli-interactive
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Výstup:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  -------------------
default  mytestaks007     1.8.10           1.8.10             1.9.1, 1.9.2, 1.9.6
```

Máme tři verze, které jsou k dispozici pro upgrade: 1.9.1, 1.9.2 a otázku 1.9.6. Pomocí příkazu `az aks upgrade` můžeme upgradovat na nejnovější dostupnou verzi.  Během procesu upgradu, jsou uzly pečlivě [cordoned a k nečekaně] [ kubernetes-drain] minimalizovat přerušení spuštěných aplikací.  Před zahájením upgradu clusteru se ujistěte, že máte dostatek další výpočetní kapacity pro zvládnutí úloh během přidávání a odebírání uzlů clusteru.

> [!NOTE]
> Při aktualizaci clusteru AKS, nelze přeskočit Kubernetes podverze. Například upgraduje mezi 1.7.x > 1.8.x nebo 1.8.x > 1.9.x jsou povoleny, ale 1.7 > 1.9 není.

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.9.6
```

Výstup:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.9.6",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

Potvrďte, že upgrade bylo úspěšně dokončeno s `az aks show` příkaz.

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Výstup:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.9.6                 Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Další postup

Další informace o nasazení a správě AKS najdete v kurzech AKS.

> [!div class="nextstepaction"]
> [Kurz AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md