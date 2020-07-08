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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597779"
---
Pokud chcete chránit data ve sdílených složkách Azure proti ztrátě nebo poškození dat, všechny sdílené složky Azure uchovávají při zápisu více kopií každého souboru. V závislosti na požadavcích vaší úlohy můžete vybrat další stupně redundance. Soubory Azure v současné době podporují následující možnosti redundance dat:

- **Místně redundantní**: místně redundantní úložiště, které se často označuje jako LRS, znamená, že každý soubor je uložený třikrát v rámci clusteru Azure Storage. To chrání před ztrátou dat z důvodu hardwarových chyb, jako je například chybná disková jednotka.
- **Zóna redundantní**: redundantní úložiště zóny, které se často označuje jako ZRS, znamená, že každý soubor je uložen třikrát ve třech různých clusterech Azure Storage. Tři samostatné clustery úložiště Azure každý soubor uloží třikrát, stejně jako u místně redundantního úložiště a fyzicky se izolují v různých *zónách dostupnosti*Azure. Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Zápis do úložiště není přijatý, dokud se nezapíše do clusterů úložiště ve všech třech zónách dostupnosti. 
- **Geograficky redundantní**: geograficky redundantní úložiště, často označované jako GRS, je jako místně redundantní úložiště, v tom, že soubor je uložen třikrát v rámci clusteru Azure Storage v primární oblasti. Všechny zápisy se pak asynchronně replikují do sekundární oblasti definované Microsoftem. Geograficky redundantní úložiště poskytuje 6 kopií dat rozdělených mezi dvě oblasti Azure. V případě závažné havárie, jako je trvalá ztráta oblasti Azure z důvodu přírodní havárie nebo jiné podobné události, provede Microsoft převzetí služeb při selhání, aby se sekundární v důsledku stalo primárním a obsluhující všechny operace. Vzhledem k tomu, že replikace mezi primárními a sekundárními oblastmi je asynchronní, v případě závažné havárie dojde ke ztrátě dat, která se ještě nereplikují do sekundární oblasti. Můžete také provést ruční převzetí služeb při selhání geograficky redundantního účtu úložiště.
- **Redundantní zóna redundantní**: redundantní úložiště geografických zón, často označovaná jako GZRS, je jako redundantní úložiště zóny, v tom, že soubor je uložený devětně ve třech různých clusterech úložiště v primární oblasti. Všechny zápisy se pak asynchronně replikují do sekundární oblasti definované Microsoftem. Proces převzetí služeb při selhání u redundantního úložiště s geografickou zónou funguje stejně jako u geograficky redundantního úložiště.

Standardní sdílené složky Azure podporují všechny čtyři typy redundance, zatímco sdílené složky Azure úrovně Premium podporují jenom místně redundantní a redundantní úložiště zóny.

Účty úložiště pro obecné účely verze 2 (GPv2) poskytují dvě další možnosti redundance, které nejsou podporovány službou Azure Files: přístup k geograficky redundantnímu úložišti, který se často označuje jako RA-GRS, a v geograficky redundantním úložišti s přístupem pro čtení, které se často označuje jako RA-GZRS. Můžete zřídit sdílené složky Azure v účtech úložiště s těmito možnostmi nastavení, ale soubory Azure nepodporují čtení ze sekundární oblasti. Sdílené složky Azure nasazené do geograficky redundantních nebo geografických účtů s přístupem pro čtení se budou fakturovat jako geograficky redundantní nebo geograficky redundantní úložiště, v uvedeném pořadí.