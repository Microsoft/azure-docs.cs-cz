---
title: Úrovně konzistence a rozhraní API služby Azure Cosmos DB | Dokumentace Microsoftu
description: Principy úrovní konzistence napříč rozhraní API ve službě Azure Cosmos DB.
keywords: konzistence, služby azure cosmos db, azure, modely, mongodb, cassandra, graf, tabulky, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: ed08b90b9e216ee8713bfe445e98144bf2ba02d4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244189"
---
# <a name="consistency-levels-and-cosmos-db-apis"></a>Úrovně konzistence a rozhraní API Cosmos DB

Pět modelů konzistence nativně podporuje rozhraní SQL API, což je výchozí rozhraní API, při použití služby Cosmos DB. Kromě rozhraní SQL API Cosmos DB také poskytuje nativní podporu přenosový protokol kompatibilních rozhraní API pro oblíbené databáze, například MongoDB, Apache Cassandra, Gremlin a tabulek Azure. Tyto databáze nabízet ani přesně definované modely konzistence ani podporou SLA zaručuje pro úrovně konzistence a obvykle poskytují pouze podmnožinu pět modelů konzistence, které nabízí služby Cosmos DB. Pro rozhraní SQL API, Gremlin API a rozhraní API tabulky se používá výchozí úroveň konzistence nakonfigurovaný na účtu Cosmos.

V následující tabulce jsou uvedeny mapování mezi požadoval ovladač klienta OSS pro Apache Cassandra konzistence dat 4.x a MongoDB 3.4 při použití rozhraní Cassandra API a MongoDB API, v uvedeném pořadí a příslušné úrovně konzistence, Cosmos DB.

## <a id="cassandra-mapping"></a>Mapování úrovní konzistence Apache Cassandra a Cosmos DB

Následující tabulka uvádí mapování pro čtení konzistence mezi klientem 4.x Apache Cassandra a Cosmos DB "Výchozí" úrovně konzistence ve více oblastech a v jedné oblasti nasazení.

| **Apache Cassandra 4.x** | **Cosmos DB (více oblastí)** | **Cosmos DB (jedné oblasti)** |
| - | - | - |
| JEDNA DVA TŘI | Konzistentní předpona | Konzistentní předpona |
| LOCAL_ONE | Konzistentní předpona | Konzistentní předpona |
| KVORA, VŠECHNY, SÉRIOVÉ | Omezená Neaktuálnost (výchozí) nebo silné (ve verzi private preview) | Silné |
| LOCAL_QUORUM | Omezená neaktuálnost | Silné |
| LOCAL_SERIAL | Omezená neaktuálnost | Silné |

## <a id="mongo-mapping"></a>Mapování mezi úrovněmi konzistence MongoDB 3.4 a Cosmos DB

Následující tabulka uvádí mapování pro "čtení"úrovně MongoDB 3.4 a Cosmos DB "Výchozí" konzistence týkající se ve více oblastech a v jedné oblasti nasazení.

| **MongoDB 3.4** | **Cosmos DB (více oblastí)** | **Cosmos DB (jedné oblasti)** |
| - | - | - |
| Linearizable | Silné | Silné |
| Většina | Omezená neaktuálnost | Silné |
| Místní | Konzistentní předpona | Konzistentní předpona |

## <a name="next-steps"></a>Další postup

Další informace o úrovních konzistence a kompatibilitu API služby Cosmos DB s open source rozhraním API v následujících článcích:

* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [MongoDB funkcích podporovaných jednotlivými MongoDB API služby Cosmos DB](mongodb-feature-support.md)
* [Apache Cassandra funkcích podporovaných jednotlivými Cosmos DB Cassandra API](cassandra-support.md)