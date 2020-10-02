---
title: Av2-series
description: Specifikace pro virtuální počítače řady Av2-Series.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 65aab240dd0be97aafa370883aa953f8eb766cf2
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650744"
---
# <a name="av2-series"></a>Av2-series

Virtuální počítače řady Av2-Series je možné nasadit na nejrůznější typy hardwaru a procesory. Virtuální počítače řady Av2-Series mají pro úlohy na vstupní úrovni, jako je vývoj a testování, nejvhodnější konfigurace výkonu a paměti procesoru. Velikost je omezená, aby poskytovala konzistentní výkon procesoru pro spuštěnou instanci bez ohledu na hardware, na kterém je nasazený. Pokud chcete zjistit fyzický hardware, na kterém je virtuální počítač dané velikosti nasazený, zadejte dotaz na virtuální hardware přímo z virtuálního počítače. Příklady případů použití zahrnují vývojové a testovací servery, webové servery s nízkým provozem, malé až střední databáze, testování konceptů a úložiště kódu.

[ACU](acu.md): 100<br>
[Premium Storage](premium-storage-performance.md): nepodporováno <br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno <br>
[Migrace za provozu](maintenance-and-updates.md): podporováno <br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno <br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 <br>
<br>

| Velikost | Virtuální jádro | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet | Očekávaná šířka pásma sítě (MB/s)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Další velikosti a informace

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

Cenová kalkulačka: [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/)

Další informace o typech disků: [typy disků](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
