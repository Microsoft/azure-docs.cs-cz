---
title: Výkon pro velikost virtuálního počítače řady HBv2-Series
description: Přečtěte si o výsledcích testování výkonu pro velikosti virtuálních počítačů HBv2-Series v Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 28e7066c166a4c61c2f6436e9bd126f712f89f78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670072"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>Velikosti virtuálních počítačů řady HBv2-Series

Na virtuálních počítačích [HBv2-Series](../../hbv2-series.md) bylo spuštěno několik testů výkonnosti. Níže jsou uvedeny některé výsledky tohoto testování výkonu.


| Úloha                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Triad STREAMu                                    | 350 GB/s (21-23 GB/s za CCX)                                     |
| High-Performance Linpack (HPL)                  | 4 TeraFLOPS (Rpeak, FP64), 8 TeraFLOPS (Rmax, FP32)               |
| Latence & šířky pásma RDMA                        | 1,2 mikrosekund, 190 GB/s                                        |
| FIO na místní disk SSD NVMe                           | čtení 2,7 GB/s, 1,1 GB/s zápisy; čtení 102k IOPS, 115 zápis IOPS |
| IOR na 8 * Azure SSD úrovně Premium (P40 Managed Disks, RAID0) * *  | čtení 1,3 GB/s, 2,5 GB/zápisů; 101k IOPS čtení, 105k IOPS zápisy |


## <a name="mpi-latency"></a>Latence MPI

Test latence MPI ze sady OSU mikrobenchmark Suite se spustí. Ukázkové skripty jsou na [GitHubu](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="MPI latence v Azure.":::


## <a name="mpi-bandwidth"></a>MPI šířka pásma

MPI se test šířky pásma z OSU sady mikrotestů. Ukázkové skripty jsou na [GitHubu](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="MPI šířka pásma v Azure.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Balíček Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) má mnoho InfiniBand testů, jako je latence (ib_send_lat) a šířka pásma (ib_send_bw). Příklad příkazu je uveden níže. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>Další kroky

- Přečtěte si o nejnovějších oznámeních a některých příkladech HPC (High Performance Computing) a výsledcích na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Podrobné zobrazení architektury prostředí HPC, které běží na vyšší úrovni, najdete v tématu věnovaném technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).