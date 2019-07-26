---
title: Upgrade clusteru Azure Kubernetes Service (AKS)
description: Přečtěte si, jak upgradovat cluster Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 2ed58846b9e7816092f0fc0787204921071d75e9
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498555"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Upgrade clusteru Azure Kubernetes Service (AKS)

V rámci životního cyklu clusteru AKS je často potřeba upgradovat na nejnovější verzi Kubernetes. Je důležité použít nejnovější verze zabezpečení Kubernetes nebo upgradovat, abyste získali nejnovější funkce. V tomto článku se dozvíte, jak upgradovat hlavní komponenty nebo jeden výchozí fond uzlů v clusteru AKS.

Clustery AKS, které používají více fondů uzlů nebo uzlů Windows serveru (aktuálně ve verzi Preview v AKS), najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Před zahájením

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.65 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

> [!WARNING]
> Upgrade clusteru AKS spustí Cordon a vyprázdní uzly. Pokud máte k dispozici nízkou kvótu výpočetních prostředků, upgrade může selhat.  Další informace najdete v tématu [zvýšení kvót](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request?branch=pr-en-us-83289) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Vyhledat dostupné upgrady clusteru AKS

Chcete-li zjistit, které verze Kubernetes jsou pro váš cluster k dispozici, použijte příkaz [AZ AKS Get-Upgrades][az-aks-get-upgrades] . Následující příklad zkontroluje dostupné upgrady pro cluster s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Při upgradu clusteru AKS nelze přeskočí podverze Kubernetes. Například upgrady mezi *1.11. x* -> *1.12. x* nebo *1.12. x* -> *1.13. x* jsou povoleny, ale *1.11. x* -> *1.13. x* není.
>
> Chcete-li provést upgrade, z *1.11. x* -> *1.13. x*, nejprve upgradujte z *1.11. x* -> *1.12. x*a potom upgradujte z *1.12. x* -> *1.13. x*.

Následující příklad výstupu ukazuje, že cluster je možné upgradovat na verzi *1.12.7* nebo *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Upgrade clusteru AKS

Seznam dostupných verzí pro cluster AKS můžete upgradovat pomocí příkazu [AZ AKS upgrade][az-aks-upgrade] . Během procesu upgradu AKS přidá nový uzel do clusteru, na kterém je spuštěná zadaná verze Kubernetes, a pak pečlivě [Cordon a][kubernetes-drain] vyprázdní jeden ze starých uzlů, aby se minimalizovalo přerušení spouštění aplikací. Když se nový uzel potvrdí jako běžící aplikace, Starý uzel se odstraní. Tento proces se opakuje, dokud nebudou upgradovány všechny uzly v clusteru.

Následující příklad upgraduje cluster na verzi *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Upgrade clusteru může trvat několik minut v závislosti na tom, kolik uzlů máte.

Pokud chcete ověřit, že upgrade proběhl úspěšně, použijte příkaz [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Následující příklad výstupu ukazuje, že cluster teď spouští *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Další postup

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
