---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: c2bd10ab4c98fe2e77332c3cc2566ab2f0c7ad42
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87841815"
---
#### <a name="additional-premium-file-share-level-limits"></a>Další omezení úrovně sdílení souborů Premium

|Oblast  |Cíl  |
|---------|---------|
|Minimální velikost – zvýšení a snížení velikosti    |1 GiB      |
|Směrný IOPS    |1 IOPS na GiB, až do 100 000|
|Shlukování IOPS    |3x IOPS za GiB, až do 100 000|
|Míra odchozích dat         |60 MiB/s + 0,06 * zřízené GiB        |
|Míra příchozího přenosu dat| 40 MiB/s + 0,04 * zřízené GiB |

#### <a name="file-level-limits"></a>Omezení na úrovni souborů

|Oblast  |Soubor úrovně Premium  |Standardní soubor |
|---------|---------|---------|
|Velikost                  |4 TiB         |1 TiB|
|Maximální počet vstupně-výstupních operací na soubor     |5 000         |1 000|
|Souběžné popisovače    |2 000         |2 000|
|Výchozí přenos dat  |300 MiB/s|      Zobrazit standardní hodnoty propustnosti souboru|
|Příchozí přenos dat  |200 MiB/s| Zobrazit standardní hodnoty propustnosti souboru|
|Propustnost| Viz hodnoty vstupně-výstupních souborů Premium| Až 60 MiB/s|
