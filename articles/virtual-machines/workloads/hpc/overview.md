---
title: Vysoce výkonné výpočetní prostředí na InfiniBand s povolenými virtuálními počítači řady H-Series a N-Series – Azure Virtual Machines
description: Seznamte se s funkcemi a funkcemi pro InfiniBand s povolenými virtuálními počítači řady H-Series a N-Series optimalizovanými pro HPC.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 03/18/2021
ms.reviewer: cynthn
ms.openlocfilehash: 65b37a8c07e083f5e9809812e2d4446cc48717d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720589"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>Vysoce výkonné výpočetní prostředí na InfiniBand s povolenými virtuálními počítači řady H-Series a N-Series

Virtuální počítače řady H-Series a N-Series s povoleným InfiniBand Azure jsou navržené tak, aby poskytovaly škálovatelnost výkonné třídy, škálovatelnost rozhraní MPI (Message Passing Interface) a cenovou efektivitu pro celou řadu úloh HPC a AI z reálného světa. Tyto vysoce výkonné výpočetní virtuální počítače (HPC) se používají k řešení některých z nejúčinnějších náročných problémů v oblasti vědy a techniky, jako jsou například kapalné dynamiky, modelování země, simulace počasí atd.

Tyto články popisují, jak začít s virtuálními počítači řady H-Series s podporou InfiniBand a N-Series v Azure a taky s optimální konfigurací úloh HPC a AI na virtuálních počítačích pro zajištění škálovatelnosti.

## <a name="features-and-capabilities"></a>Funkce a možnosti

Virtuální počítače řady H-Series a N-Series s podporou InfiniBand jsou navržené tak, aby poskytovaly nejlepší výkon HPC, škálovatelnost MPI a cenovou efektivitu pro úlohy HPC. Další informace o funkcích a možnostech virtuálních počítačů najdete v tématu virtuální počítače řady [H-Series](../../sizes-hpc.md) a [N-Series](../../sizes-gpu.md) .

### <a name="rdma-and-infiniband"></a>RDMA a InfiniBand

Virtuální [počítače s](../../sizes-hpc.md) [podporou RDMA](../../sizes-hpc.md#rdma-capable-instances) a [řady N-Series](../../sizes-gpu.md) komunikují přes InfiniBand síť s nízkou latencí a velkou šířkou pásma. Schopnost RDMA v takovém propojení je zásadní pro zvýšení škálovatelnosti a výkonu funkcí HPC a AI distribuovaných uzlů. Virtuální počítače s povoleným InfiniBand H-Series a N-Series se připojují v neblokujícím stromu FAT s návrhem s nízkým průměrem pro optimalizaci a konzistentní výkon RDMA.
Další informace o nastavení InfiniBand na virtuálních počítačích s podporou InfiniBand najdete v tématu [Povolení InfiniBand](enable-infiniband.md) .

### <a name="message-passing-interface"></a>Rozhraní pro předávání zpráv

Povolené rozhraní SR-IOV s podporou H-Series a N-series podporují skoro všechny MPI knihovny a verze. Některé z nejčastěji používaných knihoven MPI jsou: Intel MPI, OpenMPs, HPC-X, MVAPICH2, MPICH, Platform MPI. Všechny operace vzdáleného přímého přístupu do paměti (RDMA) jsou podporovány.
Další informace o instalaci různých podporovaných knihoven MPI a jejich optimální konfiguraci najdete v tématu [Nastavení MPI](setup-mpi.md) .

## <a name="get-started"></a>Začínáme

Prvním krokem je výběr typu virtuálních počítačů [řady H-Series](../../sizes-hpc.md) a [N-Series](../../sizes-gpu.md) optimální pro úlohy na základě specifikací virtuálních počítačů a [Možnosti RDMA](../../sizes-hpc.md#rdma-capable-instances).
Potom nakonfigurujte virtuální počítač tak, že povolíte InfiniBand. Existují různé metody, včetně použití optimalizovaných imagí virtuálních počítačů s vloženými ovladačů. Podrobnosti najdete v tématu [optimalizace pro Linux](configure.md) a [Povolení InfiniBand](enable-infiniband.md) .
Třetí pro úlohy distribuovaných uzlů volba a konfigurace MPI je kritická. Podrobnosti najdete v tématu [Nastavení MPI](setup-mpi.md) .
Čtvrtá, z hlediska výkonu a škálovatelnosti můžete optimálně nakonfigurovat úlohy podle pokynů, které jsou specifické pro rodinu virtuálních počítačů, jako jsou například [HBv3-Series Overview](hbv3-series-overview.md) a [HC-Series Overview](hc-series-overview.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [konfiguraci a optimalizaci](configure.md) virtuálních počítačů řady [H-Series](../../sizes-hpc.md) a [N-Series](../../sizes-gpu.md) s podporou InfiniBand.
- V přehledu [HBv3-Series](hb-series-overview.md) Overview a [HC-Series](hc-series-overview.md) najdete informace o optimální konfiguraci úloh pro zajištění výkonu a škálovatelnosti.
- Přečtěte si o nejnovějších oznámeních, příkladech úloh HPC a výsledcích výkonu na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
