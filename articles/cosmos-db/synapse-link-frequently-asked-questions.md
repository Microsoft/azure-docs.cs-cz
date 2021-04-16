---
title: Nejčastější dotazy k Azure Synapse Linku pro Azure Cosmos DB
description: Získejte odpovědi na nejčastější dotazy týkající se synapse odkazů pro Azure Cosmos DB v oblastech, jako je fakturace, analytické úložiště, zabezpečení, doba provozu v analytickém úložišti.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 906651f8c48824e391879e0a579c6587231e7dfd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483822"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Nejčastější dotazy k Azure Synapse Linku pro Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Odkaz na Azure synapse pro Azure Cosmos DB vytvoří těsnou integraci mezi Azure Cosmos DB a analýzou Azure synapse. Umožňuje zákazníkům provozovat analýzy téměř v reálném čase prostřednictvím jejich provozních dat s plnou izolací výkonu ze svých transakčních úloh a bez kanálu ETL. Tento článek odpovídá na nejčastější dotazy k Synapse Linku pro Azure Cosmos DB.

## <a name="general-faq"></a>Obecné nejčastější dotazy

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Je připojení Azure synapse podporované pro všechna Azure Cosmos DB rozhraní API?

Odkaz na Azure synapse se podporuje pro rozhraní API Azure Cosmos DB SQL (Core) a pro Azure Cosmos DB API pro MongoDB. 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>Podporuje se propojení Azure synapse pro Azure Cosmos DB účty ve více oblastech?

Ano, pro účty Azure Cosmos ve více oblastech je data uložená v analytickém úložišti také globálně distribuována. Bez ohledu na to, jestli se využívá jedna nebo více oblastí zápisu, se analytické dotazy prováděné z Azure Synapse Analytics můžou obsluhovat z nejbližší místní oblasti.

Když plánujete nakonfigurovat Azure Cosmos DB účet ve více oblastech s podporou analytického úložiště, doporučuje se mít v době vytváření účtu přidané všechny nezbytné oblasti.

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Můžu povolit připojení Azure synapse jenom pro určitou oblast a ne pro všechny oblasti v nastavení účtu s více oblastmi?

Pokud je pro účet ve více oblastech povolený odkaz Azure synapse, vytvoří se analytické úložiště ve všech oblastech. Podkladová data jsou optimalizována pro propustnost a transakční konzistenci v transakčním úložišti.

### <a name="is-analytical-store-supported-in-all-azure-cosmos-db-regions"></a>Je analytické úložiště podporované ve všech oblastech Azure Cosmos DB?

Ano.

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Podporuje se zálohování a obnovení pro účty s povoleným propojením Azure synapse?

U kontejnerů se zapnutým analytickým úložištěm se v současnosti nepodporuje automatické zálohování a obnovování vašich dat v analytickém úložišti. 

Když je na databázovém účtu povoleno propojení synapse, Azure Cosmos DB bude pokračovat v automatickém ukládání [záloh](./online-backup-and-restore.md) vašich dat do transakčního úložiště (pouze) kontejnerů v naplánovaných intervalech zálohování, jako vždycky. Je důležité si uvědomit, že když se na nový účet obnoví kontejner se zapnutým analytickým úložištěm, kontejner se obnoví jenom v transakčním úložišti a není povolený žádný analytický obchod. 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>Můžu pro svůj Azure Cosmos DB účet zakázat funkci odkazu na Azure synapse?

V současné době není možné po povolení funkce Synapse Link na úrovni účtu tuto funkci zakázat. Mějte na paměti, že pokud je funkce Synapse Link povolená na úrovni účtu a neexistují žádné kontejnery s povoleným analytickým úložištěm, na fakturaci to nebude mít žádný vliv.

Pokud potřebujete tuto funkci vypnout, máte 2 možnosti. První možností je odstranit účet Azure Cosmos DB a vytvořit nový a v případě potřeby migrovat data. Druhou možností je otevřít lístek podpory a požádat o pomoc s migrací dat do jiného účtu.

### <a name="does-analytical-store-have-any-impact-on-cosmos-db-transactional-slas"></a>Má analytické úložiště nějaký dopad na Cosmos DB transakčního SLA?

Ne, neexistuje žádný dopad.

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analytické úložiště

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Můžu povolit analytické úložiště u existujících kontejnerů?

V současné době se analytické úložiště dá povolit jenom pro nové kontejnery (v nových i stávajících účtech).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>Můžu na mých Azure Cosmos DBch kontejnerech zakázat analytické úložiště po jeho povolení při vytváření kontejnerů?

V současné době není možné zakázat analytické úložiště pro kontejner Azure Cosmos DB po jeho povolení při vytváření kontejneru.

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>Podporuje se analytické úložiště pro kontejnery Azure Cosmos DB se zajištěnou propustností automatického škálování?

Ano, analytické úložiště je možné povolit u kontejnerů s zřízenou propustností automatického škálování.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Existují nějaké vliv na Azure Cosmos DB zřízené transakční úložiště ru?

Azure Cosmos DB garantuje izolaci výkonu mezi transakčními a analytickými úlohami. Povolení analytického úložiště na kontejneru nebude mít vliv na RU/s zřízené v Azure Cosmos DB transakčním úložišti. Transakce (čtení & zápisu) a náklady na úložiště pro analytické úložiště se účtují samostatně. Další podrobnosti najdete v tématu [ceny za Azure Cosmos DB analytické úložiště](analytical-store-introduction.md#analytical-store-pricing) .

### <a name="can-i-restrict-network-access-to-azure-cosmos-db-analytical-store"></a>Můžu omezit přístup k síti na Azure Cosmos DB analytické úložiště?

Ano, můžete nakonfigurovat [spravovaný privátní koncový bod](analytical-store-private-endpoints.md) a omezit přístup k síti z analytického úložiště do Azure synapse spravované virtuální sítě. Spravované soukromé koncové body vytvoří privátní odkaz na analytické úložiště. 

Do stejného Azure Cosmos DB účtu v pracovním prostoru Azure synapse Analytics můžete přidat jak transakční, tak i i analytické úložiště. Pokud chcete pouze spouštět analytické dotazy, můžete v pracovním prostoru synapse Analytics povolit pouze analytický privátní koncový bod.

### <a name="can-i-use-customer-managed-keys-with-the-azure-cosmos-db-analytical-store"></a>Můžu pomocí Azure Cosmos DB analytického úložiště použít klíče spravované zákazníky?

Data můžete v různých transakčních a analytických úložištích hladce snadno šifrovat pomocí stejných klíčů spravovaných zákazníkem automaticky a transparentním způsobem. Pokud chcete používat klíče spravované zákazníkem v analytickém úložišti, musíte ve svých zásadách Azure Key Vault přístupu použít spravovanou identitu vaší Azure Cosmos DB účtu. To je popsáno [zde](how-to-setup-cmk.md#using-managed-identity). Ve svém účtu byste pak měli být schopni povolit analytické úložiště.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Projeví se operace odstranění a aktualizace v transakčním úložišti v analytickém úložišti?

Ano, odstranění a aktualizace dat v transakčním úložišti se projeví v analytickém úložišti. Hodnotu TTL (Time to Live) na kontejneru můžete nakonfigurovat tak, aby zahrnovala historická data, aby analytické úložiště zachovalo všechny verze položek, které odpovídají kritériím analytického TTL. Další podrobnosti najdete v tématu [Přehled analytického TTL](analytical-store-introduction.md#analytical-ttl) .

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Můžu se připojit k analytickému úložišti z jiných analytických strojů než Azure synapse Analytics?

K analytickému úložišti můžete přistupovat a spouštět pro něj dotazy pouze pomocí různých modulů runtime, které poskytuje Azure Synapse Analytics. K dotazování a analýzám analytického úložiště je možné použít:

* Synapse Spark s plnou podporou pro Scala, Python, SparkSQL a C#. Synapse Spark je zcela zásadní pro scénáře přípravy dat a datových věd.
* Fond SQL bez serveru s jazykem T-SQL a podporou pro známé nástroje BI (například Power BI Premium atd.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Můžu se připojit k analytickému úložišti z synapse SQL zřízené?

V tuto chvíli není k dispozici úložiště analýzy z synapse SQL zřízené.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Můžu do analytického úložiště zapsat zpátky výsledky agregace dotazů z synapse?

Analytické úložiště je úložiště jen pro čtení v kontejneru Azure Cosmos DB. Proto nemůžete přímo zapisovat výsledky agregace do analytického úložiště, ale můžete je zapsat do Azure Cosmos DB transakčního úložiště jiného kontejneru, který lze později využít jako obsluhu vrstvy.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>Je replikace automatické synchronizace z transakčního úložiště do analytického úložiště asynchronní nebo synchronní a jaká je latence?

Latence automatické synchronizace je obvykle do 2 minut. V případě sdílené databáze propustnosti s velkým počtem kontejnerů může být latence automatické synchronizace v jednotlivých kontejnerech vyšší a trvat až 5 minut. Rádi bychom se dozvěděli, jak tato latence vyhovuje vašim scénářům. V takovém případě se prosím obraťte na [tým Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Existují nějaké scénáře, kdy položky z transakčního úložiště nejsou automaticky šířeny do analytického úložiště?

Pokud konkrétní položky ve vašem kontejneru porušují schéma, které je [pro analýzu](analytical-store-introduction.md#analytical-schema)správné, nebudou součástí analytického úložiště. Pokud máte scénáře zablokované ve schématu pro analýzu, pošlete [Azure Cosmos DB týmu](mailto:cosmosdbsynapselink@microsoft.com) , aby vám pomohli.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Můžu rozdělit data v analytickém úložišti odlišně z transakčního úložiště?

Data v analytickém úložišti se dělí na základě horizontálního dělení horizontálních oddílů v transakčním úložišti. V současné době není možné pro analytické úložiště zvolit jinou strategii dělení.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Je možné přizpůsobit nebo přepsat způsob transformace transakčních dat na sloupcový formát v analytickém úložišti?

Datové položky aktuálně nemůžete transformovat, když jsou automaticky šířeny z transakčního úložiště do analytického úložiště. Pokud máte scénáře blokované tímto omezením, odešlete e-mailem [Azure Cosmos DB týmu](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="is-analytical-store-supported-by-terraform"></a>Podporuje Terraform analytické úložiště?

V současné době Terraform nepodporuje kontejnery analytického úložiště. Další informace najdete v [problémech s Terraformem na GitHubu](https://github.com/hashicorp/terraform/issues).

## <a name="analytical-time-to-live-ttl"></a>Doba analýzy – Live (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>Je hodnota TTL pro analytická data podporovaná na úrovni kontejneru a položky?

V současné době je možné nakonfigurovat hodnotu TTL pro analytická data pouze na úrovni kontejneru a nastavení analytické hodnoty TTL na úrovni položky se nepodporuje.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Můžu po nastavení analytické hodnoty TTL na úrovni kontejneru u Azure Cosmos DB kontejneru změnit na jinou hodnotu později?

Ano, analytická hodnota TTL se dá aktualizovat na jakoukoli platnou hodnotu. Další podrobnosti o analýze TTL najdete v článku [Analytická hodnota TTL](analytical-store-introduction.md#analytical-ttl) .

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Můžu aktualizovat nebo odstranit položku z analytického úložiště poté, co byla hodnota TTL z transakčního úložiště vycházející?

Všechny transakční aktualizace a odstranění jsou zkopírovány do analytického úložiště, ale pokud byla položka vymazána z transakčního úložiště, nelze ji v analytickém úložišti aktualizovat. Další informace najdete v článku [Analytická hodnota TTL](analytical-store-introduction.md#analytical-ttl) .

## <a name="billing"></a>Fakturace

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Jaký je model fakturace pro Azure Cosmos DB odkaz na Azure synapse?

Model fakturace propojení Azure synapse zahrnuje náklady vzniklé pomocí Azure Cosmos DBho analytického úložiště a modulu runtime synapse. Další informace najdete v článcích o cenách služby [Azure Cosmos DB Analytics Store](analytical-store-introduction.md#analytical-store-pricing) a [cenách Azure synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/) .

### <a name="what-is-the-billing-impact-if-i-enable-synapse-link-in-my-azure-cosmos-db-database-account"></a>Jaký je dopad na fakturaci, když povolím synapse odkaz v účtu databáze Azure Cosmos DB?

Žádné Bude se vám účtovat jenom když vytvoříte kontejner analytického úložiště s povolenými daty a začnete načítat data.


## <a name="security"></a>Zabezpečení

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Jaké jsou způsoby ověřování pomocí analytického úložiště?

Ověřování pomocí analytického úložiště je stejné jako transakční úložiště. Pro danou databázi se můžete ověřit pomocí primárního klíče nebo klíče jen pro čtení. V Azure synapse studiu můžete využít propojenou službu k tomu, aby se zabránilo vkládání klíčů Azure Cosmos DB do notebooků Spark. Přístup k této propojené službě je k dispozici všem uživatelům, kteří mají přístup k pracovnímu prostoru.

Pokud používáte synapse fondy SQL serverů bez serveru, můžete dotazovat se na Azure Cosmos DB analytické úložiště pomocí předběžného Vytváření přihlašovacích údajů SQL, který ukládá klíče účtu a odkazuje na ně ve funkci OPENROWSET. Další informace najdete v článku [dotazování s fondem SQL bez serveru v článku s odkazem na Azure synapse](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) .

## <a name="synapse-run-times"></a>Synapse doby běhu

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Jaké jsou aktuálně podporované synapse doby běhu pro přístup k Azure Cosmos DB analytickému obchodu?

|Modul runtime Azure synapse |Aktuální podpora |
|---------|---------|
|Fondy Azure synapse Spark | Čtení, zápis (prostřednictvím transakčního úložiště), tabulka, dočasné zobrazení |
|Synapse fond SQL bez serveru Azure    | Číst, zobrazit |
|Zřízený SQL Azure synapse   |  Není k dispozici |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-serverless-sql-pool-tables-the-same-way-they-do-with-azure-data-lake"></a>Synchronizují se moje tabulky Azure synapse Spark s tabulkami fondu SQL bez serveru Azure synapse stejným způsobem jako s Azure Data Lake?

Tato funkce v současné době není k dispozici.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Můžu se strukturované streamování Sparku z analytického úložiště udělat?

V současné době je podpora strukturovaného streamování Sparku pro Azure Cosmos DB implementovaná pomocí funkce změny kanálu transakčního úložiště a zatím není podporovaná z analytického úložiště.

### <a name="is-streaming-supported"></a>Podporuje streamování?

Streamování dat z analytického úložiště nepodporujeme.

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Jak zjistím, že je v Azure synapse studiu připojená k kontejneru Azure Cosmos DB s povoleným úložištěm analýz?

Azure Cosmos DB kontejner povolený pomocí analytického úložiště má následující ikonu:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Azure Cosmos DB kontejner povolený pomocí analytického úložiště – ikona":::

Kontejner transakčního úložiště bude reprezentován následující ikonou:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Azure Cosmos DB kontejner povolen s transakčním úložištěm – ikona":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Jak předáte Azure Cosmos DB přihlašovací údaje ze služby Azure synapse Studio?

Přihlašovací údaje jsou v současné době Azure Cosmos DB předány při vytváření propojené služby uživatelem, který má přístup k databázím Azure Cosmos DB. Přístup k tomuto úložišti je dostupný ostatním uživatelům, kteří mají přístup k pracovnímu prostoru.

## <a name="next-steps"></a>Další kroky

* Další informace o [výhodách Azure synapse Link](synapse-link.md#synapse-link-benefits)

* Přečtěte si o [integraci mezi synapse a propojením Azure a Azure Cosmos DB](synapse-link.md#synapse-link-integration).
