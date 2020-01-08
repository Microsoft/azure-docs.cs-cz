---
title: Nejčastější dotazy týkající se propustnosti v Azure Cosmos DB režimu autopilotu
description: Nejčastější dotazy týkající se režimu autopilotu pro Azure Cosmos DB databáze a kontejnery
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483307"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Nejčastější dotazy týkající se zřízené propustnosti v Azure Cosmos DB režimu automatického pilotního nasazení (Preview)
V režimu autopilotu bude Azure Cosmos DB automaticky spravovat a škálovat RU/s vašeho kontejneru nebo databáze na základě využití. Tento článek obsahuje odpovědi na nejčastější dotazy týkající se režimu autopilotu. 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="is-autopilot-mode-supported-for-all-apis"></a>Podporuje se režim autopilotu pro všechna rozhraní API?
Ano, režim autopilotu je podporován pro všechna rozhraní API: Core (SQL), Gremlin, Table, Cassandra a API pro MongoDB.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>Podporuje se režim automatického pilotního nasazení pro účty s více hlavními servery?
Ano, režim automatického pilotního nasazení je podporován pro účty s více hlavními servery. Maximální RU/s jsou dostupné v každé oblasti, která se přidá k účtu Cosmos. 

### <a name="what-is-the-pricing-for-autopilot"></a>Jaké jsou ceny pro funkci autopilot?
Podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Návody povolit autopilot pro moje kontejnery nebo databáze?
Režim autopilotu lze povolit pro nové kontejnery a databáze vytvořené pomocí Azure Portal. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Je k dispozici podpora CLI nebo sady SDK pro vytváření kontejnerů nebo databází pomocí režimu autopilotu?
V současné době ve verzi Preview můžete vytvořit prostředky jenom pomocí režimu autopilotu z Azure Portal. Podpora rozhraní příkazového řádku a sady SDK ještě není dostupná.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>Můžu povolit autopilotování pro existující kontejner nebo databázi?
V současné době můžete povolit autopilotování pro nové kontejnery a databáze při jejich vytváření. Podpora pro povolení režimu autopilotu u existujících kontejnerů a databází ještě není dostupná. Existující kontejnery můžete migrovat do nového kontejneru pomocí [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) nebo [změnit informační kanál](change-feed.md). 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>Můžu vypnout režim autopilotu na kontejneru nebo databázi?
Ano, funkci autopilot můžete vypnout přepnutím na možnost ruční pro zřízenou propustnost. V rámci verze Preview se po přepnutí z režimu autopilot do ručního režimu nedá znovu povolit autopilot pro stejný prostředek. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>Podporuje se režim autopilotu pro sdílené databáze propustnosti?
Ano, režim autopilotu se podporuje pro sdílené databáze propustnosti. Chcete-li povolit tuto funkci, vyberte možnost režim autopilotu a možnost **zajištění propustnosti** při vytváření databáze. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Jaký je počet povolených kolekcí pro jednu sdílenou databázi propustnosti, když je povolený autopilot?
U sdílených databází propustnosti s povoleným režimem autopilotu je počet povolených kolekcí: MIN (25, max. RU/s databáze/1000). Pokud je například maximální propustnost databáze 20 000 RU/s, může mít databáze minimální (25, 20 000 RU/s/1000) = 20 kolekcí. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Jaké je omezení úložiště přidružené k jednotlivým možnostem maximálního RU/s?  
Limit úložiště v GB pro každé maximum RU/s je: max. RU/s databáze nebo kontejneru/100. Pokud je například maximum RU/s 20 000 RU/s, prostředek může podporovat 200 GB úložiště. Dostupné maximum RU/s a možnosti úložiště najdete v článku [omezení autopilotu](provision-throughput-autopilot.md#autopilot-limits) . 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Co se stane, když překročím limit úložiště přidružený k naší maximální propustnosti?
Pokud dojde k překročení limitu úložiště přidruženého k maximální propustnosti databáze nebo kontejneru, Azure Cosmos DB automaticky zvýší maximální propustnost na další nejvyšší úroveň, která může podporovat tuto úroveň úložiště. Předpokládejme například, že databáze nebo kontejner se zřídí s možností 4000 RU/s maximální propustnost, která má limit úložiště 50 GB. Pokud se úložiště prostředků zvyšuje na 100 GB, maximální RU/s databáze nebo kontejneru se automaticky zvýší na 20 000 RU/s, což může podporovat až 200 GB. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Jak rychle bude automatické navýšení a snížení kapacity automatického pilotního nasazení na základě Špičk v provozu?
Pomocí automatického pilotního nasazení se okamžitě navýšení nebo snížení kapacity v rámci rozsahu minimální a maximálního RU/s v závislosti na příchozím provozu. Fakturace se provádí při 1 hodinové členitosti, kde se vám za konkrétní hodinu účtuje nejvyšší RU/s. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Můžu zadat vlastní hodnotu maximální propustnosti (RU/s) pro režim autopilotu?
V současné době ve verzi Preview můžete vybrat [čtyři možnosti](provision-throughput-autopilot.md#autopilot-limits) pro maximální propustnost (ru/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Můžu u databáze nebo kontejneru zvýšit maximum RU/s (přesunout na vyšší úroveň)? 
Ano. V možnosti **škálování & nastavení** pro váš kontejner nebo možnost **škálování** pro vaši databázi můžete vybrat vyšší maximální ru/s pro autopilot. Toto je asynchronní operace škálování, která může trvat delší dobu (obvykle 4-6 hodin, v závislosti na vybraném RU/s), protože služba zřídí více prostředků pro podporu vyšší škály. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Můžu snížit maximální RU/s (přesunout na nižší úroveň) v databázi nebo kontejneru?
Ano. Pokud je aktuální úložiště databáze nebo kontejneru pod limitem úložiště přidruženým k úrovni maximálního RU/s, na kterou chcete horizontální [navýšení](#what-is-the-storage-limit-associated-with-each-max-rus-option) kapacity, můžete snížit maximální ru/s na tuto úroveň. Pokud jste například vybrali 20 000 RU/s jako maximální RU/s, můžete snížit kapacitu maximálního RU/s na 4000 RU/s, pokud máte méně než 50 GB úložiště (limit úložiště přidružený ke 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Jaké je mapování mezi maximálním RU/s a fyzickými oddíly?
Při prvním výběru maximálního RU/s se Azure Cosmos DB zřídí: max. RU/s/10 000 RU/s = # fyzických oddílů. Každý [fyzický oddíl](partition-data.md#physical-partitions) může podporovat až 10 000 ru/s a 50 GB úložiště. Když velikost úložiště roste, Azure Cosmos DB bude automaticky rozdělit oddíly a přidat tak další fyzické oddíly pro zpracování zvýšení úložiště nebo zvýšit maximální úroveň RU/s, pokud úložiště [překročí přidružený limit](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Maximální RU/s databáze nebo kontejneru se rovnoměrně rozděluje napříč všemi fyzickými oddíly. Celkové propustnosti, na které se může každý fyzický oddíl škálovat, tedy: max. RU/s databáze nebo kontejner/# fyzické oddíly. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Co se stane, když příchozí požadavky překročí maximální RU/s databáze nebo kontejneru?
Pokud celkový počet spotřebovaných RU/s překročí maximální RU/s kontejneru nebo databáze, požadavky, které překračují maximum RU/s, budou omezené a vrátí stavový kód 429. Požadavky, které mají za následek více než 100% normalizovaného využití, budou omezeny také. Normalizované využití je definováno jako maximum využití RU/s ve všech fyzických oddílech. Předpokládejme například, že vaše maximální propustnost je 20 000 RU/s, a máte dva fyzické oddíly, P_1 a P_2, každý schopný škálování na 10 000 RU/s. Pokud v dané druhé P_1 používal 6000 ru a P_2 8000 ru, je normalizované využití MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

> [!NOTE]
> Azure Cosmos DB klientské sady SDK a nástroje pro import dat (Azure Data Factory, hromadně prováděč Library) se automaticky opakují na 429s, takže občasné 429sy jsou přesné. V případě trvalého vysokého počtu 429s může být potřeba zvýšit maximální RU/s nebo zkontrolovat strategii dělení pro [aktivní oddíl](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Je možné, že pokud je povolený autopilot, je stále možné zobrazit 429s (omezení rychlosti/omezení)? 
Ano. 429s je možné zobrazit ve dvou scénářích. V případě, že celkový počet spotřebovaných RU/s přesáhne maximum RU/s kontejneru nebo databáze, služba omezí požadavky odpovídajícím způsobem. 

V případě, že je k dispozici aktivní oddíl, tj. hodnota klíče logického oddílu, která má neúměrně vyšší množství požadavků v porovnání s jinými hodnotami klíče oddílu, je možné, že základní fyzický oddíl překročí svůj rozpočet RU/s. Jako osvědčený postup pro zamezení aktivních oddílů [Vyberte dobrý klíč oddílu](partitioning-overview.md#choose-partitionkey) , který vede k rovnoměrné distribuci úložiště i propustnosti. 

Pokud například vyberete možnost maximální propustnost 20 000 RU/s a máte 200 GB úložiště se čtyřmi fyzickými oddíly, je možné každý fyzický oddíl automatické škálování až na 5000 RU/s. Pokud se v určitém klíči logického oddílu objevil nějaký aktivní oddíl, zobrazí se 429s, pokud se základní fyzický oddíl, na kterém se nachází, překročí 5000 RU/s, tj. překračuje 100% normalizovaného využití.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [Povolit autopilotu na kontejneru nebo databázi Azure Cosmos](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode).
* Seznamte se s [výhodami autopilotu](provision-throughput-autopilot.md#benefits-of-autopilot-mode).
* Další informace o [logických a fyzických oddílech](partition-data.md).
