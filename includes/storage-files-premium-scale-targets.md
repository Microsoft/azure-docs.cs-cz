---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71180040"
---
#### <a name="additional-premium-file-share-level-limits"></a>Další limity úrovně sdílené složky premium

|Oblast  |Cíl  |
|---------|---------|
|Minimální zvětšení/snížení velikosti    |1 GiB      |
|IOP Směrného plánu    |1 IOPS na GiB, až 100 000|
|Roztržení IOPS    |3x IOPS za GiB, až 100 000|
|Rychlost výstupu         |60 MiB/s + 0,06 * zřízený GiB        |
|Rychlost příchozího přenosu dat| 40 MiB/s + 0,04 * zřízený GiB |

#### <a name="file-level-limits"></a>Omezení úrovně souborů

|Oblast  |Prémiový soubor  |Standardní soubor |
|---------|---------|---------|
|Velikost                  |1 TiB         |1 TiB|
|Maximální počet vipos na soubor     |5 000         |1 000|
|Souběžné popisovače    |2 000         |2 000|
|Výchozí přenos dat  |300 MiB/s|      Viz standardní hodnoty propustnosti souborů|
|Příchozí přenos dat  |200 MiB/s| Viz standardní hodnoty propustnosti souborů|
|Propustnost| Viz hodnoty příchozího/odchozího přenosu a výstupu souboru premium| Až 60 MiB/s|
