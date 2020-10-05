---
title: Vysoce výkonné výpočetní prostředí na InfiniBand s povolenými virtuálními počítači řady H-Series a N-Series – Azure Virtual Machines
description: Seznamte se s funkcemi a funkcemi pro InfiniBand s povolenými virtuálními počítači řady H-Series a N-Series optimalizovanými pro HPC.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms-subservice: hpc
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/29/2020
ms.openlocfilehash: f9a672777fbc8a75cb6af10ba25232f8a274c894
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87543720"
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

Povolené rozhraní SR-IOV s podporou H-Series a N-series podporují skoro všechny MPI knihovny a verze. Některé z nejběžnějších podporovaných knihoven MPI jsou: Intel MPI, OpenMPs, MPICH, MVAPICH2, Platform MPI a všechny operace vzdáleného přímého přístupu do paměti (RDMA).
Další informace o instalaci různých podporovaných knihoven MPI a jejich optimální konfiguraci najdete v tématu [Nastavení MPI](setup-mpi.md) .

## <a name="get-started"></a>Začínáme

Prvním krokem je výběr typu virtuálních počítačů [řady H-Series](../../sizes-hpc.md) a [N-Series](../../sizes-gpu.md) optimální pro úlohy na základě specifikací virtuálních počítačů a [Možnosti RDMA](../../sizes-hpc.md#rdma-capable-instances).
Potom nakonfigurujte virtuální počítač tak, že povolíte InfiniBand. Existují různé metody, včetně použití optimalizovaných imagí virtuálních počítačů s vloženými ovladačů. Podrobnosti najdete v tématu [optimalizace pro Linux](configure.md) a [Povolení InfiniBand](enable-infiniband.md) .
Pro úlohy distribuovaných uzlů, které jsou pro úlohy distribuovaného uzlu třetí, je důležité vybrat a nakonfigurovat MPI. Podrobnosti najdete v tématu [Nastavení MPI](setup-mpi.md) .
Čtvrtá, z hlediska výkonu a škálovatelnosti můžete optimálně nakonfigurovat úlohy podle pokynů, které jsou specifické pro rodinu virtuálních počítačů, jako jsou například přehledy v rámci řady [7000](hb-series-overview.md) a [Přehled pro řady HC](hc-series-overview.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [konfiguraci a optimalizaci](configure.md) virtuálních počítačů řady [H-Series](../../sizes-hpc.md) a [N-Series](../../sizes-gpu.md) s podporou InfiniBand.
- Seznamte se s přehledem a [řadou HC](hc-series-overview.md) - [Series](hb-series-overview.md) – přehled s optimální konfigurací úloh pro zajištění výkonu a škálovatelnosti.
- Přečtěte si o nejnovějších oznámeních a některých příkladech HPC a výsledcích na [blogu Azure COMPUTE tech Community](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pro zobrazení architektury na vyšší úrovni pro spouštění úloh HPC si přečtěte téma věnované technologii [HPC (High Performance Computing) v Azure](/azure/architecture/topics/high-performance-computing/).
