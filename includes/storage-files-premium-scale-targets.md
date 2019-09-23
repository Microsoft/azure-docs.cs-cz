---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180040"
---
#### <a name="additional-premium-file-share-level-limits"></a>Další omezení úrovně sdílení souborů Premium

|Oblast  |Target  |
|---------|---------|
|Minimální velikost – zvýšení a snížení velikosti    |1 GiB      |
|Základní IOPS    |1 IOPS na GiB, až do 100 000|
|Shlukování IOPS    |3x IOPS za GiB, až do 100 000|
|Míra odchozích dat         |60 MiB/s + 0,06 * zřízené GiB        |
|Míra příchozího přenosu dat| 40 MiB/s + 0,04 * zřízené GiB |

#### <a name="file-level-limits"></a>Omezení na úrovni souborů

|Oblast  |Soubor úrovně Premium  |Standardní soubor |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|Maximální počet vstupně-výstupních operací na soubor     |5,000         |1 000|
|Souběžné popisovače    |2 000         |2 000|
|Výchozí přenos  |300 MiB/s|      Zobrazit standardní hodnoty propustnosti souboru|
|Příchozí přenos dat  |200 MiB/s| Zobrazit standardní hodnoty propustnosti souboru|
|Propustnost| Viz hodnoty vstupně-výstupních souborů Premium| Až 60 MiB/s|
