---
title: Přehled virtuálních počítačů řady HB – virtuální počítače Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o podpoře ve verzi preview pro velikost virtuálních zařízení řady HB v Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707764"
---
# <a name="hb-series-virtual-machines-overview"></a>Přehled virtuálních počítačů řady HB

Maximalizace vysoce výkonnévýpočetní (HPC) výkonu aplikací na AMD EPYC vyžaduje promyšlený přístup paměti lokality a umístění procesu. Níže uvádíme architekturu AMD EPYC a její implementaci v Azure pro aplikace HPC. Termín "pNUMA" použijeme k označení fyzické domény NUMA a "vNUMA" k označení virtualizované domény NUMA.

Fyzicky je řada HB 2 * 32jádrové procesory EPYC 7551 pro celkem 64 fyzických jader. Těchto 64 jader je rozděleno do 16 pNUMA domén (8 na soket), z nichž každá je čtyři jádra a známá jako "CPU Complex" (nebo "CCX"). Každý CCX má vlastní mezipaměť L3, což je způsob, jakým operační režim uvidí hranici pNUMA/vNUMA. Dvojice sousedních CCX sdílí přístup ke dvěma kanálům fyzické paměti DRAM (32 GB paměti DRAM na serverech řady HB).

Chcete-li poskytnout prostor pro hypervisor Azure pracovat bez narušení virtuálního počítače, vyhrazujeme si fyzické pNUMA domény 0 (první CCX). Potom přiřadíme domény pNUMA 1-15 (zbývající jednotky CCX) pro virtuální hod. Virtuální ms uvidí:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`jádra na virtuální virtuální měn

Virtuální ms, sám, neví, že pNUMA 0 nebyla dána k němu. Virtuální počítač chápe pNUMA 1-15 jako vNUMA 0-14, s 7 vNUMA na vSocket 0 a 8 vNUMA na vSocket 1. I když je to asymetrické, operační systém by měl zavést a pracovat normálně. Dále v této příručce instruujeme, jak nejlépe spustit aplikace MPI v tomto asymetrickém rozložení NUMA.

Připnutí procesu bude fungovat na virtuálních počítačích řady HB, protože základní křemík tak, jak je, bude hostovanému virtuálnímu počítači. Důrazně doporučujeme připnutí procesu pro optimální výkon a konzistenci.

Více informací o [architektuře AMD EPYC](https://bit.ly/2Epv3kC) a [vícečipových architekturách](https://bit.ly/2GpQIMb) na LinkedIn. Podrobnější informace naleznete v [Průvodci laděním HPC pro procesory AMD EPYC](https://bit.ly/2T3AWZ9).

Následující diagram znázorňuje oddělení jader vyhrazených pro Azure Hypervisor a virtuální počítač řady HB.

![Oddělení jader vyhrazených pro Azure Hypervisor a virtuální počítač řady HB](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifikace hardwaru

| SPECIFIKACE HW                | Virtuální počítač řady HB                     |
|----------------------------------|----------------------------------|
| Cores                            | 60 (SMT zakázáno)                |
| Procesor                              | AMD EPYC 7551*                   |
| Frekvence procesoru (bez AVX)          | ~ 2,55 GHz (jeden + všechna jádra)   |
| Memory (Paměť)                           | 4 GB/jádro (celkem 240)            |
| Místní disk                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Network (Síť)                          | 50 Gb Ethernet (40 Gb použitelný) Azure druhý Gen SmartNIC *** |

## <a name="software-specifications"></a>Specifikace softwaru

| Specifikace SW           |Virtuální počítač řady HB           |
|-----------------------------|-----------------------|
| Maximální velikost úlohy MPI            | 6000 jader (100 škálovacích sad virtuálních strojů) 12000 jader (200 škálovacích sad virtuálních strojů)  |
| Podpora MPI                 | MVAPICH2, OpenMPI, MPICH, Platform MPI, Intel MPI  |
| Další rámce       | Sjednocená komunikace X, libfabric, PGAS |
| Podpora úložiště Azure       | Std + Premium (maximálně 4 disky) |
| Podpora operačního režimu pro SRIOV RDMA   | Centos/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Podpora Azure CycleCloud    | Ano                         |
| Dávková podpora Azure         | Ano                         |

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o velikostech virtuálních počítačů HPC pro [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) v Azure.

* Další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
