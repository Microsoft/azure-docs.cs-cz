---
title: Velikosti virtuálních počítačů v Azure
description: Zobrazuje seznam různých velikostí dostupných pro virtuální počítače v Azure.
author: ju-shim
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 16d6bfb34489df49ffccb87822ce885d8aa42d37
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248868"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Velikosti virtuálních počítačů s Windows v Azure

Tento článek popisuje dostupné velikosti a možnosti pro virtuální počítače Azure, které můžete použít ke spouštění aplikací a úloh pro Windows. Nabízí také informace o nasazení, které vám pomůžeme při plánování používání těchto prostředků.  Tento článek se týká virtuálních počítačů se systémem Windows i Linux.

| Typ | Velikosti | Popis |
|------|-------|-------------|
| [Obecné účely](../sizes-general.md) | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Ddv4, Ddsv4 | Poměr vyrovnaných PROCESORů k paměti. Tato možnost je ideální pro testování a vývoj, malé až střední databáze a webové servery s nízkým až středním provozem. |
| [Optimalizované z hlediska výpočetních služeb](../sizes-compute.md) | Fsv2 | Vysoký poměr PROCESORů k paměti. Vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery. |
| [Optimalizované z hlediska paměti](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Vysoký poměr paměti k procesoru. Jsou velmi vhodné pro servery s relační databází, střední a velké mezipaměti a analýzu v paměti. |
| [Optimalizované z hlediska úložiště](../sizes-storage.md)  | Lsv2 | Vysoká propustnost disku a vstupně-výstupní operace jsou ideální pro velké objemy dat, SQL, databáze NoSQL, datové sklady a velké transakční databáze.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (Preview), NV, NVv3, NVv4 | Specializované virtuální počítače určené pro náročné vykreslování grafiky a úpravy videa a také školení modelů a Inferencing (ND) s hloubkovým učením. K dispozici s jedním nebo více grafickými procesory. |
| [Vysokovýkonné výpočetní prostředí](../sizes-hpc.md) | HBv2, HC, H | Naši nejrychlejší a nejvíc nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). |

- Informace o cenách různých velikostí najdete v článku [Virtual Machines ceny](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows).
- Obecné omezení virtuálních počítačů Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-subscription-service-limits.md).
- Náklady na úložiště se počítají samostatně na základě využitých stránek v účtu úložiště. Podrobnosti [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/).
- Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](../acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

## <a name="rest-api"></a>REST API

Informace o použití REST API k dotazování na velikosti virtuálních počítačů najdete v následujících tématech:

- [Vypsat dostupné velikosti virtuálních počítačů pro změnu velikosti](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Zobrazit seznam dostupných velikostí virtuálních počítačů pro předplatné](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Zobrazit seznam dostupných velikostí virtuálních počítačů ve skupině dostupnosti](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](../acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

## <a name="benchmark-scores"></a>Výsledky srovnávacích testů

Přečtěte si další informace o výpočetním výkonu pro virtuální počítače s Windows pomocí [skóre srovnávacího testu CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Další kroky

Další informace o různých velikostech virtuálních počítačů, které jsou k dispozici:

- [Obecné účely](../sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](../sizes-compute.md)
- [Optimalizované z hlediska paměti](../sizes-memory.md)
- [Optimalizované z hlediska úložiště](../sizes-storage.md)
- [Optimalizované z hlediska GPU](../sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](../sizes-hpc.md)
- Podívejte se na stránku [předchozí generace](../sizes-previous-gen.md) pro standardní, Dv1 (D1-4 a D11-14 V1) a řady A8-A11.
