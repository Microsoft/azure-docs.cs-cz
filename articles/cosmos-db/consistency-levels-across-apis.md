---
title: Úrovně konzistence a rozhraní API služby Azure Cosmos DB | Dokumentace Microsoftu
description: Pochopení úrovní konzistence napříč rozhraní API ve službě Azure Cosmos DB.
keywords: konzistence, služby azure cosmos db, azure, modely, mongodb, cassandra, graf, tabulky, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 22ddfbf86dacdd5d7f80d1fa7826d6ae11c4de45
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52680391"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Úrovně konzistence a rozhraní API služby Azure Cosmos DB

Pět modelů konzistence nabízených službou Azure Cosmos DB nativně podporuje SQL API služby Azure Cosmos DB. Při použití služby Azure Cosmos DB, rozhraní SQL API je výchozí hodnota. 

Azure Cosmos DB poskytuje nativní podporu také pro přenosový protokol kompatibilní rozhraní API pro oblíbené databáze. Zahrnout databáze MongoDB, Apache Cassandra, Gremlin a Azure Table storage. Tyto databáze nenabízí přesně definovaných modelů konzistence a záruky jištěná smlouva SLA pro úrovně konzistence. Obvykle poskytují pouze podmnožinu pět modelů konzistence nabízených službou Azure Cosmos DB. Pro rozhraní SQL API, Gremlin API a rozhraní API tabulky se používá výchozí úroveň konzistence nakonfigurovaný na účtu Azure Cosmos DB. 

V následujících částech se dozvíte mapování mezi konzistence dat požadoval ovladač klienta OSS pro Apache Cassandra 4.x a MongoDB 3.4. Tento dokument taky zobrazuje odpovídající úrovně konzistence služby Azure Cosmos DB pro Apache Cassandra a MongoDB.

## <a id="cassandra-mapping"></a>Mapování mezi úrovněmi konzistence Apache Cassandra a Azure Cosmos DB

Tato tabulka ukazuje "konzistence čtení" mapování mezi klientovi Cassandry 4.x a výchozí úrovně konzistence ve službě Azure Cosmos DB. V tabulce jsou uvedeny nasazení ve více oblastech a jedné oblasti.

| **Apache Cassandra 4.x** | **Azure Cosmos DB (více oblastí)** | **Azure Cosmos DB (jedné oblasti)** |
| - | - | - |
| JEDNA DVA TŘI | Konzistentní předpona | Konzistentní předpona |
| LOCAL_ONE | Konzistentní předpona | Konzistentní předpona |
| KVORA, VŠECHNY, SÉRIOVÉ | Omezená neaktuálnost je výchozí nastavení. Silná je ve verzi private preview. | Silné |
| LOCAL_QUORUM | Omezená neaktuálnost | Silné |
| LOCAL_SERIAL | Omezená neaktuálnost | Silné |

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
* [Funkce MongoDB podporovaných rozhraním MongoDB API Azure Cosmos DB](mongodb-feature-support.md)
* [Apache Cassandra funkcí podporovaných službou Azure Cosmos DB Cassandra API](cassandra-support.md)