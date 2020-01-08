---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c88cba57ed987074c0dde3c8b251f54393667740
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75002674"
---
| Velikosti SSD úrovně Premium | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Velikost disku v GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2 048 | 4 096 | 8 192 | 16 384 | 32,767 |
| Počet vstupně-výstupních operací za sekundu na disk | 120 | 120 | 120 | 120 | 240 | 500 | 1 100 | 2 300 | 5 000 | 7 500 | 7 500 | 16,000 | 18 000 | 20,000 |
| Propustnost / disk | 25 MiB/s | 25 MiB/s | 25 MiB/s | 25 MiB/s | 50 MiB/s | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s| 500 MiB/s | 750 MiB/s | 900 MiB/s |
| Maximální počet shlukových vstupně-výstupních operací na disk * * | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 |
| Maximální propustnost shluku na disk * * | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s |
| Maximální doba trvání shluku * * | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |

\*označuje velikost disku, která je aktuálně ve verzi Preview, pro regionální informace o dostupnosti se zobrazí [nové velikosti disků: spravované a nespravované](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).

\*\*označuje funkci, která je momentálně ve verzi Preview, další informace najdete v tématu věnovaném [shlukování disku](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) .
