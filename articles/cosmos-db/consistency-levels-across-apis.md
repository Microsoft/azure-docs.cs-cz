---
title: Úrovně konzistence a rozhraní API služby Cosmos DB
description: Porozumění mapování úrovně konzistence mezi různými rozhraními API v Azure Cosmos DB a Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/6/2020
ms.reviewer: sngun
ms.openlocfilehash: bb8a413f2e2a3aa4a8facd533d822312bb61fa0e
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613556"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Úrovně konzistence a rozhraní API služby Cosmos DB

Azure Cosmos DB poskytuje nativní podporu pro rozhraní API kompatibilní se síťovými protokoly pro oblíbené databáze. Patří mezi ně MongoDB, Apache Cassandra, Gremlin a Azure Table Storage. Tyto databáze pro úrovně konzistence nenabízejí přesně definované modely konzistence nebo záruky na základě smlouvy SLA. Obvykle poskytují pouze podmnožinu pěti modelů konzistence nabízených Azure Cosmos DB.

Při použití rozhraní API SQL, rozhraní Gremlin API a rozhraní API pro tabulky se používá výchozí úroveň konzistence nakonfigurovaná na účtu Azure Cosmos. 

Pokud používáte rozhraní API Cassandra nebo rozhraní API Azure Cosmos DB pro MongoDB, aplikace získají úplnou sadu úrovní konzistence nabízených Apache Cassandra a MongoDB, a to ještě silnější konzistence a záruky trvanlivosti. Tento dokument zobrazuje odpovídající Azure Cosmos DB úrovně konzistence pro úrovně konzistence Apache Cassandra a MongoDB.

> [!NOTE]
> Výchozím modelem konzistence pro Azure Cosmos DB je relace. Relace je model konzistence orientovaný na klienta, který není nativně podporován buď Cassandra nebo MongoDB. Další informace o tom, jaký model konzistence se má zvolit, najdete [v tématu úrovně konzistence v Azure Cosmos DB](consistency-levels.md)

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Mapování mezi úrovněmi konzistence Apache Cassandra a Azure Cosmos DB

Na rozdíl od Azure Cosmos DB Apache Cassandra nativně neposkytuje přesně definované záruky konzistence.  Místo toho Apache Cassandra poskytuje úroveň konzistence pro zápis a úroveň konzistence pro čtení, aby bylo možné dosáhnout vysoké dostupnosti, konzistence a kompromisů latence. Při použití rozhraní API Cassandra Azure Cosmos DB: 

* Úroveň konzistence zápisu Apache Cassandra je namapovaná na výchozí úroveň konzistence nakonfigurovanou na vašem účtu Azure Cosmos. Konzistence operace zápisu (CL) se u jednotlivých požadavků nedá změnit.

* Azure Cosmos DB bude dynamicky mapovat úroveň konzistence čtení určenou ovladačem klienta Cassandra na jednu z Azure Cosmos DB úrovní konzistence nakonfigurovaných dynamicky pro požadavek na čtení. 

Následující tabulka ukazuje, jak jsou nativní Cassandra úrovně konzistence mapovány na úrovně konzistence Azure Cosmos DB při použití rozhraní API Cassandra:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Mapování modelu konzistence Cassandra" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Mapování mezi MongoDB a Azure Cosmos DB úrovní konzistence

Na rozdíl od Azure Cosmos DB nativní MongoDB neposkytuje přesně definované záruky konzistence. Místo toho nativní MongoDB umožňuje uživatelům konfigurovat následující záruky konzistence: informace o zápisu, problém týkající se čtení a direktiva The-Master – k nasměrování operací čtení do primární nebo sekundární repliky za účelem dosažení požadované úrovně konzistence.

Pokud používáte rozhraní API Azure Cosmos DB pro MongoDB, ovladač MongoDB považuje vaši oblast zápisu za primární repliku a všechny ostatní oblasti jsou repliky čtení. Můžete zvolit, která oblast je přidružená k vašemu účtu Azure Cosmos, jako primární repliku. 

Při použití rozhraní API Azure Cosmos DB pro MongoDB:

* V části týkající se zápisu je namapována na výchozí úroveň konzistence nakonfigurovanou v účtu Azure Cosmos.

* Azure Cosmos DB bude k dis dynamicky mapovat obavy týkající se čtení určené ovladačem klienta MongoDB na jednu z Azure Cosmos DB úrovní konzistence nakonfigurovanou dynamicky pro žádost o čtení.  

* Konkrétní oblast, která je přidružená k účtu Azure Cosmos, můžete označit jako "primární" tím, že tuto oblast nastavíte jako první zapisovatelnou oblast. 

Následující tabulka ukazuje, jak se při použití rozhraní Azure Cosmos DB API pro MongoDB namapují nativní problémy zápisu a čtení nativního MongoDB na úrovně konzistence Azure Cosmos:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Mapování modelu konzistence Cassandra" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o úrovních konzistence a kompatibilitě mezi Azure Cosmos DB API pomocí Open Source rozhraní API. Viz následující články:

* [Kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [MongoDB funkce podporované rozhraním API Azure Cosmos DB pro MongoDB](mongodb-feature-support.md)
* [Funkce Apache Cassandra podporované Azure Cosmos DB rozhraní API Cassandra](cassandra-support.md)
