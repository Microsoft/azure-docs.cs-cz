---
title: Úrovně konzistence a rozhraní API služby Cosmos DB
description: Principy úrovní konzistence napříč rozhraní API ve službě Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a8617ae2b01fc89a4c957b8610164a2b53a16f5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59274769"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Úrovně konzistence a rozhraní API služby Cosmos DB

Azure Cosmos DB poskytuje nativní podporu pro přenosový protokol kompatibilní rozhraní API pro oblíbené databáze. Patří mezi ně MongoDB, Apache Cassandra, Gremlin a Azure Table storage. Tyto databáze nenabízejí přesně definovaných modelů konzistence a záruky jištěná smlouva SLA pro úrovně konzistence. Obvykle poskytují pouze podmnožinu pět modelů konzistence nabízených službou Azure Cosmos DB. 

Při použití rozhraní SQL API, Gremlin API a rozhraní API tabulky, se používá výchozí úroveň konzistence nakonfigurovaný na účtu Azure Cosmos. 

Při použití rozhraní Apache Cassandra API nebo Azure Cosmos DB: API pro MongoDB, získávání aplikací kompletní o úrovních konzistence nabízených databází Apache Cassandra a MongoDB, v uvedeném pořadí, s ještě přesvědčivější konzistence a záruky odolnosti. Tento dokument ukazuje odpovídající úrovně konzistence služby Azure Cosmos DB pro Apache Cassandra a úrovně konzistence MongoDB.


## <a id="cassandra-mapping"></a>Mapování mezi úrovněmi konzistence Apache Cassandra a Azure Cosmos DB

Na rozdíl od AzureCosmos databáze Apache Cassandra neposkytuje nativní přesně záruky konzistence definované.  Místo toho Apache Cassandra poskytuje úroveň konzistence zápisu a úroveň konzistence čtení, chcete povolit vysokou dostupnost, konzistence a latence kompromisy. Při použití služby Azure Cosmos DB Cassandra API: 

* Úroveň konzistence zápisu Apache Cassandra je namapována na výchozí úroveň konzistence nakonfigurovaný na vašem účtu Azure Cosmos. 

* Azure Cosmos DB dynamicky namapuje úroveň konzistence čtení zadaný ovladač Cassandra klienta na jednu z úrovní konzistence služby Azure Cosmos DB dynamicky konfigurovat na žádost o čtení. 

Následující tabulka ukazuje, jak nativní úrovně konzistence Cassandra jsou mapované na úrovně konzistence rozhraní Azure Cosmos DB při používání rozhraní Cassandra API:  

[![Mapování modelu konzistence Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Mapování mezi úrovněmi konzistence MongoDB a Azure Cosmos DB

Na rozdíl od služby Azure Cosmos DB neposkytuje nativní MongoDB přesně záruky konzistence definované. Místo toho nativní MongoDB umožňuje uživatelům konfigurovat následující záruky konzistence: žádný problém zápisu, čtení znepokojení a ismaster nebo direktivě - směrovat operace čtení na primární nebo sekundární repliky k dosažení úrovně požadovaného konzistence. 

Při použití rozhraní API služby Azure Cosmos DB pro MongoDB, ovladač MongoDB považuje za oblast zápisu na primární repliku a všechny ostatní oblasti jsou repliky pro čtení. Můžete zvolit, které oblasti přidružené k účtu Azure Cosmos jako primární repliku. 

Při používání služby Azure Cosmos DB přes rozhraní API pro MongoDB:

* Zápis zájmem je namapována na výchozí úroveň konzistence nakonfigurovaný na vašem účtu Azure Cosmos.
 
* Azure Cosmos DB dynamicky namapuje čtení problém určené ovladače klienta MongoDB na jednu z úrovní konzistence služby Azure Cosmos DB, která je dynamicky nakonfigurovaná na žádost o čtení. 

* Přidání poznámek ke konkrétní oblasti přidružené k účtu Azure Cosmos jako "Hlavní" tím, že oblast jako první zapisovatelný oblasti. 

Následující tabulka ukazuje, jak nativní MongoDB zápisu/čtení otázky jsou mapovány na úrovně konzistence Azure Cosmos, při použití rozhraní API služby Azure Cosmos DB pro MongoDB:

[![Mapování modelu konzistence MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Další postup

Další informace o úrovních konzistence a kompatibilitu API služby Azure Cosmos DB pomocí rozhraní API pro open source. Viz následující články:

* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Funkce MongoDB podporovaných rozhraním API Azure Cosmos DB pro MongoDB](mongodb-feature-support.md)
* [Apache Cassandra funkcí podporovaných službou Azure Cosmos DB Cassandra API](cassandra-support.md)