---
title: Omezení Azure Cosmos DB Gremlin
description: Referenční dokumentace pro běhová omezení modulu grafu
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: f1fe4cfac22a651f44338986d0a767fe3e1a360b
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910990"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB omezení Gremlin
Tento článek pojednává o omezeních Azure Cosmos DBho stroje Gremlin a vysvětluje, jak mohou ovlivnit zákaznická procházení.

Cosmos DB Gremlin je postavená na Cosmos DB infrastruktuře, což je důvod, proč stále platí všechna omezení v [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) . 

## <a name="limits"></a>Omezení

Když je dosaženo limitu Gremlin, přecházení se zruší s **x-MS-Status-Code** = 429 indikující chybu omezování.

**Prostředek**    | **Výchozí omezení** | **Vysvětlení**
--- | --- | ---
*Paměť na žádost* | **2 GB** | Maximální velikost paměti, kterou může požadavek při zpracování využít. Požadavky, které musí počítat s velkými datovými sadami, budou spotřebovávat další paměť. Vezměte v úvahu rozsah požadavků na menší datové sady, abyste se vyhnuli překročení tohoto limitu nebo použití řešení OLAP.
*Délka skriptu* | **64 kB** | Maximální délka skriptu procházení Gremlin na požadavek.
*Hloubka operátoru* | **400** |  Celkový počet jedinečných kroků v procházení. Například ```g.V().out()``` má operátor počet 2: V () a out (), ```g.V('label').repeat(out()).times(100)``` má hloubku operátoru 3: V (), opakujte () a out (), protože ```.times(100)``` je ```.repeat()``` parametr operátoru.
*Stupeň paralelismu* | **32** | Maximální počet oddílů úložiště dotazovaných v rámci jednoho požadavku na vrstvu úložiště. Toto omezení bude mít vliv na grafy se stovkami oddílů.
*Omezení opakování* | **32** | Maximální počet iterací, které může provést operátor ```.repeat()```. Každá iterace ```.repeat()``` kroku ve většině případů spouští průchod po šířce, což znamená, že všechny průchody jsou omezeny na nejvíce 32 segmentů směrování mezi vrcholy.
*Časový limit pro procházení* | **30 sekund** | Pokud překročí tento čas, bude procházení zrušeno. Graf Azure Cosmos DB je databáze OLTP a naprostá většina procházení se dokončí v řádu milisekund. Pokud chcete spouštět dotazy OLAP v Cosmos DB graphu, použijte [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) s [datovými rámečky grafu](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) a [Cosmos DB Spark Connector](https://github.com/Azure/azure-cosmosdb-spark).
*Omezení predikátu* | **20** | Počet kroků ```.has()``` nebo ```.hasNot()``` použitých u jednotlivých vrcholů nebo hran. Když dojde k dosažení tohoto limitu, v aplikaci se zobrazí chyba ```The SQL query exceeded the maximum number of joins. The allowed limit is 20```. Je to dočasnou nepříjemnosti, protože tým pracuje na zvedání tohoto limitu. 
*Časový limit nečinného připojení* | **5 hodin** | Doba, po kterou se bude server grafu zobrazovat, zůstane připojení protokolu WebSocket otevřené bez provozu. Pakety Keep-Alive protokolu TCP nebo udržování Keep-Alive neprodlužují připojení po uplynutí tohoto limitu, ale pokud se neodesílají, podkladová infrastruktura Azure může připojení ještě dřív ukončit. Modul Cosmos DB Graph považuje za nečinný, pokud v něm nejsou spuštěny žádné procházeníy Gremlin.
*Token prostředku za hodinu* | **100** | Počet jedinečných tokenů prostředků používaných klienty Gremlin pro připojení k účtu Gremlin v oblasti Pokud aplikace překročí limit hodinových jedinečných `"Exceeded allowed resource token limit of 100 that can be used concurrently"` tokenů, bude vrácena při příštím požadavku na ověření.

## <a name="next-steps"></a>Další kroky
* [Hlavičky odpovědi Azure Cosmos DB Gremlin](gremlin-headers.md) 
* [Azure Cosmos DB tokeny prostředků pomocí Gremlin](how-to-use-resource-tokens-gremlin.md)
