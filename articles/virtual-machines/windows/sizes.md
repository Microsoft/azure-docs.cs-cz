---
title: Velikosti virtuálních počítačů s Windows v Azure
description: Zobrazuje seznam různých velikostí dostupných pro virtuální počítače s Windows v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 755ea3ea3c246a1fa9fb7d514569c6fcecc0c04c
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598710"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Velikosti virtuálních počítačů s Windows v Azure

Tento článek popisuje dostupné velikosti a možnosti pro virtuální počítače Azure, které můžete použít ke spouštění aplikací a úloh pro Windows. Nabízí také informace o nasazení, které vám pomůžeme při plánování používání těchto prostředků.  Tento článek je k dispozici také pro [virtuální počítače se systémem Linux](../linux/sizes.md).

| Typ | Velikosti | Popis |
|------|-------|-------------|
| [Obecné účely](../sizes-general.md) | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC | Poměr vyrovnaných PROCESORů k paměti. Ideální pro testování a vývoj, malé až střední databáze a webové servery s nízkým a středním provozem. |
| [Optimalizované z hlediska výpočetních služeb](../sizes-compute.md) | Fsv2 | Vysoký poměr PROCESORů k paměti. Vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery. |
| [Optimalizované z hlediska paměti](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2 | Vysoký poměr paměti k procesoru. Skvělé pro servery relačních databází, střední až velké mezipaměti a analýzu v paměti. |
| [Optimalizované z hlediska úložiště](../sizes-storage.md)  | Lsv2 | Vysoká propustnost disku a vstupně-výstupní operace jsou ideální pro velké objemy dat, SQL, databáze NoSQL, datové sklady a velké transakční databáze.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (Preview), NV, NVv3, NVv4 | Specializované virtuální počítače určené pro náročné vykreslování grafiky a úpravy videa a také školení modelů a Inferencing (ND) s hloubkovým učením. K dispozici s jedním nebo více grafickými procesory. |
| [Vysokovýkonné výpočetní prostředí](../sizes-hpc.md) | NEJENOM, HC, H | Naši nejrychlejší a nejvíc nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). |

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
