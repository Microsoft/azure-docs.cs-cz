---
title: Práce s databází Azure Cosmos DB, kontejnery a položek
description: Tento článek popisuje postup vytvoření a použití databáze Azure Cosmos DB, kontejnery a položek
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 8eaca83b7ea89737a63fe56a18505c8df7e93fdc
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678790"
---
# <a name="work-with-databases-containers-and-items"></a>Práce s databázemi, kontejnery a položkami

Po vytvoření [účet služby Azure Cosmos](account-overview.md) v rámci vašeho předplatného Azure můžete spravovat data ve vašem účtu tak, že vytvoříte databází, kontejnerů a položek. Tento článek popisuje, každá z těchto entit: databází, kontejnerů a položek. Následující obrázek ukazuje hierarchii různé entity v účtu služby Azure Cosmos:

![Entity účtu Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Databáze Azure Cosmos

Jeden nebo více databází Azure Cosmos můžete vytvořit v rámci vašeho účtu. Databáze je obdobou do oboru názvů. Je jednotkou správy pro skupinu kontejnerů Azure Cosmos. Následující tabulka ukazuje, jak databáze Azure Cosmos je namapována na různé entity specifické pro rozhraní API:

| **Entita Azure Cosmos** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- |
|Databáze Azure Cosmos | Databáze | Prostor klíčů | Databáze | Databáze | Není k dispozici |

> [!NOTE]
> Pomocí rozhraní Table API účtů při vytváření vaší první tabulky vytvoří i výchozí databáze se automaticky vytvoří v rámci svého účtu Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operace v databázi Azure Cosmos

Můžete pracovat s databází Azure Cosmos pomocí rozhraní API služby Azure Cosmos následujícím způsobem:

| **Operace** | **Azure CLI**|**ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- |
|Vytvořit výčet všech databází| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |
|Databáze pro čtení| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |
|Vytvořit novou databázi| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |
|Aktualizace databáze| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |


## <a name="azure-cosmos-containers"></a>Kontejnery služby Azure Cosmos

Kontejner služby Azure Cosmos je jednotkou škálovatelnosti za zřízenou propustnost a úložiště. Kontejner je horizontálně dělené do oddílů a pak replikuje napříč několika oblastmi. Položky, které přidáte do kontejneru a propustnost, kterou zřídíte v něm automaticky distribuují do sady logické oddíly na základě klíče oddílu. Další informace o vytváření oddílů a klíče oddílů, naleznete v tématu [to](partition-data.md) článku. 

Při vytváření kontejneru Azure Cosmos, nakonfigurujete propustnost v jednom z těchto režimů:

* **Vyhrazená propustnost** režimu: Propustnost zřízený v kontejneru je rezervovaných exkluzivně pro tento kontejner a je zajištěná smluv SLA. Další informace najdete v tématu [jak zřídit propustnost kontejneru Azure Cosmos](how-to-provision-container-throughput.md).

* **Sdílené zřízená propustnost** režimu: Tyto kontejnery sdílejí zřízená propustnost pomocí jiných kontejnerů ve stejné databázi (s výjimkou těchto kontejnerů, které jsou nakonfigurované s vyhrazenou zřízená propustnost). Jinými slovy zřízená propustnost v databázi se sdílí mezi všechny kontejnery "sdílené propustnost". Další informace najdete v tématu [konfigurace zřízenou propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

Kontejner služby Azure Cosmos můžete Elasticky škálovat, zda vytvořit kontejnery s oběma "sdílené" nebo "vyhrazených" zřízené propustnosti režimy.

Kontejner služby Azure Cosmos je nezávislý na schématu kontejner položek. Položky v rámci kontejneru může mít libovolný schémata. Například položky představující osobu, položku představující automobilu je možné použít v *stejný kontejner*. Ve výchozím nastavení získat všechny položky, které přidáte do kontejneru automaticky indexován bez vyžadování jakéhokoli explicitní index nebo Správa schématu. Indexování chování můžete přizpůsobit tím, že nakonfigurujete [zásady indexování](index-overview.md) v kontejneru. 

Můžete nastavit [čas To Live (TTL)](time-to-live.md) na vybrané položky v rámci kontejneru Azure Cosmos nebo pro celý kontejner řádně vymazání těchto položek mimo systém. Azure Cosmos DB se automaticky odstraní položky po vypršení jejich platnosti. Také zaručuje, že dotaz provedené na kontejneru nevrací neplatné položky v rámci pevné vazby. Další informace najdete v tématu [konfigurace hodnoty TTL na kontejnerech](how-to-time-to-live.md).

S použitím [Change Feed](change-feed.md), můžete se přihlásit odběru k provoznímu protokolu, který je spravovaný pro všechny logické oddíly vašeho kontejneru. Kanál změn poskytuje protokolu všechny aktualizace provedené na kontejneru spolu s před a po obrázků položek. Zobrazit [postupy vytváření reaktivních aplikací pomocí Change Feed](serverless-computing-database.md). Doba uchovávání můžete také nakonfigurovat pro změnit informační kanál pomocí kanál v kontejneru zásad změn. 

Můžete zaregistrovat [uložené procedury, aktivační události, uživatelem definované funkce (UDF)](stored-procedures-triggers-udfs.md) a [sloučit postupy](how-to-manage-conflicts.md) se váš kontejner Azure Cosmos. 

Můžete zadat [omezení unique key](unique-keys.md) na váš kontejner Azure Cosmos. Vytvořením zásady jedinečného klíče zajistíte jedinečnost jedné nebo více hodnot za klíč logického oddílu. Po vytvoření kontejneru s zásady jedinečného klíče zabraňuje vytváření nové nebo aktualizované položky s hodnotami, které duplicitní hodnoty určené omezení unique key. Další informace najdete v tématu [omezení jedinečných klíčů](unique-keys.md).

Kontejner služby Azure Cosmos je specializovaný do entity specifické pro rozhraní API následujícím způsobem:

| **Entita Azure Cosmos** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- |
|Kontejner Azure Cosmos | Kolekce | Table | Kolekce | Graph | Table |

### <a name="properties-of-an-azure-cosmos-container"></a>Vlastnosti kontejneru Azure Cosmos

Kontejner služby Azure Cosmos obsahuje sadu vlastností definovaných systémem. V závislosti na výběru rozhraní API některé z nich nesmí být zveřejněné přímo. Následující tabulka obsahuje seznam vlastností definovaných systémem:

| **Vlastnost definovaná systémem** | **Systémem vygenerované nebo uživatelem konfigurovatelné** | **Účel** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_rid | Vygenerované systémem | Jedinečný identifikátor kontejneru | Ano | Ne | Ne | Ne | Ne |
|_etag | Vygenerované systémem | Značka entity používá pro optimistického řízení souběžnosti | Ano | Ne | Ne | Ne | Ne |
|_ts | Vygenerované systémem | Časové razítko poslední aktualizace kontejneru | Ano | Ne | Ne | Ne | Ne |
|_self | Vygenerované systémem | Adresovatelný URI kontejneru | Ano | Ne | Ne | Ne | Ne |
|id | Konfigurovatelná uživatelem | Uživatelem definované jedinečný název kontejneru | Ano | Ano | Ano | Ano | Ano |
|indexingPolicy | Konfigurovatelná uživatelem | Umožňuje změnit index cesty, typ indexu a indexace. | Ano | Ne | Ne | Ne | Ano |
|TimeToLive | Konfigurovatelná uživatelem | Umožňuje automaticky odstranit položky z kontejneru po určité časové období. Další podrobnosti najdete v tématu [Time To Live](time-to-live.md) článku. | Ano | Ne | Ne | Ne | Ano |
|changeFeedPolicy | Konfigurovatelná uživatelem | Umožňuje číst změny provedené u položek v kontejneru. Další podrobnosti najdete v tématu [Change Feed](change-feed.md) článku. | Ano | Ne | Ne | Ne | Ano |
|uniqueKeyPolicy | Konfigurovatelná uživatelem | Používá k zajištění jedinečnost jedné nebo více hodnot v rámci logického oddílu. Další informace najdete v tématu [jedinečný klíč omezení](unique-keys.md) článku. | Ano | Ne | Ne | Ne | Ano |

### <a name="operations-on-an-azure-cosmos-container"></a>Operace v kontejneru Azure Cosmos

Kontejner služby Azure Cosmos podporuje následující operace pomocí kteréhokoli z rozhraní API služby Azure Cosmos.

| **Operace** | **Azure CLI** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| Zobrazení výčtu kontejnery v databázi | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Přečtěte si kontejneru | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Vytvořit nový kontejner | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Aktualizovat kontejner | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Odstranění kontejneru | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |

## <a name="azure-cosmos-items"></a>Azure Cosmos položky

V závislosti na výběru rozhraní API položka Azure Cosmos může představovat dokumentu v kolekci řádek v tabulce nebo uzlů nebo hran v grafu. V následující tabulce jsou uvedeny mapování mezi entitami specifické pro rozhraní API do Azure Cosmos položky:

| **Cosmos entity** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos položky | Dokument | Řádek | Dokument | Uzlů nebo hran | Položka |

### <a name="properties-of-an-item"></a>Vlastnosti položky

Každá položka Azure Cosmos má následující vlastnosti definovaná systémem. V závislosti na výběru rozhraní API některé z nich nesmí být zveřejněné přímo.

|**Vlastnost definovaná systémem** | **Systémem vygenerované nebo uživatelem konfigurovatelné**| **Účel** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|ID _ovládacího | Vygenerované systémem | Jedinečný identifikátor položky | Ano | Ne | Ne | Ne | Ne |
|_etag | Vygenerované systémem | Značka entity používá pro optimistického řízení souběžnosti | Ano | Ne | Ne | Ne | Ne |
|_ts | Vygenerované systémem | Časové razítko poslední aktualizace položky | Ano | Ne | Ne | Ne | Ne |
|_self | Vygenerované systémem | Adresovatelný identifikátor URI položky | Ano | Ne | Ne | Ne | Ne |
|id | Buď | Uživatelem definované jedinečný název v rámci logického oddílu. Pokud uživatel nemá určenou ID, systém jej vytvoří automaticky. | Ano | Ano | Ano | Ano | Ano |
|Libovolné uživatelem definované vlastnosti | Definované uživatelem | Uživatelem definované vlastnosti v rozhraní API nativní reprezentace (JSON, BSON, CQL atd.) | Ano | Ano | Ano | Ano | Ano |

### <a name="operations-on-items"></a>Operace u položek

Azure Cosmos položka podporuje následující operace, které je možné provádět pomocí některé z rozhraní API služby Azure Cosmos.

| **Operace** | **Azure CLI** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| Vložit, nahradí, odstranit, Upsert, přečtěte si | Ne | Ano | Ano | Ano | Ano | Ano |

## <a name="next-steps"></a>Další postup

Teď můžete přejít k další následující pojmy:

* [Postup konfigurace zřízenou propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md)
* [Postup konfigurace zřízené propustnosti na kontejner služby Azure Cosmos](how-to-provision-container-throughput.md)
* [Logické oddíly](partition-data.md)
* [Postup konfigurace hodnoty TTL na kontejneru Azure Cosmos](how-to-time-to-live.md)
* [Postup vytváření reaktivních aplikací pomocí Change Feed](change-feed.md)
* [Jak nakonfigurovat omezení unique key na váš kontejner Azure Cosmos](unique-keys.md)
