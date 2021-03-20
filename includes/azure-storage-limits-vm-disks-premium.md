---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "80334975"
---
**Nespravované disky virtuálních počítačů úrovně Premium: omezení podle účtů**

| Prostředek | Omezení |
| --- | --- |
| Celková kapacita disku na účet |35 TB |
| Celková kapacita snímků na účet |10 TB |
| Maximální šířka pásma na účet (příchozí přenos dat a odchozí přenos dat)<sup>1</sup> |<=50 Gbp/s |

<sup>1</sup>příchozí přenos *dat odkazuje na* všechna data z požadavků, které se odesílají do účtu úložiště. *Výstupní* služba odkazuje na všechna data z odpovědí, které jsou přijímány z účtu úložiště.

**Nespravované disky virtuálních počítačů úrovně Premium: omezení na disk**

| Typ disku pro Premium Storage | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Velikost disku |128 GiB |512 GiB |1 024 GiB (1 TB) |2 048 GiB (2 TB)|4 095 GiB (4 TB)|
| Maximální počet IOPS na disk |500 |2 300 |5 000 |7 500 |7 500 |
| Maximální propustnost na disk |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Maximální počet disků na účet úložiště |280 |70 |35 | 17 | 8 |

**Nespravované disky virtuálních počítačů úrovně Premium: omezení na jeden virtuální počítač**

| Prostředek | Omezení |
| --- | --- |
| Maximální počet vstupně-výstupních operací na virtuální počítač |80 000 IOPS s virtuálním počítačem s GS5 |
| Maximální propustnost na virtuální počítač |2 000 MB/s s virtuálním počítačem s GS5 |

