---
title: Práce s databází Azure Cosmos DB, kontejnery a položek
description: Tento článek popisuje postup vytvoření a použití databáze Azure Cosmos DB, kontejnery a položek
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 85d8b531f19ac35f091f54c2627f7fe6d3a62a42
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468134"
---
# <a name="work-with-databases-containers-and-items"></a>Práce s databází, kontejnerů a položek

Po vytvoření [účtu služby Azure Cosmos DB](account-overview.md) v rámci vašeho předplatného Azure můžete spravovat data ve vašem účtu tak, že vytvoříte databází, kontejnerů a položek. Tento článek popisuje, každá z těchto entit: databází, kontejnerů a položek. Následující obrázek ukazuje hierarchii různé entity v účtu služby Azure Cosmos:

![Entity účtu Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Databáze Azure Cosmos

Jeden nebo více databází Azure Cosmos můžete vytvořit v rámci vašeho účtu. Databáze je obdobou do oboru názvů, je to jednotka správy pro skupinu kontejnerů Azure Cosmos. Následující tabulka ukazuje, jak databáze Azure Cosmos je namapována na různé entity specifické pro rozhraní API:

| **Entita Azure Cosmos** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- |
|Databáze Azure Cosmos | Databáze | Prostor klíčů | Databáze | Databáze | Není k dispozici |

> [!NOTE]
> S účty rozhraní API tabulky když vytvoříte první tabulky vytvoří i výchozí databáze se automaticky vytvoří v rámci svého účtu Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operace v databázi Azure Cosmos

Můžete pracovat s databází Azure Cosmos pomocí následující rozhraní API Azure Cosmos:

| **Operace** | **Azure CLI**|**ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- |
|Vytvořit výčet všech databází| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |
|Databáze pro čtení| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |
|Vytvořit novou databázi| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |
|Aktualizace databáze| Ano | Ano | Ano (databáze je namapován prostor klíčů) | Ano | Není k dispozici | Není k dispozici |


## <a name="azure-cosmos-containers"></a>Kontejnery služby Azure Cosmos

Kontejner služby Azure Cosmos je jednotka škálovatelnost pro obě zřízenou propustnost a úložiště položek. Kontejner je horizontálně dělené do oddílů a pak replikuje napříč několika oblastmi. Položky, které přidáte do kontejneru a propustnost, kterou zřídíte v něm automaticky distribuují do sady logické oddíly na základě klíče oddílu. Další informace o vytváření oddílů a klíč oddílu, naleznete v tématu [logické oddíly](partition-data.md) článku. 

Při vytváření kontejneru Azure Cosmos, nakonfigurujete propustnost v jednom z těchto režimů:

* **Vyhrazená propustnost** režimu: V kontejneru zřízenou propustnost výhradně pro ně vyhrazené a je zajištěná smluv SLA. Další informace najdete v tématu [jak zřídit propustnost kontejneru Azure Cosmos](how-to-provision-container-throughput.md).

* **Sdílené zřízená propustnost** režimu: Tyto kontejnery sdílejí zřízená propustnost pomocí dalších kontejnerů ve stejné databázi (s výjimkou těchto kontejnerů, které jsou nakonfigurované s vyhrazenou zřízená propustnost). Jinými slovy zřízená propustnost v databázi se sdílí mezi "sdílené" kontejnery. Další informace najdete v tématu [konfigurace zřízenou propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

Kontejner služby Azure Cosmos můžete Elasticky škálovat, zda vytvořit kontejnery s oběma "sdílené" nebo "vyhrazených" zřízené propustnosti režimy.

Kontejner služby Azure Cosmos je nezávislý na schématu kontejner položek. Položky v rámci kontejneru může mít libovolný schémata. Například položky představující osobu, položku představující automobilu mohou být umístěny ve stejném kontejneru. Ve výchozím nastavení získat všechny položky, které přidáte do kontejneru automaticky indexován bez vyžadování jakéhokoli explicitní index nebo Správa schématu. Indexování chování můžete přizpůsobit tím, že nakonfigurujete zásady indexování v kontejneru. 

Čas To Live (TTL) můžete nastavit na vybrané položky v rámci kontejneru Azure Cosmos nebo pro celý kontejner řádně vymazání těchto položek mimo systém. Azure Cosmos DB se automaticky odstraní položky po vypršení jejich platnosti. Také zaručuje, že dotaz provedené na kontejneru nevrací neplatné položky v rámci pevné vazby. Další informace najdete v tématu [konfigurace hodnoty TTL na kontejnerech](how-to-time-to-live.md).

Pomocí kanálu změn, můžete se přihlásit k provoznímu protokolu, který je spravovaný pro každý logický oddíl vašeho kontejneru. Kanál změn poskytuje protokolu všechny aktualizace provedené na kontejneru spolu s před a po obrázků položek. Zobrazit [způsob k vytváření reaktivních aplikací s použitím změn kanálu](change-feed.md). Můžete také nakonfigurovat doba uchovávání pro kanál v kontejneru zásad změn pomocí kanálu změn. 

Uložené procedury, aktivační události, uživatelem definované funkce (UDF) a sloučení postupy lze zaregistrovat kontejner Azure Cosmos. 

Zadejte jedinečný klíč, který na váš kontejner Azure Cosmos. Vytvořením zásady jedinečného klíče zajistíte jedinečnost jedné nebo více hodnot za klíč logického oddílu. Po vytvoření kontejneru s zásady jedinečného klíče zabraňuje vytváření nové nebo aktualizované položky s hodnotami, které duplicitní hodnoty určené omezení unique key. Další informace najdete v tématu [omezení jedinečných klíčů](unique-keys.md).

Kontejner služby Azure Cosmos je specializovaný do entity specifické pro rozhraní API následujícím způsobem:

| **Entita Azure Cosmos** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- |
|Kontejner Azure Cosmos | Kolekce | Table | Kolekce | Graph | Table |

### <a name="properties-of-an-azure-cosmos-container"></a>Vlastnosti kontejneru Azure Cosmos

Kontejner služby Azure Cosmos je sada vlastností definovaná systémem. V závislosti na výběru rozhraní API některé z nich nesmí být zveřejněné přímo. Následující tabulka obsahuje seznam vlastností podporovaných definovaná systémem:

| **Vlastnost definovaná systémem** | **Systém generované nebo nastavit uživatele** | **Účel** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_rid | Vygenerované systémem | Jedinečný identifikátor kontejneru | Ano | Ne | Ne | Ne | Ne |
|_etag | Vygenerované systémem | Značka entity používá pro optimistického řízení souběžnosti | Ano | Ne | Ne | Ne | Ne |
|_ts | Vygenerované systémem | Časové razítko poslední aktualizace kontejneru | Ano | Ne | Ne | Ne | Ne |
|_self | Vygenerované systémem | Adresovatelný URI kontejneru | Ano | Ne | Ne | Ne | Ne |
|id | Konfigurovatelná uživatelem | Uživatelem definované jedinečný název kontejneru | Ano | Ano | Ano | Ano | Ano |
|indexingPolicy | Konfigurovatelná uživatelem | Umožňuje změnit cestu index, jejich přesnosti a model pro zajištění konzistence. | Ano | Ne | Ne | Ne | Ano |
|TimeToLive | Konfigurovatelná uživatelem | Umožňuje automaticky odstranit položky z kontejneru po určité časové období. Další podrobnosti najdete v tématu [Time To Live](time-to-live.md) článku. | Ano | Ne | Ne | Ne | Ano |
|changeFeedPolicy | Konfigurovatelná uživatelem | Umožňuje číst změny provedené u položek v kontejneru. Další podrobnosti najdete v tématu [kanálu změn](change-feed.md) článku. | Ano | Ne | Ne | Ne | Ano |
|uniqueKeyPolicy | Konfigurovatelná uživatelem | S jedinečnými klíči zajistíte jedinečnost jedné nebo více hodnot v rámci logického oddílu. Další informace najdete v tématu [jedinečné klíče](unique-keys.md) článku. | Ano | Ne | Ne | Ne | Ano |

### <a name="operations-on-an-azure-cosmos-container"></a>Operace v kontejneru Azure Cosmos

Kontejner služby Azure Cosmos podporuje následující operace pomocí kteréhokoli z rozhraní API služby Azure Cosmos.

| **Operace** | **Azure CLI** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Zobrazení výčtu kontejnery v databázi | Ano* | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
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

|**Vlastnost definovaná systémem** | **Systém generované nebo nastavit uživatele**| **Účel** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|ID _ovládacího | Vygenerované systémem | Jedinečný identifikátor položky | Ano | Ne | Ne | Ne | Ne |
|_etag | Vygenerované systémem | Značka entity používá pro optimistického řízení souběžnosti | Ano | Ne | Ne | Ne | Ne |
|_ts | Vygenerované systémem | Časové razítko poslední aktualizace položky | Ano | Ne | Ne | Ne | Ne |
|_self | Vygenerované systémem | Adresovatelný identifikátor URI položky | Ano | Ne | Ne | Ne | Ne |
|id | Buď | Uživatelem definované jedinečný název v rámci logického oddílu. Pokud uživatel nemá určenou id, systém jej vytvoří automaticky. | Ano | Ano | Ano | Ano | Ano |
|Libovolné uživatelem definované vlastnosti | Definované uživatelem | Uživatelem definované vlastnosti v rozhraní API nativní reprezentace (JSON, BSON, CQL atd.) | Ano | Ano | Ano | Ano | Ano |

### <a name="operations-on-items"></a>Operace u položek

Azure Cosmos položka podporuje následující operace, které je možné provádět pomocí některé z rozhraní API služby Azure Cosmos.

| **Operace** | **Azure CLI** | **ROZHRANÍ SQL API** | **Rozhraní Cassandra API** | **Rozhraní API služby Azure Cosmos DB pro MongoDB** | **Gremlin API** | **Rozhraní Table API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Vložit, nahradí, odstranit, Upsert, přečtěte si | Ne | Ano | Ano | Ano | Ano | Ano |

## <a name="next-steps"></a>Další postup

Teď můžete přejít k zjistěte, jak zřídit propustnost v účtu Azure Cosmos nebo najdete další koncepty:

* [Postup konfigurace zřízenou propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md)
* [Jak nakonfigurovat zřízené propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md)
* [Logické oddíly](partition-data.md)
* [Postup konfigurace hodnoty TTL na kontejneru Azure Cosmos](how-to-time-to-live.md)
* [Postup vytváření reaktivních aplikací pomocí Change Feed](change-feed.md)
* [Jak nakonfigurovat omezení unique key na váš kontejner Azure Cosmos](unique-keys.md)
