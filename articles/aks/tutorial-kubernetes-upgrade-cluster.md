---
title: Kurz Kubernetes v Azure – Upgrade clusteru
description: V tomto kurzu Azure Kubernetes Service (AKS) se dozvíte, jak upgradovat existující cluster AKS na nejnovější dostupnou verzi Kubernetes.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1c0710be11b95b66d16661b5aff9cbf739ccda92
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901925"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Kurz: Upgrade Kubernetes ve službě Azure Kubernetes Service (AKS)

V rámci životního cyklu aplikace a clusteru možná budete chtít provést upgrade na nejnovější dostupnou verzi Kubernetes a využívat nové funkce. Cluster Azure Kubernetes Service (AKS) je možné upgradovat pomocí Azure CLI. Aby se minimalizovalo přerušení spuštěných aplikací, jsou uzly Kubernetes během procesu upgradu pečlivě [uzavřené a vyprázdněné][kubernetes-drain].

V tomto kurzu, který je sedmou částí sedmidílné série, se upgraduje cluster Kubernetes. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Identifikace aktuální verze a dostupných verzí Kubernetes
> * Upgrade uzlů Kubernetes
> * Ověření úspěšného upgradu

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech se aplikace zabalila do image kontejneru, tato image se odeslala do Azure Container Registry a vytvořil se cluster Kubernetes. Aplikace se potom spustila v tomto clusteru Kubernetes. Pokud jste tyto kroky neprovedli a chcete si je projít, vraťte se ke [kurzu 1 – Vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje použití Azure CLI verze 2.0.44 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Získání dostupných verzí clusteru

Před upgradem clusteru pomocí příkazu [az aks get-upgrades][] zkontrolujte, které vydané verze Kubernetes jsou pro upgrade k dispozici:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

V následujícím příkladu je aktuální verze *1.9.6* a dostupné verze se zobrazují ve sloupci *Upgrades*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------
default  myResourceGroup  1.9.9            1.9.9              1.10.3, 1.10.5, 1.10.6
```

## <a name="upgrade-a-cluster"></a>Upgrade clusteru

Cluster AKS můžete upgradovat pomocí příkazu [az aks upgrade][]. Následující příklad upgraduje cluster na verzi Kubernetes *1.10.6*.

> [!NOTE]
> Najednou můžete upgradovat pouze jednu dílčí verzi. Můžete například upgradovat z verze *1.9.6* na verzi *1.10.3*, ale nemůžete upgradovat z verze *1.9.6* přímo na verzi *1.11.x*. Pokud chcete upgradovat z verze *1.9.6* na verzi *1.11.x*, nejprve upgradujte z verze *1.9.6* na verzi *1.10.3* a pak proveďte další upgrade z verze *1.10.3* na verzi *1.11.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.6
```

Následující zkrácený příklad výstupu ukazuje *kubernetesVersion* nyní s hodnotou *1.10.6*:

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
  "kubernetesVersion": "1.10.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Ověření upgradu

Následujícím způsobem ověřte úspěšné provedení upgradu pomocí příkazu [az aks show][]:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Následující příklad výstupu ukazuje, že cluster AKS používá *KubernetesVersion 1.10.6*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Odstranění clusteru

Toto je poslední část této série kurzů, takže možná budete chtít cluster AKS odstranit. Jelikož uzly prostředí Kubernetes běží na virtuálních počítačích v Azure, účtují se za ně poplatky, i když cluster nevyužíváte. Pomocí příkazu [az group delete][az-group-delete] odeberete skupinu prostředků, službu kontejneru a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete].

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste upgradovali Kubernetes v clusteru AKS. Naučili jste se tyto postupy:

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
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
