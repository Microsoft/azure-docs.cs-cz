---
title: Velikosti virtuálních počítačů s Linuxem v Azure
description: Uvádí různé velikosti, které jsou k dispozici pro virtuální počítače s Linuxem v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 46fad0dd2e39f1f563ed248458da622ad83f061e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161112"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Velikosti virtuálních počítačů s Linuxem v Azure

Tento článek popisuje dostupné velikosti a možnosti pro virtuální počítače Azure, které můžete použít ke spuštění linuxových aplikací a úloh. Poskytuje také důležité informace o nasazení, které je třeba znát, když plánujete použít tyto prostředky. Tento článek je k dispozici také pro [virtuální počítače s Windows](../windows/sizes.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json).

| Typ | Velikosti | Popis |
|------|-------|-------------|
| [Obecné účely](../sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2  | Vyvážený poměr procesoru k paměti. Tato možnost je ideální pro testování a vývoj, malé až střední databáze a webové servery s nízkým až středním provozem. |
| [Optimalizované z hlediska výpočetních služeb](../sizes-compute.md) | Fsv2 | Vysoký poměr procesoru k paměti. Vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery. |
| [Optimalizované z hlediska paměti](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2 | Vysoký poměr paměti k procesoru. Jsou velmi vhodné pro servery s relační databází, střední a velké mezipaměti a analýzu v paměti.                 |
| [Optimalizované z hlediska úložiště](../sizes-storage.md) | Lsv2 | Vysoká propustnost disku a IO ideální pro big data, SQL, NoSQL databáze, datové sklady a velké transakční databáze.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (náhled), NV, NVv3, NVv4 | Specializované virtuální stroje určené pro těžké grafické vykreslování a střih videa, stejně jako školení a odvozování modelů (ND) s hlubokým učením. K dispozici s jedním nebo více gpu. |
| [Vysokovýkonné výpočetní prostředí](../sizes-hpc.md) | HB, HBv2, HC, H | Naše nejrychlejší a nejvýkonnější virtuální počítače cpu s volitelným síťovým rozhraním s vysokou propustností (RDMA). |

- Informace o cenách různých velikostí najdete v [tématu Ceny virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Dostupnost velikostí virtuálních počítačů v oblastech Azure najdete v [tématu Produkty dostupné podle oblasti](https://azure.microsoft.com/regions/services/).
- Obecné limity na virtuální chod Azure najdete v [tématu Limity předplatného a služeb Azure, kvóty a omezení](../../azure-subscription-service-limits.md).
- Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](../acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.

## <a name="rest-api"></a>REST API

Informace o použití rozhraní REST API k dotazování na velikosti virtuálních počítačů najdete v následujících tématech:

- [Seznam dostupných velikostí virtuálních strojů pro změna velikosti](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Seznam dostupných velikostí virtuálních strojů pro předplatné](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Seznam dostupných velikostí virtuálních strojů v sadě dostupnosti](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](../acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.

## <a name="benchmark-scores"></a>Výsledky srovnávacích testů

Přečtěte si další informace o výpočetním výkonu pro virtuální počítače s Linuxem pomocí [skóre benchmarku CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Další kroky

Další informace o různých velikostech virtuálních počítače, které jsou k dispozici:

- [Obecné účely](../sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](../sizes-compute.md)
- [Optimalizované z hlediska paměti](../sizes-memory.md)
- [Optimalizované z hlediska úložiště](../sizes-storage.md)
- [GPU](../sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](../sizes-hpc.md)
- Podívejte se na stránku [Předchozí generace](../sizes-previous-gen.md) pro standardy A, Dv1 (D1-4 a D11-14 v1) a řady A8-A11