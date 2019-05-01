---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 4b7cda593bd4dd39a7220aa282529535c6a63bea
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64914536"
---
Virtuální počítače Azure řady H-series (VM) jsou určeny k vedení třídy výkon, škálovatelnost MPI a cenově výhodné řešení pro různé úlohy HPC v reálném světě.

Virtuální počítače HB-Series jsou optimalizované pro aplikace využívající šířku pásma paměti, jako je dynamika tekutin, explicitní analýza konečných prvků a modelování počasí. Virtuální počítače řady HB-Series jsou vybavené 60 procesorovými jádry AMD EPYC 7551 bez technologie hyperthreading a 4 GB paměti RAM na procesorové jádro. Platforma AMD EPYC poskytuje šířku pásma paměti víc než 260 GB/s.

Hybridní připojení series virtuálních počítačů optimalizovaných pro aplikace s využitím hustému výpočtu, jako je například analýza konečných prvků implicitní, molekulární dynamics a výpočetní chemie. Virtuální počítače řady HC-Series jsou vybavené 44 procesorovými jádry Intel Xeon Platinum 8168 bez technologie hyperthreading a 8 GB paměti RAM na procesorové jádro. Podporuje platformy Intel Xeon Platinum bohatý ekosystém společnosti Intel řady softwarových nástrojů, jako je například knihovna Intel matematické jádra.

Virtuální počítače hybridní připojení i HB funkce 100 Gb/s InfiniBand EDR Mellanox v systému souborů fat neblokující strom konfigurace pro konzistentní výkon RDMA. HB a hybridní připojení virtuálních počítačů podporují standardní ovladače Mellanox/OFED tak, aby všechny typy MPI a verze, jakož i příkazy RDMA, jsou podporovány také.

Virtuální počítače řady H-series jsou optimalizované pro aplikace s využitím vysoké frekvence procesoru nebo velké paměti na základních požadavků. Řada H-series virtuálních počítačů funkci 8 nebo 16 Intel Xeon E5 2667 v3 procesoru jader, 7 nebo 14 GB paměti RAM na jádro procesoru a žádné hyperthreadingem. Řada H-series, funkcí, 56 Gb/s Mellanox díky technologii FDR InfiniBand v systému souborů fat neblokující strom konfigurace pro konzistentní výkon RDMA. Virtuální počítače řady H-series podporují Intel MPI 5.x a MS-MPI.

## <a name="hb-series"></a>HB-series

Premium Storage: Storage úrovně Premium podporované ukládání do mezipaměti: Podporováno

| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Šířka pásma paměti GB/s | Základní frekvenci procesoru (GHz) | Frekvence všech jader (GHz, ve špičce) | Jednojádrový frekvence (GHz, ve špičce) | Výkon RDMA (GB/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální ethernetové síťové adaptéry |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Vše | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>Hybridní připojení series

Premium Storage: Storage úrovně Premium podporované ukládání do mezipaměti: Podporováno


| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Šířka pásma paměti GB/s | Základní frekvenci procesoru (GHz) | Frekvence všech jader (GHz, ve špičce) | Jednojádrový frekvence (GHz, ve špičce) | Výkon RDMA (GB/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální ethernetové síťové adaptéry |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Vše | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H-series

ACU: 290-300

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

| Velikost | Virtuální procesory | Procesor | Paměť (GB) | Šířka pásma paměti GB/s | Základní frekvenci procesoru (GHz) | Frekvence všech jader (GHz, ve špičce) | Jednojádrový frekvence (GHz, ve špičce) | Výkon RDMA (GB/s) | Podpora MPI | Dočasné úložiště (GB) | Max. datových disků | Maximální ethernetové síťové adaptéry |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | 2000 | Intel 5.x, MS-MPI | 64 | 4 |
| Typu Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | 2000 | Intel 5.x, MS-MPI | 64 | 4 |

<sup>1</sup> pro aplikace MPI vyhrazenou síť back-end RDMA zapnutá síť FDR InfiniBand, která zajišťuje mimořádně nízkou latenci a velkou šířku pásma.

<br>