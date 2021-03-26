---
title: Virtuální počítače řady HBv3-Series – výkon a škálovatelnost
description: Přečtěte si o výkonu a škálovatelnosti velikostí virtuálních počítačů HBv3-Series v Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: bf64cfc8ad00fc7f761019ed2fa66089434a96ba
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604766"
---
# <a name="hbv3-series-virtual-machine-performance"></a>Výkon virtuálního počítače řady HBv3-Series

Očekávání výkonu pomocí běžných mikrosrovnávacích testů HPC jsou následující:

| Úloha                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Triad STREAMu                                    | 330-350 GB/s (~ 82-86 GB/s na NUMA)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, FP32) pro velikost virtuálního počítače 120 – Core               |
| Latence & šířky pásma RDMA                        | 1,2 mikrosekund (1 bajt), 192 GB/s (jednosměrný)                                        |
| FIO na místní NVMe SSD (RAID0)                  | 7 GB/s čtení, 3 GB/s zápisy; 186k IOPS čtení, 201k IOPS zápisy |

## <a name="process-pinning"></a>Připnutí procesů

[Připnutí procesu](compiling-scaling-applications.md#process-pinning) funguje dobře na virtuálních počítačích HBv3-Series, protože vystaví základní silikon jako je na virtuálním počítači hosta. Důrazně doporučujeme připnutí procesů pro zajištění optimálního výkonu a konzistence.

## <a name="mpi-latency"></a>Latence MPI

Test latence MPI ze sady OSU mikrobenchmark Suite se dá spustit podle níže uvedených pokynů. Ukázkové skripty jsou na [GitHubu](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>MPI šířka pásma
Test šířky pásma MPI ze sady OSU mikrobenchmark Suite se dá spustit podle níže uvedených pokynů. Ukázkové skripty jsou na [GitHubu](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox Perftest
[Balíček Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) má mnoho InfiniBand testů, jako je latence (ib_send_lat) a šířka pásma (ib_send_bw). Příklad příkazu je uveden níže.
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Další kroky
- Přečtěte si o [škálování aplikací MPI](compiling-scaling-applications.md).
- Projděte si výsledky výkonu a škálovatelnosti aplikací HPC na virtuálních počítačích s HBv3 v [článku TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/hpc-performance-and-scalability-results-with-azure-hbv3-vms/bc-p/2235843).
- Přečtěte si o nejnovějších oznámeních, příkladech úloh HPC a výsledcích výkonu na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Podrobné zobrazení architektury prostředí HPC, které běží na vyšší úrovni, najdete v tématu věnovaném technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
