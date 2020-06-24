---
title: Azure Kubernetes Service (AKS) s smlouvou SLA pro provozuschopnost
description: Přečtěte si o volitelné nabídce SLA pro dobu provozu pro Server API služby Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: references_regions
ms.openlocfilehash: b360f36dfc80033ac95e4face438b66eed33cec4
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945507"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Smlouva SLA pro Azure Kubernetes Service (AKS) pro provozuschopnost

Smlouva SLA pro dobu provozu je volitelná funkce, která umožňuje finančně zajištěnou a vyšší smlouvu SLA pro cluster. Smlouva SLA pro dobu provozu garantuje 99,95% dostupnost koncového bodu serveru Kubernetes API pro clustery, které používají [zóny dostupnosti][availability-zones] a 99,9% dostupnosti pro clustery, které nepoužívají zóny dostupnosti. AKS používá k zajištění splnění požadavků smlouvy SLA repliky hlavních uzlů napříč aktualizacemi a doménami selhání.

Zákazníci, kteří potřebují smlouvu SLA pro splnění požadavků na dodržování předpisů nebo vyžadují rozšíření smlouvy SLA koncovým uživatelům, by měli tuto funkci povolit. Zákazníci s důležitými úlohami, které budou využívat zvýšení smlouvy SLA na vyšší dobu provozu, můžou také těžit. Použití funkce SLA pro dobu provozu s Zóny dostupnosti umožňuje vyšší dostupnost pro dobu provozu serveru rozhraní Kubernetes API.  

Zákazníci můžou pořád vytvářet neomezený počet bezplatných clusterů s cílem úrovně služeb (SLO) o 99,5% a podle potřeby považovat za dobu provozu na úrovni SLO nebo SLA.

> [!Important]
> Clustery s uzamčením odchozích dat najdete v tématu [omezení odchozího provozu](limit-egress-traffic.md) pro otevření odpovídajících portů.

## <a name="region-availability"></a>Dostupnost v oblastech

Smlouva SLA pro dobu provozu je dostupná ve veřejných oblastech, kde [se podporuje AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

* Azure Government se momentálně nepodporuje.
* Azure Čína 21Vianet se momentálně nepodporuje.

## <a name="sla-terms-and-conditions"></a>Podmínky a ujednání SLA

Smlouva SLA pro dobu provozu je placená funkce a je povolená pro jednotlivé clustery. Ceny smlouvy SLA pro dobu provozu určují počet diskrétních clusterů, a ne velikost jednotlivých clusterů. Další informace najdete v [podrobnostech o cenách smlouvy SLA pro dobu provozu](https://azure.microsoft.com/pricing/details/kubernetes-service/) .

## <a name="before-you-begin"></a>Než začnete

* Nainstalujte [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) verze 2.7.0 nebo novější.

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
```

## <a name="limitations"></a>Omezení

* V současné době nejde převést existující cluster, aby se povolila smlouva SLA pro provozuschopnost.
* V současné době neexistuje způsob, jak odebrat smlouvu SLA pro provozuschopnost z clusteru AKS po vytvoření s povoleným povolením.  
* Soukromé clustery se aktuálně nepodporují.

## <a name="next-steps"></a>Další kroky

Pomocí [zóny dostupnosti][availability-zones] můžete zvýšit vysokou dostupnost úloh clusteru AKS.
Nakonfigurujte cluster tak, aby [omezil přenos odchozího provozu](limit-egress-traffic.md).

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
