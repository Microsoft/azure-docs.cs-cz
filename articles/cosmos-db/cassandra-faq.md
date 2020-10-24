---
title: Nejčastější dotazy týkající se rozhraní API Cassandra pro Azure Cosmos DB
description: Získejte odpovědi na nejčastější dotazy týkající se rozhraní API Cassandra pro Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: e38096a0c5de266be6be6a58e09a251ce28a55df
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482600"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>Nejčastější dotazy týkající se rozhraní API Cassandra v Azure Cosmos DB

Tento článek popisuje rozdíly mezi funkcemi Apache Cassandra a rozhraní API Cassandra v Azure Cosmos DB. Obsahuje také odpovědi na nejčastější dotazy týkající se rozhraní API Cassandra v Azure Cosmos DB.

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Klíčové rozdíly mezi Apache Cassandra a rozhraní API Cassandra

- Apache Cassandra doporučuje pro velikost klíče oddílu omezení 100 MB. Rozhraní API Cassandra pro Azure Cosmos DB umožňuje až 20 GB na oddíl.
- Apache Cassandra umožňuje zakázat trvalá potvrzení změn. Můžete přeskočit zápis do protokolu potvrzení a přejít přímo na memtables. To může způsobit ztrátu dat v případě, že se uzel přestane vyprázdnit, než se memtables SSTables na disk. Azure Cosmos DB vždy provádí trvalá potvrzení, aby se zabránilo ztrátě dat.
- Apache Cassandra se může podívat na snížený výkon, pokud zatížení zahrnuje mnoho nahrazení nebo odstranění. Důvodem je označení, že úlohy čtení musí přeskočit, aby se načetla nejnovější data. Rozhraní API Cassandra se nezobrazuje snížený výkon při čtení, pokud má zatížení mnoho nahrazení nebo odstranění.
- V rámci scénářů s vysokým počtem úloh se musí komprimace spustit pro sloučení SSTables na disku. (Sloučení je potřeba, protože zápisy Apache Cassandra jsou jenom připojené. Několik aktualizací je uloženo jako jednotlivé SSTable položky, které je třeba pravidelně slučovat. Tato situace může také vést k nižšímu výkonu čtení během komprimace. Tento dopad na výkon se v rozhraní API Cassandra neprovádí, protože rozhraní API neimplementuje komprimaci.
- Pro Apache Cassandra je možné nastavit faktor replikace 1. Pokud ale jediný uzel s daty nefunguje, bude mít za následek nízkou dostupnost. Nejedná se o problém s rozhraní API Cassandra pro Azure Cosmos DB, protože je vždy faktor replikace 4 (kvorum 3).
- Přidání nebo odebrání uzlů v Apache Cassandra vyžaduje ruční zásah, společně s vysokým využitím procesoru na novém uzlu, zatímco stávající uzly přesunují do nového uzlu některé z jeho rozsahů tokenů. Tato situace je stejná, pokud vyřadíte existující uzel z provozu. Rozhraní API Cassandra se ale škálují bez problémů zjištěných ve službě nebo v aplikaci.
- V každém uzlu v clusteru není nutné nastavovat **num_tokens** jako v Apache Cassandra. Azure Cosmos DB plně spravuje uzly a rozsahy tokenů.
- Rozhraní API Cassandra je plně spravovaná. Nepotřebujete příkazy **nodetool** , jako je třeba oprava a vyřazení z provozu, které se používají v Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Další nejčastější dotazy

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Jakou verzi protokolu podporuje rozhraní API Cassandra?

Rozhraní API Cassandra pro Azure Cosmos DB podporuje CQL verze 3. x. Jeho kompatibilita s CQL je založená na veřejném [úložišti GitHubu Apache Cassandra](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile). Pokud máte zpětnou vazbu k podpoře dalších protokolů, dejte nám vědět prostřednictvím [zpětné vazby uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db) nebo odesláním e-mailu [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>Proč zvolit propustnost pro tabulku, kterou požadujete?

Azure Cosmos DB nastaví výchozí propustnost pro váš kontejner na základě místa, kde tabulku vytvoříte: Azure Portal nebo CQL.

Azure Cosmos DB poskytuje záruky pro výkon a latenci s horními mezemi operací. Tyto záruky jsou možné i v případě, že modul může vyhovět zásadám správného řízení operací klienta. Nastavení propustnosti zajistí, že získáte zaručenou propustnost a latenci, protože platforma rezervuje tuto kapacitu a zaručuje úspěch operace.
Můžete [elasticky měnit propustnost](manage-scale-cassandra.md) , abyste využili sezónnost vaší aplikace a ušetřili náklady.

Koncept propustnosti je vysvětlen v tématu [jednotky žádostí v Azure Cosmos DB](request-units.md) . Propustnost pro tabulku je rovnoměrně rozložená mezi příslušné fyzické oddíly.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>Jaká je propustnost tabulky, která je vytvořená prostřednictvím CQL?

Azure Cosmos DB používá jednotky žádostí za sekundu (RU/s) jako měnu pro zajištění propustnosti. Tabulky vytvořené prostřednictvím CQL ve výchozím nastavení mají 400 RU. RU můžete změnit z Azure Portal.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Co se stane, když se propustnost využije?

Azure Cosmos DB poskytuje záruky pro výkon a latenci s horními mezemi operací. Tyto záruky jsou možné i v případě, že modul může vyhovět zásadám správného řízení operací klienta. Nastavení propustnosti zajistí, že získáte zaručenou propustnost a latenci, protože platforma rezervuje tuto kapacitu a zaručuje úspěch operace.

Po převzetí této kapacity se zobrazí následující chybová zpráva, která indikuje, že se vaše kapacita použila:

**0x1001 přetížení: požadavek nejde zpracovat, protože frekvence požadavků je velká.** 

Je nutné zjistit, jaké operace (a jejich objem) způsobují tento problém. Můžete získat představu o využité kapacitě v rámci zřízené kapacity a metriky na Azure Portal. Pak je potřeba zajistit, aby byla kapacita spotřebována skoro stejně ve všech podkladových oddílech. Pokud vidíte, že jeden oddíl spotřebovává většinu propustností, máte k dispozici zešikmení úlohy.

K dispozici jsou metriky, které ukazují, jak se propustnost používá za hodiny, za dny a za sedm dní, napříč oddíly nebo v agregaci. Další informace najdete v tématu [monitorování a ladění pomocí metrik v Azure Cosmos DB](use-metrics.md).

Diagnostické protokoly jsou vysvětleny v článku [Azure Cosmos DB diagnostické protokolování](./monitor-cosmos-db.md) .

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Mapuje primární klíč na klíč oddílu Azure Cosmos DB konceptu?

Ano, klíč oddílu se použije k umístění entity do správného umístění. V Azure Cosmos DB se používá k vyhledání správného logického oddílu, který je uložený na fyzickém oddílu. Koncept dělení se dobře vysvětluje v [oddílu a měřítku v Azure Cosmos DB](partitioning-overview.md) článku. Základní poznatkem tady je, že logický oddíl by neměl přecházet přes limit 20 GB.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>Co se stane, když získám oznámení, že je oddíl plný?

Azure Cosmos DB je systém založený na smlouvě o úrovni služeb (SLA). Poskytuje neomezenou škálu a zaručuje latenci, propustnost, dostupnost a konzistenci. Toto neomezené úložiště je založené na horizontálním škálování dat a používá dělení jako klíčové pojmy. Koncept dělení se dobře vysvětluje v [oddílu a měřítku v Azure Cosmos DB](partitioning-overview.md) článku.

U počtu entit nebo položek na logický oddíl byste měli dodržovat limit 20 GB. Aby se zajistilo, že se vaše aplikace dobře škáluje, doporučujeme, abyste nevytvořili aktivní oddíl tím, *že budete ukládat* všechny informace v jednom oddílu a dotazovat se na něj. Tato chyba se může nacházet jenom v případě, že jsou vaše data nakloněná: to znamená, že máte spoustu dat pro jeden klíč oddílu (víc než 20 GB). Distribuci dat můžete najít pomocí portálu úložiště. Tuto chybu lze vyřešit tak, že znovu vytvoříte tabulku a vyberete podrobný primární (klíč oddílu), který umožňuje lepší distribuci dat.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>Můžu použít rozhraní API Cassandra jako úložiště hodnot klíčů s miliony nebo miliardami klíčů oddílů?

Azure Cosmos DB může ukládat neomezená data tak, že se škáluje úložiště. Toto úložiště je nezávisle na propustnosti. Ano, rozhraní API Cassandra můžete vždy použít k ukládání a načítání klíčů a hodnot zadáním správného primárního klíče/oddílu. Tyto jednotlivé klíče získají svůj vlastní logický oddíl a základem fyzický oddíl bez problémů.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Můžu vytvořit více než jednu tabulku s rozhraní API Cassandra?

Ano, je možné vytvořit více než jednu tabulku s rozhraní API Cassandra. Každá z těchto tabulek je považována za jednotku pro propustnost a úložiště.

### <a name="can-i-create-more-than-one-table-in-succession"></a>Je možné vytvořit více než jednu tabulku v případě úspěchu?

Azure Cosmos DB je systém řízený prostředky pro aktivity dat i řízení. Kontejnery, jako jsou kolekce a tabulky, jsou běhové entity, které jsou zřízené pro danou kapacitu propustnosti. Vytváření těchto kontejnerů v rychlém úspěchu není očekávanou aktivitou a může být omezené. Pokud máte testy pro okamžité vyřazení nebo vytváření tabulek, zkuste je umístit na místo.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>Jaký je maximální počet tabulek, které lze vytvořit?

Počet tabulek není nijak fyzickým omezením. Pokud máte velký počet tabulek (kde celková stálá velikost překročí 10 TB dat), která je potřeba vytvořit, ne běžné desítky nebo stovky, odešlete e-mail na adresu [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>Jaký je maximální počet míst, který můžu vytvořit?

Neexistuje žádný fyzický limit počtu prostorů klíčů, protože se jedná o kontejnery metadat. Pokud máte velký počet paměťových prostorů, odešlete e-mail na adresu [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>Můžu po spuštění z normální tabulky dostat spoustu dat?

Ano. Za předpokladu stejnoměrně distribuovaných oddílů je kapacita úložiště automaticky spravovaná a při doručování do více dat se zvyšuje. Takže můžete bez obav naimportovat tolik dat, kolik potřebujete, aniž byste museli spravovat a zřizovat uzly a další. Pokud však očekáváte hodně bezprostředního nárůstu dat, je vhodnější přímo [zřídit předpokládanou propustnost](set-throughput.md) , a to ani začít rychleji a okamžitě zvýšit.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>Můžu použít nastavení souboru YAML ke konfiguraci chování rozhraní API?

Rozhraní API Cassandra pro Azure Cosmos DB poskytuje kompatibilitu na úrovni protokolu pro provádění operací. Skrývá složitost správy, monitorování a konfigurace. Jako vývojář nebo uživatel se nemusíte starat o dostupnost, neplatnou hodnotu, mezipaměť klíčů, mezipaměť řádků, filtr Bloom a velké množství dalších nastavení. Rozhraní API Cassandra se zaměřuje na zajištění výkonu čtení a zápisu, které potřebujete, bez režie konfigurace a správy.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Bude rozhraní API Cassandra podporovat příkazy pro přidání uzlu, stav clusteru a stav uzlu?

Rozhraní API Cassandra zjednodušuje plánování kapacity a reaguje na požadavky na pružnost v případě propustnosti a úložiště. S Azure Cosmos DB zřizujete propustnost, kterou potřebujete. Pak můžete v průběhu dne kdykoli škálovat a snížit množství, aniž byste se museli starat o přidávání, odstraňování nebo správu uzlů. Nemusíte používat nástroje pro správu uzlů a clusterů.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>Co se stane s různými nastaveními konfigurace pro vytvoření prostoru, jako je jednoduchá síť nebo síť?

Azure Cosmos DB poskytuje globální distribuci z okna pro účely dostupnosti a nízké latence. Nemusíte nastavovat repliky ani jiné věci. Zápisy jsou vždy trvale kvorum potvrzené v jakékoli oblasti, kde píšete, a přitom poskytují záruky na výkon.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Co se stane s různými nastaveními pro metadata tabulky?

Azure Cosmos DB poskytuje záruky výkonu pro čtení, zápisy a propustnost. Nemusíte si dělat starosti se zabývat libovolnými konfiguračními nastaveními a omylem manipulovat. Mezi tato nastavení patří filtr Bloom, ukládání do mezipaměti, možnost opravy gc_grace a komprese memtable_flush_period.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Je pro tabulky Cassandra podporováno TTL (Time to Live)?

Ano, hodnota TTL je podporována.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Jak můžu monitorovat infrastrukturu spolu s propustností?

Azure Cosmos DB je služba platformy, která vám pomůže zvýšit produktivitu a nedělejte si starosti se správou a monitorováním infrastruktury. Například nemusíte monitorovat stav uzlu, stav repliky, GC a parametry operačního systému dříve s různými nástroji. Stačí jenom zajistit propustnost, která je k dispozici v metrikách portálu, abyste viděli, jestli se vám omezilo omezení, a pak tuto propustnost zvýšíte nebo snížíte. Máte následující možnosti:

- Monitorovat [SLA](./monitor-cosmos-db.md)
- Použití [metrik](use-metrics.md)
- Použití [diagnostických protokolů](./monitor-cosmos-db.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Které klientské sady SDK můžou pracovat s rozhraní API Cassandra?

Ovladače klienta sady Apache Cassandra SDK, které používají CQLv3, byly použity pro klientské programy. Pokud máte jiné ovladače, které používáte, nebo pokud máte potíže, odešlete e-mail na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="are-composite-partition-keys-supported"></a>Jsou podporovány klíče kompozitního oddílu?

Ano, k vytvoření klíčů složených oddílů můžete použít běžnou syntaxi.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Můžu použít sstableloader k načítání dat?

Ne, sstableloader se nepodporuje.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>Můžu párovat místní cluster Apache Cassandra pomocí rozhraní API Cassandra?

V současné době Azure Cosmos DB má optimalizované prostředí pro cloudové prostředí bez režie provozu. Pokud požadujete párování, pošlete e-mail na [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) Popis vašeho scénáře. Pracujeme na nabídce, která vám pomůžou spárovat místní nebo cloudový cluster Cassandra s rozhraní API Cassandra pro Azure Cosmos DB.

### <a name="does-the-cassandra-api-provide-full-backups"></a>Poskytuje rozhraní API Cassandra úplné zálohy?

Azure Cosmos DB poskytuje dvě bezplatné zálohy, které jsou pořízeny ve čtyřnásobných intervalech napříč všemi rozhraními API. Takže nemusíte nastavovat plán zálohování. 

Pokud chcete upravit dobu uchovávání a četnosti, odešlete e-mailem [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) nebo vyvolejte případ podpory. Informace o možnostech zálohování najdete v článku [automatické online zálohování a obnovení pomocí Azure Cosmos DB](online-backup-and-restore.md) .

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Jak účet rozhraní API Cassandra zpracovává převzetí služeb při selhání, pokud dojde k výpadku oblasti?

Rozhraní API Cassandra se vypůjčuje z globálně distribuované platformy Azure Cosmos DB. Chcete-li zajistit, aby vaše aplikace mohla tolerovat výpadky datového centra, povolte pro účet v Azure Portal alespoň jednu další oblast. Další informace najdete v tématu [Vysoká dostupnost pomocí Azure Cosmos DB](high-availability.md).

Pro účet můžete přidat tolik oblastí, kolik chcete, a určit, kde při převzetí služeb při selhání bude možné převzít služby při selhání. Chcete-li použít databázi, je nutné zadat také aplikaci. Když to uděláte, vaši zákazníci nebudou mít žádný výpadek.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Má rozhraní API Cassandra index všechny atributy entity ve výchozím nastavení?

Ne. Rozhraní API Cassandra podporuje [sekundární indexy](cassandra-secondary-index.md), které se chovají podobně jako Apache Cassandra. Rozhraní API ve výchozím nastavení neindexuje každý atribut.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Můžu novou rozhraní API Cassandra sadu SDK použít lokálně s emulátorem?

Ano, tato možnost je podporována. Podrobnosti o tom, jak to povolit, najdete v článku [použití emulátoru Azure Cosmos pro místní vývoj a testování](local-emulator.md#cassandra-api) .


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Jak mohu migrovat data z clusterů Apache Cassandra do Azure Cosmos DB?

Informace o možnostech migrace najdete v článku [migrace dat do rozhraní API Cassandra účtu v Azure Cosmos DB](cassandra-import-data.md) kurzu.


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Kde můžu sdělit svůj názor na funkce rozhraní API Cassandra?

Poskytněte zpětnou vazbu pomocí [hlasu pro uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Další kroky

- Začněte s [elastickým škálováním Azure Cosmos DB rozhraní API Cassandra účet](manage-scale-cassandra.md).