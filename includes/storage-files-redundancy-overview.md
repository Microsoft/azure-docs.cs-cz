---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 87457bb103f49be4ca3e7bf9f463c5bf63f3a119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597779"
---
K ochraně dat ve sdílených složek Azure před ztrátou nebo poškozením dat, všechny sdílené složky Azure ukládat více kopií každého souboru, jak jsou zapsány. V závislosti na požadavcích vašeho pracovního vytížení můžete vybrat další stupně redundance. Soubory Azure aktuálně podporuje následující možnosti redundance dat:

- **Místně redundantní**: Místně redundantní úložiště, často označované jako LRS, znamená, že každý soubor je uložen třikrát v clusteru úložiště Azure. To chrání před ztrátou dat v důsledku selhání hardwaru, jako je například chybná disková jednotka.
- **Redundantní zóna**: Zónové redundantní úložiště, často označované jako ZRS, znamená, že každý soubor je uložen třikrát ve třech různých clusterech úložišť Azure. Tři odlišné clustery úložišť Azure každý uložit soubor třikrát, stejně jako s místně redundantní úložiště a jsou fyzicky izolované v různých *zónách dostupnosti*Azure . Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Zápis do úložiště není přijat, dokud není zapsán do clusterů úložiště ve všech třech zónách dostupnosti. 
- **Geograficky redundantní**: Geograficky redundantní úložiště, často označované jako GRS, je jako místně redundantní úložiště v tom, že soubor je uložen třikrát v clusteru úložiště Azure v primární oblasti. Všechny zápisy jsou pak asynchronně replikovány do sekundární oblasti definované společností Microsoft. Geograficky redundantní úložiště poskytuje 6 kopií vašich dat rozprostřených mezi dvěma oblastmi Azure. V případě závažné havárie, jako je trvalá ztráta oblasti Azure v důsledku přírodní katastrofy nebo jiné podobné události, Microsoft provede převzetí služeb při selhání tak, aby sekundární v platnosti stane primární, sloužící všechny operace. Vzhledem k tomu, že replikace mezi primární a sekundární oblasti jsou asynchronní, v případě závažné havárie budou ztracena data, která ještě nejsou replikována do sekundární oblasti. Můžete také provést ruční převzetí služeb při selhání geograficky redundantního účtu úložiště.
- **Redundantní geografická zóna**: Redundantní úložiště v geografické zóně, často označované jako GZRS, je jako zónově redundantní úložiště v tom, že soubor je uložen devětkrát ve 3 různých clusterech úložišť v primární oblasti. Všechny zápisy jsou pak asynchronně replikovány do sekundární oblasti definované společností Microsoft. Proces převzetí služeb při selhání pro geograficky redundantní úložiště funguje stejně jako pro geograficky redundantní úložiště.

Standardní sdílené složky Azure podporují všechny čtyři typy redundance, zatímco prémiové sdílené složky Azure podporují jenom místně redundantní a zónově redundantní úložiště.

Účty úložiště pro obecné účely verze 2 (GPv2) poskytují dvě další možnosti redundance, které nejsou podporovány soubory Azure: čtení přístupného geograficky redundantního úložiště, často označované jako RA-GRS, a čtení přístupného geograficky redundantního úložiště, často označované jako RA-GZRS. Můžete zřídit sdílené složky Azure v účtech úložiště s těmito nastavenými možnostmi, ale Soubory Azure nepodporuje čtení ze sekundární oblasti. Sdílené složky Azure nasazené do účtů redundantního úložiště s přístupem na čtení nebo geozónu se budou účtovat jako geograficky redundantní úložiště nebo úložiště s geografickou zónou.