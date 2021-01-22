---
title: Azure Kubernetes Service (AKS) s smlouvou SLA pro provozuschopnost
description: Přečtěte si o volitelné nabídce SLA pro dobu provozu pro Server API služby Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 9f8f697da7499d370c96b77e7e543dec9fbafa3e
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664091"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Smlouva SLA pro Azure Kubernetes Service (AKS) pro provozuschopnost

Smlouva SLA pro dobu provozu je volitelná funkce, která umožňuje finančně zajištěnou a vyšší smlouvu SLA pro cluster. Smlouva SLA pro dobu provozu garantuje 99,95% dostupnost koncového bodu serveru Kubernetes API pro clustery, které používají [zóny dostupnosti][availability-zones] a 99,9% dostupnosti pro clustery, které nepoužívají zóny dostupnosti. AKS používá k zajištění splnění požadavků smlouvy SLA repliky hlavních uzlů napříč aktualizacemi a doménami selhání.

Zákazníci, kteří potřebují smlouvu SLA pro splnění požadavků na dodržování předpisů nebo vyžadují rozšíření smlouvy SLA koncovým uživatelům, by měli tuto funkci povolit. Zákazníci s důležitými úlohami, které budou využívat zvýšení smlouvy SLA na vyšší dobu provozu, můžou také těžit. Použití funkce SLA pro dobu provozu s Zóny dostupnosti umožňuje vyšší dostupnost pro dobu provozu serveru rozhraní Kubernetes API.  

Zákazníci můžou pořád vytvářet neomezený počet bezplatných clusterů s cílem úrovně služeb (SLO) o 99,5% a podle potřeby považovat za dobu provozu na úrovni SLO nebo SLA.

> [!Important]
> Clustery s uzamčením odchozích dat najdete v tématu [omezení odchozího provozu](limit-egress-traffic.md) pro otevření odpovídajících portů.

## <a name="region-availability"></a>Dostupnost v oblastech

* Smlouva SLA pro dobu provozu je dostupná ve veřejných oblastech a Azure Government oblastech, kde [se podporuje AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).
* Smlouva SLA o provozuschopnosti je dostupná pro [privátní clustery AKS][private-clusters] ve všech veřejných oblastech, kde se podporuje AKS.

## <a name="sla-terms-and-conditions"></a>Podmínky a ujednání SLA

Smlouva SLA pro dobu provozu je placená funkce a je povolená pro jednotlivé clustery. Ceny smlouvy SLA pro dobu provozu určují počet diskrétních clusterů, a ne velikost jednotlivých clusterů. Další informace najdete v [podrobnostech o cenách smlouvy SLA pro dobu provozu](https://azure.microsoft.com/pricing/details/kubernetes-service/) .

## <a name="before-you-begin"></a>Než začnete

* Nainstalujte [Azure CLI](/cli/azure/install-azure-cli) verze 2.8.0 nebo novější.

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Vytvoření nového clusteru s smlouvou SLA pro dobu provozu

> [!NOTE]
> Pokud v současné době povolíte smlouvu SLA pro dobu provozu, neexistuje žádný způsob, jak ho z clusteru odebrat.

Pokud chcete vytvořit nový cluster s smlouvou SLA pro dobu provozu, použijte rozhraní příkazového řádku Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Pomocí [`az aks create`][az-aks-create] příkazu vytvořte cluster AKS. Následující příklad vytvoří cluster *myAKSCluster* s jedním uzlem. Dokončení této operace trvá několik minut:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON. Následující fragment kódu JSON ukazuje placené úrovně SKU, což značí, že je cluster povolený s smlouvou SLA pro dobu provozu:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Úprava existujícího clusteru pro použití smlouvy SLA pro dobu provozu

Volitelně můžete aktualizovat existující clustery, aby používaly smlouvu SLA pro provozuschopnost.

Pokud jste vytvořili cluster AKS s předchozím postupem, odstraňte skupinu prostředků:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Vytvořte novou skupinu prostředků:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Vytvoření nového clusteru a nevyužívání smlouvy SLA pro dobu provozu:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Pomocí [`az aks update`][az-aks-update] příkazu aktualizujte stávající cluster:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 Následující fragment kódu JSON ukazuje placené úrovně SKU, což značí, že je cluster povolený s smlouvou SLA pro dobu provozu:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="clean-up"></a>Vyčištění

Pokud se chcete vyhnout poplatkům, vyčistěte prostředky, které jste vytvořili. Pokud chcete cluster odstranit, pomocí [`az group delete`][az-group-delete] příkazu odstraňte skupinu prostředků AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Další kroky

Pomocí [zóny dostupnosti][availability-zones] můžete zvýšit vysokou dostupnost úloh clusteru AKS.

Nakonfigurujte cluster tak, aby [omezil přenos odchozího provozu](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-update]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_update
[az-group-delete]: /cli/azure/group#az-group-delete
[private-clusters]: private-clusters.md
