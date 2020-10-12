---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88057692"
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

|Oblast  |Standardní soubor  |Soubor úrovně Premium  |
|---------|---------|---------|
|Velikost     |1 TiB         |4 TiB         |
|Maximální počet vstupně-výstupních operací na soubor      |1 000         |5 000         |
|Souběžné popisovače     |2 000         |2 000         |
|Výchozí přenos dat     |Zobrazit standardní hodnoty propustnosti souboru         |300 MiB/s         |
|Příchozí přenos dat     |Zobrazit standardní hodnoty propustnosti souboru         |200 MiB/s         |
|Propustnost     |Až 60 MiB/s         |Viz hodnoty vstupně-výstupních souborů Premium         |