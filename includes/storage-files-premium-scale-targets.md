---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542666"
---
#### <a name="additional-premium-file-share-level-limits"></a>Další prémiové souboru omezení úrovní sdílené složky

|Oblast  |Target  |
|---------|---------|
|Minimální velikost zvětšit nebo zmenšit    |1 GiB      |
|Základní IOPS    |1 vstupně-výstupních operací za GiB maximálně 100 000|
|Shlukování vstupně-výstupních operací    |3 x IOPS za GiB maximálně 100 000|
|Frekvence odchozího přenosu dat         |60 MiB/s + 0,06 * zřízené GiB        |
|Rychlost příchozího přenosu dat| 40 MiB/s + 0,04 * zřízené GiB |

#### <a name="file-level-limits"></a>Omezení na úrovni souboru

|Oblast  |Soubor Premium  |Standardní soubor |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|Maximální počet vstupně-výstupních operací na soubor     |5 000         |1 000|
|Souběžné obslužné rutiny    |2 000         |2 000|
|Příchozí přenos dat  |300 MiB za sekundu|      Zobrazit hodnoty propustnosti standardní soubor|
|Výchozí přenos   |200 Mib za sekundu| Zobrazit hodnoty propustnosti standardní soubor|
|Propustnost| Zobrazit hodnoty příchozí a odchozí přenos souboru premium| Až 60 MiB za sekundu|