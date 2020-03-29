---
title: Práce s databázemi, kontejnery a položkami v Azure Cosmos DB
description: Tento článek popisuje, jak vytvořit a používat databáze, kontejnery a položky v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 43a842c3b6d6d421eca4196c7f3facc7876318cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246783"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Práce s databázemi, kontejnery a položkami v Azure Cosmos DB

Po vytvoření [účtu Azure Cosmos DB](account-overview.md) v rámci předplatného Azure můžete spravovat data ve svém účtu vytvořením databází, kontejnerů a položek. Tento článek popisuje každou z těchto entit. 

Následující obrázek znázorňuje hierarchii různých entit v účtu Azure Cosmos DB:

![Entity účtu Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Databáze Azure Cosmos DB

Můžete vytvořit jednu nebo více databází Azure Cosmos pod vaším účtem. Databáze je obdobou oboru názvů. Databáze je jednotka správy pro sadu kontejnerů Azure Cosmos. Následující tabulka ukazuje, jak je databáze Azure Cosmos mapována na různé entity specifické pro rozhraní API:

| Entita Azure Cosmos | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- |
|Databáze Azure Cosmos | Databáze | Prostor klíče | Databáze | Databáze | Není k dispozici |

> [!NOTE]
> S účty rozhraní TABLE API se při vytváření první tabulky automaticky vytvoří výchozí databáze ve vašem účtu Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operace v databázi Azure Cosmos

S databází Azure Cosmos můžete pracovat s api Azure Cosmos, jak je popsáno v následující tabulce:

| Operace | Azure CLI | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- |
|Výčet všech databází| Ano | Ano | Ano (databáze je mapována na keyspace) | Ano | Není k dispozici | Není k dispozici |
|Číst v databázi| Ano | Ano | Ano (databáze je mapována na keyspace) | Ano | Není k dispozici | Není k dispozici |
|Vytvořit novou databázi| Ano | Ano | Ano (databáze je mapována na keyspace) | Ano | Není k dispozici | Není k dispozici |
|Aktualizovat databázi| Ano | Ano | Ano (databáze je mapována na keyspace) | Ano | Není k dispozici | Není k dispozici |


## <a name="azure-cosmos-containers"></a>Kontejnery Azure Cosmos DB

Kontejner Azure Cosmos je jednotka škálovatelnosti pro zřízenou propustnost i úložiště. Kontejner je vodorovně rozdělena a pak replikovány napříč více oblastí. Položky, které přidáte do kontejneru a propustnost, kterou zřídíte na něm jsou automaticky distribuovány přes sadu logických oddílů na základě klíče oddílu. Další informace o rozdělení a rozdělení klíčů naleznete v [tématu Data oddílu](partition-data.md). 

Když vytvoříte kontejner Azure Cosmos, nakonfigurujete propustnost v jednom z následujících režimů:

* **Vyhrazený režim zřízená propustnost**: Propustnost zřízená v kontejneru je vyhrazena výhradně pro tento kontejner a je zálohována smlouvami SLA. Další informace najdete v tématu [Jak zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).

* **Režim sdílené zřízené propustnost**: Tyto kontejnery sdílejí zřízenou propustnost s ostatními kontejnery ve stejné databázi (s výjimkou kontejnerů, které byly nakonfigurovány s vyhrazenou zřízenou propustností). Jinými slovy zřízená propustnost v databázi je sdílena mezi všechny kontejnery "sdílené propustnost". Další informace najdete v tématu [Jak zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

> [!NOTE]
> Sdílenou a vyhrazenou propustnost můžete konfigurovat pouze při vytváření databáze a kontejneru. Pokud chcete po vytvoření kontejneru přejít z režimu vyhrazené propustnosti na režim sdílené propustnosti (nebo naopak), musíte vytvořit nový kontejner a migrovat data do nového kontejneru. Data můžete migrovat pomocí funkce zdroje změn Azure Cosmos DB.

Kontejner Azure Cosmos můžete škálovat elasticky, ať už vytvoříte kontejnery pomocí vyhrazených nebo sdílených režimů zřízené propustnost.

Kontejner Azure Cosmos je kontejner položek bez schématu. Položky v kontejneru může mít libovolné schémata. Například položku, která představuje osobu a položku, která představuje automobil, lze umístit do *stejného kontejneru*. Ve výchozím nastavení jsou všechny položky, které přidáte do kontejneru, automaticky indexovány bez nutnosti explicitní správy indexu nebo schématu. Chování indexování můžete přizpůsobit konfigurací [zásad indexování](index-overview.md) v kontejneru. 

Můžete nastavit [Time to Live (TTL)](time-to-live.md) na vybrané položky v kontejneru Azure Cosmos nebo pro celý kontejner řádně vymazat tyto položky ze systému. Azure Cosmos DB automaticky odstraní položky po vypršení jejich platnosti. Také zaručuje, že dotaz provedený v kontejneru nevrátí položky, jejichž platnost vypršela v rámci pevné svázané. Další informace najdete [v tématu Konfigurace ttl v kontejneru](how-to-time-to-live.md).

[Informační kanál změn](change-feed.md) můžete použít k odběru protokolu operací, který je spravován pro každý logický oddíl kontejneru. Kanál změn poskytuje protokol všech aktualizací provedených v kontejneru, spolu s obrázky před a po položek. Další informace naleznete v [tématu Vytváření reaktivních aplikací pomocí kanálu změn](serverless-computing-database.md). Můžete také nakonfigurovat dobu uchovávání pro zdroj změn pomocí zásady zdroje změn v kontejneru. 

Můžete zaregistrovat [uložené procedury, aktivační události, uživatelem definované funkce (UD)](stored-procedures-triggers-udfs.md)a [sloučit procedury](how-to-manage-conflicts.md) pro váš kontejner Azure Cosmos. 

Můžete zadat [omezení jedinečnéklíče](unique-keys.md) pro váš kontejner Azure Cosmos. Vytvořením zásady jedinečného klíče zajistíte jedinečnost jedné nebo více hodnot na klíč logického oddílu. Pokud vytvoříte kontejner pomocí zásady jedinečného klíče, nelze vytvořit žádné nové nebo aktualizované položky s hodnotami, které duplikují hodnoty určené jedinečným omezením klíče. Další informace naleznete v [tématu Jedinečná omezení klíče](unique-keys.md).

Kontejner Azure Cosmos se specializuje na entity specifické pro rozhraní API, jak je znázorněno v následující tabulce:

| Entita Azure Cosmos | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- |
|Kontejner Azure Cosmos | Kontejner | Table | Kolekce | Graph | Table |

### <a name="properties-of-an-azure-cosmos-container"></a>Vlastnosti kontejneru Azure Cosmos

Kontejner Azure Cosmos má sadu vlastností definovaných systémem. V závislosti na rozhraní API, které používáte, některé vlastnosti nemusí být přímo vystaveny. Následující tabulka popisuje seznam vlastností definovaných systémem:

| Systémem definovaná vlastnost | Systémově generované nebo uživatelsky konfigurovatelné | Účel | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Systémově generovaný | Jedinečný identifikátor kontejneru | Ano | Ne | Ne | Ne | Ne |
|\_Etag | Systémově generovaný | Značka entity použitá pro optimistický ovládací prvek souběžnosti | Ano | Ne | Ne | Ne | Ne |
|\_Ts | Systémově generovaný | Poslední aktualizované časové razítko kontejneru | Ano | Ne | Ne | Ne | Ne |
|\_Vlastní | Systémově generovaný | Adresovatelný identifikátor URI kontejneru | Ano | Ne | Ne | Ne | Ne |
|id | Uživatelsky konfigurovatelné | Uživatelem definovaný jedinečný název kontejneru | Ano | Ano | Ano | Ano | Ano |
|indexováníZásady | Uživatelsky konfigurovatelné | Poskytuje možnost změnit cestu indexu, typ indexu a režim indexu. | Ano | Ne | Ne | Ne | Ano |
|TimeToLive | Uživatelsky konfigurovatelné | Poskytuje možnost automaticky odstranit položky z kontejneru po uplynutí nastaveného časového období. Podrobnosti viz [Time to Live](time-to-live.md). | Ano | Ne | Ne | Ne | Ano |
|changeFeedPolicy | Uživatelsky konfigurovatelné | Slouží ke čtení změn provedených v položkách v kontejneru. Podrobnosti naleznete v [tématu Změna informačního kanálu](change-feed.md). | Ano | Ne | Ne | Ne | Ano |
|uniqueKeyPolicy | Uživatelsky konfigurovatelné | Slouží k zajištění jedinečnosti jedné nebo více hodnot v logickém oddílu. Další informace naleznete v [tématu Jedinečná omezení klíče](unique-keys.md). | Ano | Ne | Ne | Ne | Ano |

### <a name="operations-on-an-azure-cosmos-container"></a>Operace v kontejneru Azure Cosmos

Kontejner Azure Cosmos podporuje následující operace při použití některého z azure cosmos api:

| Operace | Azure CLI | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Výčet kontejnerů v databázi | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Čtení kontejneru | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Vytvoření nového kontejneru | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Aktualizace kontejneru | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |
| Odstranění kontejneru | Ano | Ano | Ano | Ano | Není k dispozici | Není k dispozici |

## <a name="azure-cosmos-items"></a>Položky Azure Cosmos

V závislosti na rozhraní API, které používáte, může položka Azure Cosmos představovat dokument v kolekci, řádek v tabulce nebo uzel nebo okraj v grafu. V následující tabulce je zobrazeno mapování entit specifických pro rozhraní API na položku Azure Cosmos:

| Cosmos entita | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- |
|Položka Azure Cosmos | Dokument | Řádek | Dokument | Uzel nebo hrana | Položka |

### <a name="properties-of-an-item"></a>Vlastnosti položky

Každá položka Azure Cosmos má následující vlastnosti definované systémem. V závislosti na rozhraní API, které používáte, některé z nich nemusí být přímo vystaveny.

| Systémem definovaná vlastnost | Systémově generované nebo uživatelsky konfigurovatelné| Účel | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Systémově generovaný | Jedinečný identifikátor položky | Ano | Ne | Ne | Ne | Ne |
|\_Etag | Systémově generovaný | Značka entity použitá pro optimistický ovládací prvek souběžnosti | Ano | Ne | Ne | Ne | Ne |
|\_Ts | Systémově generovaný | Časové razítko poslední aktualizace položky | Ano | Ne | Ne | Ne | Ne |
|\_Vlastní | Systémově generovaný | Adresovatelný identifikátor URI položky | Ano | Ne | Ne | Ne | Ne |
|id | Buď | Uživatelem definovaný jedinečný název v logickém oddílu. | Ano | Ano | Ano | Ano | Ano |
|Libovolné uživatelem definované vlastnosti | Definované uživatelem | Uživatelem definované vlastnosti reprezentované v nativní reprezentaci rozhraní API (včetně JSON, BSON a CQL) | Ano | Ano | Ano | Ano | Ano |

> [!NOTE]
> Jedinečnost vlastnosti `id` je vynucena pouze v rámci každého logického oddílu. Více dokumentů může `id` mít stejnou vlastnost s různými hodnotami klíče oddílu.

### <a name="operations-on-items"></a>Operace s položkami

Položky Azure Cosmos podporují následující operace. K provádění operací můžete použít libovolné z api Služby Azure Cosmos.

| Operace | Azure CLI | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Vložit, Nahradit, Odstranit, Upsert, Číst | Ne | Ano | Ano | Ano | Ano | Ano |

## <a name="next-steps"></a>Další kroky

Seznamte se s těmito úkoly a koncepty:

* [Zřizování propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md)
* [Zřizování propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md)
* [Práce s logickými oddíly](partition-data.md)
* [Konfigurace ttl v kontejneru Azure Cosmos](how-to-time-to-live.md)
* [Vytváření reaktivních aplikací pomocí kanálu změn](change-feed.md)
* [Konfigurace jedinečného omezení klíče pro kontejner Azure Cosmos](unique-keys.md)
