---
title: Přehled virtuálních počítačů řady HC – virtuální počítače Azure| Dokumenty společnosti Microsoft
description: Přečtěte si o podpoře ve verzi preview pro velikost virtuálních počítačů řady HC v Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906743"
---
# <a name="hc-series-virtual-machine-overview"></a>Přehled virtuálních strojů řady HC

Maximalizace výkonu aplikací HPC na škálovatelných procesorech Intel Xeon vyžaduje promyšlený přístup ke zpracování umístění na této nové architektuře. Tady nastíníme jeho implementaci na virtuálních počítačích řady Azure HC pro aplikace HPC. Termín "pNUMA" použijeme k označení fyzické domény NUMA a "vNUMA" k označení virtualizované domény NUMA. Podobně použijeme termín "pCore" k označení fyzických jader procesoru a "vCore" k označení virtualizovaných procesorových jader.

Fyzicky je HC server 2 * 24jádrové procesory Intel Xeon Platinum 8168 pro celkem 48 fyzických jader. Každý procesor je jedna doména pNUMA a má jednotný přístup k šesti kanálům DRAM. Procesory Intel Xeon Platinum jsou vybaveny 4x větší mezipamětí L2 než v předchozích generacích (256 KB/jádro -> 1 MB/jádro) a zároveň snižují mezipaměť L3 ve srovnání s předchozími procesory Intel (2,5 MB/jádro -> 1,375 MB/jádro).

Výše uvedená topologie se přenáší i do konfigurace hypervisoru řady HC. Chcete-li poskytnout prostor pro hypervisor Azure pracovat bez narušení virtuálního počítače, vyhrazujeme pCores 0-1 a 24-25 (to znamená, že první 2 pCores na každé soketu). Potom přiřadíme domény pNUMA všechna zbývající jádra virtuálnímu virtuálnímu soudu. Virtuální ms tak uvidí:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`jádra na virtuální virtuální měn

Virtuální měkce nevědí, že pCore0-1 a 24-25 mu nebyly dány. Proto zveřejňuje každý vNUMA, jako by nativně měl 22 jader.

Procesory Intel Xeon Platinum, Gold a Silver také zavádějí síť 2D mesh on-die pro komunikaci v rámci a mimo zásuvku cpu. Důrazně doporučujeme připnutí procesu pro optimální výkon a konzistenci. Připnutí procesu bude fungovat na virtuálních počítačích řady HC, protože základní křemík je vystaven tak, jak je virtuálnímu počítači hosta. Další informace naleznete v [tématu Intel Xeon SP architecture](https://bit.ly/2RCYkiE).

Následující diagram znázorňuje oddělení jader vyhrazených pro Azure Hypervisor a virtuální počítač řady HC.

![Oddělení jader vyhrazených pro Azure Hypervisor a virtuální počítač řady HC](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifikace hardwaru

| Hardwarové specifikace          | Virtuální počítač řady HC                     |
|----------------------------------|----------------------------------|
| Cores                            | 44 (HT zakázáno)                 |
| Procesor                              | Intel Xeon Platinum 8168*        |
| Frekvence procesoru (bez AVX)          | 3,7 GHz (jednojádrový), 2,7–3,4 GHz (všechna jádra) |
| Memory (Paměť)                           | 8 GB/jádro (celkem 352)            |
| Místní disk                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Network (Síť)                          | 50 Gb Ethernet (40 Gb použitelný) Azure druhý Gen SmartNIC *** |

## <a name="software-specifications"></a>Specifikace softwaru

| Specifikace softwaru     | Virtuální počítač řady HC          |
|-----------------------------|-----------------------|
| Maximální velikost úlohy MPI            | 13200 jader (300 virtuálních zařízení v jednom vmss s singlePlacementGroup=true) |
| Podpora MPI                 | MVAPICH2, OpenMPI, MPICH, Platform MPI, Intel MPI  |
| Další rámce       | Sjednocená komunikace X, libfabric, PGAS |
| Podpora úložiště Azure       | Std + Premium (maximálně 4 disky) |
| Podpora operačního režimu pro SRIOV RDMA   | Centos/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Podpora Azure CycleCloud    | Ano                         |
| Dávková podpora Azure         | Ano                         |

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o velikostech virtuálních počítačů HPC pro [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) v Azure.

* Další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
