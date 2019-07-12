---
title: Vysoce výkonného výpočetního prostředí na virtuálních počítačích řady H-series – Azure Virtual Machines | Dokumentace Microsoftu
description: Další informace o funkcích a možnostech virtuálních počítačů řady H-series optimalizované pro prostředí HPC.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.author: amverma
ms.openlocfilehash: d6e857a87e4c7df8ffb2be1eefb7a0290da5b10a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800061"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Vysoce výkonného výpočetního prostředí na virtuálních počítačích řady H-series

Vysokovýkonné výpočetní prostředí (HPC) na virtuální počítače řady hybridní připojení a HB-series umožňují nejvíce optimalizaci výkonu HPC, virtuálních počítačů v Azure. Virtuální počítače optimalizované HPC se používají k vyřešit některé automatizování nejobtížnějších matematických problémů, jako například: dynamika tekutin, ropa a plyn simulace nebo modelování počasí.

Tento článek popisuje některé klíčové funkce služby HB-series a virtuální počítače řady hybridní připojení, proč se tyto virtuální počítače provádět i v prostředí HPC scénáře a jak začít pracovat.

## <a name="features-and-capabilities"></a>Funkce a možnosti

HB-series a virtuální počítače řady hybridní připojení jsou navržené tak, aby poskytovat nejlepší výkon HPC, message passing interface (MPI) škálovatelnost a cenově výhodné řešení pro úlohy v prostředí HPC.

### <a name="message-passing-interface"></a>Rozhraní předávání zpráv

Hybridní připojení řady a HB series podporují téměř všechny MPI typy a verze. Některé z nejvíc běžné podporované MPI typy jsou: OpenMPI, MVAPICH2, platforma MPI, Intel MPI a paměti všech vzdáleného přímého přístupu příkazy (počítače RDMA). Další informace najdete v tématu [nastavení rozhraní předávání zpráv pro prostředí HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA a InfiniBand

Rozhraní RDMA je standardní HB-series a virtuální počítače řady hybridní připojení. Instance s podporou RDMA komunikovat přes síť InfiniBand, provozní sazby Rozšířená data (EDR) pro virtuální počítače HB řady a series hybridní připojení. Podporující RDMA instance může zvýšit škálovatelnost a výkonnostní některých aplikací MPI.

Konfigurace sítí InfiniBand podpora HB-series a virtuální počítače řady hybridní připojení se neblokující fat stromové struktury s nízkou průměr návrhem konzistentní výkon RDMA.

Zobrazit [povolit InfiniBand](enable-infiniband.md) Další informace o nastavení InfiniBand HB řady nebo virtuálních počítačích řady hybridní připojení.

## <a name="get-started"></a>Začínáme

Nejdřív se rozhodněte, kterou řada H-series virtuálních počítačů se chystáte používat. Podrobnosti o HPC optimalizovaná virtuální počítače, naleznete v tématu [přehled HB řad](hb-series-overview.md) a [hybridní připojení řady přehled](hc-series-overview.md). Specifikace, naleznete v tématu [vysoce výkonné výpočetní velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Jakmile jsme vybrali a vytvoření virtuálního počítače pro vaši aplikaci, budete muset nakonfigurovat tím, že InfiniBand. Zjistěte, jak povolit InfiniBand na virtuální počítače s Linuxem i Windows, najdete v článku [povolit InfiniBand](enable-infiniband.md).

Je zásadní součástí sady úloh HPC MPI. Hybridní připojení řady a HB series podporují téměř všechny MPI typy a verze. Další informace najdete v tématu [nastavení rozhraní předávání zpráv pro prostředí HPC](setup-mpi.md).

Jakmile zvolíte řady virtuálních počítačů, nastavení Infiniband a MPI, jste připraveni začít vytvářet úlohy HPC.

## <a name="next-steps"></a>Další postup

- Zkontrolujte [přehled HB řad](hb-series-overview.md) a [přehled hybridní připojení series](hc-series-overview.md) Další informace o klíčových rozdílů a specifikace.

- Vyšší úroveň zobrazení architektury spuštěných úloh prostředí HPC, naleznete v tématu [vysoký výkon výpočetního prostředí (HPC) v Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
