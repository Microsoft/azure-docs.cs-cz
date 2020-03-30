---
title: Upgrade clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak upgradovat cluster služby Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 4520297e83f96f95b10ecafd5af52a913dc5f450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621983"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Upgrade clusteru Azure Kubernetes Service (AKS)

V rámci životního cyklu clusteru AKS je často nutné upgradovat na nejnovější verzi Kubernetes. Je důležité použít nejnovější verze zabezpečení Kubernetes nebo upgradovat, abyste získali nejnovější funkce. Tento článek ukazuje, jak upgradovat hlavní součásti nebo jeden, výchozí fond uzlů v clusteru AKS.

Pro clustery AKS, které používají více fondů uzlů nebo uzly Windows Serveru (aktuálně ve verzi preview v AKS), najdete v [tématu Upgrade fondu uzlů v AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0.65 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

> [!WARNING]
> Upgrade clusteru AKS aktivuje kordon a vyprázdnění uzlů. Pokud máte k dispozici nízkou výpočetní kvótu, upgrade může selhat. Další informace naleznete v tématu [zvýšení kvót.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)
> Pokud používáte vlastní nasazení automatického škálování clusteru, zakažte jej (můžete jej škálovat na nulu replik) během upgradu, protože existuje šance, že bude zasahovat do procesu upgradu. Spravovaný autoscaler to automaticky zpracovává. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Kontrola dostupných upgradů clusteru AKS

Chcete-li zkontrolovat, které verze Kubernetes jsou k dispozici pro váš cluster, použijte příkaz [az aks get-upgrade.][az-aks-get-upgrades] Následující příklad kontroluje dostupné upgrady clusteru s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Při upgradu clusteru AKS nelze přeskočit dílčí verze Kubernetes. Například upgrady mezi *1.12.x* -> *1.13.x* nebo *1.13.x* -> *1.14.x* jsou povoleny, ale *1.12.x* -> *1.14.x* není.
>
> Chcete-li upgradovat z *1.12.x* -> *1.14.x*, nejprve upgrade z *1.12.x* -> *1.13.x*, potom upgrade z *1.13.x* -> *1.14.x*.

Následující příklad výstupu ukazuje, že cluster lze upgradovat na verze *1.13.9* a *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Pokud není k dispozici žádný upgrade, získáte:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Upgrade clusteru AKS

Pomocí seznamu dostupných verzí clusteru AKS použijte k inovaci příkaz [upgrade az aks.][az-aks-upgrade] Během procesu upgradu AKS přidá nový uzel do clusteru, který spouští zadanou verzi Kubernetes, pak pečlivě [cordon a vyprázdní][kubernetes-drain] jeden ze starých uzlů minimalizovat narušení spuštěných aplikací. Když je nový uzel potvrzen jako spuštěné pody aplikace, starý uzel se odstraní. Tento proces se opakuje, dokud nebyly upgradovány všechny uzly v clusteru.

Následující příklad upgraduje cluster na verzi *1.13.10*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Upgrade clusteru trvá několik minut v závislosti na počtu uzlů, které máte. 

> [!NOTE]
> K dokončení upgradu clusteru je k dispozici celková povolená doba. Tato doba se vypočítá tak, že součin `10 minutes * total number of nodes in the cluster`. Například v clusteru s 20 uzlovacími skupinami musí být operace upgradu úspěšné za 200 minut, jinak aks nepodaří operaci, aby se zabránilo neopravitelnému stavu clusteru. Chcete-li obnovit při selhání upgradu, opakujte operaci upgradu po dosažení časového času.

Chcete-li ověřit, zda byl upgrade úspěšný, použijte příkaz [az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Následující příklad výstupu ukazuje, že cluster nyní běží *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Další kroky

Tento článek vám ukázal, jak upgradovat existující cluster AKS. Další informace o nasazení a správě clusterů AKS najdete v sadě kurzů.

> [!div class="nextstepaction"]
> [AKS výukové programy][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
