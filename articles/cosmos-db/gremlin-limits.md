---
title: Limity Azure Cosmos DB Gremlin
description: Referenční dokumentace pro omezení modulu Graph modulu za běhu
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72029850"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Omezení Gremlin ve službě Azure Cosmos DB
Tento článek popisuje omezení modulu Azure Cosmos DB Gremlin a vysvětluje, jak mohou ovlivnit průchody zákazníků.

Cosmos DB Gremlin je postaven na infrastruktuře Cosmos DB. Z tohoto důvodu platí všechna omezení vysvětlená v [limitech služeb Azure Cosmos DB.](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) 

## <a name="limits"></a>Omezení

Po dosažení gremlin limitu je traversal zrušen s **x-ms-status-code** 429 označující chybu omezení. Další informace naleznete [v záhlaví odpovědí serveru Gremlin.](gremlin-limits.md)

**Zdrojů**    | **Výchozí omezení** | **Vysvětlení**
--- | --- | ---
*Délka skriptu* | **64 kB** | Maximální délka skriptu procházení Gremlin na požadavek.
*Hloubka obsluhy* | **400** |  Celkový počet jedinečných kroků v procházení. Například ```g.V().out()``` má počet operátorů 2: V() ```g.V('label').repeat(out()).times(100)``` a out(), má hloubku operátoru 3: ```.times(100)``` V(), ```.repeat()``` repeat(), a out(), protože je parametr operátor.
*Stupeň paralelismu* | **32** | Maximální počet oddílů úložiště dotazovaných v rámci jednoho požadavku na vrstvu úložiště. Grafy se stovkami oddílů budou ovlivněny tímto limitem.
*Limit opakování* | **32** | Maximální počet iterací, které může provést operátor ```.repeat()```. Každá iterace ```.repeat()``` kroku ve většině případů spustí šířku první průchod, což znamená, že všechny traversal je omezena na maximálně 32 směrování mezi vrcholy.
*Časový čas průchodu* | **30 sekund** | Traversal bude zrušena, pokud překročí tento čas. Graf Azure Cosmos DB je databáze OLTP a naprostá většina procházení se dokončí v řádu milisekund. Chcete-li spustit dotazy OLAP na Cosmos DB Graph, použijte [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) s [datovými rámci grafu](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) a [Cosmos DB Spark Connector](https://github.com/Azure/azure-cosmosdb-spark).
*Časový limit připojení* | **1 hodina** | Doba, po kterou bude služba Gremlin udržovat nečinná připojení websocketu otevřená. Pakety udržování protokolu TCP nebo požadavky na udržování při připojení http neprodlužují životnost připojení nad tento limit. Modul Cosmos DB Graph považuje připojení websocket za nečinný, pokud na něm nejsou spuštěny žádné aktivní požadavky Gremlin.
*Počet tokenů prostředků za hodinu* | **100** | Počet jedinečných tokenů prostředků, pomocí kterých se klienti Gremlin připojují k účtu Gremlin v příslušné oblasti. Pokud aplikace překročí hodinový limit jedinečného tokenu, `"Exceeded allowed resource token limit of 100 that can be used concurrently"` bude vrácena při dalším požadavku na ověření.

## <a name="next-steps"></a>Další kroky
* [Hlavičky odpovědí Azure Cosmos DB Gremlin](gremlin-headers.md) 
* [Tokeny prostředků Azure Cosmos DB s gremlinem](how-to-use-resource-tokens-gremlin.md)
