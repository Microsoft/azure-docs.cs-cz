---
title: Kvóty, SKU a dostupnost oblastí ve službě Azure Kubernetes Service (AKS)
description: Další informace o výchozí kvóty, velikosti omezen uzlů skladovou Položku virtuálního počítače a dostupnost oblastí Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mlearned
ms.openlocfilehash: 318846cddecdf020e2e751d3a0b9e05fc83bba73
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614557"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvóty, omezení velikosti virtuálního počítače a dostupnost oblastí ve službě Azure Kubernetes Service (AKS)

Všechny služby Azure nastavit výchozí omezení a kvóty pro prostředky a funkce. Určité skladové položky virtuálních počítačů (VM) se také pro použití s omezeným přístupem.

Tento článek podrobně popisuje výchozí limity prostředků pro prostředky Azure Kubernetes Service (AKS) a dostupnost služby AKS v oblastech Azure.

## <a name="service-quotas-and-limits"></a>Kvóty a omezení služeb

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Zřízená infrastruktura

Na zřízenou infrastrukturu se vztahují všechny další omezení sítě, výpočtů a úložiště. Příslušná omezení najdete v části [předplatného Azure a omezení služeb](../azure-subscription-service-limits.md).

> [!IMPORTANT]
> Při upgradu clusteru AKS se dočasně spotřebuje další prostředky. Tyto prostředky zahrnují dostupnými IP adresami v podsíti virtuální sítě nebo kvóty virtuálních procesorů virtuálního počítače. Pokud používáte Windows Server kontejnery (aktuálně ve verzi preview ve službě AKS), je pouze doporučené přístup k nejnovější aktualizace pro uzly k provedení operace upgradu. Proces upgradu selhání clusteru může znamenat, že nemáte k dispozici IP adresy místo nebo virtuálních procesorů kvóty pro zpracování těchto dočasné prostředků. Další informace o procesu upgradu uzlu Windows serveru najdete v tématu [fond uzlů ve službě AKS Upgrade][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Omezené velikosti virtuálních počítačů

Každý uzel v clusteru AKS obsahuje pevně dané množství výpočetních prostředků, jako jsou virtuální procesor a paměť. Pokud některý uzel AKS obsahuje dostatek výpočetní prostředky, pody nemusí podařit správně spustit. Chcete-li zajistit požadované *kube-system* podů a vaše aplikace je možné spolehlivě naplánovat, nepoužívejte následující skladové položky virtuálních počítačů ve službě AKS:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Další informace o typy virtuálních počítačů a jejich výpočetní prostředky, najdete v části [velikosti virtuálních počítačů v Azure][vm-skus].

## <a name="region-availability"></a>Dostupnost v oblastech

Nejnovější seznam, kde můžete nasadit a spustit clustery naleznete v tématu [dostupnosti oblast AKS][region-availability].

## <a name="next-steps"></a>Další kroky

Některé výchozí limity a kvóty je možné zvýšit. Pokud váš prostředek podporuje zvýšení, požádat o zvýšení prostřednictvím [žádost o podporu Azure][azure-support] (pro **typ problému**vyberte **kvóty**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
