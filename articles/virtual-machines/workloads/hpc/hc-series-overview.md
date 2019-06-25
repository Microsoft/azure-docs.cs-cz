---
title: Přehled virtuálních počítačů řady hybridní připojení - Azure Virtual Machines | Dokumentace Microsoftu
description: Další informace o podpoře pro velikost series hybridní připojení virtuálního počítače v Azure preview.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: f96a1179c103dd9dfb4d358572f9a9adbe24b977
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809869"
---
# <a name="hc-series-virtual-machine-overview"></a>Přehled virtuálních počítačů řady hybridní připojení

Dosažení maximálního výkonu aplikace HPC na škálovatelné procesory Intel Xeon vyžaduje důkladné přístup k umístění proces na tuto novou architekturu. Tady uvádíme naši implementaci ji na virtuální počítače Azure řady hybridní připojení pro aplikace HPC. Termín "pNUMA" použije k odkazování na fyzických uzlů NUMA domény a "vNUMA" k odkazování na virtualizované domény NUMA. Podobně použijeme k odkazování na fyzických jader procesoru termín "pCore" a "vCore" k odkazování na virtualizované jader procesoru.

Fyzicky, serveru hybridní připojení je 2 * 24-core Intel Xeon Platinum 8168 procesory zase nabízejí celkem 48 fyzických jader. Každý procesor je jeden pNUMA domény a má přístup do šesti kanálů DRAM unified. Funkce Intel Xeon Platinum procesory a 4 x větší vyrovnávací paměť L2 než v předchozích generací (256 KB/core-> 1 MB/jádro), a také snižuje nutnost mezipaměti L3 ve srovnání s předchozí procesory Intel (2,5 MB/core-> 1.375 MB/core).

Výše uvedené topologie se přenesou do konfigurace hypervisoru hybridní připojení series také. K poskytování místo pro hypervisor Azure provoz, aniž by zasahovala do virtuálního počítače, vyhrazujeme si pCores 0-1 a 25. 24 (tj. prvních 2 pCores každý soketem). Jsme pak přiřadit domény pNUMA všechny zbývající počet jader virtuálního počítače. Proto se zobrazí virtuální počítač:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` počet jader na virtuální počítač

Virtuální počítač je vůbec nezná, že k němu nebyly zadané pCores 0-1 a 24 25. Proto zpřístupňuje každý vNUMA, jako kdyby byla nativně 22 jader.

Intel Xeon Platinum, zlatá a stříbrná procesory také zavést síť na kostka 2D síť pro komunikaci v rámci a externí soketu procesoru. Důrazně doporučujeme Připnutí proces pro zajištění optimálního výkonu a konzistenci. Připnutí procesu bude fungovat na virtuálních počítačích řady hybridní připojení, protože základní silicon vystavena jako-je na virtuálním počítači hosta. Další informace o Intel Xeon SP architektury na: https://bit.ly/2RCYkiE

Následující diagram znázorňuje oddělení jader vyhrazené pro Azure hypervisoru a virtuálních počítačů řady hybridní připojení.

![Oddělení jader vyhrazené pro Azure hypervisoru a virtuálních počítačů řady hybridní připojení](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifikace hardwaru

| Specifikace hardwaru          | Virtuální počítač řady hybridní připojení                     |
|----------------------------------|----------------------------------|
| Jádra                            | 40 (HT zakázána)                 |
| Procesor                              | Intel Xeon Platinum 8168*        |
| Frekvenci procesoru (bez AVX)          | 3,7 GHz (jednojádrový) 2.7 3.4 GHz (všech jader) |
| Memory (Paměť)                           | 8 GB/jader (celkem 352)            |
| Místní disk                       | 700 GB NVMe                      |
| Sítí Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 ** |
| Síť                          | 50 Gb Ethernet (40 Gb použitelné) Azure druhé generace SmartNIC *** |

## <a name="software-specifications"></a>Specifikace softwaru

| Specifikace softwaru     | Virtuální počítač řady hybridní připojení          |
|-----------------------------|-----------------------|
| Velikost úlohy Max MPI            | 4400 jádra (100 škálovací sady virtuálních počítačů), 8800 jádra (200 škálovací sady virtuálních počítačů) |
| Podpora MPI                 | MVAPICH2, OpenMPI, MPICH platformy MPI, Intel MPI  |
| Další architektury       | Sjednocené komunikace X, libfabric PGAS |
| Podpora služby Azure Storage       | Std + Premium (max. 4 disky) |
| Podpora operačního systému pro SR-IOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Podpora Azure CycleCloud    | Ano                         |
| Podpora služby Azure Batch         | Ano                         |

## <a name="next-steps"></a>Další postup

* Další informace o velikostech virtuálních počítačů v prostředí HPC pro [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) v Azure.

* Další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
