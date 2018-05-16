---
title: Kurz Kubernetes v Azure – Aktualizace clusteru
description: Kurz Kubernetes v Azure – Aktualizace clusteru
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 22513ed9c6f726f2d3dd5d8b3327fedfa6d3ca04
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Kurz: Upgrade Kubernetes ve službě Azure Kubernetes Service (AKS)

Cluster Azure Kubernetes Service (AKS) je možné upgradovat pomocí Azure CLI. Během procesu upgradu jsou uzly Kubernetes pečlivě [uzavřené a vyprázdněné][kubernetes-drain], aby se minimalizovalo přerušení spuštěných aplikací.

V tomto kurzu, který je osmou částí osmidílné série, se upgraduje cluster Kubernetes. Úlohy, které provedete:

> [!div class="checklist"]
> * Identifikace aktuální verze a dostupných verzí Kubernetes
> * Upgrade uzlů Kubernetes
> * Ověření úspěšného upgradu

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do image kontejneru, tato image se odeslala do Azure Container Registry a vytvořil se cluster Kubernetes. Aplikace se potom spustila v tomto clusteru Kubernetes.

Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru][aks-tutorial-prepare-app].


## <a name="get-cluster-versions"></a>Získání verzí clusteru

Před upgradem clusteru pomocí příkazu `az aks get-upgrades` zkontrolujte, které vydané verze Kubernetes jsou pro upgrade k dispozici.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

V tomto příkladu je aktuální verze uzlu `1.7.9` a dostupné verze k upgradu se zobrazí ve sloupci Upgrades.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

## <a name="upgrade-cluster"></a>Upgrade clusteru

Uzly clusteru můžete upgradovat pomocí příkazu `az aks upgrade`. Následující příklad aktualizuje cluster na verzi `1.8.2`.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
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
    "kubernetesVersion": "1.8.2",
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

## <a name="validate-upgrade"></a>Ověření upgradu

Ověřte úspěšné provedení upgradu pomocí příkazu `az aks show`.

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Výstup:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
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