---
title: Kvóty, SKU a dostupnost oblastí ve službě Azure Kubernetes Service (AKS)
description: Další informace o výchozí kvóty, velikosti omezen uzlů skladovou Položku virtuálního počítače a dostupnost oblastí Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "64413052"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvóty, omezení velikosti virtuálního počítače a dostupnost oblastí ve službě Azure Kubernetes Service (AKS)

Všechny služby Azure zahrnují určitá výchozí omezení a kvóty pro prostředky a funkce. Pro velikost uzlu některých virtuálních počítačů (VM) skladové položky jsou pak s omezením pomocí specifikátoru pro použití.

Tento článek podrobně popisuje výchozí limity prostředků pro prostředky Azure Kubernetes Service (AKS) a také dostupnost služby AKS v oblastech Azure.

## <a name="service-quotas-and-limits"></a>Kvóty a omezení služeb

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Zřízená infrastruktura

Na zřízenou infrastrukturu se vztahují všechny další omezení sítě, výpočtů a úložiště. Příslušná omezení najdete v tématu [Omezení služby a předplatného Azure](../azure-subscription-service-limits.md).

## <a name="restricted-vm-sizes"></a>Omezené velikosti virtuálních počítačů

Každý uzel v clusteru AKS obsahuje pevně dané množství výpočetních prostředků, jako jsou virtuální procesor a paměť. Pokud některý uzel AKS obsahuje dostatek výpočetní prostředky, pody nemusí správně spustit. Chcete-li zajistit požadované *kube-system* podů a vaše aplikace můžete spolehlivě naplánovat, následující skladové položky virtuálních počítačů nelze použít ve službě AKS:

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

## <a name="next-steps"></a>Další postup

Některé výchozí limity a kvóty je možné zvýšit. Pokud si chcete vyžádat zvýšení jednoho nebo několika prostředků, které takové zvýšení podporují, odešlete [žádost o podporu Azure][azure-support] (jako **typ problému** vyberte kvótu).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
