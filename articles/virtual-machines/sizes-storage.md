---
title: Velikosti virtuálních zařízení Azure – úložiště | Dokumenty společnosti Microsoft
description: Uvádí různé velikosti optimalizované pro úložiště, které jsou k dispozici pro virtuální počítače v Azure. Uvádí informace o počtu virtuálních procesorů, datových disků a síťových síťových připojení, jakož i propustnost úložiště a šířku pásma sítě pro velikosti v této řadě.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913332"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Velikosti virtuálních strojů optimalizované pro úložiště

Velikosti virtuálních počítačů optimalizované pro úložiště nabízejí vysokou propustnost disku a vi a jsou ideální pro big data, SQL, NoSQL databáze, datové sklady a velké transakční databáze.  Příklady zahrnují Cassandra, MongoDB, Cloudera a Redis. Tento článek obsahuje informace o počtu virtuálních procesorů, datových disků a síťových připojení, jakož i o propustnosti místního úložiště a šířce pásma sítě pro každou optimalizovanou velikost.

Řada [Lsv2](lsv2-series.md) je vybavena vysokou propustností, nízkou latencí, přímo mapovaným místním úložištěm NVMe běžícím na [procesoru AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) s podporou všech jader 2,55 GHz a maximálním nárůstem 3,0 GHz. Virtuální počítače řady Lsv2 jsou dodávány ve velikostech od 8 do 80 virtuálních procesorů v simultánní konfiguraci s více vlákny.  K dispozici je 8 GiB paměti na virtuální procesor a jedno 1.92TB NVMe SSD M.2 zařízení na 8 virtuálních procesorů, s až 19.2TB (10x1.92TB) k dispozici na L80s v2.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.

Přečtěte si, jak optimalizovat výkon na virtuálních počítačích řady Lsv2 pro [Windows](windows/storage-performance.md) nebo [Linux](linux/storage-performance.md).
