---
title: Práce s databázemi, kontejnery a položkami v Azure Cosmos DB
description: Tento článek popisuje, jak vytvořit a používat databáze, kontejnery a položky v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.reviewer: sngun
ms.openlocfilehash: f3906878755b7c7c2e3801da1bfa70a50d73ea16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318785"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Práce s databázemi, kontejnery a položkami v Azure Cosmos DB

Po vytvoření [účtu Azure Cosmos DB](account-overview.md) v rámci předplatného Azure můžete spravovat data v účtu vytvořením databází, kontejnerů a položek. Tento článek popisuje každou z těchto entit. 

Následující obrázek znázorňuje hierarchii různých entit v účtu Azure Cosmos DB:

:::image type="content" source="./media/databases-containers-items/cosmos-entities.png" alt-text="Entity účtu Azure Cosmos" border="false":::

## <a name="azure-cosmos-databases"></a>Databáze Azure Cosmos DB

V rámci svého účtu můžete vytvořit jednu nebo víc databází Azure Cosmos. Databáze je obdobná jako obor názvů. Databáze je jednotka správy pro sadu Cosmos kontejnerů Azure. Následující tabulka ukazuje, jak je databáze Azure Cosmos namapovaná na různé entity specifické pro rozhraní API:

| Entita Azure Cosmos | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- |
|Databáze Azure Cosmos | databáze | Prostor klíčů | databáze | databáze | Není k dispozici |

> [!NOTE]
> Při vytváření první tabulky pomocí rozhraní API pro tabulky účty se ve vašem účtu Azure Cosmos automaticky vytvoří výchozí databáze.

### <a name="operations-on-an-azure-cosmos-database"></a>Operace v databázi Azure Cosmos

S Azure Cosmos Database můžete pracovat s Azure Cosmos API, jak je popsáno v následující tabulce:

| Operace | Azure CLI | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- |
|Zobrazit výčet všech databází| Yes | Yes | Ano (databáze je namapována na místo na disku) | Yes | Není k dispozici | Není k dispozici |
|Čtení databáze| Yes | Yes | Ano (databáze je namapována na místo na disku) | Yes | Není k dispozici | Není k dispozici |
|Vytvořit novou databázi| Yes | Yes | Ano (databáze je namapována na místo na disku) | Yes | Není k dispozici | Není k dispozici |
|Aktualizovat databázi| Yes | Yes | Ano (databáze je namapována na místo na disku) | Yes | Není k dispozici | Není k dispozici |


## <a name="azure-cosmos-containers"></a>Kontejnery Azure Cosmos

Kontejner Azure Cosmos je jednotka škálovatelnosti pro zřízenou propustnost a úložiště. Kontejner je vodorovně rozdělený a pak replikován napříč více oblastmi. Položky, které přidáte do kontejneru, se automaticky seskupí do logických oddílů, které jsou distribuované mezi fyzickými oddíly, a to na základě klíče oddílu. Propustnost na kontejneru je rovnoměrně rozložená napříč fyzickými oddíly. Další informace o dělení a klíčích oddílů naleznete v tématu [data oddílů](partition-data.md). 

Když vytvoříte kontejner Azure Cosmos, nakonfigurujete propustnost v jednom z následujících režimů:

* **Režim vyhrazené zajištěné propustnosti**: propustnost zřízená na kontejneru je exkluzivně vyhrazena pro daný kontejner a je zajištěna SLA. Další informace najdete v tématu [postup zřízení propustnosti v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).

* **Sdílený režim zřízené propustnosti**: tyto kontejnery sdílejí zřízenou propustnost s ostatními kontejnery ve stejné databázi (kromě kontejnerů, které byly nakonfigurovány s vyhrazenou zřízené propustností). Jinými slovy, zřízená propustnost v databázi se sdílí mezi všemi kontejnery "Shared propustnost". Další informace najdete v tématu [jak zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

> [!NOTE]
> Sdílenou a vyhrazenou propustnost můžete nakonfigurovat pouze při vytváření databáze a kontejneru. Pokud chcete po vytvoření kontejneru přejít z režimu vyhrazené propustnosti na režim sdílené propustnosti (nebo naopak), musíte vytvořit nový kontejner a migrovat data do nového kontejneru. Data můžete migrovat pomocí funkce Azure Cosmos DB změny kanálu.

Kontejner Azure Cosmos se může elasticky škálovat bez ohledu na to, jestli vytváříte kontejnery pomocí vyhrazeného nebo sdíleného režimu zřízené propustnosti.

Kontejner Azure Cosmos je kontejner položek nezávislá schématu. Položky v kontejneru mohou mít libovolná schémata. Například položka, která představuje osobu, a položku, která představuje automobil, lze umístit do *stejného kontejneru*. Ve výchozím nastavení se všechny položky, které přidáte do kontejneru, automaticky indexují bez nutnosti explicitní správy indexů nebo schémat. Můžete přizpůsobit chování indexování konfigurací [zásad indexování](index-overview.md) v kontejneru. 

Můžete nastavit [hodnotu TTL (Time to Live)](time-to-live.md) u vybraných položek v kontejneru Azure Cosmos nebo pro celý kontejner, aby se tyto položky korektně vymazaly ze systému. Azure Cosmos DB po vypršení platnosti položky automaticky odstraní. Také zaručuje, že dotaz prováděný na kontejneru nevrátí položky s vypršenou platností v rámci pevné vazby. Další informace najdete v tématu [Konfigurace TTL pro váš kontejner](how-to-time-to-live.md).

Můžete použít možnost [změnit kanál](change-feed.md) k přihlášení k odběru protokolu operací, který je spravovaný pro každý logický oddíl vašeho kontejneru. Změna kanálu poskytuje protokol všech aktualizací provedených v kontejneru spolu s obrázky před a po. Další informace najdete v tématu [sestavování reaktivních aplikací pomocí změny kanálu](serverless-computing-database.md). Dobu uchování pro kanál změn můžete nakonfigurovat také pomocí zásady změnit kanál na kontejneru.

Můžete registrovat [uložené procedury, triggery, uživatelsky definované funkce (UDF)](stored-procedures-triggers-udfs.md)a [postupy sloučení](how-to-manage-conflicts.md) pro váš kontejner Azure Cosmos.

V kontejneru Azure Cosmos můžete zadat [jedinečné omezení klíčů](unique-keys.md) . Vytvořením zásady jedinečného klíče zajistíte jedinečnost jedné nebo více hodnot na klíč logického oddílu. Pokud vytvoříte kontejner pomocí jedinečné zásady klíčů, je možné vytvořit nové nebo aktualizované položky s hodnotami, které duplikují hodnoty zadané pomocí omezení jedinečného klíče. Další informace najdete v tématu [omezení jedinečnosti klíčů](unique-keys.md).

Kontejner Azure Cosmos je specializovaný na entity specifické pro rozhraní API, jak je znázorněno v následující tabulce:

| Entita Azure Cosmos | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- |
|Kontejner Azure Cosmos | Kontejner | Table | Kolekce | Graph | Tabulka |

> [!NOTE]
> Při vytváření kontejnerů se ujistěte, že nevytvoříte dva kontejnery se stejným názvem, ale s různou velikostí písmen. Důvodem je to, že některé části platformy Azure nerozlišují velká a malá písmena a to může vést k nejasnostem/kolizi telemetrie a akcí na kontejnerech s takovými názvy.

### <a name="properties-of-an-azure-cosmos-container"></a>Vlastnosti kontejneru Azure Cosmos

Kontejner Azure Cosmos obsahuje sadu vlastností definovaných systémem. V závislosti na tom, jaké rozhraní API používáte, nemusí být některé vlastnosti přímo vystavené. Následující tabulka popisuje seznam vlastností definovaných systémem:

| Vlastnost definovaná systémem | Uživatelsky vygenerované nebo uživatelsky konfigurovatelné | Účel | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Generované systémem | Jedinečný identifikátor kontejneru | Yes | No | No | No | No |
|\_značk | Generované systémem | Značka entity použitá pro optimistické řízení souběžnosti | Yes | No | No | No | No |
|\_licence | Generované systémem | Poslední aktualizované časové razítko kontejneru | Yes | No | No | No | No |
|\_samorozbalující | Generované systémem | Adresovatelný identifikátor URI kontejneru | Yes | No | No | No | No |
|id | Uživatelsky konfigurovatelné | Uživatelsky definovaný jedinečný název kontejneru | Yes | Yes | Yes | Yes | Yes |
|indexingPolicy | Uživatelsky konfigurovatelné | Poskytuje možnost změnit cestu indexu, typ indexu a režim indexu. | Yes | No | No | No | Yes |
|TimeToLive | Uživatelsky konfigurovatelné | Poskytuje možnost automaticky odstraňovat položky z kontejneru po nastaveném časovém období. Podrobnosti najdete v tématu [Time to Live](time-to-live.md). | Yes | No | No | No | Yes |
|changeFeedPolicy | Uživatelsky konfigurovatelné | Slouží ke čtení změn provedených u položek v kontejneru. Podrobnosti najdete v tématu [Změna kanálu](change-feed.md). | Yes | No | No | No | Yes |
|uniqueKeyPolicy | Uživatelsky konfigurovatelné | Slouží k zajištění jedinečnosti jedné nebo více hodnot v logickém oddílu. Další informace najdete v tématu [omezení jedinečnosti klíčů](unique-keys.md). | Yes | No | No | No | Yes |

### <a name="operations-on-an-azure-cosmos-container"></a>Operace na kontejneru Azure Cosmos

Kontejner Azure Cosmos podporuje následující operace při použití kterékoli z rozhraní API Azure Cosmos:

| Operace | Azure CLI | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Zobrazení výčtu kontejnerů v databázi | Yes | Yes | Yes | Yes | Není k dispozici | Není k dispozici |
| Čtení kontejneru | Yes | Yes | Yes | Yes | Není k dispozici | Není k dispozici |
| Vytvořit nový kontejner | Yes | Yes | Yes | Yes | Není k dispozici | Není k dispozici |
| Aktualizace kontejneru | Yes | Yes | Yes | Yes | Není k dispozici | Není k dispozici |
| Odstranění kontejneru | Yes | Yes | Yes | Yes | Není k dispozici | Není k dispozici |

## <a name="azure-cosmos-items"></a>Položky Azure Cosmos

V závislosti na tom, jaké rozhraní API používáte, může položka Azure Cosmos představovat buď dokument v kolekci, řádek v tabulce nebo uzel nebo okraj v grafu. Následující tabulka ukazuje mapování entit specifických pro rozhraní API na položku Azure Cosmos:

| Cosmos – entita | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- |
|Položka Azure Cosmos | Položka | Řádek | Dokument | Uzel nebo hrana | Položka |

### <a name="properties-of-an-item"></a>Vlastnosti položky

Každá položka Azure Cosmos má následující vlastnosti definované systémem. V závislosti na tom, jaké rozhraní API používáte, nemusí být některé z nich přímo vystavené.

| Vlastnost definovaná systémem | Uživatelsky vygenerované nebo uživatelsky konfigurovatelné| Účel | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Generované systémem | Jedinečný identifikátor položky | Yes | No | No | No | No |
|\_značk | Generované systémem | Značka entity použitá pro optimistické řízení souběžnosti | Yes | No | No | No | No |
|\_licence | Generované systémem | Časové razítko poslední aktualizace položky | Yes | No | No | No | No |
|\_samorozbalující | Generované systémem | Adresovatelný identifikátor URI položky | Yes | No | No | No | No |
|id | Kteroukoli | Uživatelsky definovaný jedinečný název v logickém oddílu. | Yes | Yes | Yes | Yes | Yes |
|Libovolné uživatelsky definované vlastnosti | Definované uživatelem | Uživatelsky definované vlastnosti reprezentované v rozhraní API – nativní reprezentace (včetně JSON, BSON a CQL) | Yes | Yes | Yes | Yes | Yes |

> [!NOTE]
> Jedinečnost `id` vlastnosti je vynutila pouze v rámci každého logického oddílu. U více dokumentů může být stejná `id` vlastnost s různými hodnotami klíče oddílu.

### <a name="operations-on-items"></a>Operace s položkami

Položky Azure Cosmos podporují následující operace. K provedení operací můžete použít kterékoli rozhraní API Azure Cosmos.

| Operace | Azure CLI | SQL API | Rozhraní Cassandra API | Rozhraní API služby Azure Cosmos DB pro MongoDB | Rozhraní Gremlin API | Rozhraní Table API |
| --- | --- | --- | --- | --- | --- | --- |
| Vložení, nahrazení, odstranění, Upsert, čtení | No | Yes | Yes | Yes | Yes | Ano |

## <a name="next-steps"></a>Další kroky

Přečtěte si o těchto úlohách a konceptech:

* [Zřízení propustnosti v databázi Azure Cosmos](how-to-provision-database-throughput.md)
* [Zajištění propustnosti v kontejneru Azure Cosmos](how-to-provision-container-throughput.md)
* [Práce s logickými oddíly](partition-data.md)
* [Konfigurace TTL v kontejneru Azure Cosmos](how-to-time-to-live.md)
* [Sestavování reaktivních aplikací pomocí kanálu změn](change-feed.md)
* [Konfigurace omezení jedinečného klíče v kontejneru Azure Cosmos](unique-keys.md)
