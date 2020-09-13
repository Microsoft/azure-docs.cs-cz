---
title: H-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady H-Series.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: b1f30e91b9ce96daf8b2eb8ac6c8cb38b86b347f
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594403"
---
# <a name="h-series"></a>H-series

Virtuální počítače H-Series jsou optimalizované pro aplikace řízené vysokými kmitočty procesoru nebo velkým množstvím paměti podle základních požadavků. Virtuální počítače řady H-Series funkce 8 nebo 16 Intel Xeon E5 2667 V3 procesory, až 14 GB paměti RAM na jádro procesoru a žádné podprocesy. Funkce H-Series 56 GB/s Mellanox FDR InfiniBand v neblokované konfiguraci stromu FAT pro zajištění konzistentního výkonu RDMA. Virtuální počítače H-Series momentálně nepodporují rozhraní SR-IOV a podporují Intel MPI 5. x a MS-MPI.

ACU: 290–300

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Migrace za provozu: nepodporováno

Aktualizace pro zachování paměti: nepodporováno

| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Propustnost paměti GB/s | Základní frekvence procesoru (GHz) | Frekvence všech jader (GHz, špička) | Frekvence s jedním jádrem (GHz, špička) | Výkon RDMA (GB/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální propustnost disku: IOPS | Maximální počet síťových karet sítě Ethernet |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 V3 | 56 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 × 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 V3 | 112 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 × 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 V3 | 112 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 V3 | 224 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |

<sup>1</sup> pro MPI aplikace je v síti FDR InfiniBand povolena vyhrazená síť s back-end službou RDMA.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

> [!NOTE]
> V rámci [virtuálních počítačů podporujících RDMA](sizes-hpc.md#rdma-capable-instances)není sada H-Series povolená – SR-IOV. Proto se podporované [image virtuálních počítačů](./workloads/hpc/configure.md#vm-images), požadavky na [ovladače InfiniBand](./workloads/hpc/enable-infiniband.md) a podporované [knihovny MPI](./workloads/hpc/setup-mpi.md) liší od virtuálních počítačů s podporou SR-IOV.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [konfiguraci virtuálních počítačů](./workloads/hpc/configure.md), [Povolení INFINIBAND](./workloads/hpc/enable-infiniband.md), [Nastavení MPI](./workloads/hpc/setup-mpi.md) a optimalizaci aplikací HPC pro Azure v [úlohách HPC](./workloads/hpc/overview.md).
- Přečtěte si o nejnovějších oznámeních a některých příkladech HPC a výsledcích na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
- Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.
