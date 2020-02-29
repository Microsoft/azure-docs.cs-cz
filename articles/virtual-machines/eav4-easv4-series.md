---
title: Eav4-Series a Easv4-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače s Eav4 a Easv4-Series.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: e05a2b97a25ce4cab7d619f4b62d405765cb08db
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164385"
---
# <a name="eav4-and-easv4-series"></a>Řada Eav4 a Easv4

Řady Eav4-Series a Easv4-Series využívají procesor AMD 2.35 EPYC<sup>TM</sup> 7452 v konfiguraci s více vlákny s až 256mb mezipamětí L3 a zvyšují možnosti spouštění většiny paměťově optimalizovaných úloh. Řady Eav4-Series a Easv4-Series mají stejnou konfiguraci paměti a disku jako Ev3 & Esv3-Series.

## <a name="eav4-series"></a>Eav4-Series

ACU: 230-260

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

Velikosti řady Eav4-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452 v 2.35 GHz, který může dosáhnout zvýšení maximální frekvence 3.35 GHz a používání jednotky SSD úrovně Premium. Velikosti řady Eav4-Series jsou ideální pro podnikové aplikace náročné na paměť. Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disk SSD úrovně Premium, použijte velikosti řady Easv4-Series. Měřiče cen a účtování pro velikosti Easv4 jsou stejné jako pro Eav3-Series.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
| -----|-----|-----|-----|-----|-----|-----|
| Standardní\_E2A\_v4|2|16|50|4|3000 / 46 / 23|2 / 1 000 |
| Standardní\_e4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2 000 |
| Standardní\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| Standardní\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| Standardní\_E20a\_v4|20|160|500|32|30000/468/234|8 / 10000 |
| Standardní\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16 000 |
| Standardní\_E48a\_v4 <sup>**</sup> |48|384|1200|32| | |
| Standardní\_E64a\_v4 <sup>**</sup> |64|512|1600|32| | |
| Standardní\_E96a\_v4 <sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>  Tyto velikosti jsou ve verzi Preview. Pokud máte zájem o vyzkoušení těchto větších velikostí, zaregistrujte se na [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="easv4-series"></a>Easv4-Series

ACU: 230-260

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

Velikosti řady Easv4-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452 v 2.35 GHz, který může dosáhnout zvýšení maximální frekvence 3.35 GHz a používání jednotky SSD úrovně Premium. Velikosti řady Easv4-Series jsou ideální pro podnikové aplikace náročné na paměť.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 / 1 000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400/96|2 / 2 000 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 / 16 000 |
| Standard_E48as_v4 <sup>**</sup> |48|384|768|32|  | |
| Standard_E64as_v4 <sup>**</sup> |64|512|1024|32| | |
| Standard_E96as_v4 <sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>  Tyto velikosti jsou ve verzi Preview. Pokud máte zájem o vyzkoušení těchto větších velikostí, zaregistrujte se na [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

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
