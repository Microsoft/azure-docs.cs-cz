---
title: Vysoká dostupnost služby Azure Kubernetes Service (AKS) s smlouvou SLA pro provozuschopnost
description: Přečtěte si o volitelné nabídce SLA pro dobu provozu vysoké dostupnosti pro Server API služby Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1c340f85a107cac437e1241025d8c9bc6991b965
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125719"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Smlouva SLA pro Azure Kubernetes Service (AKS) pro provozuschopnost

Smlouva SLA pro provozuschopnost je volitelná funkce, která umožňuje finančně zajištěnou vyšší smlouvu SLA pro cluster. Smlouva SLA pro dobu provozu garantuje 99,95% dostupnost koncového bodu serveru Kubernetes API pro clustery, které používají [zónu dostupnosti][availability-zones] , a 99,9% dostupnosti pro clustery, které nepoužívají zóny dostupnosti. AKS používá k zajištění splnění požadavků smlouvy SLA repliky hlavních uzlů napříč aktualizacemi a doménami selhání.

Zákazníci, kteří potřebují smlouvu SLA na dodržování předpisů nebo rozšiřují smlouvy SLA na jejich zákazníky, by měli tuto funkci zapnout. Zákazníci s důležitými úlohami, kteří potřebují vyšší dostupnost s možností zvýhodnění SLA od povolení této funkce. Pokud chcete získat vyšší dostupnost serveru Kubernetes API, povolte funkci s Zóny dostupnosti.  

Zákazníci můžou vytvářet neomezený počet bezplatných clusterů s cílem úrovně služeb (SLO) 99,5%.

> [!Important]
> Clustery s uzamčením odchozích dat najdete v tématu [omezení odchozího provozu](limit-egress-traffic.md) pro otevření odpovídajících portů pro smlouvu SLA pro provozuschopnost.

## <a name="sla-terms-and-conditions"></a>Podmínky a ujednání SLA

Smlouva SLA pro dobu provozu je placená funkce a je povolená pro jednotlivé clustery. Ceny smlouvy SLA pro dobu provozu určují počet clusterů, a ne velikost clusterů. Další informace najdete v [podrobnostech o cenách smlouvy SLA pro dobu provozu](https://azure.microsoft.com/pricing/details/kubernetes-service/) .

## <a name="region-availability"></a>Dostupnost oblasti

Smlouva SLA pro dobu provozu je dostupná v následujících oblastech:

* Austrálie – východ
* Střední Kanada
* USA – východ
* USA – východ 2
* USA – středojih
* Jihovýchodní Asie
* USA – západ 2

## <a name="before-you-begin"></a>Před zahájením

* Azure CLI verze 2.7.0 nebo novější

## <a name="creating-a-cluster-with-uptime-sla"></a>Vytvoření clusteru s smlouvou SLA pro dobu provozu

Pokud chcete vytvořit nový cluster s smlouvou SLA pro dobu provozu, použijte rozhraní příkazového řádku Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Pomocí příkazu [az aks create][az-aks-create] vytvořte cluster AKS. Následující příklad vytvoří cluster *myAKSCluster* s jedním uzlem. Kromě toho se dá pomocí parametru *--enable-addons monitoring* povolit Azure Monitor pro kontejnery.  Dokončení této operace trvá několik minut.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON. Následující fragment kódu JSON ukazuje placené úrovně SKU, což značí, že cluster je povolený s smlouvou SLA pro provozuschopnost.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": null
```

## <a name="limitations"></a>Omezení

* V tuto chvíli nemůžete do existujících clusterů přidat smlouvu SLA pro dobu provozu.
* V současné době neexistuje způsob, jak odebrat smlouvu SLA pro provozuschopnost z clusteru AKS.  

## <a name="next-steps"></a>Další kroky

Pomocí [zóny dostupnosti][availability-zones] můžete zvýšit vysokou dostupnost úloh clusteru AKS.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
