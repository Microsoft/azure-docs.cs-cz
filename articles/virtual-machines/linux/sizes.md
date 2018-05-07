---
title: Velikosti virtuálního počítače s Linuxem v Azure | Microsoft Docs
description: Obsahuje seznam různých velikostí, které jsou k dispozici pro virtuální počítače s Linuxem v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jonbeck
ms.openlocfilehash: 2480a48670646d13f55b405d2594c5937d1b61ba
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Velikosti pro virtuální počítače s Linuxem v Azure
Tento článek popisuje dostupné velikosti a možnosti pro virtuální počítače Azure, které můžete použít ke spuštění vaše Linux aplikace a úlohy. Je také důležité informace o nasazení znát při plánování použití těchto prostředků. Tento článek je také k dispozici pro [virtuální počítače s Windows](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| Typ                     | Velikosti           |    Popis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Obecné účely](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7  | Vyvážený poměr procesorů k paměti. Ideální pro testování a vývoj, malé a střední databáze a webové servery s nízkým a středním provozem. |
| [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)        | Fsv2 služby Fs, F             | Vysoký poměr procesorů k paměti. Vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery.        |
| [Optimalizované z hlediska paměti](sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Vysoký poměr paměť procesoru. Velmi vhodné pro servery relačních databází, střední a velké mezipaměti a analýzu v paměti.                 |
| [Optimalizované z hlediska úložiště](sizes-storage.md)        | Ls                | Vysoká propustnost disku a V/V. Ideální pro databáze NoSQL, SQL a velké objemy dat.                                                         |
| [GPU](sizes-gpu.md)            | VS, NC, NCv2, NCv3, ND            | Specializované virtuální počítače cílené pro velkou grafické vykreslování a úpravy videa, jakož i model školení a inferencing (a) s hloubkovým učení. K dispozici jeden nebo více grafickými procesory.       |
| [Vysokovýkonné výpočetní prostředí](sizes-hpc.md) | H, A8-11          | Naše nejrychlejší a procesorově nejvýkonnější virtuální počítače s volitelnými síťovými rozhraními s vysokou propustností (RDMA). 

<br>

- Informace o cenách různých velikostí najdete v tématu [ceny služeb Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Dostupnost velikostí virtuálních počítačů v oblastech Azure, najdete v části [produkty podle oblasti](https://azure.microsoft.com/regions/services/).
- Obecná omezení na virtuálních počítačích Azure najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](../../azure-subscription-service-limits.md).
- Další informace o [Azure výpočetní jednotky (ACU)](acu.md) můžete porovnat výpočetní výkon v Azure SKU.


## <a name="rest-api"></a>REST API

Informace o používání rozhraní API REST k dotazu pro velikosti virtuálních počítačů naleznete v následujících tématech:

- [Seznam dostupných velikostí virtuálních počítačů pro změnu velikosti](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-for-resizing)
- [Seznam dostupných velikostí virtuálních počítačů pro předplatné](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)
- [Seznam dostupných velikostí virtuálních počítačů v nastavení dostupnosti](
https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-availability-set)

## <a name="acu"></a>ACU

Další informace o [Azure výpočetní jednotky (ACU)](acu.md) můžete porovnat výpočetní výkon v Azure SKU.

## <a name="benchmark-scores"></a>Výsledky srovnávacích testů

Další informace o výpočetní výkon pro virtuální počítače s Linuxem pomocí [CoreMark srovnávacího testu skóre](compute-benchmark-scores.md).

## <a name="next-steps"></a>Další postup

Další informace o různých velikosti virtuálních počítačů, které jsou k dispozici:
- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)



