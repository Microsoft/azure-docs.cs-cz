---
title: Vysoce výkonné výpočetní prostředí na virtuálních počítačích H-Series – Azure Virtual Machines
description: Seznamte se s funkcemi a funkcemi pro virtuální počítače H-Series optimalizované pro HPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "76271024"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Vysoce výkonné výpočetní prostředí na virtuálních počítačích řady H-Series

Vysoce výkonné výpočetní prostředí (HPC) na virtuálních počítačích s více procesory a řady HC-Series umožňují optimální výkon HPC u všech virtuálních počítačů v Azure. Optimalizované virtuální počítače HPC se používají k řešení některých z nejobtížnějších matematických problémů, jako jsou například kapalné dynamiky, simulace ropných a plynových simulací a modelování počasí.

Tento článek popisuje některé klíčové funkce virtuálních počítačů s více procesory a řady HC-Series, proč tyto virtuální počítače ve scénářích HPC fungují dobře a jak začít.

## <a name="features-and-capabilities"></a>Funkce a možnosti

Virtuální počítače s MPI a HC-Series jsou navržené tak, aby poskytovaly nejlepší výkon HPC, škálovatelnost rozhraní pro předávání zpráv () a cenovou efektivitu pro úlohy prostředí HPC.

### <a name="message-passing-interface"></a>Rozhraní pro předávání zpráv

Řady s více než-Series a HC-series podporují skoro všechny MPI typy a verze. Některé z nejběžnějších podporovaných typů MPI jsou: OpenMPs, MVAPICH2, Platform MPI, Intel MPI a všechny operace vzdáleného přímého přístupu do paměti (RDMA). Další informace najdete v tématu [nastavení rozhraní pro předávání zpráv pro HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA a InfiniBand

Rozhraní RDMA je standard na virtuálních počítačích s více procesory a řady HC-Series. Instance s podporou RDMA komunikují přes síť InfiniBand, která je založená na rozšířených datových tarifech (EDR) pro virtuální počítače s více instancemi a s řadou HC-Series. Instance podporující RDMA můžou zvýšit škálovatelnost a výkon některých aplikací MPI.

Konfigurace InfiniBand podporující virtuální počítače typu 7000-Series a HC-Series nejsou blokující stromy se systémem souborů FAT s návrhem s nízkým průměrem pro zajištění konzistentního výkonu RDMA.

Další informace o nastavení InfiniBand na virtuálních počítačích s InfiniBand nebo HC-Series najdete v tématu [Povolení](enable-infiniband.md) .

## <a name="get-started"></a>Začínáme

Nejdřív se rozhodněte, který virtuální počítač řady H-Series budete používat. Podrobnosti o virtuálních počítačích optimalizovaných pro HPC najdete v tématu Přehled [pro velmi řadu](hb-series-overview.md) a [přehledy HC-Series](hc-series-overview.md). Specifikace najdete v tématu [vysoce výkonné výpočetní virtuální počítače s vysokým výkonem](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Po vybrání a vytvoření virtuálního počítače pro aplikaci ho budete muset nakonfigurovat tak, že povolíte InfiniBand. Informace o tom, jak povolit InfiniBand na virtuálních počítačích s Windows i Linux, najdete v tématu [Povolení InfiniBand](enable-infiniband.md).

Kritická součást úloh HPC je MPI. Řady s více než-Series a HC-series podporují skoro všechny MPI typy a verze. Další informace najdete v tématu [nastavení rozhraní pro předávání zpráv pro HPC](setup-mpi.md).

Až si vyberete řadu virtuálních počítačů, nastavíte InfiniBand a MPI, jste připraveni začít sestavovat úlohy prostředí HPC.

## <a name="next-steps"></a>Další kroky

- Další informace o klíčových rozdílech a specifikacích najdete v článku Přehled a Přehled řady [HC](hc-series-overview.md) - [Series](hb-series-overview.md) .

- Pro vyšší úroveň architektury spouštění úloh HPC si přečtěte téma věnované architektuře [HPC (High Performance Computing) v Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
