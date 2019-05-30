---
title: Práce s databází, kontejnerů a položky ve službě Azure Cosmos DB
description: Tento článek popisuje postup vytváření a používání databází, kontejnerů a položky ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 574dd9fd6189b6d0f1e5d455146d6d083ad7ff77
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389464"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Práce s databází, kontejnerů a položky ve službě Azure Cosmos DB

Po vytvoření [účtu služby Azure Cosmos DB](account-overview.md) v rámci vašeho předplatného Azure můžete spravovat data ve vašem účtu tak, že vytvoříte databází, kontejnerů a položek. Tento článek popisuje každé z těchto entit. 

Následující obrázek ukazuje hierarchii různé entity v účtu služby Azure Cosmos DB:

![Entity účtu Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Databáze Azure Cosmos

Můžete vytvořit jednu nebo několik databází Azure Cosmos v rámci vašeho účtu. Databáze je obdobou do oboru názvů. Databáze je jednotkou správy pro skupinu kontejnerů Azure Cosmos. Následující tabulka ukazuje, jak databáze Azure Cosmos je namapována na různé entity specifické pro rozhraní API:

| Azure Cosmos entity | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- |
|Databáze Azure Cosmos | Databáze | Prostor klíčů | Databáze | Databáze | Není k dispozici |

> [!NOTE]
> Pomocí rozhraní Table API účtů při vytváření vaší první tabulky vytvoří i výchozí databáze se automaticky vytvoří ve vašem účtu Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operace v databázi Azure Cosmos

Můžete pracovat s databází Azure Cosmos pomocí rozhraní API služby Azure Cosmos, jak je popsáno v následující tabulce:

| Operace | Azure CLI | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- |
|Vytvořit výčet všech databází| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |
|Databáze pro čtení| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |
|Vytvořit novou databázi| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |
|Aktualizace databáze| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |


## <a name="azure-cosmos-containers"></a>Kontejnery služby Azure Cosmos

Kontejner služby Azure Cosmos je jednotka škálovatelnost i za zřízenou propustnost a úložiště. Kontejner je horizontálně dělené do oddílů a pak replikuje napříč několika oblastmi. Položky, které přidáte do kontejneru a propustnost, kterou zřídíte v něm automaticky distribuují do sady logické oddíly na základě klíče oddílu. Další informace o vytváření oddílů a klíče oddílů, naleznete v tématu [dělit data](partition-data.md). 

Při vytváření kontejneru Azure Cosmos, nakonfigurujete propustnost v jednom z těchto režimů:

* **Režim zřízenou vyhrazenou propustnost**: Propustnost zřízený v kontejneru je rezervovaných exkluzivně pro tento kontejner a je zajištěná smluv SLA. Další informace najdete v tématu [jak zřídit propustnost kontejneru Azure Cosmos](how-to-provision-container-throughput.md).

* **Režim sdílený zřízená propustnost**: Tyto kontejnery sdílejí zřízená propustnost pomocí jiných kontejnerů ve stejné databázi (s výjimkou kontejnerů, které byly nakonfigurovány pomocí vyhrazené zřízená propustnost). Jinými slovy zřízená propustnost v databázi se sdílí mezi všechny kontejnery "sdílené propustnost". Další informace najdete v tématu [jak zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

> [!NOTE]
> Sdílené a vyhrazené propustnosti můžete nakonfigurovat pouze při vytváření databáze a kontejner. Přepnutí z režimu vyhrazené propustnosti pro režim sdíleného propustnost (a naopak), po vytvoření kontejneru, budete muset vytvořit nový kontejner a migraci dat do nového kontejneru. Můžete migrovat data s využitím kanálu funkce změn služby Azure Cosmos DB.

Kontejner služby Azure Cosmos můžete škálovat Elasticky, ať už vytváříte kontejnery pomocí vyhrazené i sdílené zřízená propustnost režimy.

Kontejner služby Azure Cosmos je nezávislý na schématu kontejner položek. Položky v kontejneru můžete mít libovolný schémata. Například položku, která představuje osobu a položku, která představuje automobilu je možné umístit *stejný kontejner*. Ve výchozím nastavení jsou automaticky indexován všechny položky, které přidáte do kontejneru bez nutnosti explicitního index nebo Správa schématu. Indexování chování můžete přizpůsobit tím, že nakonfigurujete [zásady indexování](index-overview.md) v kontejneru. 

Můžete nastavit [čas to Live (TTL)](time-to-live.md) na vybrané položky v kontejneru Azure Cosmos, nebo pro celý kontejner řádně vymazání těchto položek ze systému. Azure Cosmos DB automaticky odstraní položky po vypršení jejich platnosti. Také zaručuje, že provedené na kontejneru dotaz nevrací neplatné položky v rámci pevnou vazbou. Další informace najdete v tématu [konfigurace hodnoty TTL na kontejnerech](how-to-time-to-live.md).

Můžete použít [kanálu změn](change-feed.md) k odběru k provoznímu protokolu, který je spravovaný pro každý logický oddíl vašeho kontejneru. Kanál změn poskytuje protokol všechny aktualizace provedené na kontejneru, spolu s před a po obrázků položek. Další informace najdete v tématu [reaktivní umožňuje sestavovat aplikace pomocí kanálu změn](serverless-computing-database.md). Můžete také nakonfigurovat doba uchovávání pro kanál pomocí kanál v kontejneru zásad změn změn. 

Můžete zaregistrovat [uložené procedury, aktivační události, uživatelem definované funkce (UDF)](stored-procedures-triggers-udfs.md), a [sloučit postupy](how-to-manage-conflicts.md) pro váš kontejner Azure Cosmos. 

Můžete zadat [omezení unique key](unique-keys.md) na váš kontejner Azure Cosmos. Vytvořením zásady jedinečného klíče zajistíte jedinečnost jedné nebo více hodnot za klíč logického oddílu. Pokud vytvoříte kontejner pomocí zásady jedinečného klíče, lze vytvořit nové nebo aktualizované položky s hodnotami, které duplicitní hodnoty určené omezení unique key. Další informace najdete v tématu [omezení jedinečných klíčů](unique-keys.md).

Kontejner služby Azure Cosmos je specializovaný do entity specifické pro rozhraní API, jak je znázorněno v následující tabulce:

| Azure Cosmos entity | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- |
|Kontejner Azure Cosmos | Kolekce | Table | Kolekce | Graph | Table |

### <a name="properties-of-an-azure-cosmos-container"></a>Vlastnosti kontejneru Azure Cosmos

Kontejner služby Azure Cosmos obsahuje sadu vlastností definovaných systémem. V závislosti na tom, které můžete použít rozhraní API nemusí být přímo vystavený některé vlastnosti. Následující tabulka obsahuje seznam vlastností definovaných systémem:

| Vlastnosti definované v systému | Generované systémem nebo uživatelem konfigurovatelné | Účel | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | Generované systémem | Jedinečný identifikátor kontejneru | Ano | Ne | Ne | Ne | Ne |
|\_etag | Generované systémem | Značka entity používá pro optimistického řízení souběžnosti | Ano | Ne | Ne | Ne | Ne |
|\_TS | Generované systémem | Časové razítko poslední aktualizace kontejneru | Ano | Ne | Ne | Ne | Ne |
|\_Vlastní | Generované systémem | Adresovatelný URI kontejneru | Ano | Ne | Ne | Ne | Ne |
|id | User-configurable | Uživatelem definované jedinečný název kontejneru | Ano | Ano | Ano | Ano | Ano |
|indexingPolicy | User-configurable | Umožňuje změnit cestu index, typ indexu a index režimu | Ano | Ne | Ne | Ne | Ano |
|timeToLive | User-configurable | Umožňuje odstranit položky z kontejneru automaticky po nastaveném časovém období. Podrobnosti najdete v tématu [TTL](time-to-live.md). | Ano | Ne | Ne | Ne | Ano |
|changeFeedPolicy | User-configurable | Umožňuje číst změny provedené u položek v kontejneru. Podrobnosti najdete v tématu [kanálu změn](change-feed.md). | Ano | Ne | Ne | Ne | Ano |
|uniqueKeyPolicy | User-configurable | Používá k zajištění jedinečnosti jednu nebo více hodnot v logického oddílu. Další informace najdete v tématu [omezení jedinečných klíčů](unique-keys.md). | Ano | Ne | Ne | Ne | Ano |

### <a name="operations-on-an-azure-cosmos-container"></a>Operace v kontejneru Azure Cosmos

Kontejner služby Azure Cosmos podporuje následující operace, když použijete některou z rozhraní API služby Azure Cosmos:

| Operace | Azure CLI | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Zobrazení výčtu kontejnery v databázi | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Přečtěte si kontejneru | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Vytvořit nový kontejner | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Aktualizovat kontejner | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Odstranění kontejneru | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |

## <a name="azure-cosmos-items"></a>Azure Cosmos položky

V závislosti na tom, které můžete použít rozhraní API položka Azure Cosmos může představovat buď dokumentů v kolekci řádek v tabulce, nebo uzel nebo okraj v grafu. V následující tabulce jsou uvedeny mapování entity specifické pro rozhraní API, které položky Azure Cosmos:

| Cosmos entity | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos položky | Dokument | Řádek | Dokument | Uzlů nebo hran | Položka |

### <a name="properties-of-an-item"></a>Vlastnosti položky

Každá položka Azure Cosmos má následující vlastnosti definované v systému. V závislosti na tom, které rozhraní API můžete použít některé z nich nemusí být přímo vystavený.

| Vlastnosti definované v systému | Generované systémem nebo uživatelem konfigurovatelné| Účel | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | Generované systémem | Jedinečný identifikátor položky | Ano | Ne | Ne | Ne | Ne |
|\_etag | Generované systémem | Značka entity používá pro optimistického řízení souběžnosti | Ano | Ne | Ne | Ne | Ne |
|\_TS | Generované systémem | Časové razítko poslední aktualizace položky | Ano | Ne | Ne | Ne | Ne |
|\_Vlastní | Generované systémem | Adresovatelný identifikátor URI položky | Ano | Ne | Ne | Ne | Ne |
|id | Buď | Uživatelem definované jedinečný název logického oddílu. Pokud uživatel nemá určenou ID, systém jej automaticky vygeneruje. | Ano | Ano | Ano | Ano | Ano |
|Libovolné uživatelem definované vlastnosti | Definované uživatelem | Uživatelem definované vlastnosti v reprezentaci nativních rozhraní API (včetně JSON, formátů BSON a CQL) | Ano | Ano | Ano | Ano | Ano |

### <a name="operations-on-items"></a>Operace u položek

Azure Cosmos položky podporují tyto operace. K provádění operací můžete použít některý z rozhraní API služby Azure Cosmos.

| Operace | Azure CLI | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Vložit, nahradí, odstranit, Upsert, přečtěte si | Ne | Ano | Ano | Ano | Ano | Ano |

## <a name="next-steps"></a>Další postup

Další informace o těchto úlohách a koncepty:

* [Zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md)
* [Zřizování propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md)
* [Práce s logickými oddíly](partition-data.md)
* [Konfigurace hodnoty TTL na kontejner služby Azure Cosmos](how-to-time-to-live.md)
* [Vytváření reaktivních aplikací s využitím kanálu změn](change-feed.md)
* [Konfigurace omezení unique key u kontejneru Azure Cosmos](unique-keys.md)
