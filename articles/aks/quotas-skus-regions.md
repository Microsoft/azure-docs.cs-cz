---
title: Omezení pro prostředky, SKU, oblasti
titleSuffix: Azure Kubernetes Service
description: Přečtěte si o výchozích kvótách, omezených velikostech SKU virtuálních počítačů uzlů a dostupnosti oblastí služby Azure Kubernetes (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 3e1e74834153584525d2093d2a1bb8ba8e991e5a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011460"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kvóty, omezení velikosti virtuálních počítačů a dostupnost oblastí ve službě Azure Kubernetes Service (AKS)

Všechny služby Azure nastavily výchozí limity a kvóty pro prostředky a funkce, včetně omezení využití pro určité skladové položky virtuálních počítačů (VM).

Tento článek podrobně popisuje výchozí omezení prostředků pro prostředky Azure Kubernetes Service (AKS) a dostupnost AKS v oblastech Azure.

## <a name="service-quotas-and-limits"></a>Kvóty a omezení služeb

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Zřízená infrastruktura

Na zřízenou infrastrukturu se vztahují všechny další omezení sítě, výpočtů a úložiště. Příslušné limity najdete v tématu [omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> Když upgradujete cluster AKS, dočasně se spotřebují další prostředky. Mezi tyto prostředky patří dostupné IP adresy v podsíti virtuální sítě nebo v vCPU kvótě virtuálního počítače. 
>
> Pro kontejnery Windows serveru můžete provést operaci upgradu a použít nejnovější aktualizace uzlů. Pokud nemáte dostupný adresní prostor IP adres nebo vCPU kvótu pro zpracování těchto dočasných prostředků, proces upgradu clusteru se nezdaří. Další informace o procesu upgradu uzlů Windows serveru najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

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

Můžete zvýšit určitá výchozí omezení a kvóty. Pokud váš prostředek podporuje zvýšení, požádejte o zvýšení žádosti o [podporu Azure][azure-support] (pro **typ problému** vyberte možnost **kvóta**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
