---
title: Kurz Kubernetes v Azure – Upgrade clusteru
description: V tomto kurzu Azure Kubernetes Service (AKS) se dozvíte, jak upgradovat existující cluster AKS na nejnovější dostupnou verzi Kubernetes.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: f64ff611516b972d9440e212309ee22e1a12a928
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719434"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Kurz: Upgrade Kubernetes ve službě Azure Kubernetes Service (AKS)

V rámci životního cyklu aplikace a clusteru možná budete chtít provést upgrade na nejnovější dostupnou verzi Kubernetes a využívat nové funkce. Cluster Azure Kubernetes Service (AKS) je možné upgradovat pomocí Azure CLI.

V tomto kurzu, který je sedmou částí sedmidílné série, se upgraduje cluster Kubernetes. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Identifikace aktuální verze a dostupných verzí Kubernetes
> * Upgrade uzlů Kubernetes
> * Ověření úspěšného upgradu

## <a name="before-you-begin"></a>Před zahájením

V předchozích kurzech se aplikace zabalila do image kontejneru. Tato image se odeslala do Azure Container Registry a vytvoření clusteru AKS. Aplikace se pak nasadí do clusteru AKS. Pokud jste tyto kroky neprovedli a chcete postupovat s námi, začněte tématem [kurzu 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje použití Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Získání dostupných verzí clusteru

Před upgradem clusteru pomocí příkazu [az aks get-upgrades][] zkontrolujte, které vydané verze Kubernetes jsou pro upgrade k dispozici:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

V následujícím příkladu je aktuální verze *1.9.11*, a dostupným verzím jsou uvedeny v části *upgrady* sloupce.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.9.11           1.9.11             1.10.8, 1.10.9
```

## <a name="upgrade-a-cluster"></a>Upgrade clusteru

Aby se minimalizovalo přerušení spuštěných aplikací, jsou uzlů AKS pečlivě uzavřené a Vyprázdněné. V tomto procesu jsou prováděny následovně:

1. Plánovač Kubernetes zabrání další pody naplánované na uzel, který je potřeba upgradovat.
1. Pody spuštěné na uzlu jsou naplánovány na jiných uzlech v clusteru.
1. Uzel je vytvořen, na kterém běží nejnovější komponenty Kubernetes.
1. Po novém uzlu bude připravena a připojené k doméně do clusteru, Plánovač Kubernetes začne běžet podů.
1. Odstranit staré uzlu a další uzel v clusteru spustí proces cordon a vyprazdňování.

Cluster AKS můžete upgradovat pomocí příkazu [az aks upgrade][]. Následující příklad provede clusteru upgrade na Kubernetes verze *1.10.9*.

> [!NOTE]
> Najednou můžete upgradovat pouze jednu dílčí verzi. Například můžete upgradovat z *1.9.11* k *1.10.9*, ale nemohu upgradovat z *otázku 1.9.6* k *1.11.x* přímo. Upgrade z *1.9.11* k *1.11.x*, první upgrade z *1.9.11* k *1.10.x*, proveďte další upgrade z *1.10.x* k *1.11.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.9
```

Následujícímu zhuštěnému příkladu výstup ukazuje *kubernetesVersion* nyní hlásí *1.10.9*:

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
  "kubernetesVersion": "1.10.9",
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

Následující příklad výstupu ukazuje spuštění clusteru AKS *KubernetesVersion 1.10.9*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.9               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Odstranění clusteru

V tomto kurzu je poslední části série, můžete odstranit AKS cluster. Jelikož uzly prostředí Kubernetes běží na virtuálních počítačích v Azure, účtují se za ně poplatky, i když cluster nevyužíváte. Pomocí příkazu [az group delete][az-group-delete] odeberete skupinu prostředků, službu kontejneru a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete].

## <a name="next-steps"></a>Další postup

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
