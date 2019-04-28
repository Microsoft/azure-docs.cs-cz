---
title: Upgrade clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak upgradovat cluster Azure Kubernetes Service (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 02/12/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 59d52db8c3f5f8968eae1a544abe1e5c6bbaacca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61028436"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Upgrade clusteru Azure Kubernetes Service (AKS)

Jako součást životního cyklu clusteru AKS často musíte upgradovat na nejnovější verzi Kubernetes. Je důležité použít nejnovější vydané verzi Kubernetes zabezpečení nebo upgradu získáte nejnovější funkce. Tento článek ukazuje, jak upgrade stávajícího clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje použití Azure CLI verze 2.0.56 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Zkontrolujte dostupné upgrady clusteru AKS

Chcete-li zkontrolovat, které vydané verze Kubernetes jsou k dispozici pro váš cluster, použijte [az aks get upgrady] [ az-aks-get-upgrades] příkazu. Následující příklad zkontroluje dostupné upgrady do clusteru s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Při upgradu clusteru AKS nelze přeskočit dílčí verze Kubernetes. Například upgraduje mezi *1.10.x* -> *1.11.x* nebo *1.11.x* -> *1.12.x* jsou povolené, ale *1.10.x* -> *1.12.x* není.
>
> Pokud chcete upgradovat, z *1.10.x* -> *1.12.x*, první upgrade z *1.10.x* -> *1.11.x*, poté proveďte upgrade z *1.11.x* -> *1.12.x*.

Následující příklad výstupu ukazuje, že je možné cluster upgradovat na verzi *1.11.5* nebo *1.11.6*:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.10.12          1.10.12            1.11.5, 1.11.6
```

## <a name="upgrade-an-aks-cluster"></a>Upgrade clusteru AKS

Seznam dostupných verzí pro váš cluster AKS, použijte [az aks upgrade] [ az-aks-upgrade] příkaz pro upgrade. Během procesu upgradu AKS přidá nového uzlu do clusteru, pak pečlivě [cordon a vyprazdňuje] [ kubernetes-drain] jeden uzel v době, aby se minimalizovalo přerušení spuštěných aplikací. Následující příklad upgraduje na verzi clusteru *1.11.6*:

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

Trvá několik minut upgradu clusteru, v závislosti na počtu uzlů, které máte.

Chcete-li ověřit, že upgrade proběhl úspěšně, použijte [az aks zobrazit] [ az-aks-show] příkaz:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Následující příklad výstupu ukazuje, že cluster se teď spustí *1.11.6*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  chinaeast2      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.chinaeast2.azmk8s.io
```

## <a name="next-steps"></a>Další postup

Tento článek vám ukázali, jak upgrade stávajícího clusteru AKS. Další informace o nasazení a správě AKS clusterů najdete v tématu sérii kurzů.

> [!div class="nextstepaction"]
> [Kurzech AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-upgrades]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-upgrade]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-upgrade
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show