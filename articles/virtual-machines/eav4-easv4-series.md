---
title: Řada Eav4 a Řada Easv4 – virtuální počítače Azure
description: Specifikace pro virtuální chody řady Eav4 a Easv4.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: e05a2b97a25ce4cab7d619f4b62d405765cb08db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164385"
---
# <a name="eav4-and-easv4-series"></a>Řada Eav4 a Easv4

Řady Eav4 a Easv4 využívají procesor 2.35Ghz EPYC<sup>TM</sup> 7452 společnosti AMD v konfiguraci s více vlákny s mezipamětí L3 až 256 MB, což zvyšuje možnosti pro spuštění většiny úloh optimalizovaných pro paměť. Řady Eav4 a Easv4 mají stejnou konfiguraci paměti a disku jako řada Ev3 & Esv3.

## <a name="eav4-series"></a>Řada Eav4

ACU: 230 - 260

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

Migrace za provozu: Podporováno

Aktualizace pro zachování paměti: Podporováno

Velikosti řady Eav4 jsou založeny na procesoru 2.35Ghz AMD EPYC<sup>TM</sup> 7452, který dokáže dosáhnout zvýšené maximální frekvence 3,35 GHz a používat prémiové SSD. Velikosti řady Eav4 jsou ideální pro podnikové aplikace náročné na paměť. Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Chcete-li použít premium SSD, použijte velikosti řady Easv4. Měřiče cen a fakturace pro velikosti Easv4 jsou stejné jako u řady Eav3.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet síťových připojení / Očekávaná šířka pásma sítě (Mb/s) |
| -----|-----|-----|-----|-----|-----|-----|
| Standardní\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1 000 |
| Standardní\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2 000 |
| Standardní\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| Standardní\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| Standardní\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 / 10000 |
| Standardní\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16 000 |
| Standardní\_E48a\_v4<sup>**</sup> |48|384|1200|32| | |
| Standardní\_E64a\_v4<sup>**</sup> |64|512|1600|32| | |
| Standardní\_E96a\_v4<sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>Tyto velikosti jsou ve verzi Preview. Máte-li zájem vyzkoušet tyto větší velikosti, zaregistrujte se na [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="easv4-series"></a>Řada Easv4

ACU: 230 - 260

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Migrace za provozu: Podporováno

Aktualizace pro zachování paměti: Podporováno

Velikosti řady Easv4 jsou založeny na procesoru 2.35Ghz AMD EPYC<sup>TM</sup> 7452, který dokáže dosáhnout zvýšené maximální frekvence 3,35 GHz a používat prémiové SSD. Velikosti řady Easv4 jsou ideální pro podnikové aplikace náročné na paměť.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových připojení / Očekávaná šířka pásma sítě (Mb/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 / 1 000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 / 2 000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000 / 510 (800)|51200 / 768|8 / 16 000 |
| Standard_E48as_v4<sup>**</sup> |48|384|768|32|  | |
| Standard_E64as_v4<sup>**</sup> |64|512|1024|32| | |
| Standard_E96as_v4<sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>Tyto velikosti jsou ve verzi Preview. Máte-li zájem vyzkoušet tyto větší velikosti, zaregistrujte se na [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

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
