---
title: Řada HB - Virtuální počítače Azure
description: Specifikace pro virtuální počítače řady HB.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 600f10e81742e9bb66c800b747fd7b2dc062754d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164827"
---
# <a name="hb-series"></a>Řada HB

Virtuální počítače řady HB jsou optimalizované pro aplikace řízené šířkou pásma paměti, jako je dynamika tekutin, explicitní analýza konečných prvků a modelování počasí. Virtuální počítače HB jsou vybaveny procesorovými jádry 60 AMD EPYC 7551, 4 GB paměti RAM na jádro procesoru a bez simultánního multithreadingu. Virtuální počítač HB poskytuje šířku pásma paměti až 260 GB/s.

ACU: 199-216

Úložiště Premium: Podporováno

Ukládání úložiště Premium: Podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Šířka pásma paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Jednojádrová frekvence (GHz, špička) | Výkon RDMA (Gb/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální síťové karty Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Všechny | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.