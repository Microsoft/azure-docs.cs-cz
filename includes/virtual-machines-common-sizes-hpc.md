---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ac8686d0ea5704492bfc2e08972a2f70c9b34c43
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37906815"
---
Virtuální počítače Azure řady H-series je nejnovější vydání v vysokovýkonné výpočetní prostředí, že virtuální počítače zaměřené na potřeby dynamiky, jako je molekulární modelování, výpočty dynamiky tekutin a. Tyto 8 a 16 virtuálních procesorů počítače jsou postavené na procesorů Intel Haswell E5-2667 V3 procesoru technologie nabízejí paměti DDR4 a dočasné úložiště založené na SSD. 

Řada H-series nabízí kromě značného výkonu procesorů i řadu možností přímého přístupu do paměti vzdáleného počítače (RDMA) s nízkou latencí díky technologii FDR InfiniBand, a množství konfigurací paměti pro podporu požadavků na vysoce paměťově náročné výpočetní operace.



## <a name="h-series"></a>H-series

ACU: 290–300

Storage úrovně Premium: Není podporováno.

Premium Storage ukládání do mezipaměti: Není podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost disku: IOPS | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 × 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 × 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 × 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 × 500 |4  |
| Typu Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 × 500 |4  |
| Typu Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 × 500 |4 |

<sup>1</sup> pro aplikace MPI vyhrazenou síť back-end RDMA zapnutá síť FDR InfiniBand, která zajišťuje mimořádně nízkou latenci a velkou šířku pásma.

<br>






