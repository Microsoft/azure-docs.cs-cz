---
title: Nejčastější dotazy týkající se rozhraní API Azure Cosmos DB pro cassandru.
description: Získejte odpovědi na nejčastější dotazy týkající se rozhraní AZURE Cosmos DB API pro Cassandru.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 416f0c5f995a101298e84c81317c7d39fb5d43f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727382"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-db-api-for-cassandra"></a>Nejčastější dotazy týkající se rozhraní AZURE Cosmos DB API pro Cassandra

## <a name="what-are-some-key-differences-between-apache-cassandra-and-cassandra-api"></a>Jaké jsou některé klíčové rozdíly mezi Apache Cassandra a Cassandra API?

- Apache Cassandra doporučuje limit 100 MB na velikost klíče oddílu. Cassandra API umožňuje až 10 GB na oddíl.
- Apache Cassandra umožňuje zakázat trvalé revize - tj. To může vést ke ztrátě dat, pokud uzel přejde před Memtables vyprázdnění sStables na disku. Cosmos DB vždy provádí trvalé revize, takže nikdy nebudete mít ztrátu dat.
- Apache Cassandra může vidět snížený výkon, pokud zatížení zahrnuje mnoho nahrazení a / nebo odstraní. Důvodem je neplatné, že úlohy čtení musí přeskočit načíst nejnovější data. Cassandra API neuvidí snížený výkon čtení, pokud má zatížení velké množství nahrazení nebo odstranění.
- Během scénáře vysoké nahrazení úlohy, zhutnění je třeba spustit sloučit SSTables na disku (sloučení je potřeba, protože Apache Cassandra zápisy jsou pouze připojit, tedy více aktualizací jsou uloženy jako jednotlivé položky SSTable, které je třeba pravidelně slučovat). To může také vést ke snížení výkonu čtení během zhutnění. K tomu nedochází v rozhraní API Cassandra, protože neimplementuje zhutnění.
- Nastavení replikačního faktoru 1 je možné s Apache Cassandra. Však vede k nízké dostupnosti, pokud pouze uzel s daty přejde dolů. Toto není problém s rozhraním API Azure Cosmos DB Cassandra, protože je vždy replikační faktor 4 (kvorum 3).
- Přidání nebo odebrání uzlů v Apache Cassandra vyžaduje mnoho ručního zásahu, ale také vysoký procesor na novém uzlu, zatímco existující uzly přesunou některé z jejich rozsahů tokenů do nového uzlu. To je stejné při vyřazení existujícího uzlu z provozu. Horizontální navýšení kapacity se však provádí bez problémů pod kapotou v rozhraní API Azure Cosmos DB Cassandra, bez jakýchkoli problémů zjištěných ve službě nebo aplikaci.
- Není třeba nastavit num_tokens na každém uzlu v clusteru jako v Apache Cassandra. Uzly a rozsahy tokenů jsou plně spravovány cosmos DB.
- Rozhraní API Azure Cosmos DB Cassandra je plně spravované, takže nepotřebujete příkazy nodetool, jako je oprava, vyřazení z provozu atd., které se používají v Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Další nejčastější dotazy

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Jaká je verze protokolu podporovaná rozhraním AZURE Cosmos DB Cassandra API? Existuje plán na podporu jiných protokolů?

Rozhraní API Azure Cosmos DB Cassandra podporuje rozhraní CQL verze 3.x. Je to CQL kompatibilita je založena na veřejném [apache cassandra github repozitář .](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile) Pokud máte zpětnou vazbu ohledně podpory jiných protokolů, dejte nám vědět prostřednictvím [hlasové zpětné vazby od uživatelů](https://feedback.azure.com/forums/263030-azure-cosmos-db) nebo pošlete e-mail na adresu [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Proč je výběr propustnost pro tabulku požadavek?

Azure Cosmos DB nastaví výchozí propustnost pro váš kontejner na základě toho, kde vytvoříte tabulku z – portálu nebo CQL.
Azure Cosmos DB poskytuje záruky výkonu a latence s horní hranice na provoz. Tato záruka je možné, když modul můžete vynutit zásadsprávné zásady na operace klienta. Propustnost nastavení zajišťuje, že získáte garantovanou propustnost a latenci, protože platforma rezervuje tuto kapacitu a zaručuje úspěch provozu.
Můžete [elasticky změnit propustnost,](manage-scale-cassandra.md) abyste mohli těžit ze sezónnosti vaší aplikace a ušetřit náklady.

Koncept propustnost je vysvětleno v [jednotkách požadavků v azure cosmos DB](request-units.md) článku. Propustnost pro tabulku je distribuována napříč základní fyzické oddíly stejně.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Co je výchozí RU/s tabulky při vytvoření prostřednictvím CQL? Co když to budu muset změnit?

Azure Cosmos DB používá jednotky požadavků za sekundu (RU/s) jako měnu pro poskytování propustnost. Tabulky vytvořené prostřednictvím CQL mají 400 RU. ŽP můžete změnit z portálu.

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

### <a name="what-happens-when-throughput-is-used-up"></a>Co se stane, když je využita propustnost?

Azure Cosmos DB poskytuje záruky výkonu a latence s horní hranice na provoz. Tato záruka je možné, když modul můžete vynutit zásadsprávné zásady na operace klienta. To je možné na základě nastavení propustnosti, která zajišťuje, že získáte garantovanou propustnost a latenci, protože platforma rezervuje tuto kapacitu a zaručuje úspěch operace.
Při překročení této kapacity se zobrazí přetížená chybová zpráva označující, že vaše kapacita byla vymásnuta.
0x1001 Přetížené: požadavek nelze zpracovat, protože "Míra požadavků je velký". V tomto okamžiku je důležité zjistit, jaké operace a jejich objem způsobuje tento problém. Můžete získat představu o spotřebované kapacitě, která prochází zřízenou kapacitu s metrikami na portálu. Pak je třeba zajistit, že kapacita je spotřebována téměř stejně ve všech základních oddílech. Pokud se zobrazí většina propustnost je spotřebována jeden oddíl, máte zkosení pracovního vytížení.

K dispozici jsou metriky, které ukazují, jak se propustnost používá v průběhu hodin, dnů a za sedm dní, napříč oddíly nebo v souhrnu. Další informace najdete [v tématu Monitorování a ladění s metrikami v Azure Cosmos DB](use-metrics.md).

Diagnostické protokoly jsou vysvětleny v článku [protokolování diagnostiky Azure Cosmos DB.](logging.md)

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Má primární klíč mapovat na koncept klíče oddílu Azure Cosmos DB?

Ano, klíč oddílu se používá k umístění entity do správného umístění. V Azure Cosmos DB se používá k nalezení správného logického oddílu, který se ukládá na fyzickém oddílu. Koncept dělení je dobře vysvětleno v [oddílu a škálování v článku Azure Cosmos DB.](partition-data.md) Zásadní vzít pryč zde je, že logický oddíl by neměl jít přes 10-GB limit dnes.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Co se stane, když dostanu plnou kvótu" oznámení o tom, že oddíl je plný?

Azure Cosmos DB je systém založený na sla, který poskytuje neomezené škálování se zárukami latence, propustnosti, dostupnosti a konzistence. Toto neomezené úložiště je založeno na horizontálníškálování dat pomocí dělení jako klíčový koncept. Koncept dělení je dobře vysvětleno v [oddílu a škálování v článku Azure Cosmos DB.](partition-data.md)

Limit 10 GB na počet entit nebo položek na logický oddíl, které byste měli dodržovat. Chcete-li zajistit, že vaše aplikace škáluje dobře, doporučujeme *nevytvářet* aktivní oddíl uložením všech informací v jednom oddílu a dotazování. Tato chyba může přijít pouze v případě, že vaše data jsou zkosená:&nbsp;to znamená, že máte velké množství dat pro jeden klíč oddílu (více než 10 GB). Distribuci dat můžete najít pomocí portálu úložiště. Způsob, jak opravit tuto chybu, je znovu vytvořit tabulku a zvolit granulární primární (klíč oddílu), který umožňuje lepší distribuci dat.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Je možné použít Cassandra API jako úložiště hodnot klíčů s miliony nebo miliardami jednotlivých klíčů oddílů?

Azure Cosmos DB můžete ukládat neomezená data horizontálním navýšením kapacity úložiště. To je nezávislé na propustnost. Ano, můžete vždy stačí použít Cassandra API pro ukládání a načítání klíče / hodnoty zadáním pravé primární / partition klíč. Tyto jednotlivé klíče získat své vlastní logický oddíl a sedět na vrcholu fyzického oddílu bez problémů.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Je možné vytvořit více než jednu tabulku s Apache Cassandra API Azure Cosmos DB?

Ano, je možné vytvořit více než jednu tabulku s Apache Cassandra API. Každá z těchto tabulek je považována za jednotku propustnost a úložiště.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Je možné vytvořit více než jednu tabulku za sebou?

Azure Cosmos DB je systém řízený prostředky pro aktivity dat i řízení roviny. Kontejnery jako kolekce, tabulky jsou runtime entity, které jsou zřízeny pro danou kapacitu propustnosti. Vytvoření těchto kontejnerů v rychlém sledu není očekávané aktivity a omezené. Pokud máte testy, které přetažení /vytvoření tabulky okamžitě, zkuste je rozmístit.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Co je maximální počet tabulek, které lze vytvořit?

Neexistuje žádný fyzický limit na počet tabulek, [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) pošlete e-mail, pokud máte velký počet tabulek (kde celková stabilní velikost přesahuje 10 TB dat), které je třeba vytvořit z obvyklých 10s nebo 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Jaký je maximální počet keyspace, které můžeme vytvořit?

Neexistuje žádný fyzický limit na počet klíčových prostorů, protože jsou [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) kontejnery metadat, pošlete e-mail na pokud máte velký počet klíčových prostorů z nějakého důvodu.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Je možné přinést velké množství dat po spuštění z normální tabulky?

Ano, za předpokladu, že rovnoměrně distribuované oddíly, kapacita úložiště je automaticky spravována a zvyšuje, jak tlačit více dat. Takže můžete s jistotou importovat tolik dat, kolik potřebujete, bez správy a zřizování uzlů a další. Pokud však očekáváte velký nárůst dat, má větší smysl přímo [zjištdat očekávanou propustnost,](set-throughput.md) než ji spustit nižší a okamžitě ji zvýšit.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Je možné zadat nastavení souborů yaml ke konfiguraci apache casssandra API chování Azure Cosmos DB?

Apache Cassandra API Azure Cosmos DB je služba platformy. Poskytuje kompatibilitu na úrovni protokolu pro provádění operací. Skrývá složitost správy, monitorování a konfigurace. Jako vývojář/uživatel se nemusíte starat o dostupnost, neplatné kameny, mezipaměť klíčů, mezipaměť řádků, filtr květů a množství dalších nastavení. Rozhraní Apache Cassandra rozhraní Azure Cosmos DB se zaměřuje na poskytování výkonu pro čtení a zápis, který potřebujete bez režie konfigurace a správy.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Bude apache cassandra API pro Azure Cosmos DB podporovat příkazy pro přidání uzlu/stav clusteru/stav uzlu?

Apache Cassandra API je platformová služba, která umožňuje plánování kapacity, reagovat na požadavky na elasticitu propustnosti & úložiště hračkou. S Azure Cosmos DB, kterou zřídíte propustnost, potřebujete. Pak můžete škálovat nahoru a dolů libovolný počet krát přes den bez obav o přidání / odstranění uzlů nebo jejich správu. To znamená, že nemusíte používat uzel, nástroj pro správu clusteru příliš.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Co se stane s ohledem na různá nastavení konfigurace pro vytvoření keyspace, jako je jednoduchý / síť?

Azure Cosmos DB poskytuje globální distribuci po vybalení z důvodu dostupnosti a nízké latence. Není nutné nastavit repliky nebo jiné věci. Všechny zápisy jsou vždy trvale kvorum potvrzena v jakékoli oblasti, kde píšete při poskytování záruk výkonu.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Co se stane s ohledem na různá nastavení metadat tabulky, jako je filtr květu, ukládání do mezipaměti, čtení změn oprav, gc_grace, kompresní memtable_flush_period a další?

Azure Cosmos DB poskytuje výkon pro čtení a zápisy a propustnost bez nutnosti dotýkat se libovolného nastavení konfigurace a náhodně s nimi manipulovat.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Je pro tabulky Cassandra podporován a aktivní čas (TTL)?

Ano, ttl je podporován.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Je možné sledovat stav uzlu, stav repliky, gc a parametry operačního serveru dříve pomocí různých nástrojů? Co je třeba nyní sledovat?

Azure Cosmos DB je platformová služba, která vám pomůže zvýšit produktivitu a nemusíte se starat o správu a monitorování infrastruktury. Stačí se postarat o propustnost, která je k dispozici na portálu metriky najít, pokud jste stále omezené a zvýšit nebo snížit, že propustnost.
Monitor [SLA](monitor-accounts.md).
Použití [metrik y](use-metrics.md) Použít diagnostické [protokoly](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Které klientské sady SDK můžou pracovat s rozhraním Apache Cassandra API Azure Cosmos DB?

Ovladače klientů Apache Cassandra SDK, které používají CQLv3, byly použity pro klientské programy. Pokud máte další ovladače, které používáte, nebo pokud [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)čelíte problémům, pošlete e-mail na adresu .

### <a name="is-composite-partition-key-supported"></a>Je složený klíč oddílu podporován?

Ano, k vytvoření složeného klíče oddílu můžete použít běžnou syntaxi.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Mohu použít sstableloader pro načítání dat?

Ne, sstableloader není podporován.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Může být místní cluster Apache Cassandra spárován s rozhraním Cassandra rozhraní Azure Cosmos DB?

V současné době má Azure Cosmos DB optimalizované prostředí pro cloudové prostředí bez režie operací. Pokud požadujete párování, pošlete poštu [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) s popisem scénáře. Pracujeme na nabídce, která vám pomůže spárovat místní/jiný cloudový cluster Cassandra s rozhraním Cassandra API společnosti Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>Poskytuje rozhraní CASSANDRA API úplné zálohy?

Azure Cosmos DB poskytuje dvě bezplatné úplné zálohy pořízené v intervalu čtyři hodiny dnes ve všech rozhraních API. Tím zajistíte, že nemusíte nastavovat plán zálohování a další věci.
Pokud chcete upravit uchovávání informací a frekvenci, pošlete e-mail nebo [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) vyvoláte případ podpory. Informace o možnosti zálohování je k dispozici v [automatické zálohování online a obnovení pomocí Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) článku.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Jak zpracovává účet rozhraní API Cassandra převzetí služeb při selhání, pokud dojde k selhání oblasti?

Rozhraní API Azure Cosmos DB Cassandra si půjčuje z globálně distribuované platformy Azure Cosmos DB. Chcete-li zajistit, aby vaše aplikace mohla tolerovat prostoje datového centra, povolte alespoň jednu další oblast pro účet na portálu Azure Cosmos DB [Vývoj s účty Azure Cosmos DB s více oblastmi](high-availability.md). Prioritu oblasti můžete nastavit pomocí portálu [Vývoj s účty Azure Cosmos DB s více oblastmi](high-availability.md).

Můžete přidat libovolný počet oblastí pro účet a řídit, kde může převzít služby při selhání, a to poskytnutím priority převzetí služeb při selhání. Chcete-li použít databázi, musíte také zadat aplikaci. Pokud tak učiníte, vaši zákazníci nebudou mít prostoje.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Indexuje rozhraní API Apache Cassandra ve výchozím nastavení všechny atributy entity?

Ne. Cassandra API podporuje [sekundární indexy](cassandra-secondary-index.md), které se chovají velmi podobně jako Apache Cassandra. Ve výchozím nastavení neindexuje každý atribut.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Lze použít nový Cassandra API SDK místně s emulátorem?

Ano, to to je podporováno. Podrobnosti o tom, jak to povolit, naleznete [zde](local-emulator.md#cassandra-api)


### <a name="how-can-i-migrate-data-from-their-apache-cassandra-clusters-to-cosmos-db"></a>Jak mohu migrovat data z jejich clusterů Apache Cassandra do Cosmos DB?

O možnostech migrace si můžete přečíst [zde](cassandra-import-data.md).


### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Funkce x pravidelného Rozhraní API Cassandra nefunguje jako dnes, kde může být poskytnuta zpětná vazba?

Poskytněte zpětnou vazbu prostřednictvím [hlasové zpětné vazby od uživatelů](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>Další kroky

- Začínáme s [elastickým škálováním účtu rozhraní API Azure Cosmos DB Cassandra](manage-scale-cassandra.md).
