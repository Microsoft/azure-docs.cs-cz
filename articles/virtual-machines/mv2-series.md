---
title: Řada Mv2 – virtuální počítače Azure
description: Specifikace pro virtuální chod řady Mv2.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 04/07/2020
ms.author: lahugh
ms.openlocfilehash: 764dc93608ae3b8882b7048a722c6d3415cbc644
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885782"
---
# <a name="mv2-series"></a>Řada Mv2

Řada Mv2 je vybavena vysokou propustností, platformou s nízkou latencí, která běží na hyper-threaded Intel® Xeon® Platinum 8180M 2.5GHz (Skylake) procesor s základní frekvencí jádra 2,5 GHz a maximální turbo frekvencí 3,8 GHz. Všechny velikosti virtuálních počítačů řady Mv2 mohou používat standardní i prémiové trvalé disky. Instance řady Mv2 jsou velikosti virtuálních počítače optimalizované pro paměť, které poskytují bezkonkurenční výpočetní výkon pro podporu velkých databází a úloh v paměti s vysokým poměrem paměti k procesoru, který je ideální pro relační databázové servery, velké mezipaměti a analýzy v paměti.

Funkce virtuálního rozhraní Mv2 intel® technologie hyper-threadingu

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

Akcelerátor zápisu: [Podporováno](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových připojení / Očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4 096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |
| <sup>1.</sup> Standard_M208s_v2 | 208 | 2850 | 4 096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Virtuální klíče řady Mv2 jsou pouze generace 2. Pokud používáte Linux, přečtěte si informace o tom, jak najít a vybrat bitovou kopii nawebu [podpory virtuálních počítačích generace 2 v Azure.](./linux/generation-2.md) Pokud používáte Windows, přečtěte si informace o tom, jak najít a vybrat bitovou kopii nawebu [podpory virtuálních počítačích generace 2 v Azure.](./windows/generation-2.md) Minimální verze operačního systému požadované pro všechny čtyři různé typy virtuálních počítačů řady Mv2, jako jsou:

- Windows Server 2019 nebo novější
- SUSE Linux Enterprise Server 12 SP4 a novější nebo SUSE Linux Enterprise Server 15 SP1 a novější
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 nebo novější 
- Oracle Enterprise Linux 7.7 nebo novější



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
