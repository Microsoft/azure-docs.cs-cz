---
title: Kvóty, SKU a dostupnost oblastí ve službě Azure Kubernetes Service (AKS)
description: Přečtěte si o výchozích kvótách, omezených velikostech SKU virtuálních počítačů uzlů a dostupnosti oblastí služby Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 03e7396932f0813ef4bd00d644dcdaddfe229e6a
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594545"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvóty, omezení velikosti virtuálních počítačů a dostupnost oblastí ve službě Azure Kubernetes Service (AKS)

Všechny služby Azure nastavily výchozí limity a kvóty pro prostředky a funkce. Některé skladové položky virtuálních počítačů jsou taky omezené pro použití.

Tento článek podrobně popisuje výchozí omezení prostředků pro prostředky Azure Kubernetes Service (AKS) a dostupnost AKS v oblastech Azure.

## <a name="service-quotas-and-limits"></a>Kvóty a omezení služeb

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Zřízená infrastruktura

Na zřízenou infrastrukturu se vztahují všechny další omezení sítě, výpočtů a úložiště. Příslušné limity najdete v tématu [omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> Při upgradu clusteru AKS se dočasně spotřebují další prostředky. Tyto prostředky zahrnují dostupné IP adresy v podsíti virtuální sítě nebo vCPU kvótu virtuálních počítačů. Pokud používáte kontejnery Windows serveru (aktuálně ve verzi Preview v AKS), jediný doporučený přístup k použití nejnovějších aktualizací uzlů je provedení operace upgradu. Selhání procesu upgradu clusteru může znamenat, že nemáte dostupný adresní prostor IP adres ani vCPU kvótu pro zpracování těchto dočasných prostředků. Další informace o procesu upgradu uzlů Windows serveru najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Omezené velikosti virtuálních počítačů

Každý uzel v clusteru AKS obsahuje pevné množství výpočetních prostředků, jako je vCPU a paměť. Pokud uzel AKS obsahuje nedostatečné výpočetní prostředky, mohou se stát, že v některých případech nemusí fungovat správně. Aby bylo zajištěno, že požadované *Kube* a vaše aplikace budou spolehlivě plánovány, **nepoužívejte následující SKU virtuálních počítačů v AKS**:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Další informace o typech virtuálních počítačů a jejich výpočetních prostředcích najdete v tématu [velikosti virtuálních počítačů v Azure][vm-skus].

## <a name="region-availability"></a>Dostupnost v oblastech

Nejnovější seznam, kde můžete nasadit a spouštět clustery, najdete v tématu [dostupnost oblasti AKS][region-availability].

## <a name="next-steps"></a>Další kroky

Některé výchozí limity a kvóty je možné zvýšit. Pokud váš prostředek podporuje zvýšení, požádejte o zvýšení žádosti o [podporu Azure][azure-support] (pro **typ problému**vyberte možnost **kvóta**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
