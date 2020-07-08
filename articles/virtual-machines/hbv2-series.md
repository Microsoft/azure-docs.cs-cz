---
title: HBv2-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady HBv2-Series.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: 730065333fde7431b7018ef0a6e614e12727d7ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84673849"
---
# <a name="hbv2-series"></a>Řada HBv2

Virtuální počítače řady HBv2-Series jsou optimalizované pro aplikace, které jsou založené na šířce pásma paměti, jako je například kapalinová dynamika, analýza omezeného prvku a simulace zásobníku. Virtuální počítače s HBv2 120 AMD EPYC 7742 procesory, 4 GB paměti RAM na jádro procesoru a žádné souběžné multithreadingy. Každý virtuální počítač HBv2 poskytuje šířku pásma až 340 GB/s a až 4 teraFLOPSy FP64 Compute.

Premium Storage: podporováno

Migrace za provozu: nepodporováno

Aktualizace pro zachování paměti: nepodporováno

| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Propustnost paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Frekvence s jedním jádrem (GHz, špička) | Výkon RDMA (GB/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální počet síťových karet sítě Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | Vše | 480 + 960 | 8 | 1 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.