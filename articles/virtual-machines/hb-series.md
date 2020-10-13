---
title: Řada HB
description: Specifikace pro virtuální počítače s velmi řadou.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: f3fbc1ad3f9e7f3f3c9d2667ae9c3f566325aa51
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931695"
---
# <a name="hb-series"></a>Řada HB

Virtuální počítače s velmi řadou jsou optimalizované pro aplikace, které jsou založené na šířce pásma paměti, jako je například kapalinová dynamika, explicitní konečná analýza elementu a modelování počasí. Virtuální počítače s funkcí 60 AMD EPYC 7551 procesory, 4 GB paměti RAM na jádro procesoru a žádné souběžné multithreadingy. Virtuální počítač s pamětí typu "a" poskytuje šířku pásma až 260 GB/s.

Virtuální počítače s funkcí "7000" 100 GB/s Mellanox EDR InfiniBand. Tyto virtuální počítače jsou připojené v neblokujícím stromu FAT pro optimalizaci a konzistentní výkon RDMA. Tyto virtuální počítače podporují adaptivní směrování a dynamický přenos (DCT), a to v dalších až ke standardním RC a UD transportech. Tyto funkce zvyšují výkon, škálovatelnost a konzistenci aplikací a jejich využití se důrazně doporučuje.

[ACU](acu.md): 199-216<br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
<br>

| Velikost | Virtuální procesory | Procesor | Paměť (GiB) | Propustnost paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Frekvence s jedním jádrem (GHz, špička) | Výkon RDMA (GB/s) | Podpora MPI | Dočasné úložiště (GiB) | Max. datových disků | Maximální počet ethernetových virtuální síťové adaptéry |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2,0 | 2.55 | 2.55 | 100 | Vše | 700 | 4 | 8 |

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
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
- Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
