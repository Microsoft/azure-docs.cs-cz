---
title: Velikosti virtuálních počítačů Azure – Storage | Microsoft Docs
description: Obsahuje seznam různých velikostí optimalizovaných pro úložiště, které jsou dostupné pro virtuální počítače v Azure. Uvádí informace o počtu vCPU, datových discích a síťových adaptérů a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
ms.subservice: sizes
documentationcenter: ''
author: sasha-melamed
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 028de173c5cda1e95de9dd64d6ccd2a6b0ad7eb2
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84674309"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Velikosti virtuálních počítačů optimalizované pro úložiště

Velikosti virtuálních počítačů optimalizované pro úložiště nabízejí vysokou propustnost disku a vstupně-výstupní operace a jsou ideální pro velké objemy dat, SQL, databáze NoSQL, datové sklady a velké transakční databáze.  Mezi příklady patří Cassandra, MongoDB, Cloudera a Redis. Tento článek poskytuje informace o počtu vCPU, datových discích a síťových adaptérů a propustnosti místního úložiště a šířce pásma sítě pro každou optimalizovanou velikost.

Funkce [Lsv2-Series](lsv2-series.md) s vysokou propustností, nízkou latencí a přímo namapovaným místním úložištěm NVMe běžícím na [procesorech AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) se všemi základními zvýšeními úrovně 2.55 GHz a maximálním zvýšením velikosti 3,0 GHz. Virtuální počítače řady Lsv2-Series přicházejí v případě souběžné konfigurace s více vlákny do velikosti od 8 do 80 vCPU.  K dispozici je 8 GiB paměti na vCPU a jedno 1.92 TB NVMe SSD M. 2 zařízení na 8 vCPU a až 19.2 TB (10x 1.92 TB), které je dostupné na L80s v2.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

Naučte se optimalizovat výkon virtuálních počítačů řady Lsv2-Series pro [Windows](windows/storage-performance.md) nebo [Linux](linux/storage-performance.md).
