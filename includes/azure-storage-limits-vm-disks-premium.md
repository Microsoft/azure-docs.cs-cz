---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386293"
---
**Disky Premium nespravovaného virtuálního počítače: Omezení na konkrétním účtu**

| Prostředek | Výchozí omezení |
| --- | --- |
| Celková kapacita disku na účet |35 TB |
| Celková kapacita snímků na účet |10 TB |
| Maximální šířka pásma na účet (příchozí přenos dat + odchozí přenos dat)<sup>1</sup> |<=50 Gbp/s |

<sup>1</sup>*příchozího přenosu dat* odkazuje na všechna data z požadavků, které se odesílají na účet úložiště. *Výchozí přenos dat* odkazuje na všechna data z odpovědi, které byly přijaty z účtu úložiště.

**Disky Premium nespravovaného virtuálního počítače: Omezení na disku**

| Typ disku pro Premium Storage | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Velikost disku |128 GiB |512 GiB |1,024 GiB (1 TB) |2,048 GiB (2 TB)|4,095 GiB (4 TB)|
| Maximální IOPS na disk |500 |2 300 |5 000 |7 500 |7 500 |
| Maximální propustnost na disk |100 MB/s | 150 MB/s |200 MB/s |250 MB/sec |250 MB/sec |
| Maximální počet disků v účtu úložiště |280 |70 |35 | 17 | 8 |

**Disky Premium nespravovaného virtuálního počítače: Limity pro jednotlivé virtuální počítače**

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální IOPS na virtuální počítač |80 000 IOPS s virtuálním Počítačem GS5 |
| Maximální propustnost na virtuální počítač |2 000 MB/s s virtuálním Počítačem GS5 |

