---
title: Dav4 a Dasv4-series – virtuální počítače Azure
description: Specifikace pro virtuální chody řady Dav4 a Dasv4.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 809843bddc4216df4dab44330172d36778248b1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78162175"
---
# <a name="dav4-and-dasv4-series"></a>Řada Dav4 a Dasv4

Dav4-series a Dasv4-series jsou nové velikosti využívající procesor 2.35Ghz EPYC<sup>TM</sup> 7452 společnosti AMD v konfiguraci s více vlákny s až 256 MB mezipaměti L3, která věnuje 8 GB této mezipaměti L3 každému 8 jádrům, což zvyšuje možnosti zákazníků pro spuštění jejich úloh pro obecné účely. Řady Dav4 a Dasv4 mají stejnou konfiguraci paměti a disku jako řada D & Dsv3.

## <a name="dav4-series"></a>Řada Dav4

ACU: 230-260

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

Migrace za provozu: Podporováno

Aktualizace pro zachování paměti: Podporováno

Velikosti řady Dav4 jsou založeny na procesoru 2.35Ghz AMD EPYC<sup>TM</sup> 7452, který může dosáhnout zvýšené maximální frekvence 3.35GHz. Velikosti řady Dav4 nabízejí kombinaci virtuálních procesorů, paměti a dočasného úložiště pro většinu produkčních úloh. Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Chcete-li použít premium SSD, použijte velikosti Dasv4. Měřiče cen a fakturace pro velikosti Dasv4 jsou stejné jako řady Dav4.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet síťových připojení / Očekávaná šířka pásma sítě (Mb/s) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1 000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2 000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 / 16 000 |
| Standard_D48a_v4<sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4<sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4<sup>**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup>Tyto velikosti jsou ve verzi Preview.  Máte-li zájem vyzkoušet tyto větší velikosti, zaregistrujte se na [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dasv4-series"></a>Řada Dasv4

ACU: 230-260

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Migrace za provozu: Podporováno

Aktualizace pro zachování paměti: Podporováno

Velikosti řady Dasv4 jsou založeny na procesoru 2.35Ghz AMD EPYC<sup>TM</sup> 7452, který dokáže dosáhnout zvýšené maximální frekvence 3,35 GHz a používat prémiové SSD. Velikosti řady Dasv4 nabízejí kombinaci virtuálních procesorů, paměti a dočasného úložiště pro většinu produkčních úloh.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových připojení / Očekávaná šířka pásma sítě (Mb/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000 / 32 (50)|3200 / 48|2 / 1 000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400 / 96|2 / 2 000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000 / 510 (800)|51200 / 768|8 / 16 000 |
| Standard_D48as_v4<sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4<sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4<sup>**</sup>|96|384|768|32| | | 

<sup>**</sup>Tyto velikosti jsou ve verzi Preview.  Máte-li zájem vyzkoušet tyto větší velikosti, zaregistrujte se na [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

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
