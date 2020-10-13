---
title: Kurz Kubernetes v Azure – Upgrade clusteru
description: V tomto kurzu Azure Kubernetes Service (AKS) se dozvíte, jak upgradovat existující cluster AKS na nejnovější dostupnou verzi Kubernetes.
services: container-service
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: mvc
ms.openlocfilehash: c41f6dbd3b85125ef290539040819ffa1833ef6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629638"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Kurz: Upgrade Kubernetes ve službě Azure Kubernetes Service (AKS)

V rámci životního cyklu aplikace a clusteru možná budete chtít provést upgrade na nejnovější dostupnou verzi Kubernetes a využívat nové funkce. Cluster Azure Kubernetes Service (AKS) je možné upgradovat pomocí Azure CLI.

V tomto kurzu, který je sedmou částí sedmidílné série, se upgraduje cluster Kubernetes. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Identifikace aktuální verze a dostupných verzí Kubernetes
> * Upgrade uzlů Kubernetes
> * Ověření úspěšného upgradu

## <a name="before-you-begin"></a>Než začnete

V předchozích kurzech byla aplikace zabalena do image kontejneru. Tato image se nahrála do Azure Container Registry a vytvořili jste cluster AKS. Aplikace se pak nasadí do clusteru AKS. Pokud jste tyto kroky neudělali a chcete je sledovat, začněte s [kurzem 1 – vytváření imagí kontejneru][aks-tutorial-prepare-app].

Tento kurz vyžaduje, abyste spustili Azure CLI verze 2.0.53 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Získání dostupných verzí clusteru

Před upgradem clusteru pomocí příkazu [az aks get-upgrades][] zkontrolujte, které vydané verze Kubernetes jsou pro upgrade k dispozici:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

V následujícím příkladu je aktuální verze *1.15.11*a v části *upgrady*se zobrazí dostupné verze.

```json
{
  "agentPoolProfiles": null,
  "controlPlaneProfile": {
    "kubernetesVersion": "1.15.11",
    ...
    "upgrades": [
      {
        "isPreview": null,
        "kubernetesVersion": "1.16.8"
      },
      {
        "isPreview": null,
        "kubernetesVersion": "1.16.9"
      }
    ]
  },
  ...
}
```

## <a name="upgrade-a-cluster"></a>Upgrade clusteru

Aby se minimalizovalo přerušení spuštěných aplikací, uzly AKS pečlivě uzavřené a vyprázdní. V tomto procesu jsou provedeny následující kroky:

1. Scheduler Kubernetes zabrání v naplánování dalších lusků v uzlu, který se má upgradovat.
1. Běžící lusky na uzlu jsou plánovány na jiných uzlech v clusteru.
1. Vytvoří se uzel, který spustí nejnovější součásti Kubernetes.
1. Když je nový uzel připravený a připojený ke clusteru, začne Plánovač Kubernetes v něm spouštět lusky.
1. Starý uzel se odstraní a další uzel v clusteru zahájí proces Cordon a vyprázdnění.

Cluster AKS můžete upgradovat pomocí příkazu [az aks upgrade][].

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

> [!NOTE]
> Najednou můžete upgradovat pouze jednu dílčí verzi. Můžete třeba upgradovat z *1.14. x* na *1.15. x*, ale nemůžete upgradovat z *1.14. x* na *1.16. x* přímo. Pokud chcete upgradovat z *1.14. x* na *1.16. x*, nejdřív upgradujte z *1.14. x* na *1.15. x*a pak proveďte jiný upgrade z *1.15. x* na *1.16. x*.

Následující zhuštěný příklad výstupu ukazuje výsledek upgradu na *1.16.8*. Všimněte si, že *kubernetesVersion* nyní hlásí *1.16.8*:

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
  "kubernetesVersion": "1.16.8",
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

Následující příklad výstupu ukazuje, že cluster AKS spouští *KubernetesVersion 1.16.8*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.16.8               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Odstranění clusteru

Vzhledem k tomu, že se jedná o poslední část této série, možná budete chtít odstranit cluster AKS. Jelikož uzly prostředí Kubernetes běží na virtuálních počítačích v Azure, účtují se za ně poplatky, i když cluster nevyužíváte. Pomocí příkazu [az group delete][az-group-delete] odeberete skupinu prostředků, službu kontejneru a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Při odstranění clusteru se neodebere instanční objekt služby Azure Active Directory používaný clusterem AKS. Postup odebrání instančního objektu najdete v tématu věnovaném [aspektům instančního objektu AKS a jeho odstranění][sp-delete]. Pokud jste použili spravovanou identitu, tato identita je spravovaná platformou a nevyžaduje, abyste zřídili ani nepoužívali žádné tajné kódy.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste upgradovali Kubernetes v clusteru AKS. Naučili jste se:

> [!div class="checklist"]
> * Identifikace aktuální verze a dostupných verzí Kubernetes
> * Upgrade uzlů Kubernetes
> * Ověření úspěšného upgradu

Další informace o AKS najdete v tématu [Přehled AKS][aks-intro]. Pokyny k vytváření úplných řešení s AKS najdete v tématu [doprovodné materiály k řešení AKS][aks-solution-guidance].

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
[aks-solution-guidance]: /azure/architecture/reference-architectures/containers/aks-start-here
