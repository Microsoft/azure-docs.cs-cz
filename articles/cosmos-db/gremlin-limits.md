---
title: Omezení Azure Cosmos DB Gremlin
description: Referenční dokumentace pro běhová omezení modulu grafu
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: sngun
ms.openlocfilehash: 23a265f1ae6e925440e2804056348cb705b09da5
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490556"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Omezení Gremlin ve službě Azure Cosmos DB
Tento článek pojednává o omezeních Azure Cosmos DBho stroje Gremlin a vysvětluje, jak mohou ovlivnit zákaznická procházení.

Cosmos DB Gremlin je postavená na Cosmos DB infrastruktuře. Z tohoto důvodu jsou všechna omezení vysvětlena v [Azure Cosmos DB omezení služby](./concepts-limits.md) jsou stále použitelná.

## <a name="limits"></a>Omezení

Po dosažení limitu Gremlin se přecházení zruší s **x-MS-Status-code** 429, což značí chybu omezení. Další informace najdete v tématu [hlavičky odpovědi Gremlin serveru](gremlin-limits.md) .

**Prostředek**    | **Výchozí omezení** | **Vysvětlení**
--- | --- | ---
*Délka skriptu* | **64 kB** | Maximální délka skriptu procházení Gremlin na požadavek.
*Hloubka operátoru* | **400** |  Celkový počet jedinečných kroků v procházení. Například ```g.V().out()``` má operátor počtu 2: v () a out (), ```g.V('label').repeat(out()).times(100)``` má hloubku operátoru 3: V (), opakovat () a out (), protože ```.times(100)``` je parametrem ```.repeat()``` operátoru.
*Stupeň paralelismu* | **32** | Maximální počet oddílů úložiště dotazovaných v rámci jednoho požadavku na vrstvu úložiště. Toto omezení bude mít vliv na grafy se stovkami oddílů.
*Omezení opakování* | **32** | Maximální počet iterací, které může provést operátor ```.repeat()```. Každá iterace ```.repeat()``` kroku ve většině případů spouští průchod po šířce, což znamená, že všechny průchody jsou omezeny na nejvíce 32 segmentů směrování mezi vrcholy.
*Časový limit pro procházení* | **30 sekund** | Pokud překročí tento čas, bude procházení zrušeno. Graf Azure Cosmos DB je databáze OLTP a naprostá většina procházení se dokončí v řádu milisekund. Pokud chcete spouštět dotazy OLAP v Cosmos DB graphu, použijte [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) s [datovými rámečky grafu](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) a [Cosmos DB Spark Connector](https://github.com/Azure/azure-cosmosdb-spark).
*Časový limit připojení* | **1 hodina** | Doba, po kterou bude služba Gremlin uchovávat neaktivní otevřená připojení WebSocket. Pakety Keep-Alive protokolu TCP nebo udržování Keep-Alive nešíří po tomto limitu životnost připojení. Modul Cosmos DB Graph považuje připojení protokolu WebSocket za nečinné, pokud v něm nejsou spuštěny žádné aktivní žádosti Gremlin.
*Počet tokenů prostředků za hodinu* | **100** | Počet jedinečných tokenů prostředků, pomocí kterých se klienti Gremlin připojují k účtu Gremlin v příslušné oblasti. Pokud aplikace překročí limit hodinových jedinečných tokenů, `"Exceeded allowed resource token limit of 100 that can be used concurrently"` bude vrácena na další požadavek na ověření.

## <a name="next-steps"></a>Další kroky
* [Hlavičky odpovědi Azure Cosmos DB Gremlin](gremlin-headers.md)
* [Azure Cosmos DB tokeny prostředků pomocí Gremlin](how-to-use-resource-tokens-gremlin.md)