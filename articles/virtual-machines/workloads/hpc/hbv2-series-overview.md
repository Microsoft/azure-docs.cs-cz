---
title: Virtuální počítač HBv2-Series – přehled – Azure Virtual Machines | Microsoft Docs
description: Přečtěte si o velikosti virtuálního počítače řady HBv2-Series v Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6648f77c5eacf40f848bc9b24aa6e257d8adf626
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674640"
---
# <a name="hbv2-series-virtual-machine-overview"></a>Přehled virtuálního počítače řady HBv2 

 
Maximalizace výkonu aplikace COMPUTE s vysokým výkonem (HPC) na AMD EPYC vyžaduje, aby se důkladné přístup k paměti a umístění procesu. Pod seosnovou architektury AMD EPYC a naší implementací IT v Azure pro aplikace HPC. Pojem **pNUMA** použijeme pro odkaz na fyzickou doménu NUMA a **vNUMA** , aby odkazovala na virtualizované domény NUMA. 

Server [HBv2-Series](../../hbv2-series.md) je fyzicky 2 × 64 – jádro EPYC 7742 procesorů, které celkově 128 fyzických jader. Tyto 128 jádra jsou rozdělené do 32 pNUMAch domén (16 na jeden soket), z nichž každá je 4 jádra a je v systému AMD jako **základní komplexní** (nebo **CCX**). Každý CCX má vlastní mezipaměť L3, což znamená, jak se v operačním systému zobrazí hranice pNUMA/vNUMA. Čtyři sousedící CCXs sdílení přístupu ke dvěma kanálům fyzické DRAM. 

Pokud chcete, aby hypervisor Azure fungoval bez rušivého virtuálního počítače, vyhrazujeme si fyzické pNUMA domény 0 a 16 (tj. první CCX každého soketu CPU). K virtuálnímu počítači se přiřadí všechny zbylé 30 pNUMA domény, ve kterých se stanou vNUMA. Proto se virtuální počítač zobrazí:

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` jader na virtuální počítač 

Samotný virtuální počítač nemá žádné povědomí, že jsou rezervované pNUMA 0 a 16. Vypíše vNUMA, který se zobrazuje jako 0-29, s 15 vNUMA na soket symetricky, vNUMA 0-14 na vSocket 0 a vNUMA 15-29 na vSocket 1. V další části najdete pokyny, jak nejlépe spouštět aplikace MPI v tomto asymetrickém rozložení NUMA. 

Připnutí procesu bude fungovat na virtuálních počítačích s HBv2-Series, protože vystaví základní silikon jako je na virtuálním počítači hosta. Důrazně doporučujeme připnutí procesů pro zajištění optimálního výkonu a konzistence. 


## <a name="hardware-specifications"></a>Specifikace hardwaru 

| Specifikace hardwaru          | Virtuální počítač HBv2-Series                   | 
|----------------------------------|----------------------------------|
| Cores                            | 120 (SMT zakázané)               | 
| Procesor                              | AMD EPYC 7742                    | 
| Frekvence procesoru (ne AVX)          | ~ 3,1 GHz (jedna + všechna jádra)    | 
| Memory (Paměť)                           | 4 GB/jádro (celkem 480 GB)         | 
| Místní disk                       | 960 GB NVMe (Block), 480 GB SSD (stránkovací soubor) | 
| InfiniBand                       | 200 GB/s EDR Mellanox ConnectX – 6 | 
| Síť                          | 50 GB/s Ethernet (40 GB/s použitelné) Azure Second gen SmartNIC | 


## <a name="software-specifications"></a>Specifikace softwaru 

| Specifikace softwaru     | Virtuální počítač HBv2-Series                                            | 
|-----------------------------|-----------------------------------------------------------|
| Maximální velikost úlohy MPI            | 36000 jader (300 virtuálních počítačů v jedné sadě škálování virtuálních počítačů s singlePlacementGroup = true) |
| Podpora MPI                 | HPC-X, Intel MPI, OpenMPy, MVAPICH2, MPICH, Platform MPI  |
| Další architektury       | Unified Communications X, libfabric, PGAS                  |
| Podpora Azure Storage       | Disky úrovně Standard a Premium (maximální počet disků: 8)              |
| Podpora operačního systému pro SRIOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +           |
| Podpora nástroje Orchestrator        | CycleCloud, Batch                                         | 


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [architektuře AMD EPYC](https://bit.ly/2Epv3kC) a architekturách [s více čipy](https://bit.ly/2GpQIMb). Podrobnější informace najdete v [Průvodci optimalizací HPC pro procesory AMD EPYC](https://bit.ly/2T3AWZ9).
- Přečtěte si o nejnovějších oznámeních a některých příkladech HPC na [blogu technické komunity Azure COMPUTE](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).