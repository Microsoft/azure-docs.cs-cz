---
title: Mv2-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady Mv2-Series.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: caa80443a189874bfd699dba412fb749c8711556
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493394"
---
# <a name="mv2-series"></a>Mv2-Series

Mv2-Series funkce s vysokou propustností a nízkou latencí běžící na procesorech Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) se základní frekvencí 2,5 GHz a maximální frekvencí 3,8 GHz. Všechny velikosti virtuálních počítačů řady Mv2-Series můžou využívat trvalé disky úrovně Standard i Premium. Instance Mv2-Series jsou paměťové optimalizované velikosti virtuálních počítačů, které poskytují bezkonkurenční výpočetní výkon pro podporu rozsáhlých databází a úloh s vysokým výkonem, které jsou ideální pro servery relačních databází, velké mezipaměti a v paměti. Analytics.

Funkce virtuálního počítače Mv2-Series Intel® technologie Hyper-Threading

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Akcelerátor zápisu: [podporováno](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4 096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16 000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4 096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16 000 |
| Standard_M416ms_v2<sup>1, 2</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1, 2</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |

<sup>1</sup> virtuální počítače řady Mv2-Series jsou pouze 2. generace. Pokud používáte Linux, přečtěte si téma [Podpora pro virtuální počítače generace 2 v Azure](/linux/generation-2.md) , kde najdete pokyny, jak najít a vybrat obrázek.

<sup>2</sup> pro M416ms_v2 a velikosti M416s_v2 si všimněte, že je k dispozici pouze počáteční podpora pro následující obrázek: "GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 for SAP Applications".

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