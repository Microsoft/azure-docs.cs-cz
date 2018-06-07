---
title: Velikost virtuálního počítače s Windows v Azure | Microsoft Docs
description: Obsahuje seznam různých velikostí, které jsou k dispozici pro virtuální počítače s Windows v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/22/2018
ms.author: jonbeck
ms.openlocfilehash: d4e1b56ba695f3305d1be023c4352a3c4bcfe27d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658345"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Velikosti pro virtuální počítače s Windows v Azure

Tento článek popisuje dostupné velikosti a možnosti pro virtuální počítače Azure, které můžete použít ke spuštění aplikace pro Windows a úlohy. Je také důležité informace o nasazení znát při plánování použití těchto prostředků.  Tento článek je také k dispozici pro [virtuální počítače s Linuxem](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Typ                     | Velikosti           |    Popis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Obecné účely](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2 | Vyvážený poměr procesorů k paměti. Ideální pro testování a vývoj, malé a střední databáze a webové servery s nízkým a středním provozem. |
| [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)        | Fsv2 služby Fs, F             | Vysoký poměr procesorů k paměti. Vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery.        |
| [Optimalizované z hlediska paměti](../virtual-machines-windows-sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Vysoký poměr paměť procesoru. Velmi vhodné pro servery relačních databází, střední a velké mezipaměti a analýzu v paměti.                 |
| [Optimalizované z hlediska úložiště](../virtual-machines-windows-sizes-storage.md)        | Ls                | Vysoká propustnost disku a V/V. Ideální pro databáze NoSQL, SQL a velké objemy dat.                                                         |
| [GPU](sizes-gpu.md)            | VS, NC, NCv2, NCv3, ND            | Specializované virtuální počítače cílené pro velkou grafické vykreslování a úpravy videa, jakož i model školení a inferencing (a) s hloubkovým učení. K dispozici jeden nebo více grafickými procesory.       |
| [Vysokovýkonné výpočetní prostředí](sizes-hpc.md) | H       | Naše nejrychlejší a procesorově nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). 


<br> 

- Informace o cenách různých velikostí najdete v tématu [ceny služeb Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Obecná omezení na virtuálních počítačích Azure najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](../../azure-subscription-service-limits.md).
- Náklady na úložiště se počítají samostatně na základě využitých stránek v účtu úložiště. Podrobnosti najdete [Azure Storage – ceny](https://azure.microsoft.com/pricing/details/storage/).
- Další informace o [Azure výpočetní jednotky (ACU)](acu.md) můžete porovnat výpočetní výkon v Azure SKU.


## <a name="rest-api"></a>REST API

Informace o používání rozhraní API REST k dotazu pro velikosti virtuálních počítačů naleznete v následujících tématech:

- [Seznam dostupných velikostí virtuálních počítačů pro změnu velikosti](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Seznam dostupných velikostí virtuálních počítačů pro předplatné](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall)
- [Seznam dostupných velikostí virtuálních počítačů v nastavení dostupnosti](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Další informace o [Azure výpočetní jednotky (ACU)](acu.md) můžete porovnat výpočetní výkon v Azure SKU.

## <a name="benchmark-scores"></a>Výsledky srovnávacích testů

Další informace o výpočetní výkon virtuálních počítačů Windows pomocí [CoreMark srovnávacího testu skóre](compute-benchmark-scores.md).

## <a name="next-steps"></a>Další postup

Další informace o různých velikosti virtuálních počítačů, které jsou k dispozici:
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](../virtual-machines-windows-sizes-memory.md)
- [Optimalizované z hlediska úložiště](../virtual-machines-windows-sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- Zkontrolujte [předchozí generace](sizes-previous-gen.md) stránky pro standardní A Dv1 (D1 4 a D11 14 v1) a řady A8-A11




