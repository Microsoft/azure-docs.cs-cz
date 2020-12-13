---
title: Nejčastější dotazy k zřízené propustnosti automatického škálování v Azure Cosmos DB
description: Nejčastější dotazy týkající se zajištěné propustnosti automatického škálování pro Azure Cosmos DB databází a kontejnerů
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: a740ad62dacc9a29cab1cc144f1789e125ec2e89
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368575"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Nejčastější dotazy týkající se zajištěné propustnosti automatického škálování v Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Díky zajištění propustnosti s automatickým škálováním Azure Cosmos DB bude automaticky spravovat a škálovat RU/s vaší databáze nebo kontejneru na základě využití. Tento článek obsahuje odpovědi na nejčastější dotazy týkající se automatického škálování.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Jaký je rozdíl mezi "autopilot" a AutoScale "v Azure Cosmos DB?
"Automatické škálování" nebo "zajištěná propustnost automatického škálování" je aktualizovaný název funkce, dříve označovaná jako autopilot. S aktuální verzí automatického škálování jsme přidali nové funkce, včetně možnosti nastavit vlastní maximální RU/s a programovou podporu. 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>Co se stane s databázemi nebo kontejnery vytvořenými v předchozím modelu vrstvy autopilotu?
Prostředky, které byly vytvořené pomocí modelu předchozí vrstvy, se automaticky podporují s novým modelem maximálního počtu RU/s pro automatické škálování. Horní mez úrovně se změní na nové maximum RU/s, což vede ke stejnému rozsahu stupnice. 

Pokud jste například dříve vybrali vrstvu, která se škáluje mezi 400 až 4000 RU/s, databáze nebo kontejner se teď bude zobrazovat s maximálním počtem RU/s 4000 RU/s, který se škáluje mezi 400 a 4000 RU/s. Tady můžete změnit maximální RU/s na vlastní hodnotu, aby odpovídala vašim úlohám. 

### <a name="how-quickly-will-autoscale-scale-up-based-on-spikes-in-traffic"></a>Jak rychle bude horizontální horizontální navýšení kapacity na základě špičky v provozu?
Díky automatickému škálování systém škáluje propustnost (RU/s) `T` v rozmezí od do `0.1 * Tmax` rozsahu a, a to na `Tmax` základě příchozího provozu. Vzhledem k tomu, že škálování je automatické a okamžité, můžete v jakémkoli bodě v čase spotřebovávat až do zřízené `Tmax` bez prodlevy. 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>Návody určit, na kolik RU/s se má systém aktuálně škálovat?
Pomocí [Azure monitor metriky](how-to-choose-offer.md#measure-and-monitor-your-usage) můžete monitorovat jak zřízené, tak i aktuální propustnost (ru/s), na které se systém škáluje. 

### <a name="what-is-the-pricing-for-autoscale"></a>Jaké jsou ceny pro automatické škálování?
Každou hodinu se vám bude účtovat nejvyšší propustnost, `T` kterou systém během hodiny škáluje. Pokud váš prostředek neobsahoval žádné požadavky během hodiny nebo nepřekračuje rozsah `0.1 * Tmax` , bude se vám účtovat minimum z `0.1 * Tmax` . Podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB. 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>Jak se ve vyúčtování projeví automatické škálování?
V rámci účtů oblastí s jedním zápisem je sazba automatického škálování za 100 RU/s nastavená na 1,5 × sazba standardní (ruční) zřízené propustnosti. Ve vyúčtování se zobrazí stávající standardní měřič propustnosti. Množství tohoto měřiče se vynásobí 1,5. Například pokud je nejvyšší RU/s v systému po celou hodinu 6000 RU/s, bude se vám za tuto hodinu účtovat 60 * 1,5 = 90 jednotky měřiče.

V účtech s více oblastmi zápisu je sazba automatického škálování za 100 RU/s stejná jako sazba pro standardní (ruční) zřízené více než jednu propustnost v oblasti zápisu. Ve vyúčtování se zobrazí existující měřič více oblastí zápisu. Vzhledem k tomu, že sazby jsou stejné, pokud používáte automatické škálování, uvidíte stejné množství jako u standardní propustnosti.

### <a name="does-autoscale-work-with-reserved-capacity"></a>Funguje automatické škálování s vyhrazenou kapacitou?
Ano. Když si koupíte rezervovanou kapacitu pro účty s jednou oblastí zápisu, použije se sleva rezervace pro prostředky automatického škálování na využití měřiče v poměru 1,5 * [poměr konkrétní oblasti](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region). 

Vyhrazená kapacita oblasti s více zápisy funguje stejně pro zajištění propustnosti v rámci automatického škálování a standardní (ruční). Zobrazit [rezervovanou kapacitu Azure Cosmos DB](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>Funguje automatické škálování na úrovni Free?
Ano. Na úrovni Free můžete použít propustnost automatického škálování na kontejneru. Podpora pro sdílené databáze propustnosti automatického škálování s vlastními maximálními RU/s ještě není k dispozici. Podívejte se, jak [funguje fakturace úrovně Free s automatické škálování](understand-your-bill.md#billing-examples-with-free-tier-accounts).

### <a name="is-autoscale-supported-for-all-apis"></a>Podporuje se automatické škálování pro všechna rozhraní API?
Ano, automatické škálování se podporuje pro všechna rozhraní API: Core (SQL), Gremlin, Table, Cassandra a API pro MongoDB.

### <a name="is-autoscale-supported-for-multi-region-write-accounts"></a>Podporuje se automatické škálování pro účty pro zápis ve více oblastech?
Ano. Maximální RU/s jsou dostupné v každé oblasti, která se přidá k účtu Azure Cosmos DB. 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>Návody povolit automatické škálování pro nové databáze nebo kontejnery?
V tomto článku najdete informace o [tom, jak povolit automatické škálování](how-to-provision-autoscale-throughput.md).

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>Můžu povolit automatické škálování u existující databáze nebo kontejneru?
Ano. V případě potřeby můžete také přepínat mezi zajištěním automatického škálování a standardní (ruční) zřízené propustnost. V současné době se pro všechna rozhraní API dá k těmto operacím použít jenom [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) .

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>Jak funguje migrace mezi automatickým škálováním a standardní (ruční) zřízené propustností?
V koncepční fázi je změna typu propustnosti dvoustupňový proces. Nejdřív odešlete žádost o změnu nastavení propustnosti pro použití automatického škálování nebo ruční zajištěné propustnosti. V obou případech systém automaticky určí a nastaví počáteční hodnotu RU/s na základě aktuálního nastavení a úložiště propustnosti. V průběhu tohoto kroku nebude přijata žádná hodnota RU/s zadaná uživatelem. Až se aktualizace dokončí, můžete [změnit ru/s](#can-i-change-the-max-rus-on-the-database-or-container) tak, aby odpovídaly vašim úlohám. 

**Migrace ze standardní (ruční) zřízené propustnosti do automatického škálování**

V případě kontejneru použijte následující vzorec k odhadu počátečního automatického škálování max. RU/s ``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)`` , zaokrouhleno na nejbližší 1000 ru/s. Skutečné počáteční automatické škálování na maximum RU/s se může lišit v závislosti na konfiguraci vašeho účtu.

Příklad #1: Předpokládejme, že máte kontejner s ruční zřízenou propustností 10 000 RU/s a 25 GB úložiště. Pokud povolíte automatické škálování, počáteční automatické škálování max. RU/s bude: 10 000 RU/s, které se bude škálovat mezi 1000-10 000 RU/s. 

Příklad #2: Předpokládejme, že máte kontejner s ruční zřízenou propustností 50 000 RU/s a 2500 GB úložiště. Pokud povolíte automatické škálování, počáteční automatické škálování max. RU/s bude: 250 000 RU/s, které se bude škálovat mezi 25 000-250 000 RU/s. 

**Migrace z automatického škálování na standardní (ruční) zřízené propustnost**

Počáteční ruční zajištěná propustnost bude stejná jako aktuální automatické škálování max. RU/s. 

Příklad: Předpokládejme, že máte databázi nebo kontejner automatického škálování s maximálním RU/s 20 000 RU/s (škály 2000-20 000 RU/s). Když aktualizujete pro použití ruční zřízené propustnosti, počáteční propustnost bude 20 000 RU/s. 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>Je k dispozici podpora Azure CLI nebo PowerShellu pro správu databází nebo kontejnerů pomocí automatického škálování?
V současné době můžete vytvářet a spravovat prostředky pomocí automatického škálování z Azure Portal, sady .NET V3 SDK, Java v4 SDK a Azure Resource Manager. Podpora v Azure CLI, PowerShellu a dalších sadách SDK ještě není dostupná.

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>Podporuje se automatické škálování pro sdílené databáze propustnosti?
Ano, automatické škálování se podporuje pro databáze sdílené propustnosti. Pokud chcete tuto funkci povolit, vyberte při vytváření databáze možnost automatického škálování a možnost **zřízení propustnosti** . 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>Jaký je počet povolených kontejnerů na sdílenou databázi propustnosti, pokud je povoleno automatické škálování?
Azure Cosmos DB vynutila maximálně 25 kontejnerů ve sdílené databázi propustnosti, která platí pro databáze s propustností automatického škálování nebo standardní (ruční). 

### <a name="what-is-the-impact-of-autoscale-on-database-consistency-level"></a>Jaký je dopad automatického škálování na úrovni konzistence databáze?
Neexistuje žádný dopad na úroveň konzistence databáze.
Další informace o dostupných úrovních konzistence najdete v článku o [úrovních konzistence](consistency-levels.md) .

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Jaké je omezení úložiště přidružené k jednotlivým možnostem maximálního RU/s?  
Limit úložiště v GB pro každé maximum RU/s je: max. RU/s databáze nebo kontejneru/100. Pokud je například maximum RU/s 20 000 RU/s, prostředek může podporovat 200 GB úložiště. V článku [omezení automatického škálování](provision-throughput-autoscale.md#autoscale-limits) najdete seznam dostupných maximálních ru/s a možnosti úložiště. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Co se stane, když překročím limit úložiště přidružený k naší maximální propustnosti?
Pokud dojde k překročení limitu úložiště přidruženého k maximální propustnosti databáze nebo kontejneru, Azure Cosmos DB automaticky zvýší maximální propustnost na další nejvyšší RU/s, která může podporovat tuto úroveň úložiště.

Pokud například zadáte maximální RU/s 50 000 RU/s (škály 5000-50 000 RU/s), můžete ukládat až 500 GB dat. Pokud překročíte 500 GB, například na 600 GB úložiště, nový maximální počet RU/s bude 60 000 (škálování mezi 6 000 a 60 000 RU/s).

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>Můžu u databáze nebo kontejneru změnit maximum RU/s? 
Ano. V tomto [článku](how-to-provision-autoscale-throughput.md) najdete informace o tom, jak změnit maximální ru/s. Když změníte maximální RU/s, v závislosti na požadované hodnotě může to být asynchronní operace, která může nějakou dobu trvat (může to být až 4-6 hodin, v závislosti na vybraném RU/s).

#### <a name="increasing-the-max-rus"></a>Zvýšení maximálního RU/s
Když odešlete požadavek na zvýšení maximálního RU/s `Tmax` , v závislosti na vybraném maximálním počtu ru/s zřídí služba další prostředky pro podporu vyšších maximálních ru/s. I když se to děje, vaše stávající úlohy a operace nebudou ovlivněny. Systém bude pokračovat ve škálování databáze nebo kontejneru mezi předchozí `0.1*Tmax` až do `Tmax` chvíle, kdy nový rozsah škálování `0.1*Tmax_new` na `Tmax_new` je připravený.

#### <a name="lowering-the-max-rus"></a>Snížení maximálního počtu RU/s
Když snížíte maximální RU/s, minimální hodnota, kterou můžete nastavit, je: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` , zaokrouhlená na nejbližší 1000 ru/s. 

Příklad #1: Předpokládejme, že máte kontejner automatického škálování s maximálním RU/s 20 000 RU/s (škáluje se v rozmezí 2000-20 000 RU/s) a 50 GB úložiště. Nejnižší minimální hodnota, kterou můžete nastavit na maximum RU/s, je: MAX (4000, 20 000/10, **50 * 100**) = 5000 ru/s (stupnice mezi 500 a 5000 ru/s).

Příklad #2: Předpokládejme, že máte kontejner automatického škálování s maximálním RU/s 100 000 RU/s a 100 GB úložiště. Nyní můžete škálovat maximum RU/s až 150 000 RU/s (škály mezi 15 000-150 000 RU/s). Nejnižší minimální hodnota, kterou teď můžete nastavit na maximum RU/s, je: MAX (4000, **150 000/10**, 100 * 100) = 15 000 ru/s (škáluje se mezi 1500 a 15 000 ru/s). 

U sdílené databáze propustnosti platí, že při snížení maximálního počtu RU/s je minimální hodnota, kterou můžete nastavit, na: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))` , zaokrouhlená na nejbližší 1000 ru/s.  

Výše uvedené vzorce a příklady se vztahují k minimálnímu automatickému škálování max. RU/s můžete nastavit a liší se od hodnoty `0.1 * Tmax` do rozsahu, který `Tmax` systém automaticky škáluje. Bez ohledu na to, co je maximum RU/s, bude systém vždycky škálovat `0.1 * Tmax` mezi `Tmax` . 

### <a name="how-does-ttl-work-with-autoscale"></a>Jak funguje TTL s AutoScale?
Díky automatickému škálování operace TTL neovlivňují škálování RU/s. Všechny ru spotřebované v důsledku TTL nejsou součástí fakturované RU/s v kontejneru automatického škálování. 

Předpokládejme například, že máte kontejner automatického škálování s 400 – 4000 RU/s:
- Hodina 1: T = 0: kontejner nemá žádné použití (žádné požadavky TTL nebo úloh). Fakturovatelné RU/s je 400 RU/s.
- Hodina 1: T = 1: hodnota TTL je povolena.
- Hodina 1: T = 2: kontejner zahajuje získávání požadavků, které spotřebovávají 1000 RU za 1 sekundu. K dispozici jsou také 200 Ruy, které se musí vyskytnout. Fakturovatelné RU/s je stále 1000 RU/s. Bez ohledu na to, kdy k TTLs dojde, nebude mít vliv na logiku škálování automatického škálování.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Jaké je mapování mezi maximálním RU/s a fyzickými oddíly?
Při prvním výběru maximálního RU/s se Azure Cosmos DB zřídí: max. RU/s/10 000 RU/s = # fyzických oddílů. Každý [fyzický oddíl](partitioning-overview.md#physical-partitions) může podporovat až 10 000 ru/s a 50 GB úložiště. Když velikost úložiště roste, Azure Cosmos DB bude automaticky rozdělit oddíly a přidat tak další fyzické oddíly pro zpracování zvýšení úložiště nebo zvýšit maximální RU/s, pokud úložiště [překročí přidružený limit](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Maximální RU/s databáze nebo kontejneru se rovnoměrně rozděluje napříč všemi fyzickými oddíly. Celkové propustnosti, na které se může každý fyzický oddíl škálovat, tedy: max. RU/s databáze nebo kontejner/# fyzické oddíly. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Co se stane, když příchozí požadavky překročí maximální RU/s databáze nebo kontejneru?
Pokud celkový počet spotřebovaných RU/s překročí maximální RU/s databáze nebo kontejneru, požadavky, které překračují maximum RU/s, budou omezené a budou vracet stavový kód 429. Požadavky, které mají za následek více než 100% normalizovaného využití, budou omezeny také. Normalizované využití je definováno jako maximum využití RU/s ve všech fyzických oddílech. Předpokládejme například, že vaše maximální propustnost je 20 000 RU/s, a máte dva fyzické oddíly, P_1 a P_2, každý schopný škálování na 10 000 RU/s. Pokud v dané druhé P_1 používal 6000 ru a P_2 8000 ru, je normalizované využití MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

> [!NOTE]
> Azure Cosmos DB klientské sady SDK a nástroje pro import dat (Azure Data Factory, hromadně prováděč Library) se automaticky opakují na 429s, takže občasné 429sy jsou přesné. V případě trvalého vysokého počtu 429s může být potřeba zvýšit maximální RU/s nebo zkontrolovat strategii dělení pro [aktivní oddíl](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> Je stále možné zobrazit 429s (omezení četnosti a přenosů), pokud je povoleno automatické škálování? 
Ano. Chyby 429 se můžou zobrazovat ve dvou scénářích. V případě, že celkový počet spotřebovaných RU/s přesáhne maximum RU/s databáze nebo kontejneru, služba omezí požadavky odpovídajícím způsobem. 

V případě, že je k dispozici aktivní oddíl, tj. hodnota klíče logického oddílu, která má neúměrně vyšší množství požadavků v porovnání s jinými hodnotami klíče oddílu, je možné, že základní fyzický oddíl překročí svůj rozpočet RU/s. Pokud se chcete vyhnout horkým oddílům, doporučujeme [zvolit vhodný klíč oddílu](partitioning-overview.md#choose-partitionkey), který zajistí rovnoměrnou distribuci úložiště a propustnosti. 

Pokud například vyberete možnost maximální propustnost 20 000 RU/s a máte 200 GB úložiště se čtyřmi fyzickými oddíly, je možné každý fyzický oddíl automatické škálování až na 5000 RU/s. Pokud se v určitém klíči logického oddílu objevil nějaký aktivní oddíl, zobrazí se 429s, pokud se základní fyzický oddíl, na kterém se nachází, překročí 5000 RU/s, tj. překračuje 100% normalizovaného využití.


## <a name="next-steps"></a>Další kroky

* Naučte se, jak [Povolit automatické škálování u Azure Cosmos DB databáze nebo kontejneru](how-to-provision-autoscale-throughput.md).
* Přečtěte si o [výhodách zřízené propustnosti pomocí automatického škálování](provision-throughput-autoscale.md#benefits-of-autoscale).
* Další informace o [logických a fyzických oddílech](partitioning-overview.md).
                        
