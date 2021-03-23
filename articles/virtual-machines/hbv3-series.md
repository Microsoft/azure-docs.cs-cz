---
title: HBv3-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady HBv3-Series.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b435547d7cd3b0fab781cc6cf3b617b13ac1120a
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774690"
---
# <a name="hbv3-series"></a>HBv3-Series

Virtuální počítače řady HBv3-Series jsou optimalizované pro aplikace HPC, jako je například kapalinový dynamika, explicitní a implicitní analýza nekonečných prvků, modelování počasí, zpracování seismických procesů, simulace zásobníku a simulace zprava doleva. Virtuální počítače s HBv3 jsou až 120 AMD EPYC™ 7003-Series (Milán) jádra procesoru, 448 GB paměti RAM a žádného podprocesu. Virtuální počítače řady HBv3-Series také poskytují 350 GB/s šířky pásma paměti, až 32 MB mezipaměti L3 na jádro, až 7 GB/s v případě výkonu disku SSD a frekvence hodin až 3,675 GHz. 

Všechny virtuální počítače HBv3-Series funkce 200 GB/s HDR InfiniBand ze sítě NVIDIA umožňují úlohy MPI s horizontálním škálováním na více počítačů. Tyto virtuální počítače jsou připojené v neblokujícím stromu FAT pro optimalizaci a konzistentní výkon RDMA. Topologie HDR InfiniBand také podporuje adaptivní směrování a dynamický propojený přenos (DCT, ve více než standard RC a UD Transports). Tyto funkce zvyšují výkon, škálovatelnost a konzistenci aplikací a jejich použití se důrazně doporučuje.

[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): již brzy<br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): nepodporované <br>
<br>

|Velikost |Virtuální procesory |Procesor |Paměť (GiB) |Propustnost paměti GB/s |Základní frekvence procesoru (GHz) |Frekvence všech jader (GHz, špička) |Frekvence s jedním jádrem (GHz, špička) |Výkon RDMA (GB/s) |Podpora MPI |Dočasné úložiště (GiB) |Max. datových disků |Maximální počet ethernetových virtuální síťové adaptéry |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Vše |2 * 960 |32 |8 |
|Standard_HB120 – 96rs_v3 |96  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Vše |2 * 960 |32 |8 |
|Standard_HB120 – 64rs_v3 |64  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Vše |2 * 960 |32 |8 |
|Standard_HB120 – 32rs_v3 |32  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Vše |2 * 960 |32 |8 |
|Standard_HB120 – 16rs_v3 |16  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Vše |2 * 960 |32 |8 |

Další informace o:
- [Architektura a topologie virtuálních počítačů](./workloads/hpc/hbv3-series-overview.md),
- podporovaný [softwarový zásobník](./workloads/hpc/hbv3-series-overview.md#software-specifications) , včetně PODPOROVANÉHO operačního systému a
- byl očekáván [výkon](./workloads/hpc/hbv3-performance.md) virtuálního počítače HBv3-Series.

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
