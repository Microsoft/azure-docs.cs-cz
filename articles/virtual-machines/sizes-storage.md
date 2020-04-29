---
title: Velikosti virtuálních počítačů Azure – Storage | Microsoft Docs
description: Obsahuje seznam různých velikostí optimalizovaných pro úložiště, které jsou dostupné pro virtuální počítače v Azure. Uvádí informace o počtu vCPU, datových discích a síťových adaptérů a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77913332"
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
