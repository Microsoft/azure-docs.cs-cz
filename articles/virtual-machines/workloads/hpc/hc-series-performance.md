---
title: Výkon pro velikost virtuálního počítače řady HC-Series
description: Přečtěte si o výsledcích testování výkonu pro velikosti virtuálních počítačů řady HC-Series v Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/10/2020
ms.author: amverma
ms.openlocfilehash: 0d63d9770dacf6a200e8b81e8d47d9f807a8a448
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603420"
---
# <a name="hc-series-virtual-machine-sizes"></a>Velikosti virtuálních počítačů řady HC-Series

U velikostí řady HC-Series bylo spuštěno několik testů výkonnosti. Níže jsou uvedeny některé výsledky tohoto testování výkonu.

| Úloha                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Triad STREAMu                                    | 190 GB/s (Intel MLC AVX-512)  |
| Vysoce výkonné Linpack (HPL)                  | 3520 GigaFLOPS (Rpeak), 2970 GigaFLOPS (Rmax) |
| Latence & šířky pásma RDMA                        | 1,05 mikrosekund, 96,8 GB/s   |
| FIO na místní disk SSD NVMe                           | čtení 1,3 GB/s, 900 MB/s zápisy |  
| IOR na 4 Azure SSD úrovně Premium (P30 Managed Disks, RAID0) * *  | čtení z 780 MB/s, 780 MB/zápisy |

## <a name="mpi-latency"></a>Latence MPI

Test latence MPI ze sady OSU mikrobenchmark Suite se spustí. Ukázkové skripty jsou na [GitHubu](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) .

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="Latence MPI v Azure HC.":::

## <a name="mpi-bandwidth"></a>MPI šířka pásma

MPI se test šířky pásma z OSU sady mikrotestů. Ukázkové skripty jsou na [GitHubu](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) .

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="MPI šířka pásma v systému Azure HC.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

[Balíček Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) má mnoho InfiniBand testů, jako je latence (ib_send_lat) a šířka pásma (ib_send_bw). Příklad příkazu je uveden níže.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o nejnovějších oznámeních a některých příkladech HPC (High Performance Computing) a výsledcích na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Podrobné zobrazení architektury prostředí HPC, které běží na vyšší úrovni, najdete v tématu věnovaném technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
