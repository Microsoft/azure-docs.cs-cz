---
title: Virtuální počítač HBv3-Series – přehled – Azure Virtual Machines | Microsoft Docs
description: Přečtěte si o velikosti virtuálního počítače řady HBv3-Series v Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d1abd03f517f9e0b13a2994418cbae5cfbe22454
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801858"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Přehled virtuálních počítačů s HBv3-Series 

Server [HBv3-Series](../../hbv3-series.md) Features 2 * 64-Core EPYC procesory 7V13 po dobu celkem 128 fyzických jader "Zen3". Souběžné multithreading (SMT) je v HBv3 zakázané. Tyto 128 jádra jsou rozdělené do 16 částí (8 na jeden soket), každý oddíl obsahující 8 procesorových jader s jednotným přístupem k mezipaměti L3 s 32 MB. Servery Azure HBv3 také spouští následující nastavení systému AMD BIOS:

```bash
Nodes per Socket =2
L3 as NUMA = Disabled
```

V důsledku toho se Server spouští se 4 doménami NUMA (2 na jeden soket), které mají velikost 32 – jader. Každé rozhraní NUMA má přímý přístup ke 4 kanálům fyzického DRAM, který pracuje na 3200. MT/s.

Pro zajištění toho, aby měl hypervisor Azure fungovat, aniž by došlo ke konfliktu s virtuálním počítačem, vyhrazujeme si 8 fyzických jader na server. 

Počítejte s tím, že velikosti virtuálních počítačů s omezeními omezují jenom počet fyzických jader vystavených pro virtuální počítač. Všechny globální sdílené prostředky (RAM, propustnost paměti, mezipaměť L3, GMI a připojení xGMI, InfiniBand, síť Ethernet Azure, místní SSD) zůstávají konstantní. To umožňuje zákazníkovi vybrat velikost virtuálního počítače, která je nejlépe přizpůsobená dané sadě úloh nebo požadavků na licencování softwaru.

Následující diagram znázorňuje oddělení jader rezervovaných pro Azure hypervisor (žlutá) a virtuální počítač HBv3-Series (zelený).
![Oddělení jader rezervovaných pro virtuální počítač s hypervisorem Azure a HBv3-Series](./media/architecture/hbv3-segregation-cores.png)

## <a name="infiniband-networking"></a>InfiniBand sítě
Virtuální počítače s HBv3 také vypracují síťové adaptéry NVIDIA Mellanox HDR InfiniBand (ConnectX-6), které fungují až 200 gigabitů za sekundu. Síťové rozhraní se předá přes SRIOV k virtuálnímu počítači a povoluje tak použití síťového provozu k obejít hypervisor. V důsledku toho zákazníci načtou standardní ovladače Mellanox OFED na virtuální počítače HBv3, protože by to byly holé prostředí.

Virtuální počítače s HBv3 podporují adaptivní směrování, dynamický propojený přenos (DCT, ve všech verzích Standard RC a UD) a hardwarové snižování zátěže MPI kolektivních prostředků pro procesor ConnectX-6. Tyto funkce zvyšují výkon, škálovatelnost a konzistenci aplikací a jejich využití se důrazně doporučuje.

## <a name="temporary-storage"></a>Dočasné úložiště
Virtuální počítače s HBv3 – funkce 3: fyzicky místní zařízení SSD. Jedno zařízení je předem naformátováno, aby sloužilo jako stránkovací soubor a se zobrazí ve vašem VIRTUÁLNÍm počítači jako obecné zařízení SSD.

Dva další, větší SSD se poskytují jako neformátované blokové NVMe zařízení prostřednictvím NVMeDirect. Vzhledem k tomu, že blokové zařízení NVMe obchází hypervisoru, bude mít větší šířku pásma, vyšší IOPS a nižší latenci na IOP.

Při párování v prokládaném poli poskytuje NVMe SSD až 7 GB/s čtení a 3 GB/s a až 186 000 IOPS (čtení) a 201 000 IOPS (zápisy) pro hloubky hloubkové fronty.

## <a name="hardware-specifications"></a>Specifikace hardwaru 

| Specifikace hardwaru          | Virtuální počítače řady HBv3-Series              |
|----------------------------------|----------------------------------|
| Cores                            | 120, 96, 64, 32 nebo 16 (SMT zakázané)               | 
| Procesor                              | AMD EPYC 7V13                   | 
| Frekvence procesoru (ne AVX)          | 3,1 GHz (všechny jádra), 3,675 GHz (až 10 jader)    | 
| Memory (Paměť)                           | 448 GB (RAM na jádro závisí na velikosti virtuálního počítače)         | 
| Místní disk                       | 2 * 960 GB NVMe (Block), 480 GB SSD (stránkovací soubor) | 
| InfiniBand                       | 200 GB/s Mellanox ConnectX-6 HDR InfiniBand | 
| Síť                          | 50 GB/s Ethernet (40 GB/s použitelné) Azure Second gen SmartNIC | 

## <a name="software-specifications"></a>Specifikace softwaru 

| Specifikace softwaru        | Virtuální počítače řady HBv3-Series                                            | 
|--------------------------------|-----------------------------------------------------------|
| Maximální velikost úlohy MPI               | 36 000 jader (300 virtuálních počítačů v jedné sadě škálování virtuálních počítačů s singlePlacementGroup = true) |
| Podpora MPI                    | HPC-X, Intel MPI, OpenMPy, MVAPICH2, MPICH  |
| Další architektury          | UCX, libfabric, PGAS                  |
| Podpora Azure Storage          | Disky úrovně Standard a Premium (maximálně 32 disků)              |
| Podpora operačního systému pro SRIOV RDMA      | CentOS/RHEL 7.6 +, Ubuntu 18.04 +, SLES 12 SP4 +, WinServer 2016 +           |
| Doporučený operační systém pro výkon | CentOS 8,1, Windows Server 2019 +
| Podpora nástroje Orchestrator           | Azure CycleCloud, Azure Batch, AKS; [Možnosti konfigurace clusteru](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> Systém Windows Server 2012 R2 není podporován na HBv3 a dalších virtuálních počítačích s více než 64 (virtuálními nebo fyzickými) jádry. Další podrobnosti najdete [tady](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o nejnovějších oznámeních, příkladech úloh HPC a výsledcích výkonu na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).