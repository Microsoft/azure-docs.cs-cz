---
title: Úrovně konzistence a rozhraní API služby Cosmos DB
description: Principy úrovní konzistence napříč rozhraní API ve službě Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 4d2994ea6ab6d6472ec56f0f2e378062590c8920
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54806993"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Úrovně konzistence a rozhraní API služby Cosmos DB

Pět modelů konzistence nabízených službou Azure Cosmos DB nativně podporuje SQL API služby Azure Cosmos DB. Při použití služby Azure Cosmos DB, rozhraní SQL API je výchozí hodnota. 

Azure Cosmos DB poskytuje nativní podporu také pro přenosový protokol kompatibilní rozhraní API pro oblíbené databáze. Zahrnout databáze MongoDB, Apache Cassandra, Gremlin a Azure Table storage. Tyto databáze nenabízí přesně definovaných modelů konzistence a záruky jištěná smlouva SLA pro úrovně konzistence. Obvykle poskytují pouze podmnožinu pět modelů konzistence nabízených službou Azure Cosmos DB. Pro rozhraní SQL API, Gremlin API a rozhraní API tabulky se používá výchozí úroveň konzistence nakonfigurovaný na účtu Azure Cosmos DB. 

V následujících částech se dozvíte mapování mezi konzistence dat požadoval ovladač klienta OSS pro Apache Cassandra 4.x a MongoDB 3.4. Tento dokument taky zobrazuje odpovídající úrovně konzistence služby Azure Cosmos DB pro Apache Cassandra a MongoDB.

## <a id="cassandra-mapping"></a>Mapování mezi úrovněmi konzistence Apache Cassandra a Azure Cosmos DB

Tato tabulka uvádí mapování konzistence mezi Apache Cassandra a úrovně konzistence ve službě Azure Cosmos DB. Pro každý z Cassandra čtení a zápis úrovní konzistence odpovídající úroveň konzistence Cosmos DB nabízí silnější, například přísnější záruky.

Následující tabulka ukazuje **zápisu konzistence mapování** mezi Azure Cosmos DB a Cassandra:

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

Následující tabulka ukazuje **čtení konzistence mapování** mezi Azure Cosmos DB a Cassandra:

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