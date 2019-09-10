---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "67174996"
---
**Nespravované disky virtuálních počítačů úrovně Premium: Omezení podle účtů**

| Resource | Výchozí omezení |
| --- | --- |
| Celková kapacita disku na účet |35 TB |
| Celková kapacita snímků na účet |10 TB |
| Maximální šířka pásma na účet (příchozí přenos dat a odchozí přenos dat)<sup>1</sup> |<=50 Gbp/s |

<sup>1</sup> Příchozí přenos dat odkazuje na všechna data z požadavků, které se odesílají do účtu úložiště. *Výstupní* služba odkazuje na všechna data z odpovědí, které jsou přijímány z účtu úložiště.

**Nespravované disky virtuálních počítačů úrovně Premium: Omezení podle počtu disků**

| Typ disku pro Premium Storage | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Velikost disku |128 GiB |512 GiB |1 024 GiB (1 TB) |2 048 GiB (2 TB)|4 095 GiB (4 TB)|
| Maximální počet IOPS na disk |500 |2,300 |5 000 |7 500 |7,500 |
| Maximální propustnost na disk |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Maximální počet disků na účet úložiště |280 |70 |35 | 17 | 8 |

**Nespravované disky virtuálních počítačů úrovně Premium: Omezení pro virtuální počítače**

| Resource | Výchozí omezení |
| --- | --- |
| Maximální počet vstupně-výstupních operací na virtuální počítač |80 000 IOPS s virtuálním počítačem s GS5 |
| Maximální propustnost na virtuální počítač |2 000 MB/s s virtuálním počítačem s GS5 |

