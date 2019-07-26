---
title: Úrovně konzistence a rozhraní API služby Cosmos DB
description: Principy úrovní konzistence napříč rozhraními API v Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 26cea6243a8b6d06c132325f0b2fe830c4030e9d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467779"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Úrovně konzistence a rozhraní API služby Cosmos DB

Azure Cosmos DB poskytuje nativní podporu pro rozhraní API kompatibilní se síťovými protokoly pro oblíbené databáze. Patří mezi ně MongoDB, Apache Cassandra, Gremlin a Azure Table Storage. Tyto databáze pro úrovně konzistence nenabízejí přesně definované modely konzistence nebo záruky na základě smlouvy SLA. Obvykle poskytují pouze podmnožinu pěti modelů konzistence nabízených Azure Cosmos DB. 

Při použití rozhraní API SQL, rozhraní Gremlin API a rozhraní API pro tabulky se používá výchozí úroveň konzistence nakonfigurovaná na účtu Azure Cosmos. 

Pokud používáte rozhraní API Cassandra nebo rozhraní API Azure Cosmos DB pro MongoDB, aplikace získají úplnou sadu úrovní konzistence nabízených Apache Cassandra a MongoDB, a to ještě silnější konzistence a záruky trvanlivosti. Tento dokument zobrazuje odpovídající Azure Cosmos DB úrovně konzistence pro úrovně konzistence Apache Cassandra a MongoDB.


## <a id="cassandra-mapping"></a>Mapování mezi úrovněmi konzistence Apache Cassandra a Azure Cosmos DB

Na rozdíl od AzureCosmos DB neposkytuje Apache Cassandra nativně přesně definované záruky konzistence.  Místo toho Apache Cassandra poskytuje úroveň konzistence pro zápis a úroveň konzistence pro čtení, aby bylo možné dosáhnout vysoké dostupnosti, konzistence a kompromisů latence. Při použití rozhraní API Cassandra Azure Cosmos DB: 

* Úroveň konzistence zápisu Apache Cassandra je namapovaná na výchozí úroveň konzistence nakonfigurovanou na vašem účtu Azure Cosmos. 

* Azure Cosmos DB bude dynamicky mapovat úroveň konzistence čtení určenou ovladačem klienta Cassandra na jednu z Azure Cosmos DB úrovní konzistence nakonfigurovaných dynamicky pro požadavek na čtení. 

Následující tabulka ukazuje, jak jsou nativní Cassandra úrovně konzistence mapovány na úrovně konzistence Azure Cosmos DB při použití rozhraní API Cassandra:  

[![Mapování modelu konzistence Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Mapování mezi MongoDB a Azure Cosmos DB úrovní konzistence

Na rozdíl od Azure Cosmos DB nativní MongoDB neposkytuje přesně definované záruky konzistence. Místo toho nativní MongoDB umožňuje uživatelům konfigurovat následující záruky konzistence: informace o zápisu, problém týkající se čtení a direktiva The-Master – k nasměrování operací čtení do primární nebo sekundární repliky za účelem dosažení požadované úrovně konzistence. 

Pokud používáte rozhraní API Azure Cosmos DB pro MongoDB, ovladač MongoDB považuje vaši oblast zápisu za primární repliku a všechny ostatní oblasti jsou repliky čtení. Můžete zvolit, která oblast je přidružená k vašemu účtu Azure Cosmos, jako primární repliku. 

Při použití rozhraní API Azure Cosmos DB pro MongoDB:

* V části týkající se zápisu je namapována na výchozí úroveň konzistence nakonfigurovanou v účtu Azure Cosmos.
 
* Azure Cosmos DB bude k dis dynamicky mapovat obavy týkající se čtení určené ovladačem klienta MongoDB na jednu z Azure Cosmos DB úrovní konzistence nakonfigurovanou dynamicky pro žádost o čtení. 

* Konkrétní oblast, která je přidružená k vašemu účtu Azure Cosmos, můžete označit jako "hlavní", a to tak, že ji nastavíte jako první zapisovatelnou oblast. 

Následující tabulka ukazuje, jak se při použití rozhraní Azure Cosmos DB API pro MongoDB namapují nativní problémy zápisu a čtení nativního MongoDB na úrovně konzistence Azure Cosmos:

[![Mapování modelu konzistence MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o úrovních konzistence a kompatibilitě mezi Azure Cosmos DB API pomocí Open Source rozhraní API. Viz následující články:

* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [MongoDB funkce podporované rozhraním API Azure Cosmos DB pro MongoDB](mongodb-feature-support.md)
* [Funkce Apache Cassandra podporované Azure Cosmos DB rozhraní API Cassandra](cassandra-support.md)