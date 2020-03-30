---
title: Vysoce výkonná výpočetní technika na virtuálních počítačích řady H – virtuální počítače Azure
description: Seznamte se s funkcemi a možnostmi virtuálních počítačů řady H optimalizovaných pro HPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76271024"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Vysoce výkonná výpočetní technika na virtuálních počítačích řady H

Vysoce výkonná výpočetní technika (HPC) na virtuálních počítačích řady HB a HC umožňuje nejoptimalizovanější výkon HPC ze všech virtuálních počítačů v Azure. Virtuální počítače optimalizované pro HPC se používají k řešení některých z nejtěžších matematických problémů, jako jsou: dynamika tekutin, simulace ropy a plynu a modelování počasí.

Tento článek popisuje některé klíčové funkce hb-series a HC-series virtuálních počítačů, proč tyto virtuální počítače fungují dobře ve scénářích HPC a jak začít.

## <a name="features-and-capabilities"></a>Funkce a možnosti

Virtuální počítače řady HB a HC jsou navrženy tak, aby poskytovaly nejlepší výkon HPC, škálovatelnost rozhraní MPI (message passing interface) a efektivitu nákladů pro úlohy HPC.

### <a name="message-passing-interface"></a>Rozhraní pro předávání zpráv

Hb-series a HC-series podporují téměř všechny typy a verze MPI. Některé z nejběžnějších podporovaných typů MPI jsou: OpenMPI, MVAPICH2, Platform MPI, Intel MPI a všechna slovesa vzdáleného přímého přístupu do paměti (RDMA). Další informace naleznete v [tématu Nastavení rozhraní pro předávání zpráv pro HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA a InfiniBand

Rozhraní RDMA je standardní u virtuálních zařízení řady HB a HC. Instance podporující RDMA komunikují prostředpou sítě InfiniBand, která pracuje s vyšší přenosovou rychlostí (EDR) pro virtuální počítače řady HB a HC. Instance podporující RDMA mohou zvýšit škálovatelnost a výkon některých aplikací MPI.

Konfigurace InfiniBand podporující virtuální počítače řady HB a HC jsou neblokující tukové stromy s nízkoprůměrovým designem pro konzistentní výkon RDMA.

Další informace o nastavení infinibandu na virtuálních počítačích řady HB nebo HC najdete v tématu [Povolení infiniBandu.](enable-infiniband.md)

## <a name="get-started"></a>Začínáme

Nejprve se rozhodněte, který virtuální virtuální mon to J-series budete používat. Podrobnosti o virtuálních počítačích optimalizovaných pro HPC najdete v [tématu přehled řady HB](hb-series-overview.md) a [přehled řady HC](hc-series-overview.md). Specifikace najdete v tématu [Vysoce výkonné výpočetní velikosti virtuálních počítače](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Jakmile vyberete a vytvoříte virtuální počítač pro vaši aplikaci, budete ho muset nakonfigurovat povolením InfiniBandu. Informace o povolení programu InfiniBand na virtuálních počítačích s Windows i Linuxem najdete v [tématu Enable InfiniBand](enable-infiniband.md).

Důležitou součástí úloh HPC je MPI. Hb-series a HC-series podporují téměř všechny typy a verze MPI. Další informace naleznete v [tématu Nastavení rozhraní pro předávání zpráv pro HPC](setup-mpi.md).

Jakmile si vyberete řadu virtuálních počítačů, nastavíte infiniband a MPI, můžete začít vytvářet úlohy HPC.

## <a name="next-steps"></a>Další kroky

- Projděte si [přehled řady HB](hb-series-overview.md) a přehled řady [HC,](hc-series-overview.md) kde se dozvíte o klíčových rozdílech a specifikacích.

- Pro vyšší úroveň, architektonické zobrazení spuštěných úloh HPC, najdete v tématu [High Performance Computing (HPC) v Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
