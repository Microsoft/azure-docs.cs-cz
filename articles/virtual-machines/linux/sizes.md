---
title: Velikosti virtuálních počítačů Linux v Azure
description: Zobrazuje seznam různých velikostí dostupných pro virtuální počítače se systémem Linux v Azure.
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
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161112"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Velikosti pro virtuální počítače se systémem Linux v Azure

Tento článek popisuje dostupné velikosti a možnosti pro virtuální počítače Azure, které můžete použít ke spouštění aplikací a úloh pro Linux. Nabízí také informace o nasazení, které vám pomůžeme při plánování používání těchto prostředků. Tento článek je k dispozici také pro [virtuální počítače s Windows](../windows/sizes.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json).

| Typ | Velikosti | Popis |
|------|-------|-------------|
| [Obecné účely](../sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2  | Poměr vyrovnaných PROCESORů k paměti. Ideální pro testování a vývoj, malé až střední databáze a webové servery s nízkým a středním provozem. |
| [Optimalizované z hlediska výpočetních služeb](../sizes-compute.md) | Fsv2 | Vysoký poměr PROCESORů k paměti. Vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery. |
| [Optimalizované z hlediska paměti](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2 | Vysoký poměr paměti k procesoru. Skvělé pro servery relačních databází, střední až velké mezipaměti a analýzu v paměti.                 |
| [Optimalizované z hlediska úložiště](../sizes-storage.md) | Lsv2 | Vysoká propustnost disku a vstupně-výstupní operace jsou ideální pro velké objemy dat, SQL, databáze NoSQL, datové sklady a velké transakční databáze.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (Preview), NV, NVv3, NVv4 | Specializované virtuální počítače určené pro náročné vykreslování grafiky a úpravy videa a také školení modelů a Inferencing (ND) s hloubkovým učením. K dispozici s jedním nebo více grafickými procesory. |
| [Vysokovýkonné výpočetní prostředí](../sizes-hpc.md) | HBv2, HC, H | Naši nejrychlejší a nejvíc nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). |

- Informace o cenách různých velikostí najdete v článku [Virtual Machines ceny](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Dostupnost velikostí virtuálních počítačů v oblastech Azure najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).
- Obecné omezení virtuálních počítačů Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-subscription-service-limits.md).
- Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](../acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

## <a name="rest-api"></a>REST API

Informace o použití REST API k dotazování na velikosti virtuálních počítačů najdete v následujících tématech:

- [Vypsat dostupné velikosti virtuálních počítačů pro změnu velikosti](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Zobrazit seznam dostupných velikostí virtuálních počítačů pro předplatné](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Zobrazit seznam dostupných velikostí virtuálních počítačů ve skupině dostupnosti](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](../acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

## <a name="benchmark-scores"></a>Výsledky srovnávacích testů

Přečtěte si další informace o výpočetním výkonu pro virtuální počítače se systémem Linux pomocí [skóre srovnávacího testu CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Další kroky

Další informace o různých velikostech virtuálních počítačů, které jsou k dispozici:

- [Obecné účely](../sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](../sizes-compute.md)
- [Optimalizované z hlediska paměti](../sizes-memory.md)
- [Optimalizované z hlediska úložiště](../sizes-storage.md)
- [GPU](../sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](../sizes-hpc.md)
- Podívejte se na stránku [předchozí generace](../sizes-previous-gen.md) pro standardní, Dv1 (D1-4 a D11-14 V1) a řady A8-A11.