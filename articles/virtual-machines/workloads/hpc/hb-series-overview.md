---
title: Přehled virtuálních počítačů s funkcí neřady-Series – Azure Virtual Machines | Microsoft Docs
description: Přečtěte si o podpoře verze Preview pro velikost virtuálního počítače s rozhraním \ řady v Azure.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "67707764"
---
# <a name="hb-series-virtual-machines-overview"></a>Přehled virtuálních počítačů s více než jednou řadou

Maximalizace výkonu aplikace COMPUTE s vysokým výkonem (HPC) na AMD EPYC vyžaduje, aby se důkladné přístup k paměti a umístění procesu. Pod seosnovou architektury AMD EPYC a naší implementací IT v Azure pro aplikace HPC. K odkazování na fyzickou doménu NUMA a "vNUMA" použijeme termín "pNUMA", který odkazuje na virtualizované domény NUMA.

V případě, že je řada velmi série, je k disřadě 2 × 32 – Core EPYC 7551 procesorů a celkový počet ne64 fyzických jader. Tyto 64 jádra jsou rozdělené do 16 pNUMA domén (8 na jeden soket), z nichž každá je čtyři jádra a označuje se jako "komplexní procesor" (neboli "CCX"). Každý CCX má vlastní mezipaměť L3, což znamená, jak se v operačním systému zobrazí hranice pNUMA/vNUMA. Dvojice sousedících CCXs sdílí přístup ke dvěma kanálům fyzické paměti DRAM (32 GB TB na serverech s ne).

Pro zajištění toho, aby měl hypervisor Azure fungovat, aniž by došlo ke konfliktu s virtuálním počítačem, vyhrazujeme si fyzickou pNUMA doménu 0 (první CCX). Pak přiřadíme pNUMA domény 1-15 (zbývající jednotky CCX) pro virtuální počítač. Virtuální počítač uvidí tyto informace:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`jader na virtuální počítač

Virtuální počítač sám o sobě neví, že se mu pNUMA 0 nedostalo. Virtuální počítač rozumí pNUMA 1-15 jako vNUMA 0-14 a 7 vNUMA na vSocket 0 a 8 vNUMA v vSocket 1. I když je to asymetrické, váš operační systém by měl být spuštěný a normálně fungovat. Později v této příručce si vydáte, jak nejlépe spouštět aplikace MPI v tomto asymetrickém rozložení NUMA.

Připnutí procesu bude fungovat na virtuálních počítačích s více procesory, protože vystavení základního silikonu jako je na virtuálním počítači hosta. Důrazně doporučujeme připnutí procesů pro zajištění optimálního výkonu a konzistence.

Další informace o [architektuře AMD EPYC](https://bit.ly/2Epv3kC) a architektuře s [více čipy](https://bit.ly/2GpQIMb) na LinkedInu najdete v tématu. Podrobnější informace najdete v [Průvodci optimalizací HPC pro procesory AMD EPYC](https://bit.ly/2T3AWZ9).

Následující diagram znázorňuje oddělení jader rezervovaných pro Azure hypervisor a virtuální počítač s možností obřadu.

![Oddělení jader rezervovaných pro virtuální počítač s hypervisorem a Azure s více řadami](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specifikace hardwaru

| Specifikace hardwaru                | Virtuální počítač s nejvyšší řadou                     |
|----------------------------------|----------------------------------|
| Cores                            | 60 (SMT zakázané)                |
| Procesor                              | AMD EPYC 7551 *                   |
| Frekvence procesoru (ne AVX)          | ~ 2,55 GHz (jedna + všechna jádra)   |
| Memory (Paměť)                           | 4 GB/jádro (celkem 240)            |
| Místní disk                       | 700 GB NVMe                      |
| InfiniBand                       | 100 GB EDR Mellanox ConnectX-5 * * |
| Síť                          | 50 GB Ethernet (40 GB použitelné) Azure Second gen SmartNIC * * * |

## <a name="software-specifications"></a>Specifikace softwaru

| Specifikace SW           |Virtuální počítač s nejvyšší řadou           |
|-----------------------------|-----------------------|
| Maximální velikost úlohy MPI            | 6000 jader (100 Virtual Machine Scale Sets) 12000 jader (200 Virtual Machine Scale Sets)  |
| Podpora MPI                 | MVAPICH2, OpenMPs, MPICH, Platform MPI, Intel MPI  |
| Další architektury       | Unified Communications X, libfabric, PGAS |
| Podpora Azure Storage       | STD + Premium (max. 4 disky) |
| Podpora operačního systému pro SRIOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Podpora Azure CycleCloud    | Ano                         |
| Podpora Azure Batch         | Ano                         |

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o velikostech virtuálních počítačů HPC pro [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) v Azure.

* Přečtěte si další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
