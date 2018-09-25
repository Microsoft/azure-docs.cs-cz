---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dcd94cbd7963c13bd739b1b004fe5ac506d3ea9d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006471"
---
**Standardní spravovaný virtuální počítač HDD**

| Disk typu Standard  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60              | S70              | S80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Velikost disku v GB          | 32             | 64             | 128            | 256  | 512            | 1,024    | 2 048     | 4095    | 8 192     | 16384     | 32,767     |
| Vstupně-výstupní operace za sekundu / disk       | Až 500              | Až 500              | Až 500              | Až 500 | Až 500              | Až 500              | Až 500             | Až 500              | 1 až 300              | Až 2 000              | Až 2 000              |
| Propustnost / disk | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu| Až 300 MiB za sekundu | Až 500 MiB/s | Až 500 MiB/s |

**Standardní spravovaný virtuální počítač disky SSD**

| Typ disku SSD na úrovni Standard  | E10               | E15               | E20             | E30 | E40              | E50              | E60              | E70              | E80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Velikost disku v GB           | 128             | 256             | 512            | 1,024  | 2 048            | 4095     | 8 192     | 16384     | 32,767    |
| Vstupně-výstupní operace za sekundu / disk       | Až 500              | Až 500              | Až 500              | Až 500 | Až 500              | Až 500              | Až 500             | Až 500              | 1 až 300              | Až 2 000              | Až 2 000              |
| Propustnost / disk | Až 60 MB za sekundu | Až 60 MB za sekundu | Až 60 MB za sekundu | Až 60 MB za sekundu | Až 60 MB za sekundu | Až 60 MB za sekundu | Až 60 MB za sekundu | Až 60 MB za sekundu| Až 300 MiB za sekundu |  Až 500 MiB/s | Až 500 MiB/s |

**Spravované disky virtuálních počítačů typu Premium: omezení na jeden disk**

| Typ disku úrovně Premium  | P4               | P6               | P10             | P15 | P20              | S30              | P40              | P50              | P60              | P70              | P80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Velikost disku v GB           | 32             | 64             | 128            | 256  | 512            | 1,024    | 2 048     | 4095    | 8 192     | 16384     | 32,767     |
| Vstupně-výstupní operace za sekundu / disk       | Až 120 | Až 240              | Až 500              | Až 1100 | Až 2,300              | Až 5 000              | Až 7500             | Až 7500              | Až 12 500              | Až pro 15 000              | Až 20 000              |
| Propustnost / disk | Až pro 25 MiB za sekundu | Až na 50 MiB za sekundu | Až 100 MiB/s | Až 125 MiB za sekundu | Až 150 MiB za sekundu | Až 200 MiB za sekundu | Až 250 MiB za sekundu | Až 250 MiB za sekundu| Až 480 MiB za sekundu | Až 750 MiB za sekundu | Až 750 MiB za sekundu |

**Spravované disky virtuálních počítačů typu Premium: omezení na jeden virtuální počítač**

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximum vstupně-výstupních operací za sekundu (IOPS) na virtuální počítač |80 000 IOPS s virtuálním Počítačem GS5 |
| Maximální propustnost na virtuální počítač |2 000 MB/s s virtuálním Počítačem GS5 |
