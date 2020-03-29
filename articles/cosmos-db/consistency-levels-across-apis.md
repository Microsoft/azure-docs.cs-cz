---
title: Úrovně konzistence a rozhraní API služby Cosmos DB
description: Principy mapování úrovně konzistence mezi různými rozhraními API v Azure Cosmos DB a Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: ef7d032d37105549ff7b05f85b953cd420954602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131460"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Úrovně konzistence a rozhraní API služby Cosmos DB

Azure Cosmos DB poskytuje nativní podporu pro rozhraní API kompatibilní s protokolem pro oblíbené databáze. Patří mezi ně MongoDB, Apache Cassandra, Gremlin a Azure Table storage. Tyto databáze nenabízejí přesně definované modely konzistence nebo záruky zálohované s la pro úrovně konzistence. Obvykle poskytují pouze podmnožinu pěti modelů konzistence, které nabízí Azure Cosmos DB. 

Při použití rozhraní SQL API, Gremlin API a table API se používá výchozí úroveň konzistence nakonfigurovaná na účtu Azure Cosmos. 

Při použití rozhraní Cassandra API nebo rozhraní API Azure Cosmos DB pro MongoDB získají aplikace úplnou sadu úrovní konzistence, které nabízejí Apache Cassandra a MongoDB, s ještě silnějšími zárukami konzistence a odolnosti. Tento dokument zobrazuje odpovídající úrovně konzistence Azure Cosmos DB pro úrovně konzistence Apache Cassandra a MongoDB.

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Mapování mezi úrovněmi konzistence Apache Cassandra a Azure Cosmos DB

Na rozdíl od Azure Cosmos DB Apache Cassandra neposkytuje nativně přesně definované záruky konzistence.  Místo toho Apache Cassandra poskytuje úroveň konzistence zápisu a úroveň konzistence čtení, aby byla povolena vysoká dostupnost, konzistence a latence. Při použití rozhraní Cassandra rozhraní Azure Cosmos DB: 

* Úroveň konzistence zápisu Apache Cassandra je namapována na výchozí úroveň konzistence nakonfigurovanou na vašem účtu Azure Cosmos. Konzistenci pro operaci zápisu (CL) nelze změnit na základě požadavku.

* Azure Cosmos DB dynamicky mapuje úroveň konzistence čtení určenou klientským ovladačem Cassandra na jednu z úrovní konzistence Azure Cosmos DB, která je dynamicky nakonfigurovaná na požadavek na čtení. 

Následující tabulka ukazuje, jak jsou nativní úrovně konzistence Cassandra mapovány na úrovně konzistence Azure Cosmos DB při použití rozhraní CASSANDRA API:  

[![Mapování modelu konzistence Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Mapování mezi úrovněmi konzistence MongoDB a Azure Cosmos DB

Na rozdíl od Azure Cosmos DB nativní MongoDB neposkytuje přesně definované záruky konzistence. Místo toho nativní MongoDB umožňuje uživatelům nakonfigurovat následující záruky konzistence: problém zápisu, read concern a isMaster směrnice - nasměrovat operace čtení na primární nebo sekundární repliky k dosažení požadované úrovně konzistence. 

Při použití rozhraní API Azure Cosmos DB pro MongoDB ovladač MongoDB zachází s oblastí zápisu jako primární replikou a všechny ostatní oblasti jsou repliky pro čtení. Můžete si vybrat, která oblast přidružená k vašemu účtu Azure Cosmos jako primární replika. 

Při používání rozhraní API Azure Cosmos DB pro MongoDB:

* Problém s zápisem je mapován na výchozí úroveň konzistence nakonfigurovanou na vašem účtu Azure Cosmos.
 
* Azure Cosmos DB dynamicky mapuje problém čtení určený klientem MongoDB na jednu z úrovní konzistence Azure Cosmos DB, která je dynamicky nakonfigurovaná na požadavek na čtení.  

* Můžete oněcoovat konkrétní oblast přidruženou k vašemu účtu Azure Cosmos jako hlavní tím, že oblast jako první zapisovatelné oblasti. 

Následující tabulka ukazuje, jak jsou nativní problémy s zápisem a čtením MongoDB mapovány na úrovně konzistence Azure Cosmos při použití rozhraní API Azure Cosmos DB pro MongoDB:

[![Mapování modelu konzistence MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o úrovních konzistence a kompatibilitě mezi api Azure Cosmos DB s otevřeným zdrojovým kódem API. Viz následující články:

* [Kompromisy dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Funkce MongoDB podporované rozhraním API Azure Cosmos DB pro MongoDB](mongodb-feature-support.md)
* [Funkce Apache Cassandra podporované rozhraním API Azure Cosmos DB Cassandra](cassandra-support.md)
