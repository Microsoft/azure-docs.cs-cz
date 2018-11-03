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
ms.openlocfilehash: 974531cd5907e4f69e7d064125d3e51fa4974949
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50956379"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Úrovně konzistence a rozhraní API služby Azure Cosmos DB

Pět modelů konzistence nabízených službou Azure Cosmos DB nativně podporuje rozhraní API Cosmos DB SQL, což je výchozí rozhraní API, při použití služby Cosmos DB. Kromě rozhraní SQL API Cosmos DB také poskytuje nativní podporu přenosový protokol kompatibilních rozhraní API pro oblíbené databáze, například MongoDB, Apache Cassandra, Gremlin a tabulek Azure. Tyto databáze ani nabízejí přesně definované modely konzistence ani záruky jištěná smlouva SLA pro úrovně konzistence. Tyto databáze obvykle poskytují pouze podmnožinu pět modelů konzistence nabízených službou Cosmos DB. Pro rozhraní SQL API, Gremlin API a rozhraní API tabulky se používá výchozí úroveň konzistence, který konfigurujete v účtu Cosmos.

V následujících částech se dozvíte mapování mezi požadoval ovladač klienta OSS pro Apache Cassandra konzistence dat 4.x a MongoDB 3.4 při použití rozhraní Cassandra API a MongoDB API, v uvedeném pořadí a příslušné úrovně konzistence, Cosmos DB.

## <a id="cassandra-mapping"></a>Mapování mezi úrovněmi konzistence Apache Cassandra a Cosmos DB

V následující tabulce jsou uvedeny "konzistence čtení" mapování mezi klientem 4.x Apache Cassandra a výchozí úrovně konzistence ve službě Cosmos DB pro nasazení ve více oblastech a jedné oblasti.

| **Apache Cassandra 4.x** | **Cosmos DB (více oblastí)** | **Cosmos DB (jedné oblasti)** |
| - | - | - |
| JEDNA DVA TŘI | Konzistentní předpona | Konzistentní předpona |
| LOCAL_ONE | Konzistentní předpona | Konzistentní předpona |
| KVORA, VŠECHNY, SÉRIOVÉ | Omezená Neaktuálnost (výchozí) nebo silné (ve verzi private preview) | Silné |
| LOCAL_QUORUM | Omezená neaktuálnost | Silné |
| LOCAL_SERIAL | Omezená neaktuálnost | Silné |

## <a id="mongo-mapping"></a>Mapování mezi úrovněmi konzistence MongoDB 3.4 a Cosmos DB

V následující tabulce jsou uvedeny "načíst obavy" mapování mezi MongoDB 3.4 a výchozí úroveň konzistence ve službě Cosmos DB pro nasazení ve více oblastech a jedné oblasti.

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