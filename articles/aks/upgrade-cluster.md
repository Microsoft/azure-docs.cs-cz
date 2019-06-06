---
title: Upgrade clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak upgradovat cluster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 2cadd4b33cb52307599ce1e83eee8370ef9850fe
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692775"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Upgrade clusteru Azure Kubernetes Service (AKS)

Jako součást životního cyklu clusteru AKS často musíte upgradovat na nejnovější verzi Kubernetes. Je důležité použít nejnovější vydané verzi Kubernetes zabezpečení nebo upgradu získáte nejnovější funkce. Tento článek ukazuje, jak upgradovat hlavní součásti nebo jeden výchozí fond uzlů v clusteru AKS.

AKS clustery, které používají více fondy uzlů nebo uzlech serveru systému Windows (obojí momentálně ve verzi preview ve službě AKS), najdete v části [fond uzlů ve službě AKS Upgrade][nodepool-upgrade].

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje použití Azure CLI verze 2.0.65 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Zkontrolujte dostupné upgrady clusteru AKS

Chcete-li zkontrolovat, které vydané verze Kubernetes jsou k dispozici pro váš cluster, použijte [az aks get upgrady] [ az-aks-get-upgrades] příkazu. Následující příklad zkontroluje dostupné upgrady do clusteru s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Při upgradu clusteru AKS nelze přeskočit dílčí verze Kubernetes. Například upgraduje mezi *1.11.x* -> *1.12.x* nebo *1.12.x* -> *1.13.x* jsou povolené, ale *1.11.x* -> *1.13.x* není.
>
> Pokud chcete upgradovat, z *1.11.x* -> *1.13.x*, první upgrade z *1.11.x* -> *1.12.x*, poté proveďte upgrade z *1.12.x* -> *1.13.x*.

Následující příklad výstupu ukazuje, že je možné cluster upgradovat na verzi *1.12.7* nebo *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Upgrade clusteru AKS

Seznam dostupných verzí pro váš cluster AKS, použijte [az aks upgrade] [ az-aks-upgrade] příkaz pro upgrade. Během procesu upgradu AKS přidá nového uzlu do clusteru, na kterém běží zadaná verze Kubernetes, pak pečlivě [cordon a vyprazdňuje] [ kubernetes-drain] jeden staré uzly, aby se minimalizovalo přerušení spuštění aplikace. Když nový uzel potvrdí jakoby běžely pody aplikací, odstraní se původní uzel. Tento proces se opakuje, dokud se neupgradují všechny uzly v clusteru.

Následující příklad upgraduje na verzi clusteru *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Trvá několik minut upgradu clusteru, v závislosti na počtu uzlů, které máte.

Chcete-li ověřit, že upgrade proběhl úspěšně, použijte [az aks zobrazit] [ az-aks-show] příkaz:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Následující příklad výstupu ukazuje, že cluster se teď spustí *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Další postup

Tento článek vám ukázali, jak upgrade stávajícího clusteru AKS. Další informace o nasazení a správě AKS clusterů najdete v tématu sérii kurzů.

> [!div class="nextstepaction"]
> [Kurzech AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
