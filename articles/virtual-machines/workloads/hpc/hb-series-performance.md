---
title: Výkon pro velikost virtuálního počítače s velmi řadou
description: Přečtěte si o výsledcích testování výkonu pro velikosti virtuálních počítačů s velmi řadou v Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f37f0bb14cf29551e85c0e592c327b86048f18e1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721254"
---
# <a name="hb-series-virtual-machine-sizes"></a>Velikosti virtuálních počítačů s nejvyšší řadou

Několik testů výkonu bylo spuštěno na velikostech s velmi řadou. Níže jsou uvedeny některé výsledky tohoto testování výkonu.

| Úloha                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Triad STREAMu                                    | 260 GB/s (32-33 GB/s za CCX)  |
| High-Performance Linpack (HPL)                  | 1 000 GigaFLOPS (Rpeak), 860 GigaFLOPS (Rmax) |
| Latence & šířky pásma RDMA                        | 1,27 mikrosekund, 99,1 GB/s   |
| FIO na místní disk SSD NVMe                           | čtení 1,7 GB/s, 1,0 GB/s zápisy      |  
| IOR na 4 * Azure SSD úrovně Premium (P30 Managed Disks, RAID0) * *  | čtení z 725 MB/s, 780 MB/zápisy   |


## <a name="mpi-latency"></a>Latence MPI

Test latence MPI ze sady OSU mikrobenchmark Suite se spustí. Ukázkové skripty jsou na [GitHubu](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) .

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hb.png" alt-text="MPI latence v Azure.":::

## <a name="mpi-bandwidth"></a>MPI šířka pásma

MPI se test šířky pásma z OSU sady mikrotestů. Ukázkové skripty jsou na [GitHubu](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) .

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hb.png" alt-text="MPI šířka pásma v Azure.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Balíček Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) má mnoho InfiniBand testů, jako je latence (ib_send_lat) a šířka pásma (ib_send_bw). Příklad příkazu je uveden níže.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o nejnovějších oznámeních, příkladech úloh HPC a výsledcích výkonu na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Podrobné zobrazení architektury prostředí HPC, které běží na vyšší úrovni, najdete v tématu věnovaném technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
