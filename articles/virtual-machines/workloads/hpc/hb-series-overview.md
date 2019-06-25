---
title: Přehled virtuálních počítačů řady HB - Azure Virtual Machines | Dokumentace Microsoftu
description: Další informace o podpoře ve verzi preview velikost HB series virtuálního počítače v Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: bf143aa316a3d373a6303865cdc39ee0aaf27d87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809914"
---
# <a name="hb-series-virtual-machines-overview"></a>Virtual machines řady HB – přehled

Maximalizace výkonu aplikace vysokovýkonného výpočetního (prostředí HPC) na AMD EPYC vyžaduje důkladné přístup paměti procesu a umístění umístění. Níže uvádíme architektuře AMD EPYC a naši implementaci ji v Azure pro aplikace HPC. Termín "pNUMA" použije k odkazování na fyzických uzlů NUMA domény a "vNUMA" k odkazování na virtualizované domény NUMA.

Fyzicky, HB-series je 2 * 32 jádry EPYC 7551 procesory zase nabízejí celkem 64 fyzických jader. Tyto 64 jádra jsou rozdělené do 16 pNUMA domén (8 na soket), z nichž každý je čtyři jádra a známé jako "Pokročilý CPU" (nebo "CCX"). Každý CCX má svůj vlastní mezipaměti L3, která je, jak se operační systém zobrazí pNUMA/vNUMA hranice. Dvojice sousední CCXs sdílí přístup k dva kanály fyzické paměti DRAM (32 GB paměti DRAM na serverech HB-series).

K poskytování místo pro hypervisor Azure provoz, aniž by zasahovala do virtuálního počítače, vyhrazujeme si fyzické pNUMA domény 0 (první CCX). Pak jsme přiřadit domény pNUMA 1 – 15 (zbývající jednotky CCX) pro virtuální počítač. Virtuální počítač se zobrazí:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` počet jader na virtuální počítač

Virtuálního počítače, samostatně, nebude vědět, že tento pNUMA 0 nebyly předány do něj. Virtuální počítač rozumí pNUMA 1 – 15 jako 0-14 vNUMA s 7 vNUMA na vNUMA vSocket 0 a 8 na vSocket 1. Přestože se jedná o asymetrické, váš operační systém by měl spustit a fungovat normálně. Dál v této příručce budeme dáte pokyn, aby jak nejlépe ke spouštění aplikací MPI na tento asymetrický rozložení uzlů NUMA.

Připnutí proces bude fungovat na virtuálních počítačích řady HB zveřejněním základní silicon jako-je na virtuálním počítači hosta. Důrazně doporučujeme Připnutí proces pro zajištění optimálního výkonu a konzistenci.

Zobrazit další na [architektuře AMD EPYC](https://bit.ly/2Epv3kC) a [více čipu architektury](https://bit.ly/2GpQIMb) na Linkedinu. Podrobnější informace najdete v článku [Průvodci optimalizací HPC pro procesory AMD EPYC](https://bit.ly/2T3AWZ9).

Následující diagram znázorňuje oddělení jader vyhrazené pro Azure hypervisoru a virtuálních počítačů řady HB.

![Oddělení vyhrazené pro Azure hypervisoru a virtuálních počítačů řady HB jader](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifikace hardwaru

| Specifikace hardwaru                | HB-series virtuálních počítačů                     |
|----------------------------------|----------------------------------|
| Jádra                            | 60 (SMT zakázána)                |
| Procesor                              | AMD EPYC 7551*                   |
| Frekvenci procesoru (bez AVX)          | ~2.55 GHz (jedním + všech jader)   |
| Memory (Paměť)                           | 4 GB/jader (celkem 240)            |
| Místní disk                       | 700 GB NVMe                      |
| Sítí Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 ** |
| Síť                          | 50 Gb Ethernet (40 Gb použitelné) Azure druhé generace SmartNIC *** |

## <a name="software-specifications"></a>Specifikace softwaru

| Specifikace SW           |HB-series virtuálních počítačů           |
|-----------------------------|-----------------------|
| Velikost úlohy Max MPI            | 6000 jádra (100 škálovací sady virtuálních počítačů) 12000 jader (200 škálovací sady virtuálních počítačů)  |
| Podpora MPI                 | MVAPICH2, OpenMPI, MPICH platformy MPI, Intel MPI  |
| Další architektury       | Sjednocené komunikace X, libfabric PGAS |
| Podpora služby Azure Storage       | Std + Premium (max. 4 disky) |
| Podpora operačního systému pro SR-IOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Podpora Azure CycleCloud    | Ano                         |
| Podpora služby Azure Batch         | Ano                         |

## <a name="next-steps"></a>Další postup

* Další informace o velikostech virtuálních počítačů v prostředí HPC pro [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) v Azure.

* Další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
