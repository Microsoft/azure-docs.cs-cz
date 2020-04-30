---
title: Upgrade clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak upgradovat cluster Azure Kubernetes Service (AKS), abyste získali nejnovější funkce a aktualizace zabezpečení.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 7e9a47b7bda4cdb0ff6f1983bc884f7441a26d9b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207968"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Upgrade clusteru Azure Kubernetes Service (AKS)

V rámci životního cyklu clusteru AKS je často potřeba upgradovat na nejnovější verzi Kubernetes. Je důležité použít nejnovější verze zabezpečení Kubernetes nebo upgradovat, abyste získali nejnovější funkce. V tomto článku se dozvíte, jak upgradovat hlavní komponenty nebo jeden výchozí fond uzlů v clusteru AKS.

Clustery AKS, které používají více fondů uzlů, najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Před zahájením

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.65 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

> [!WARNING]
> Upgrade clusteru AKS spustí Cordon a vyprázdní uzly. Pokud máte k dispozici nízkou kvótu výpočetních prostředků, upgrade může selhat. Další informace najdete v tématu [zvýšení kvót](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) .
> Pokud používáte vlastní nasazení automatického škálování clusteru, zakažte ho prosím (můžete ho během upgradu škálovat na nulové repliky), protože by došlo k narušení procesu upgradu. Spravovaný autoscaleer to automaticky zpracuje. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Vyhledat dostupné upgrady clusteru AKS

Chcete-li zjistit, které verze Kubernetes jsou pro váš cluster k dispozici, použijte příkaz [AZ AKS Get-Upgrades][az-aks-get-upgrades] . Následující příklad zkontroluje dostupné upgrady pro cluster s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Při upgradu clusteru AKS nelze přeskočí podverze Kubernetes. Například upgrady mezi *1.12. x* -> *1.13. x* nebo *1.13. x* -> *1.14. x* jsou povoleny, ale *1.12. x* -> *1.14. x* není.
>
> Chcete-li provést upgrade, z *1.12. x* -> *1.14. x*, nejprve upgradujte z *1.12. x* -> *1.13. x*a potom proveďte upgrade z *1.13. x* -> *1.14. x*.

Následující příklad výstupu ukazuje, že cluster je možné upgradovat na verze *1.13.9* a *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Pokud upgrade není k dispozici, zobrazí se:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Upgrade clusteru AKS

Seznam dostupných verzí pro cluster AKS můžete upgradovat pomocí příkazu [AZ AKS upgrade][az-aks-upgrade] . Během procesu upgradu AKS přidá nový uzel do clusteru, na kterém je spuštěná zadaná verze Kubernetes, a pak pečlivě [Cordon a vyprázdní][kubernetes-drain] jeden ze starých uzlů, aby se minimalizovalo přerušení spouštění aplikací. Když se nový uzel potvrdí jako běžící aplikace, Starý uzel se odstraní. Tento proces se opakuje, dokud nebudou upgradovány všechny uzly v clusteru.

Následující příklad upgraduje cluster na verzi *1.13.10*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Upgrade clusteru může trvat několik minut v závislosti na tom, kolik uzlů máte. 

> [!NOTE]
> Je celkově povolený čas pro dokončení upgradu clusteru. Tento čas se vypočítá pomocí produktu `10 minutes * total number of nodes in the cluster`. Například v clusteru 20 uzlů musí operace upgradu úspěšně probíhat během 200 minut nebo AKS operace, aby se předešlo neopravitelnému stavu clusteru. Chcete-li provést obnovení při selhání upgradu, opakujte operaci upgradu po vypršení časového limitu.

Pokud chcete ověřit, že upgrade proběhl úspěšně, použijte příkaz [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Následující příklad výstupu ukazuje, že cluster teď spouští *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Další kroky

Tento článek vám ukázal, jak upgradovat existující cluster AKS. Další informace o nasazení a správě clusterů AKS najdete v tématu o sadě kurzů.

> [!div class="nextstepaction"]
> [Kurzy AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
