---
title: Nejčastější dotazy týkající se propustnosti v režimu automatického pilota Azure Cosmos DB
description: Nejčastější dotazy týkající se režimu autopilota pro databáze a kontejnery Azure Cosmos DB
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483307"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Nejčastější dotazy týkající se zřízené propustnosti v režimu automatického pilota Azure Cosmos DB (preview)
V režimu autopilota bude Azure Cosmos DB automaticky spravovat a škálovat RU/s vašeho kontejneru nebo databáze na základě využití. Tento článek odpovídá na nejčastější dotazy týkající se režimu autopilota. 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="is-autopilot-mode-supported-for-all-apis"></a>Je režim autopilota podporován pro všechna api?
Ano, režim autopilota je podporován pro všechna rozhraní API: Core (SQL), Gremlin, Table, Cassandra a API pro MongoDB.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>Je režim autopilota podporován pro účty s více kmenovými servery?
Ano, režim autopilota je podporován pro účty s více kmenovými servery. Maximální RU/s jsou k dispozici v každé oblasti, která je přidána do účtu Cosmos. 

### <a name="what-is-the-pricing-for-autopilot"></a>Jaké jsou ceny pro autopilota?
Podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Jak povolím autopilota pro své kontejnery nebo databáze?
Režim autopilota lze povolit v nových kontejnerech a databázích vytvořených pomocí portálu Azure. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Existuje podpora CLI nebo SDK pro vytváření kontejnerů nebo databází s režimem autopilota?
V současné době ve verzi preview můžete vytvářet prostředky jenom s režimem autopilota z portálu Azure. Podpora pro rozhraní PŘÍKAZCL a sdk ještě není k dispozici.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>Mohu povolit autopilota na existujícím kontejneru nebo databázi?
V současné době můžete povolit autopilota na nové kontejnery a databáze při jejich vytváření. Podpora pro povolení režimu autopilota na existujících kontejnerech a databázích ještě není k dispozici. Existující kontejnery můžete migrovat do nového kontejneru pomocí [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) nebo změnit [zdroj](change-feed.md). 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>Mohu vypnout režim autopilota v kontejneru nebo databázi?
Ano, autopilota můžete vypnout přepnutím na možnost "Ruční" pro zřízenou propustnost. Ve verzi preview po přepnutí z režimu autopilota do manuálního režimu nelze znovu povolit autopilota pro stejný zdroj. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>Je režim autopilota podporován pro databáze sdílených propustností?
Ano, režim autopilota je podporován pro databáze sdílených propustností. Chcete-li tuto funkci povolit, vyberte režim autopilota a možnost **Provision propustnost** při vytváření databáze. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Jaký je počet povolených kolekcí na sdílenou databázi propustností, pokud je povolen autopilot?
U sdílených databází propustností s povoleným režimem autopilota je počet povolených kolekcí: MIN(25, Max RU/s databáze / 1000). Například pokud je maximální propustnost databáze 20 000 RU/s, databáze může mít MIN(25, 20 000 RU/s / 1000) = 20 kolekcí. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Jaký je limit úložiště spojený s každou možností max RU/s?  
Limit pro ukládání v GB pro každou max RU/s je: Max RU/s databáze nebo kontejneru / 100. Pokud je například maximální RU/s 20 000 RU/s, může prostředek podporovat úložiště o velikosti 200 GB. Podívejte se na článek [o omezení autopilota,](provision-throughput-autopilot.md#autopilot-limits) kde najdete dostupné max RU/s a možnosti úložiště. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Co se stane, když překročím limit úložiště spojený s maximální propustností?
Pokud je překročen limit úložiště přidružený k maximální propustnost databáze nebo kontejneru, Azure Cosmos DB automaticky zvýší maximální propustnost na další nejvyšší úroveň, která může podporovat tuto úroveň úložiště. Předpokládejme například, že databáze nebo kontejner je zřízena s možností maximální propustnost 4000 RU/s, která má limit úložiště 50 GB. Pokud se úložiště prostředku zvýší na 100 GB, maximální RU/s databáze nebo kontejneru se automaticky zvýší na 20 000 RU/s, což může podporovat až 200 GB. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Jak rychle bude autopilot vertikálně stoupat a snižovat na základě špičky v provozu?
Autopilot okamžitě zvýší nebo zmenší Ru/s v rámci minimálního a maximálního rozsahu Ru/s na základě příchozího provozu. Fakturace se provádí na 1 hodinu granularity, kde se vám účtuje nejvyšší RU / s v určité hodině. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Mohu zadat vlastní hodnotu max propustnost (RU/s) pro režim autopilota?
V současné době můžete během verze preview vybrat mezi [čtyřmi možnostmi](provision-throughput-autopilot.md#autopilot-limits) maximální propustnosti (RU/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Mohu zvýšit max RU/s (přesunout na vyšší úroveň) v databázi nebo kontejneru? 
Ano. Z možnosti **Měřítko & nastavení** pro váš kontejner nebo **Měřítko** pro vaši databázi můžete vybrat vyšší max RU/s pro autopilota. Jedná se o asynchronní vertikálně najeté operace, která může někdy trvat dokončení (obvykle 4-6 hodin, v závislosti na ru/s vybrané) jako poskytování služeb více prostředků pro podporu vyšší škálování. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Mohu snížit maximální RU/s (přesunout na nižší úroveň) v databázi nebo kontejneru?
Ano. Tak dlouho, dokud aktuální úložiště databáze nebo kontejneru je pod [limit úložiště](#what-is-the-storage-limit-associated-with-each-max-rus-option) spojené s maximální ru/s vrstvy, které chcete vertikálně snížit na, můžete snížit maximální RU/s na tuto úroveň. Pokud jste například jako maximální RU/s vybrali 20 000 RU/s, můžete zmenšit maximální RU/s na 4000 RU/s, pokud máte méně než 50 GB úložiště (limit úložiště spojený s 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Jaké je mapování mezi maximálníru/s a fyzické oddíly?
Při prvním výběru maximální RU/s Azure Cosmos DB zřídí: Max RU/s / 10 000 RU/s = # fyzických oddílů. Každý [fyzický oddíl](partition-data.md#physical-partitions) může podporovat až 10 000 RU/s a 50 GB úložiště. Jak se zvětšuje velikost úložiště, Azure Cosmos DB automaticky rozdělí oddíly, aby přidal další fyzické oddíly pro zpracování zvýšení úložiště, nebo zvýší úroveň maximální RU/s, pokud úložiště [překročí přidružený limit](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Maximální RU/s databáze nebo kontejneru je rozdělenrovnoměrně mezi všechny fyzické oddíly. Takže celková propustnost, kterou může libovolný fyzický oddíl škálovat, je: Max RU/s databáze nebo kontejneru / # fyzické oddíly. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Co se stane, pokud příchozí požadavky překročí maximální ru/s databáze nebo kontejneru?
Pokud celková spotřebovaná RU/s překročí maximální RU/s kontejneru nebo databáze, požadavky, které překročí max RU/s, budou omezeny a vrátí stavový kód 429. Požadavky, které mají za následek více než 100 % normalizované využití bude také omezena. Normalizované využití je definováno jako max využití Ru/s ve všech fyzických oddílech. Předpokládejme například, že maximální propustnost je 20 000 RU/s a máte dva fyzické oddíly, P_1 a P_2, každý schopný škálování na 10 000 RU/s. V daném druhém případě, pokud P_1 použil 6000 RU a P_2 8000 RU, normalizované využití je MAX(6000 RU / 10 000 RU, 8000 RU / 10 000 RU) = 0,8.

> [!NOTE]
> Sady SDK klienta Azure Cosmos DB a nástroje pro import dat (Azure Data Factory, knihovna hromadného prováděcího modulu) se automaticky opakují na 429s, takže příležitostné 429s jsou v pořádku. Trvalý vysoký počet 429s může znamenat, že je třeba zvýšit max RU / s nebo zkontrolovat strategii dělení pro [horké oddíl .](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled)

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Je stále možné vidět 429s (škrcení / omezení rychlosti), když je povolen autopilot? 
Ano. Je možné vidět 429s ve dvou scénářích. Za prvé, pokud celková spotřebovaná RU/s překročí maximální RU/s kontejneru nebo databáze, služba odpovídajícím způsobem omezí požadavky. 

Za druhé, pokud existuje aktivní oddíl, tedy hodnota klíče logického oddílu, která má neúměrně vyšší množství požadavků ve srovnání s jinými hodnotami klíče oddílu, je možné, že základní fyzický oddíl překročí svůj rozpočet RU/s. Jako osvědčený postup, aby se zabránilo horké oddíly, [zvolte dobrý klíč oddílu,](partitioning-overview.md#choose-partitionkey) který má za následek rovnoměrné rozdělení úložiště a propustnost. 

Pokud například vyberete možnost maximální propustnost 20 000 RU/s a budete mít 200 GB úložiště se čtyřmi fyzickými oddíly, může být každý fyzický oddíl automaticky škálován až na 5000 RU/s. Pokud byl horký oddíl na konkrétní klíč logického oddílu, uvidíte 429s, když základní fyzický oddíl je umístěn v přesahuje 5000 RU/s, tj.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [povolit autopilota v kontejneru nebo databázi Azure Cosmos](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode).
* Další informace o [výhodách autopilota](provision-throughput-autopilot.md#benefits-of-autopilot-mode).
* Další informace o [logických a fyzických oddílech](partition-data.md).
