---
title: Řada M – virtuální počítače Azure
description: Specifikace pro virtuální chod řady M.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/05/2019
ms.author: lahugh
ms.openlocfilehash: e8952218675493710cbdd1f92661e9a9c077f34c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164045"
---
# <a name="m-series"></a>Řada M

Řada M nabízí vysoký počet virtuálních procesorů (až 128 virtuálních procesorů) a velké množství paměti (až 3,8 TiB). Je také ideální pro extrémně velké databáze nebo jiné aplikace, které těží z vysokého počtu virtuálních procesorů a velkého množství paměti. Velikosti řady M jsou založeny na procesoru Intel® Xeon® Procesor E7-8890 v3 při 2,50 GHz

Funkce virtuálního rozhraní M-series Intel® Technologie hyperthreadingu

ACU: 160–180

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

Akcelerátor zápisu: [Podporováno](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a protemplovaná propustnost úložiště: IOPS/MBps (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|
| <sup>2.</sup> Standard_M8ms       | 8   | 218.75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4/2000  |
| <sup>2. Standard_M16ms</sup>      | 16  | 437.5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8/4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| <sup>2. Standard_M32ms</sup>      | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M64s                    | 64  | 1024   | 2 048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ls                   | 64  | 512    | 2 048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ms <sup>3</sup>      | 64  | 1 792   | 2 048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| <sup>1. Standard_M128s</sup>      | 128 | 2 048   | 4 096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M128ms <sup>1,2,3.</sup> | 128 | 3892   | 4 096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M64                     | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M64m                    | 64  | 1 792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M128 <sup>1</sup>       | 128 | 2 048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |
| Standard_M128m <sup>1</sup>      | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |

<sup>1</sup> Více než 64 virtuálních procesorů vyžaduje jeden z těchto podporovaných hostovacích operačních systémů: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 a Red Hat Enterprise Linux, CentOS 7.3 nebo Oracle Linux 7.3 s LIS 4.2.1.

<sup>2</sup> K dispozici jsou omezené velikosti jader.

<sup>3</sup> Instance je izolována na hardware vyhrazený pro jednoho zákazníka.

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
