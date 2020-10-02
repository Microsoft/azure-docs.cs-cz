---
title: HC-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady HC-Series.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 9475d3f000bbfd84c16efc080cbbb9df63cac608
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653260"
---
# <a name="hc-series"></a>Řada HC

Virtuální počítače řady HC-Series jsou optimalizované pro aplikace, které jsou založené na hustém výpočtu, jako je například implicitní nekonečná analýza elementu, molekulová dynamika a výpočetovaná chemie. Virtuální počítače HC – funkce 44 Intel Xeon Platinum 8168, 8 GB paměti RAM na jádro procesoru a žádné podprocesy. Platforma Intel Xeon Platinum podporuje bohatě bohatý ekosystém softwaru od společnosti Intel, jako je například knihovna Intel Math kernel a pokročilé funkce vektorového zpracování, jako je například AVX-512.

Virtuální počítače s funkcí HC-Series 100 GB/s Mellanox EDR InfiniBand. Tyto virtuální počítače jsou připojené v neblokujícím stromu FAT pro optimalizaci a konzistentní výkon RDMA. Tyto virtuální počítače podporují adaptivní směrování a dynamický přenos (DCT), a to v dalších až ke standardním RC a UD transportech. Tyto funkce zvyšují výkon, škálovatelnost a konzistenci aplikací a jejich využití se důrazně doporučuje.

[ACU](acu.md): 297-315<br>
[Premium Storage](premium-storage-performance.md): nepodporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
<br>

| Velikost | Virtuální procesory | Procesor | Paměť (GiB) | Propustnost paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Frekvence s jedním jádrem (GHz, špička) | Výkon RDMA (GB/s) | Podpora MPI | Dočasné úložiště (GiB) | Max. datových disků | Maximální počet síťových karet sítě Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Vše | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [konfiguraci virtuálních počítačů](./workloads/hpc/configure.md), [Povolení INFINIBAND](./workloads/hpc/enable-infiniband.md), [Nastavení MPI](./workloads/hpc/setup-mpi.md)a optimalizaci aplikací HPC pro Azure v [úlohách HPC](./workloads/hpc/overview.md).
- Přečtěte si o nejnovějších oznámeních a některých příkladech HPC a výsledcích na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na nejvyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
- Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
