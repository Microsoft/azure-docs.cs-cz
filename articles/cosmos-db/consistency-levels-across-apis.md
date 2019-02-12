---
title: Úrovně konzistence a rozhraní API služby Cosmos DB
description: Principy úrovní konzistence napříč rozhraní API ve službě Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: b620ca76cfea296e504afffd91852308a01575db
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001963"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Úrovně konzistence a rozhraní API služby Cosmos DB

Pět modelů konzistence nabízených službou Azure Cosmos DB nativně podporuje rozhraní SQL API. Při použití služby Azure Cosmos DB, rozhraní SQL API je výchozí hodnota. 

Azure Cosmos DB poskytuje nativní podporu také pro přenosový protokol kompatibilní rozhraní API pro oblíbené databáze. Zahrnout databáze MongoDB, Apache Cassandra, Gremlin a Azure Table storage. Tyto databáze nenabízí přesně definovaných modelů konzistence a záruky jištěná smlouva SLA pro úrovně konzistence. Obvykle poskytují pouze podmnožinu pět modelů konzistence nabízených službou Azure Cosmos DB. Pro rozhraní SQL API, Gremlin API a rozhraní API tabulky se používá výchozí úroveň konzistence nakonfigurovaný na účtu Azure Cosmos. 

V následujících částech se dozvíte mapování mezi konzistence dat požadoval ovladač klienta OSS pro Apache Cassandra, MongoDB a odpovídající úrovně konzistence ve službě Azure Cosmos DB.

## <a id="cassandra-mapping"></a>Mapování mezi úrovněmi konzistence Apache Cassandra a Azure Cosmos DB

Následující tabulka popisuje různé kombinace konzistence, které můžete s použitím rozhraní Cassandra API a mapování úrovně konzistence ekvivalentní nativní služby Cosmos DB. Všechny kombinace Apache Cassandra zápisu a čtení režimy jsou nativně podporovány službou Cosmos DB. V každé kombinací modelu konzistence čtení a zápisu Apache Cassandra Cosmos DB bude poskytovat záruky konzistence stejné nebo větší než Apache Cassandra. Kromě toho služby Cosmos DB poskytuje záruky větší odolnost než Apache Cassandra i v nejslabší režimu zápisu.

Následující tabulka ukazuje **zápisu mapování konzistence** mezi Azure Cosmos DB a Cassandra:

| Cassandra | Azure Cosmos DB | Záruka |
| - | - | - |
|VŠE|Silné  | Linearizovatelnost |
| EACH_QUORUM   | Silné    | Linearizovatelnost | 
| KVORA, SÉRIOVÉHO PORTU |  Silné |    Linearizovatelnost |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Konzistentní předpona |Global Consistent Prefix |
| EACH_QUORUM   | Silné    | Linearizovatelnost |
| KVORA, SÉRIOVÉHO PORTU |  Silné |    Linearizovatelnost |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Konzistentní předpona | Global Consistent Prefix |
| KVORA, SÉRIOVÉHO PORTU | Silné   | Linearizovatelnost |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Konzistentní předpona | Global Consistent Prefix |
| LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE    | Omezená neaktuálnost | <ul><li>Omezená Neaktuálnost.</li><li>Maximálně K verzí nebo t čas za bránou.</li><li>Přečtěte si nejnovější potvrzená hodnota v oblasti.</li></ul> |
| ONE, LOCAL_ONE, ANY   | Konzistentní předpona | Konzistentní předpona v jednotlivých oblastech |

Následující tabulka ukazuje **mapování konzistence čtení** mezi Azure Cosmos DB a Cassandra:

| Cassandra | Azure Cosmos DB | Záruka |
| - | - | - |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO, ONE, LOCAL_ONE | Silné  | Linearizovatelnost|
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Silné |   Linearizovatelnost |
|LOCAL_ONE, ONE | Konzistentní předpona | Global Consistent Prefix |
| VŠECHNY KVORA, SÉRIOVÉ   | Silné    | Linearizovatelnost |
| LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  Konzistentní předpona   | Global Consistent Prefix |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM |    Konzistentní předpona   | Global Consistent Prefix |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Silné |   Linearizovatelnost |
| LOCAL_ONE, ONE    | Konzistentní předpona | Global Consistent Prefix|
| VŠECHNY KVORA, SERIAL silné Linearizovatelnosti
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |Konzistentní předpona  | Global Consistent Prefix |
|VŠE    |Silné |Linearizovatelnost |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  |Konzistentní předpona  |Global Consistent Prefix|
|VŠECHNY KVORA, SERIAL silné Linearizovatelnosti
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |Konzistentní předpona  |Global Consistent Prefix |
|VŠE    |Silné | Linearizovatelnost |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | Konzistentní předpona | Global Consistent Prefix |
| QUORUM, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  Omezená neaktuálnost   | <ul><li>Omezená Neaktuálnost.</li><li>Maximálně K verzí nebo t čas za bránou. </li><li>Přečtěte si nejnovější potvrzená hodnota v oblasti.</li></ul>
| LOCAL_ONE, ONE |Konzistentní předpona | Konzistentní předpona v jednotlivých oblastech |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | Konzistentní předpona | Konzistentní předpona v jednotlivých oblastech |


## <a id="mongo-mapping"></a>Mapování mezi úrovněmi konzistence MongoDB 3.4 a Azure Cosmos DB

V následující tabulce jsou uvedeny "načíst obavy" mapování mezi MongoDB 3.4 a výchozí úrovně konzistence ve službě Azure Cosmos DB. V tabulce jsou uvedeny nasazení ve více oblastech a jedné oblasti.

| **MongoDB 3.4** | **Azure Cosmos DB (více oblastí)** | **Azure Cosmos DB (jedné oblasti)** |
| - | - | - |
| Linearizable | Silné | Silné |
| Většina | Omezená neaktuálnost | Silné |
| Místní | Konzistentní předpona | Konzistentní předpona |

## <a name="next-steps"></a>Další postup

Další informace o úrovních konzistence a kompatibilitu API služby Azure Cosmos DB pomocí rozhraní API pro open source. Viz následující články:

* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Funkce MongoDB podporovaných rozhraním API Azure Cosmos DB pro MongoDB](mongodb-feature-support.md)
* [Apache Cassandra funkcí podporovaných službou Azure Cosmos DB Cassandra API](cassandra-support.md)