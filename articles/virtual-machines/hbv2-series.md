---
title: HBv2-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady HBv2-Series.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 92de7f93939f6673e2133f6eaf8086a1dcdd1964
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102616243"
---
# <a name="hbv2-series"></a>Řada HBv2

Virtuální počítače řady HBv2-Series jsou optimalizované pro aplikace, které jsou založené na šířce pásma paměti, jako je například kapalinová dynamika, analýza omezeného prvku a simulace zásobníku. Virtuální počítače s HBv2 120 AMD EPYC 7742 procesory, 4 GB paměti RAM na jádro procesoru a žádné souběžné multithreadingy. Každý virtuální počítač HBv2 poskytuje šířku pásma až 340 GB/s a až 4 teraFLOPSy FP64 Compute.

Virtuální počítače HBv2-Series funkce 200 GB/s Mellanox HDR InfiniBand. Tyto virtuální počítače jsou připojené v neblokujícím stromu FAT pro optimalizaci a konzistentní výkon RDMA. Tyto virtuální počítače podporují adaptivní směrování a dynamický (DCT) (kromě přenosů Standard RC a UD). Tyto funkce zvyšují výkon, škálovatelnost a konzistenci aplikací a jejich použití se doporučuje.

[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): podporováno (další[informace](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) o výkonu a potenciálních problémech) <br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): nepodporované <br>
<br>

| Velikost | Virtuální procesory | Procesor | Paměť (GiB) | Propustnost paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Frekvence s jedním jádrem (GHz, špička) | Výkon RDMA (GB/s) | Podpora MPI | Dočasné úložiště (GiB) | Max. datových disků | Maximální počet ethernetových virtuální síťové adaptéry |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 456 | 350 | 2.45 | 3.1 | 3.3 | 200 | Vše | 480 + 960 | 8 | 8 |

Přečtěte si další informace o základní [architektuře, topologii virtuálních počítačů](./workloads/hpc/hbv2-series-overview.md)a očekávaném [výkonu](./workloads/hpc/hbv2-performance.md) virtuálního počítače HBv2-Series.

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU.](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

- Přečtěte si o nejnovějších oznámeních, příkladech úloh HPC a výsledcích výkonu na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
- Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
