---
title: Nejčastější dotazy k Azure Synapse Linku pro Azure Cosmos DB
description: Získejte odpovědi na nejčastější dotazy týkající se synapse odkazů pro Azure Cosmos DB v oblastech, jako je fakturace, analytické úložiště, zabezpečení, doba provozu v analytickém úložišti.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 34bc8e3775c2334b0cdbb22c8cad8f8d1dd5c732
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568602"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Nejčastější dotazy k Azure Synapse Linku pro Azure Cosmos DB

Odkaz na Azure synapse pro Azure Cosmos DB vytvoří těsnou integraci mezi Azure Cosmos DB a analýzou Azure synapse. Umožňuje zákazníkům provozovat analýzy téměř v reálném čase prostřednictvím jejich provozních dat s plnou izolací výkonu ze svých transakčních úloh a bez kanálu ETL. Tento článek odpovídá na nejčastější dotazy k Synapse Linku pro Azure Cosmos DB.

## <a name="general-faq"></a>Obecné nejčastější dotazy

### <a name="is-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Podporuje se propojení synapse pro všechna Azure Cosmos DB rozhraní API?

V rámci verze Public Preview se odkaz synapse podporuje pro rozhraní API Azure Cosmos DB SQL (Core) a pro rozhraní Azure Cosmos DB API pro MongoDB. 

### <a name="is-synapse-link-supported-for-multi-region-azure-cosmos-accounts"></a>Podporuje se propojení synapse pro účty Azure Cosmos ve více oblastech?

Ano, pro účty Azure Cosmos ve více oblastech je data uložená v analytickém úložišti také globálně distribuována. Bez ohledu na to, jestli se využívá jedna nebo více oblastí zápisu, se analytické dotazy prováděné z Azure Synapse Analytics můžou obsluhovat z nejbližší místní oblasti.

Při plánování konfigurace účtu Azure Cosmos s více oblastmi s podporou analytického úložiště se doporučuje mít v době vytváření účtu přidané všechny nezbytné oblasti.

### <a name="can-i-choose-to-enable-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Můžu povolit synapse odkaz jenom na určitou oblast a ne na všechny oblasti v nastavení účtu s více oblastmi?

Pokud je v rámci verze Preview povolený odkaz synapse pro účet ve více oblastech, vytvoří se analytické úložiště ve všech oblastech. Podkladová data jsou optimalizována pro propustnost a transakční konzistenci v transakčním úložišti.

### <a name="is-backup-and-restore-supported-for-synapse-link-enabled-accounts"></a>Podporuje se zálohování a obnovení pro účty s povoleným odkazem synapse?

V Preview se pro účty databáze s povoleným odkazem synapse nepodporuje zálohování a obnovování kontejnerů. Pokud máte produkční úlohy, které vyžadují funkci zálohování a obnovení, nedoporučujeme na těchto databázových účtech povolit synapse odkaz. 

### <a name="can-i-disable-the-synapse-link-feature-for-my-azure-cosmos-account"></a>Můžu zakázat funkci synapse Link pro svůj účet Azure Cosmos?

V současné době není možné po povolení funkce Synapse Link na úrovni účtu tuto funkci zakázat.  Pokud chcete funkci vypnout, musíte odstranit a znovu vytvořit nový účet Azure Cosmos.

V případě, že na úrovni účtu není povolená možnost propojení synapse, ale neexistují žádné kontejnery s povoleným analytickým úložištěm, nebudete **mít žádný** vliv na fakturaci.

## <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analytické úložiště

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Můžu povolit analytické úložiště u existujících kontejnerů?

V současné době se analytické úložiště dá povolit jenom pro nové kontejnery (v nových i stávajících účtech).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-containers-after-enabling-it-during-container-creation"></a>Můžu na mých kontejnerech Azure Cosmos zakázat analytické úložiště po jeho povolení při vytváření kontejnerů?

V současné době se analytické úložiště nedá zakázat na kontejneru Azure Cosmos po jeho povolení při vytváření kontejneru.

### <a name="is-analytical-store-supported-for-azure-cosmos-containers-with-autoscale-provisioned-throughput"></a>Podporuje se analytické úložiště pro kontejnery Azure Cosmos se zajištěnou propustností automatického škálování?

Ano, analytické úložiště je možné povolit u kontejnerů s zřízenou propustností automatického škálování.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Existují nějaké vliv na Azure Cosmos DB zřízené transakční úložiště ru?

Azure Cosmos DB garantuje izolaci výkonu mezi transakčními a analytickými úlohami. Povolení analytického úložiště na kontejneru nebude mít vliv na RU/s zřízené v Azure Cosmos DB transakčním úložišti. Transakce (čtení & zápisu) a náklady na úložiště pro analytické úložiště se účtují samostatně. Další podrobnosti najdete v tématu [ceny za Azure Cosmos DB analytické úložiště](analytical-store-introduction.md#analytical-store-pricing) .

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>Projeví se operace odstranění a aktualizace v transakčním úložišti v analytickém úložišti?

Ano, odstranění a aktualizace dat v transakčním úložišti se projeví v analytickém úložišti. Hodnotu TTL (Time to Live) na kontejneru můžete nakonfigurovat tak, aby zahrnovala historická data, aby analytické úložiště zachovalo všechny verze položek, které odpovídají kritériím analytického TTL. Další podrobnosti najdete v tématu [Přehled analytického TTL](analytical-store-introduction.md#analytical-ttl) .

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Můžu se připojit k analytickému úložišti z jiných analytických strojů než Azure synapse Analytics?

K analytickému úložišti můžete přistupovat a spouštět pro něj dotazy pouze pomocí různých modulů runtime, které poskytuje Azure Synapse Analytics. K dotazování a analýzám analytického úložiště je možné použít:

* Synapse Spark s plnou podporou pro Scala, Python, SparkSQL a C#. Synapse Spark je zcela zásadní pro scénáře přípravy dat a datových věd.
* SQL bez serveru s jazykem T-SQL a podporou známých nástrojů BI (například Power BI Premium atd.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Můžu se připojit k analytickému úložišti z synapse SQL zřízené?

V tuto chvíli není k dispozici úložiště analýzy z synapse SQL zřízené.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Můžu do analytického úložiště zapsat zpátky výsledky agregace dotazů z synapse?

Analytické úložiště je úložiště jen pro čtení v kontejneru Azure Cosmos. Proto nemůžete přímo zapisovat výsledky agregace do analytického úložiště, ale můžete je zapsat do Azure Cosmos DB transakčního úložiště jiného kontejneru, který lze později využít jako obsluhu vrstvy.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>Je replikace automatické synchronizace z transakčního úložiště do analytického úložiště asynchronní nebo synchronní a jaká je latence?

Latence automatické synchronizace je obvykle do 2 minut. V případě sdílené databáze propustnosti s velkým počtem kontejnerů může být latence automatické synchronizace v jednotlivých kontejnerech vyšší a trvat až 5 minut. Rádi bychom se dozvěděli, jak tato latence vyhovuje vašim scénářům. V takovém případě se prosím obraťte na [tým Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Existují nějaké scénáře, kdy položky z transakčního úložiště nejsou automaticky šířeny do analytického úložiště?

Pokud konkrétní položky ve vašem kontejneru porušují schéma, které je [pro analýzu](analytical-store-introduction.md#analytical-schema)správné, nebudou součástí analytického úložiště. Pokud máte scénáře zablokované ve schématu pro analýzu, pošlete [Azure Cosmos DB týmu](mailto:cosmosdbsynapselink@microsoft.com) , aby vám pomohli.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Můžu rozdělit data v analytickém úložišti odlišně z transakčního úložiště?

Data v analytickém úložišti se dělí na základě horizontálního dělení horizontálních oddílů v transakčním úložišti. V současné době není možné pro analytické úložiště zvolit jinou strategii dělení.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Je možné přizpůsobit nebo přepsat způsob transformace transakčních dat na sloupcový formát v analytickém úložišti?

Datové položky aktuálně nemůžete transformovat, když jsou automaticky šířeny z transakčního úložiště do analytického úložiště. Pokud máte scénáře blokované tímto omezením, odešlete e-mailem [Azure Cosmos DB týmu](mailto:cosmosdbsynapselink@microsoft.com).

## <a name="analytical-time-to-live-ttl"></a>Doba analýzy – Live (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>Je hodnota TTl pro analytická data podporovaná na úrovni kontejneru a položky?

V tuto chvíli je TTl pro analytická data možné nakonfigurovat pouze na úrovni kontejneru a neexistuje žádná podpora pro nastavení analytického TTL na úrovni položky.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Můžu po nastavení analytické hodnoty TTL na úrovni kontejneru u Azure Cosmos DB kontejneru změnit na jinou hodnotu později?

Ano, analytická hodnota TTL se dá aktualizovat na jakoukoli platnou hodnotu. Další podrobnosti o analýze TTL najdete v článku [Analytická hodnota TTL](analytical-store-introduction.md#analytical-ttl) .

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Můžu aktualizovat nebo odstranit položku z analytického úložiště poté, co byla hodnota TTL z transakčního úložiště vycházející?

Všechny transakční aktualizace a odstranění jsou zkopírovány do analytického úložiště, ale pokud byla položka vymazána z transakčního úložiště, nelze ji v analytickém úložišti aktualizovat. Další informace najdete v článku [Analytická hodnota TTL](analytical-store-introduction.md#analytical-ttl) .

## <a name="billing"></a>Fakturace

### <a name="what-is-the-billing-model-of-synapse-link-for-azure-cosmos-db"></a>Jaký je model fakturace Azure Cosmos DB odkaz na synapse?

[Azure Cosmos DB analytické úložiště](analytical-store-introduction.md) je k dispozici ve verzi Public Preview bez jakýchkoli poplatků za analytické úložiště do 30. srpna 2020. Synapse Spark a synapse SQL se účtují prostřednictvím [spotřeby služby synapse](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="security"></a>Zabezpečení

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Jaké jsou způsoby ověřování pomocí analytického úložiště?

Ověřování pomocí analytického úložiště je stejné jako transakční úložiště. Pro danou databázi se můžete ověřit pomocí primárního klíče nebo klíče jen pro čtení. V synapse studiu můžete využít propojenou službu k tomu, aby se zabránilo vkládání klíčů Azure Cosmos DB do notebooků Spark. Přístup k této propojené službě je k dispozici všem uživatelům, kteří mají přístup k pracovnímu prostoru.

## <a name="synapse-run-times"></a>Synapse doby běhu

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Jaké jsou aktuálně podporované synapse doby běhu pro přístup k Azure Cosmos DB analytickému obchodu?

|Modul runtime synapse |Aktuální podpora |
|---------|---------|
|Synapse Spark – fondy | Čtení, zápis (prostřednictvím transakčního úložiště), tabulka, dočasné zobrazení |
|Synapse SQL bez serveru    | Číst, zobrazit |
|Zřízený SQL synapse   |  Není k dispozici |

### <a name="do-my-synapse-spark-tables-sync-with-my-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>Synchronizují se moje synapse tabulky Spark s tabulkami synapse SQL bez serveru stejným způsobem jako s Azure Data Lake?

Tato funkce v současné době není k dispozici.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Můžu se strukturované streamování Sparku z analytického úložiště udělat?

V současné době je podpora strukturovaného streamování Sparku pro Azure Cosmos DB implementovaná pomocí funkce změny kanálu transakčního úložiště a zatím není podporovaná z analytického úložiště.

## <a name="synapse-studio"></a>Synapse Studio

### <a name="in-the-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Jak zjistím, jestli jsem v synapse studiu připojen k kontejneru Azure Cosmos DB s povoleným úložištěm analýz?

Azure Cosmos DB kontejner povolený pomocí analytického úložiště má následující ikonu:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Azure Cosmos DB kontejner povolený pomocí analytického úložiště – ikona":::

Kontejner transakčního úložiště bude reprezentován následující ikonou:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Azure Cosmos DB kontejner povolený pomocí analytického úložiště – ikona":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-synapse-studio"></a>Jak předat Azure Cosmos DB přihlašovací údaje z synapse studia?

Přihlašovací údaje jsou v současné době Azure Cosmos DB předány při vytváření propojené služby uživatelem, který má přístup k databázím Azure Cosmos DB. Přístup k tomuto úložišti je dostupný ostatním uživatelům, kteří mají přístup k pracovnímu prostoru.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [výhodách synapse odkazů](synapse-link.md#synapse-link-benefits) .

* Přečtěte si o [integraci mezi synapse odkazem a Azure Cosmos DB](synapse-link.md#synapse-link-integration).
