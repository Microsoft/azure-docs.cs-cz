---
title: Omezení Azure Cosmos DB Gremlin
description: Referenční dokumentace pro běhová omezení modulu grafu
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029850"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB omezení Gremlin
Tento článek pojednává o omezeních Azure Cosmos DBho stroje Gremlin a vysvětluje, jak mohou ovlivnit zákaznická procházení.

Cosmos DB Gremlin je postavená na Cosmos DB infrastruktuře. Z tohoto důvodu jsou všechna omezení vysvětlena v [Azure Cosmos DB omezení služby](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) jsou stále použitelná. 

## <a name="limits"></a>Omezení

Po dosažení limitu Gremlin se přecházení zruší s **x-MS-Status-code** 429, což značí chybu omezení. Další informace najdete v tématu [hlavičky odpovědi Gremlin serveru](gremlin-limits.md) .

**Prostředek**    | **Výchozí omezení** | **Vysvětlení**
--- | --- | ---
*Délka skriptu* | **64 kB** | Maximální délka skriptu procházení Gremlin na požadavek.
*Hloubka operátorů* | **400** |  Celkový počet jedinečných kroků v procházení. Například ```g.V().out()``` má operátor počtu 2: V () a out (), ```g.V('label').repeat(out()).times(100)``` má hloubku operátoru 3: V (), opakovat () a out (), protože ```.times(100)``` je parametrem operátoru ```.repeat()```.
*Stupeň paralelismu* | **32** | Maximální počet oddílů úložiště dotazovaných v rámci jednoho požadavku na vrstvu úložiště. Toto omezení bude mít vliv na grafy se stovkami oddílů.
*Limit opakování* | **32** | Maximální počet iterací, které může provést operátor ```.repeat()```. Každá iterace kroku ```.repeat()``` ve většině případů spouští průchod po šířce, což znamená, že všechny průchody jsou omezeny na nejvíce 32 segmentů směrování mezi vrcholy.
*Časový limit procházení* | **30 sekund** | Pokud překročí tento čas, bude procházení zrušeno. Graf Azure Cosmos DB je databáze OLTP a naprostá většina procházení se dokončí v řádu milisekund. Pokud chcete spouštět dotazy OLAP v Cosmos DB graphu, použijte [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) s [datovými rámečky grafu](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) a [Cosmos DB Spark Connector](https://github.com/Azure/azure-cosmosdb-spark).
*Časový limit připojení* | **1 hodina** | Doba, po kterou bude služba Gremlin uchovávat neaktivní otevřená připojení WebSocket. Pakety Keep-Alive protokolu TCP nebo udržování Keep-Alive nešíří po tomto limitu životnost připojení. Modul Cosmos DB Graph považuje připojení protokolu WebSocket za nečinné, pokud v něm nejsou spuštěny žádné aktivní žádosti Gremlin.
*Počet tokenů prostředků za hodinu* | **100** | Počet jedinečných tokenů prostředků, pomocí kterých se klienti Gremlin připojují k účtu Gremlin v příslušné oblasti. Když aplikace překročí hodinový jedinečný token, `"Exceeded allowed resource token limit of 100 that can be used concurrently"` se vrátí k dalšímu požadavku na ověření.

## <a name="next-steps"></a>Další kroky
* [Hlavičky odpovědi Azure Cosmos DB Gremlin](gremlin-headers.md) 
* [Azure Cosmos DB tokeny prostředků pomocí Gremlin](how-to-use-resource-tokens-gremlin.md)
