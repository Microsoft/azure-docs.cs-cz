---
title: Nejčastější dotazy k režimu automatického škálování v Azure Cosmos DB
description: Nejčastější dotazy týkající se zajištěné propustnosti automatického škálování pro Azure Cosmos DB databází a kontejnerů
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 413e9c71850b047172859c681cdbb422b7def032
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196462"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Nejčastější dotazy týkající se zajištěné propustnosti automatického škálování v Azure Cosmos DB

Díky zřízené propustnosti v režimu automatického škálování Azure Cosmos DB bude automaticky spravovat a škálovat RU/s vašeho kontejneru nebo databáze na základě využití. Tento článek obsahuje odpovědi na nejčastější dotazy týkající se automatického škálování.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="is-autoscale-mode-supported-for-all-apis"></a>Podporuje se režim automatického škálování pro všechna rozhraní API?
Ano, režim automatického škálování se podporuje pro všechna rozhraní API: Core (SQL), Gremlin, Table, Cassandra a API pro MongoDB.

### <a name="is-autoscale-mode-supported-for-multi-master-accounts"></a>Podporuje se režim automatického škálování pro účty s více hlavními servery?
Ano, režim automatického škálování je podporován pro účty s více hlavními servery. Maximální RU/s jsou dostupné v každé oblasti, která se přidá k účtu Cosmos. 

### <a name="what-is-the-pricing-for-autoscale"></a>Jaké jsou ceny pro automatické škálování?
Podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB. 

### <a name="how-do-i-enable-autoscale-for-my-containers-or-databases"></a>Návody povolit automatické škálování pro moje kontejnery nebo databáze?
Režim automatického škálování je možné povolit pro nové kontejnery a databáze vytvořené pomocí Azure Portal. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autoscale-mode"></a>Je k dispozici podpora CLI nebo sady SDK pro vytváření kontejnerů nebo databází pomocí režimu automatického škálování?
V současné době můžete vytvořit pouze prostředky s režimem automatického škálování z Azure Portal. Podpora rozhraní příkazového řádku a sady SDK ještě není dostupná.

### <a name="can-i-enable-autoscale-on-an-existing-container-or-a-database"></a>Můžu povolit automatické škálování u existujícího kontejneru nebo databáze?
V současné době můžete při vytváření nových kontejnerů a databází povolit automatické škálování. Podpora pro povolení režimu automatického škálování u existujících kontejnerů a databází ještě není k dispozici. Existující kontejnery můžete migrovat do nového kontejneru pomocí [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) nebo [změnit informační kanál](change-feed.md). 

### <a name="can-i-turn-off-autoscale-mode-on-a-container-or-database"></a>Můžu zapnout režim automatického škálování u kontejneru nebo databáze?
Ano, automatické škálování můžete vypnout přepnutím na možnost ruční pro zřízenou propustnost. Když jste v aktuální verzi přepnuli z režimu automatického škálování do ručního režimu, nemůžete znovu povolit automatické škálování pro stejný prostředek. 

### <a name="is-autoscale-mode-supported-for-shared-throughput-databases"></a>Podporuje se režim automatického škálování pro databáze sdílených propustností?
Ano, režim automatického škálování je podporován pro databáze sdílené propustnosti. Pokud chcete tuto funkci povolit, vyberte režim automatického škálování a při vytváření databáze možnost **zřízení propustnosti** . 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autoscale-is-enabled"></a>Jaký je počet povolených kolekcí na sdílenou databázi propustnosti, pokud je povoleno automatické škálování?
U sdílených databází propustnosti s povoleným režimem automatického škálování je počet povolených kolekcí: MIN (25, max. RU/s databáze/1000). Pokud je například maximální propustnost databáze 20 000 RU/s, může mít databáze minimální (25, 20 000 RU/s/1000) = 20 kolekcí. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Jaké je omezení úložiště přidružené k jednotlivým možnostem maximálního RU/s?  
Limit úložiště v GB pro každé maximum RU/s je: max. RU/s databáze nebo kontejneru/100. Pokud je například maximum RU/s 20 000 RU/s, prostředek může podporovat 200 GB úložiště. V článku [omezení automatického škálování](provision-throughput-autoscale.md#autoscale-limits) najdete seznam dostupných maximálních ru/s a možnosti úložiště. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Co se stane, když překročím limit úložiště přidružený k naší maximální propustnosti?
Pokud dojde k překročení limitu úložiště přidruženého k maximální propustnosti databáze nebo kontejneru, Azure Cosmos DB automaticky zvýší maximální propustnost na další nejvyšší úroveň, která může podporovat tuto úroveň úložiště. Předpokládejme například, že databáze nebo kontejner se zřídí s možností 4000 RU/s maximální propustnost, která má limit úložiště 50 GB. Pokud se úložiště prostředků zvyšuje na 100 GB, maximální RU/s databáze nebo kontejneru se automaticky zvýší na 20 000 RU/s, což může podporovat až 200 GB. 

### <a name="how-quickly-will-autoscale-up-and-down-based-on-spikes-in-traffic"></a>Jak rychle bude automatické horizontální navýšení a snížení kapacity na základě Špičk v provozu?
V režimu automatického škálování můžete okamžitě škálovat nebo snížit velikost RU/s v rozsahu minimální a maximální RU/s na základě příchozího provozu. Fakturace se provádí při 1 hodinové členitosti, kde se vám za konkrétní hodinu účtuje nejvyšší RU/s.

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autoscale-mode"></a>Můžu pro režim automatického škálování zadat vlastní hodnotu maximální propustnosti (RU/s)?
V současné době můžete vybrat [čtyři možnosti](provision-throughput-autoscale.md#autoscale-limits) pro maximální propustnost (ru/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Můžu u databáze nebo kontejneru zvýšit maximum RU/s (přesunout na vyšší úroveň)? 
Ano. V možnosti **škálování & nastavení** pro váš kontejner nebo možnost **škálování** pro vaši databázi můžete pro režim automatického ŠKÁLOVÁNÍ vybrat vyšší maximální ru/s. Toto je asynchronní operace škálování, která může trvat delší dobu (obvykle 4-6 hodin, v závislosti na vybraném RU/s), protože služba zřídí více prostředků pro podporu vyšší škály. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Můžu snížit maximální RU/s (přesunout na nižší úroveň) v databázi nebo kontejneru?
Ano. Pokud je aktuální úložiště databáze nebo kontejneru pod limitem úložiště přidruženým k úrovni maximálního RU/s, na kterou chcete horizontální [navýšení](#what-is-the-storage-limit-associated-with-each-max-rus-option) kapacity, můžete snížit maximální ru/s na tuto úroveň. Pokud jste například vybrali 20 000 RU/s jako maximální RU/s, můžete snížit kapacitu maximálního RU/s na 4000 RU/s, pokud máte méně než 50 GB úložiště (limit úložiště přidružený ke 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Jaké je mapování mezi maximálním RU/s a fyzickými oddíly?
Při prvním výběru maximálního RU/s se Azure Cosmos DB zřídí: max. RU/s/10 000 RU/s = # fyzických oddílů. Každý [fyzický oddíl](partition-data.md#physical-partitions) může podporovat až 10 000 ru/s a 50 GB úložiště. Když velikost úložiště roste, Azure Cosmos DB bude automaticky rozdělit oddíly a přidat tak další fyzické oddíly pro zpracování zvýšení úložiště nebo zvýšit maximální úroveň RU/s, pokud úložiště [překročí přidružený limit](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Maximální RU/s databáze nebo kontejneru se rovnoměrně rozděluje napříč všemi fyzickými oddíly. Celkové propustnosti, na které se může každý fyzický oddíl škálovat, tedy: max. RU/s databáze nebo kontejner/# fyzické oddíly. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Co se stane, když příchozí požadavky překročí maximální RU/s databáze nebo kontejneru?
Pokud celkový počet spotřebovaných RU/s překročí maximální RU/s kontejneru nebo databáze, požadavky, které překračují maximum RU/s, budou omezené a vrátí stavový kód 429. Požadavky, které mají za následek více než 100% normalizovaného využití, budou omezeny také. Normalizované využití je definováno jako maximum využití RU/s ve všech fyzických oddílech. Předpokládejme například, že vaše maximální propustnost je 20 000 RU/s, a máte dva fyzické oddíly, P_1 a P_2, každý schopný škálování na 10 000 RU/s. Pokud v dané druhé P_1 používal 6000 ru a P_2 8000 ru, je normalizované využití MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

> [!NOTE]
> Azure Cosmos DB klientské sady SDK a nástroje pro import dat (Azure Data Factory, hromadně prováděč Library) se automaticky opakují na 429s, takže občasné 429sy jsou přesné. V případě trvalého vysokého počtu 429s může být potřeba zvýšit maximální RU/s nebo zkontrolovat strategii dělení pro [aktivní oddíl](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a>Je stále možné zobrazit 429s (omezení četnosti a přenosů), pokud je povoleno automatické škálování? 
Ano. 429s je možné zobrazit ve dvou scénářích. V případě, že celkový počet spotřebovaných RU/s přesáhne maximum RU/s kontejneru nebo databáze, služba omezí požadavky odpovídajícím způsobem. 

V případě, že je k dispozici aktivní oddíl, tj. hodnota klíče logického oddílu, která má neúměrně vyšší množství požadavků v porovnání s jinými hodnotami klíče oddílu, je možné, že základní fyzický oddíl překročí svůj rozpočet RU/s. Jako osvědčený postup pro zamezení aktivních oddílů [Vyberte dobrý klíč oddílu](partitioning-overview.md#choose-partitionkey) , který vede k rovnoměrné distribuci úložiště i propustnosti. 

Pokud například vyberete možnost maximální propustnost 20 000 RU/s a máte 200 GB úložiště se čtyřmi fyzickými oddíly, je možné každý fyzický oddíl automatické škálování až na 5000 RU/s. Pokud se v určitém klíči logického oddílu objevil nějaký aktivní oddíl, zobrazí se 429s, pokud se základní fyzický oddíl, na kterém se nachází, překročí 5000 RU/s, tj. překračuje 100% normalizovaného využití.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [Povolit automatické škálování v kontejneru nebo databázi Azure Cosmos](provision-throughput-autoscale.md#create-db-container-autoscale).
* Přečtěte si o [výhodách zřízené propustnosti v režimu automatického škálování](provision-throughput-autoscale.md#autoscale-benefits).
* Další informace o [logických a fyzických oddílech](partition-data.md).
