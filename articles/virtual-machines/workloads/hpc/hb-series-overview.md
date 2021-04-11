---
title: Přehled virtuálních počítačů s funkcí neřady-Series – Azure Virtual Machines | Microsoft Docs
description: Přečtěte si o podpoře verze Preview pro velikost virtuálního počítače s rozhraním \ řady v Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 2f5dddd3d59ebe778d577176e439528a86bb42a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802584"
---
# <a name="hb-series-virtual-machines-overview"></a>Přehled virtuálních počítačů s více než jednou řadou

Maximalizace výkonu aplikace COMPUTE s vysokým výkonem (HPC) na AMD EPYC vyžaduje, aby se důkladné přístup k paměti a umístění procesu. Pod seosnovou architektury AMD EPYC a naší implementací IT v Azure pro aplikace HPC. K odkazování na fyzickou doménu NUMA a "vNUMA" použijeme termín "pNUMA", který odkazuje na virtualizované domény NUMA.

Server s více instancemi, který je fyzicky pro [řady](../../hb-series.md) , je 2 × 32 – core EPYC 7551 procesorů a celkový počet fyzických jader 64. Tyto 64 jádra jsou rozdělené do 16 pNUMA domén (8 na jeden soket), z nichž každá je čtyři jádra a označuje se jako "komplexní procesor" (neboli "CCX"). Každý CCX má vlastní mezipaměť L3, což znamená, jak se v operačním systému zobrazí hranice pNUMA/vNUMA. Dvojice sousedících CCXs sdílí přístup ke dvěma kanálům fyzické paměti DRAM (32 GB TB na serverech s ne).

Pro zajištění toho, aby měl hypervisor Azure fungovat, aniž by došlo ke konfliktu s virtuálním počítačem, vyhrazujeme si fyzickou pNUMA doménu 0 (první CCX). Pak přiřadíme pNUMA domény 1-15 (zbývající jednotky CCX) pro virtuální počítač. Virtuální počítač uvidí tyto informace:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` jader na virtuální počítač

Virtuální počítač sám o sobě neví, že se mu pNUMA 0 nedostalo. Virtuální počítač rozumí pNUMA 1-15 jako vNUMA 0-14 a 7 vNUMA na vSocket 0 a 8 vNUMA v vSocket 1. I když je to asymetrické, váš operační systém by měl být spuštěný a normálně fungovat. Později v této příručce si vydáte, jak nejlépe spouštět aplikace MPI v tomto asymetrickém rozložení NUMA.

Připnutí procesu bude fungovat na virtuálních počítačích s více procesory, protože vystavení základního silikonu jako je na virtuálním počítači hosta. Důrazně doporučujeme připnutí procesů pro zajištění optimálního výkonu a konzistence.

Následující diagram znázorňuje oddělení jader rezervovaných pro Azure hypervisor a virtuální počítač s možností obřadu.

![Oddělení jader rezervovaných pro virtuální počítač s hypervisorem a Azure s více řadami](./media/architecture/hb-segregation-cores.png)

## <a name="hardware-specifications"></a>Specifikace hardwaru

| Specifikace hardwaru                | Virtuální počítač s nejvyšší řadou                     |
|----------------------------------|----------------------------------|
| Cores                            | 60 (SMT zakázané)                |
| Procesor                              | AMD EPYC 7551                    |
| Frekvence procesoru (ne AVX)          | ~ 2,55 GHz (jedna + všechna jádra)   |
| Memory (Paměť)                           | 4 GB/jádro (celkem 240 GB)         |
| Místní disk                       | 700 GB SSD                       |
| InfiniBand                       | 100 GB EDR Mellanox ConnectX-5 |
| Síť                          | 50 GB Ethernet (40 GB použitelné) Azure Second gen SmartNIC |

## <a name="software-specifications"></a>Specifikace softwaru

| Specifikace softwaru           |Virtuální počítač s nejvyšší řadou           |
|-----------------------------|-----------------------|
| Maximální velikost úlohy MPI            | 18000 jader (300 virtuálních počítačů v jedné sadě škálování virtuálních počítačů s singlePlacementGroup = true)  |
| Podpora MPI                 | HPC-X, Intel MPI, OpenMPy, MVAPICH2, MPICH, Platform MPI  |
| Další architektury       | UCX, libfabric, PGAS |
| Podpora Azure Storage       | Disky úrovně Standard a Premium (maximální počet 4 disků) |
| Podpora operačního systému pro SRIOV RDMA   | CentOS/RHEL 7.6 +, Ubuntu 16.04 +, SLES 12 SP4 +, WinServer 2016 +  |
| Podpora nástroje Orchestrator        | CycleCloud, Batch, AKS; [Možnosti konfigurace clusteru](../../sizes-hpc.md#cluster-configuration-options) |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [architektuře AMD EPYC](https://bit.ly/2Epv3kC) a architekturách [s více čipy](https://bit.ly/2GpQIMb). Podrobnější informace najdete v [Průvodci optimalizací HPC pro procesory AMD EPYC](https://bit.ly/2T3AWZ9).
- Přečtěte si o nejnovějších oznámeních, příkladech úloh HPC a výsledcích výkonu na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
