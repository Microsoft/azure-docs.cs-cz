---
title: Kvóty, sku a dostupnost oblasti ve službě Azure Kubernetes Service (AKS)
description: Další informace o výchozí kvóty, omezené velikosti virtuálních zařízení uzlu a dostupnost oblasti služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 03e7396932f0813ef4bd00d644dcdaddfe229e6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252828"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvóty, omezení velikosti virtuálního počítače a dostupnost oblasti ve službě Azure Kubernetes Service (AKS)

Všechny služby Azure nastavují výchozí limity a kvóty pro prostředky a funkce. Některé virtuální počítače (VM) SKU jsou také omezeny pro použití.

Tento článek podrobně popisuje výchozí limity prostředků pro prostředky služby Azure Kubernetes Service (AKS) a dostupnost AKS v oblastech Azure.

## <a name="service-quotas-and-limits"></a>Kvóty a omezení služeb

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Zřízená infrastruktura

Na zřízenou infrastrukturu se vztahují všechny další omezení sítě, výpočtů a úložiště. Příslušná omezení najdete v tématu [Limity předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> Při upgradu clusteru AKS jsou dočasně spotřebovány další prostředky. Tyto prostředky zahrnují dostupné IP adresy v podsíti virtuální sítě nebo kvótu virtuálního procesoru virtuálního počítače virtuálního počítače. Pokud používáte kontejnery Windows Server (aktuálně ve verzi preview v AKS), jedinou schválenou aplikací pro použití nejnovějších aktualizací na uzly je provedení operace upgradu. Neúspěšný proces upgradu clusteru může znamenat, že k zpracování těchto dočasných prostředků nemáte k dispozici adresní prostor IP ani kvótu virtuálního procesoru. Další informace o procesu upgradu uzlu systému Windows Server naleznete [v tématu Upgrade fondu uzlů v systému AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Omezené velikosti virtuálních počítače

Každý uzel v clusteru AKS obsahuje pevné množství výpočetních prostředků, jako je virtuální procesor a paměť. Pokud uzel AKS obsahuje nedostatek výpočetních prostředků, pody nemusí správně fungovat. Chcete-li zajistit, že požadované *pody kube systému* a vaše aplikace lze spolehlivě naplánovat, **nepoužívejte následující virtuální počítače sloky v AKS**:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Další informace o typech virtuálních počítačů a jejich výpočetních prostředcích najdete [v tématu Velikosti pro virtuální počítače v Azure][vm-skus].

## <a name="region-availability"></a>Dostupnost v oblastech

Nejnovější seznam, kde můžete nasadit a spustit clustery, naleznete v [tématu dostupnost oblasti AKS][region-availability].

## <a name="next-steps"></a>Další kroky

Některé výchozí limity a kvóty je možné zvýšit. Pokud váš prostředek podporuje zvýšení, požádejte o zvýšení prostřednictvím [žádosti o podporu Azure][azure-support] (pro typ **problému**, vyberte **kvótu**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
