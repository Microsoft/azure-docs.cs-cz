---
title: Azure SQL Database Nejčastější dotazy k škálování
description: Odpovědi na nejčastější dotazy, které zákazníci žádají o databázi v SQL Database ve vrstvě služeb škálování na úrovni služby – často se označuje jako databáze v rámci škálování.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: be8e38d38408bd7cf11608d71035bd7cf0808b60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488860"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL Database Nejčastější dotazy k škálování
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek obsahuje odpovědi na nejčastější dotazy pro zákazníky, kteří berou v platnost Azure SQL Database úrovně služby s škálovatelným škálováním, označované stejně jako ve zbývající části tohoto nejčastějších dotazů. Tento článek popisuje scénáře, které podporuje škálování, a funkce, které jsou kompatibilní s škálovatelným škálováním.

- Tyto nejčastější dotazy jsou určené pro čtenáře, kteří mají stručný přehled o úrovni služby úrovně služeb a hledají jejich konkrétní otázky a obavy o zodpovězení.
- Tato Nejčastější dotazy nezpůsobí, že se jedná o Guidebook nebo odpovědi na dotazy týkající se používání databáze v rámci škálování. V úvodu do škálování doporučujeme, abyste se přečtěte v dokumentaci [Azure SQL Database škálování](service-tier-hyperscale.md) na více míst.

## <a name="general-questions"></a>Obecné otázky

### <a name="what-is-a-hyperscale-database"></a>Co je databáze s škálovatelným škálováním

Databáze s technologií škálování na více instancí je databáze v SQL Database ve vrstvě služeb škálování, která je založená na technologii úložiště škálovatelného škálování na více instancí. Databáze ve velkém měřítku podporuje až 100 TB dat a poskytuje vysokou propustnost a výkon a také rychlé škálování pro přizpůsobení požadavků úloh. Škálování je transparentní pro aplikaci – připojení, zpracování dotazů atd. funguje stejně jako jakákoli jiná databáze v Azure SQL Database.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Jaké typy prostředků a nákupní modely podporují škálování

Úroveň služby Vcore je k dispozici pouze pro izolované databáze pomocí nákupního modelu založeného na v Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Jak se úroveň služby škálování na úrovni služeb liší od Pro obecné účely a Pro důležité obchodní informace úrovní služeb

Úrovně služeb založené na vCore se liší v závislosti na dostupnosti databáze a typu úložiště, výkonu a maximální velikosti, jak je popsáno v následující tabulce.

| | Typ prostředku | Pro obecné účely |  Hyperškálování | Pro důležité obchodní informace |
|:---:|:---:|:---:|:---:|:---:|
| **Nejvhodnější pro** |Vše|Nabízí možnosti pro vyvážené výpočty a úložiště s vyrovnanou rozpočtem.|Většina obchodních úloh. Automatické škálování úložiště velikosti až 100 TB, rychlé vertikální a horizontální výpočetní škálování a rychlé obnovení databáze.|OLTP aplikace s vysokou mírou transakcí a nízkou latencí v/v. Nabízí nejvyšší odolnost proti chybám a rychlé převzetí služeb při selhání s využitím několika synchronně aktualizovaných replik.|
|  **Typ prostředku** ||SQL Database/spravované instance SQL | Izolovaná databáze | SQL Database/spravované instance SQL |
| **Velikost výpočetního prostředí**|SQL Database * | 1 až 80 virtuální jádra | 1 až 80 virtuální jádra * | 1 až 80 virtuální jádra |
| **Velikost výpočetního prostředí**|Spravovaná instance SQL | 8, 16, 24, 32, 40, 64, 80 virtuální jádra | Není k dispozici | 8, 16, 24, 32, 40, 64, 80 virtuální jádra |
| **Typ úložiště** | Vše |Premium Remote Storage (na instanci) | Oddělené úložiště s místní mezipamětí SSD (na instanci) | Vysoce rychlé místní SSD úložiště (na instanci) |
| **Velikost úložiště** | SQL Database *| 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| **Velikost úložiště** | Spravovaná instance SQL  | 32 GB – 8 TB | Není k dispozici | 32 GB – 4 TB |
| **IOPS** | Izolovaná databáze | 500 IOPS na vCore s maximálním počtem vstupně-výstupních operací 7000 | Škálovatelná architektura je Vícevrstvá architektura s ukládáním do mezipaměti na více úrovních. Platnost IOPS bude záviset na zatížení. | 5000 IOPS s 200 000m maximálním IOPS|
| **IOPS** | Spravovaná instance SQL | Závisí na velikosti souboru | Není k dispozici | 1375 IOPS/vCore |
|**Dostupnost**|Vše|1 replika bez škálování na více instancí, žádná místní mezipaměť | Víc replik, až 4 horizontálního navýšení kapacity, částečná místní mezipaměť | 3 repliky, 1 škálování čtení na více instancí, redundantní HA v zóně, úplné místní úložiště |
|**Zálohování**|Vše|RA-GRS, 7-35 dnů uchování (ve výchozím nastavení 7 dní)| RA-GRS, 7 dní uchovávání, konstantní doba obnovení v čase (PITR) | RA-GRS, 7-35 dnů uchování (ve výchozím nastavení 7 dní) |

\* Elastické fondy nejsou podporované v úrovni služby škálování na úrovni služeb.

### <a name="who-should-use-the-hyperscale-service-tier"></a>Kdo má používat úroveň služby pro škálování na úrovni služeb

Úroveň služby pro škálování na úrovni služeb je určená pro zákazníky, kteří mají velké místní SQL Server databáze a chtějí modernizovat své aplikace tím, že se přesunou do cloudu, nebo pro zákazníky, kteří už používají Azure SQL Database a chtějí významně rozšířit potenciál nárůstu databáze. Škálování je také určené pro zákazníky, kteří hledají vysoký výkon i vysokou škálovatelnost. S škálovatelným škálováním získáte:

- Velikost databáze až do 100 TB
- Rychlé zálohování databáze bez ohledu na velikost databáze (zálohy jsou založené na snímcích úložiště)
- Rychlá databáze se obnovuje bez ohledu na velikost databáze (obnovení probíhá ze snímků úložiště).
- Vyšší propustnost protokolu bez ohledu na velikost databáze a počet virtuální jádra
- Přečtěte si horizontální navýšení kapacity pomocí jedné nebo několika replik jen pro čtení, které se používají ke snižování zátěže pro čtení a jako aktivní pohotovostní režim.
- Rychlé škálování výpočetních prostředků v konstantním čase, aby bylo lépe náročné na to, aby vyhovovalo velkým úlohám a pak horizontální navýšení kapacity v konstantním čase. To je podobné jako vertikální navýšení a snížení kapacity mezi P6 a P11, například mnohem rychleji, protože se nejedná o velikost datové operace.

### <a name="what-regions-currently-support-hyperscale"></a>Jaké oblasti v současné době podporují škálování

Úroveň služby pro škálování na úrovni služeb je aktuálně dostupná v oblastech uvedených v části [přehled Azure SQL Databaseho škálování](service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-server"></a>Můžu vytvořit více databází v rámci škálování na jeden server.

Ano. Další informace a omezení počtu databází v rámci škálování na jeden server najdete v tématu [SQL Database omezení prostředků pro jednu databázi a databáze ve fondu na serveru](resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Jaké jsou výkonnostní charakteristiky databáze v rámci škálování

Architektura v rámci škálování poskytuje vysoký výkon a propustnost při podpoře rozsáhlých velikostí databází.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Jaká je škálovatelnost databáze v rámci škálování

Škálovatelné škálování nabízí rychlou škálovatelnost na základě požadavků na zatížení.

- **Horizontální navýšení nebo snížení kapacity**

  S škálovatelným škálováním můžete škálovat primární výpočetní velikost z hlediska prostředků, jako je CPU a paměť, a pak v konstantním čase škálovat. Vzhledem k tomu, že je úložiště sdílené, nefunguje horizontální navýšení kapacity a zmenšování velikosti datové operace.  
- **Horizontální navýšení nebo zmenšení kapacity**

  S vlastním škálováním získáte také možnost zřídit jednu nebo více dalších výpočetních replik, které můžete použít k obsluze svých žádostí o čtení. To znamená, že tyto další výpočetní repliky můžete použít jako repliky jen pro čtení, abyste mohli přesměrovat úlohy čtení z primárního výpočetního prostředí. Kromě jen pro čtení tyto repliky slouží také jako pohotovostní úspora v případě převzetí služeb při selhání z primární služby.

  Zřizování každé z těchto dalších výpočetních replik se dá provést v konstantním čase a je to online operace. K těmto dalším výpočetním replikám, které jsou jen pro čtení, se můžete připojit nastavením `ApplicationIntent` argumentu v připojovacím řetězci na `ReadOnly` . Všechna připojení k `ReadOnly` záměru aplikace jsou automaticky směrována na jednu z dalších výpočetních replik jen pro čtení.

## <a name="deep-dive-questions"></a>Podrobné dotazy k podrobně

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-server"></a>Můžu v jednom serveru kombinovat škálovatelné a izolované databáze.

Ano, můžete.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Vyžaduje se, aby se změnila velikost aplikačního programovacího modelu

Ne, programovací model vaší aplikace zůstane tak, jak je. Připojovací řetězec použijete obvyklým způsobem a dalším běžným způsobem, jak pracovat s databází s vaším škálováním.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Jaká úroveň izolace transakce je ve výchozím nastavení v databázi s měřítkem.

U primární repliky je výchozí úroveň izolace transakce RCSI (čtení potvrzené izolace snímku). Na sekundárních replikách se škálováním na více instancí se jako výchozí úroveň izolace používá snímek.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Můžu přinášet místní nebo IaaS SQL Server licenci do škálování

Ano, [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) je k dispozici pro škálování. Každý SQL Server Standard Core se může namapovat na 1 virtuální jádra s měřítkem. Každý SQL Server Enterprise Core se může namapovat na 4 škálovatelné virtuální jádra. Pro sekundární repliky nepotřebujete licenci SQL. Zvýhodněné hybridní využití Azureová cena se automaticky použije pro čtení (sekundární) repliky se škálováním na více instancí.

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Jaký druh zatížení je škálovatelné a navržené

Škálování na úrovni Standard podporuje všechny SQL Server úlohy, ale je primárně optimalizovaná pro OLTP. Můžete přinést i hybridní úlohy (HTAP) a analytické (datové tržiště).

### <a name="how-can-i-choose-between-azure-synapse-analytics-and-azure-sql-database-hyperscale"></a>Jak si můžu vybrat mezi Azure synapse Analytics a Azure SQL Database škálováním

Pokud aktuálně spouštíte interaktivní analytické dotazy pomocí SQL Server jako datový sklad, škálovatelná možnost je skvělým způsobem, protože můžete hostovat malé a střední velikosti datových skladů (například o více TB až 100 TB) za nižší náklady a můžete migrovat své SQL Server úlohy datového skladu do škálování s minimálními změnami kódu T-SQL.

Pokud pracujete s analýzou dat ve velkém měřítku se složitými dotazy a nižšími sazbami pro ingestování vyšší než 100 MB/s nebo s využitím paralelního datového skladu (PDW), Teradata nebo dalších hromadně paralelního zpracování (MPP), může to být nejlepší volbou Azure synapse Analytics (dříve SQL Data Warehouse).
  
## <a name="hyperscale-compute-questions"></a>Dotazy na výpočetní výkon s škálovatelným škálováním

### <a name="can-i-pause-my-compute-at-any-time"></a>Můžu kdykoli pozastavit výpočetní výkon

V tuto chvíli ale můžete škálovat výpočetní výkon a počet replik dolů a snížit tak náklady v době mimo špičku.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Můžu zřídit výpočetní repliku s dodatečnou pamětí RAM pro úlohy náročné na paměť

Ne. Pokud chcete získat větší velikost paměti RAM, musíte upgradovat na vyšší výpočetní kapacitu. Další informace najdete v tématu [velikosti úložiště a výpočetních](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)prostředků ve škálování na úrovni.

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Můžu zřídit několik výpočetních replik různých velikostí.

Ne.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Kolik replik pro čtení na více instancí se podporuje

Databáze s škálovatelným škálováním jsou ve výchozím nastavení vytvářeny s jednou replikou s možností čtení na více instancí (dvě repliky včetně primárních). Počet replik, které jsou jen pro čtení, můžete škálovat mezi 0 a 4 pomocí [Azure Portal](https://portal.azure.com) nebo [REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Pro zajištění vysoké dostupnosti potřebuji zřídit další výpočetní repliky.

V databázích ve velkém měřítku se odolnost dat poskytuje na úrovni úložiště. Pro zajištění odolnosti potřebujete jenom jednu repliku. Pokud je výpočetní replika vypnutá, vytvoří se automaticky nová replika bez ztráty dat.

Pokud ale existuje jenom jedna replika, může trvat nějakou dobu, než se po převzetí služeb při selhání sestaví místní mezipaměť v nové replice. Během fáze opětovného sestavení mezipaměti databáze načítá data přímo ze stránek stránky, což má za následek vyšší latenci úložiště a snížený výkon dotazů.

Pro klíčové aplikace, které vyžadují vysokou dostupnost s minimálním dopadem na převzetí služeb při selhání, byste měli zřídit aspoň dvě výpočetní repliky, včetně primární výpočetní repliky. Toto je výchozí konfigurace. Tímto způsobem je k dispozici replika v pohotovostním stavu, která slouží jako cíl převzetí služeb při selhání.

## <a name="data-size-and-storage-questions"></a>Otázky velikosti a úložiště dat

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Jaká je maximální velikost databáze podporovaná pomocí škálování

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Jaká je velikost transakčního protokolu s měřítkem

Transakční protokol s měřítkem je prakticky nekonečný. Nemusíte se starat o nedostatek místa v protokolu v systému, který má vysokou propustnost protokolu. Nicméně je možné omezit rychlost generování protokolu pro nepřetržitě agresivní úlohy. Maximální počet trvalých generování protokolů je 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Roste můj `tempdb` Rozsah mého databáze

Vaše `tempdb` databáze je umístěná v místním ÚLOŽIŠTI SSD a je úměrná velikosti pro výpočetní velikost, kterou zřizujete. Vaše aplikace `tempdb` je optimalizovaná tak, aby poskytovala maximální výkonnostní výhody. `tempdb` velikost není konfigurovatelná a je spravovaná pro vás.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Roste velikost databáze automaticky nebo je nutné spravovat velikost datových souborů

Velikost databáze se automaticky zvětšuje při vkládání nebo přijímání dalších dat.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Jaká je nejmenší velikost databáze, kterou škálovatelné rozšíření podporuje nebo začíná na

40 GB. Vytvoří se databáze s škálováním na více verzí s počáteční velikostí 10 GB. Pak začíná růst o 10 GB každých 10 minut, dokud nedosáhne velikosti 40 GB. Každé z těchto 10 GB Chucks se přiděluje na jiný server, aby se zajistilo více vstupně-výstupních operací a vyšších paralelních vstupně-výstupních operací. Vzhledem k této optimalizaci, i když zvolíte počáteční velikost databáze menší než 40 GB, bude databáze automaticky zvětšena na nejméně 40 GB.

### <a name="in-what-increments-does-my-database-size-grow"></a>V jakých přírůstcích roste velikost databáze

Každý datový soubor roste o 10 GB. Současně může růst více datových souborů.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>Je úložiště v místním nebo vzdáleném měřítku

V měřítku jsou datové soubory uložené ve službě Azure Storage úrovně Standard. Data jsou plně ukládána do mezipaměti v místním úložišti SSD, na stránkových serverech, které jsou blízko výpočetních replik. Kromě toho mají výpočetní repliky mezipaměti dat na místních discích SSD a v paměti, aby se snížila frekvence načítání dat ze serverů vzdálené stránky.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Můžu spravovat nebo definovat soubory nebo skupiny souborů s měřítkem

Ne. Datové soubory se přidají automaticky. Běžné důvody pro vytváření dalších skupin souborů se nevztahují na architekturu úložiště s škálovatelným škálováním.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Můžu zřídit pevný limit růstu dat pro moji databázi

Ne.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Jak jsou datové soubory rozloženy s měřítkem

Datové soubory jsou ovládány serverovými servery, na kterých je jeden datový soubor s jedním stránkovým serverem. Jak roste velikost dat, přidají se datové soubory a přidružené stránkové servery.

### <a name="is-database-shrink-supported"></a>Je podporováno zmenšení databáze

Ne.

### <a name="is-data-compression-supported"></a>Je podporována komprese dat

Ano, včetně řádků, stránky a komprese columnstore.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Pokud mám obrovský stůl, data z tabulky se rozšíří do několika datových souborů.

Ano. Datové stránky přidružené k dané tabulce mohou končit více datovými soubory, které jsou součástí stejné skupiny souborů. SQL Server používá [strategii proporcionálního plnění](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) k distribuci dat nad datovými soubory.

## <a name="data-migration-questions"></a>Otázky k migraci dat

### <a name="can-i-move-my-existing-databases-in-azure-sql-database-to-the-hyperscale-service-tier"></a>Můžu přesunout moje existující databáze v Azure SQL Database do vrstvy služeb s osobním škálováním

Ano. Existující databáze můžete přesunout v Azure SQL Database do škálování. Toto je jednosměrná migrace. Databáze nemůžete přesouvat z velkého měřítka do jiné úrovně služeb. Pro zkoušku konceptu (POCs) doporučujeme vytvořit kopii databáze a migrovat kopii do škálování. 

Čas potřebný k přesunutí existující databáze do škálování se skládá z doby kopírování dat a času pro přehrání změn provedených ve zdrojové databázi při kopírování dat. Doba kopírování dat je úměrná velikosti dat. Čas k přehrání změn bude kratší, pokud se přesun provede během období s nízkou aktivitou zápisu.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Můžu přesunout databáze v osobním měřítku na jiné úrovně služeb

Ne. V tuto chvíli nemůžete přesunout databázi v rámci škálování na jinou úroveň služby.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Po migraci na úroveň služby s škálováním na úrovni služeb ztratíme žádné funkce ani možnosti

Ano. Některé funkce Azure SQL Database ještě nejsou v měřítku podporovány, mimo jiné i pro dlouhodobé uchovávání záloh. Po migraci databází do škálovatelného prostředí tyto funkce přestanou fungovat.  Očekáváme, že tato omezení budou dočasná.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Můžu přesunout místní SQL Server databázi nebo moji databázi SQL Server v cloudovém virtuálním počítači do škálování

Ano. Můžete použít všechny existující technologie migrace k migraci do škálování, včetně transakční replikace a dalších technologií pro přesun dat (hromadné kopírování, Azure Data Factory, Azure Databricks, SSIS). Viz také [Azure Database Migration Service](../../dms/dms-overview.md), který podporuje mnoho scénářů migrace.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Co má výpadek při migraci z místního prostředí nebo virtuálního počítače do škálování a jak ho můžu minimalizovat

Výpadek migrace do škálování na velká je stejný jako výpadek při migraci databází do jiných Azure SQL Database úrovní služeb. [Transakční replikaci](replication-to-sql-database.md#data-migration-scenario
) můžete použít k minimalizaci výpadku výpadku databáze o velikosti až několika TB. U velmi rozsáhlých databází (10 + TB) můžete zvážit migraci dat pomocí ADF, Sparku nebo jiných technologií pro přesun dat.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Jak dlouho by to vyžadovalo, aby se do velkého rozsahu přihlédly X objem dat

Škálovatelné škálování je schopné spotřebovávat 100 MB/s nových nebo změněných dat, ale čas potřebný k přesunu dat do databází v Azure SQL Database taky ovlivnila dostupná propustnost sítě, rychlost čtení zdroje a cíl na úrovni služby cílové databáze.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-azure-synapse-analytics"></a>Můžu číst data z úložiště objektů BLOB a rychle je načíst (jako je základem ve službě Azure synapse Analytics).

Klientská aplikace může číst data z Azure Storage a načíst data do databáze v rámci škálování (stejně jako u jakékoli jiné databáze v Azure SQL Database). V Azure SQL Database se v tuto chvíli nepodporuje základ. Jako alternativu k zajištění rychlého načtení můžete použít [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)nebo v Azure Databricks použít úlohu Sparku v [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) s [konektorem Spark pro SQL](spark-connector.md). Konektor Spark pro SQL podporuje hromadné vložení.

Je také možné hromadně číst data z úložiště objektů BLOB v Azure pomocí BULK INSERT nebo OPENROWSET: [Příklady hromadného přístupu k datům v azure BLOB Storage](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

Jednoduché obnovení nebo model hromadného protokolování není v měřítku podporován. Pro zajištění vysoké dostupnosti a obnovení k určitému bodu v čase je vyžadován úplný model obnovení. Architektura protokolu s škálovatelným škálováním ale nabízí lepší rychlost přijímání dat v porovnání s jinými Azure SQL Database úrovněmi služeb.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>Umožňuje škálování na více uzlů pro paralelní ingestování velkých objemů dat.

Ne. Škálovatelná architektura je symetrická architektura pro více procesorů (SMP) a nejedná se o výkonné paralelní zpracování (MPP) nebo architekturu s více hlavními servery. Pro horizontální navýšení kapacity úloh jen pro čtení můžete vytvořit jenom několik replik.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Jaká je nejstarší verze SQL Server podporovaná pro migraci do škálování

SQL Server 2005. Další informace najdete v tématu [migrace do izolované databáze nebo do databáze ve fondu](migrate-to-database-from-sql-server.md#migrate-to-a-single-database-or-a-pooled-database). Problémy s kompatibilitou najdete v tématu [řešení problémů s kompatibilitou migrace databáze](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Podporuje škálovatelná migrace z jiných zdrojů dat, jako jsou Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 a další databázové platformy.

Ano. [Azure Database Migration Service](../../dms/dms-overview.md) podporuje řadu migračních scénářů.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Otázky pro provozní kontinuitu a zotavení po havárii

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Jaké SLA jsou k dispozici pro databázi s škálovatelným škálováním

Azure SQL Database najdete v tématu [SLA](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Další sekundární výpočetní repliky zvyšují dostupnost, až 99,99%, pro databázi se dvěma nebo více sekundárními výpočetními replikami.

### <a name="are-the-database-backups-managed-for-me-by-azure-sql-database"></a>Jsou zálohy databáze spravované pro mě Azure SQL Database

Ano.

### <a name="how-often-are-the-database-backups-taken"></a>Jak často jsou zálohy databáze podniknuty

Nejsou k dispozici žádné tradiční zálohy úplného, rozdílového a protokolu pro databáze s škálovatelnými škálováními. Místo toho existují běžné snímky úložiště datových souborů. Generovaný protokol se jednoduše uchová tak, jak je pro nakonfigurovanou dobu uchování, což umožňuje obnovení do libovolného bodu v čase v rámci doby uchování.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Podporuje obnovení k bodu v čase.

Ano.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Jaký je cíl bodu obnovení (RPO)/Recovery Time (RTO) pro obnovení databáze v měřítku

CÍL bodu obnovení je 0 min. Většina operací obnovení se dokončila během 60 minut bez ohledu na velikost databáze. Čas obnovení může být delší pro větší databáze a v případě, že databáze znamenala významné aktivity zápisu před a až do bodu obnovení v čase.

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Ovlivňuje zálohování databáze výpočetní výkon u primárních nebo sekundárních replik

Ne. Zálohy spravuje subsystém úložiště a využívají snímky úložiště. Neovlivňují úlohy uživatelů.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Je možné provést geografickou obnovu s databází s měřítkem

Ano. Geografické obnovení je plně podporované. Na rozdíl od obnovení k určitému bodu v čase vyžaduje geografické obnovení operaci s velikostí dat. Datové soubory jsou kopírovány paralelně, takže doba trvání této operace závisí primárně na velikosti největšího souboru v databázi, nikoli na celkové velikosti databáze. Pokud se databáze obnoví v oblasti Azure, která se [spáruje](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) s oblastí zdrojové databáze, bude čas geografické obnovy výrazně kratší.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Můžu nastavit geografickou replikaci s databází s měřítkem

V tuto chvíli to není možné.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Můžu vytvořit zálohu databáze a obnovit ji na místním serveru nebo na SQL Server na VIRTUÁLNÍm počítači.

Ne. Formát úložiště pro databáze s škálovatelným škálováním se liší od jakékoli vydané verze SQL Server a Vy neřídíte zálohy nebo k nim nemáte přístup. Pokud chcete získat data z databáze s technologií škálování na více instancí, můžete extrahovat data pomocí technologií pro přesun dat, tj. Azure Data Factory, Azure Databricks, SSIS atd.

## <a name="cross-feature-questions"></a>Otázky pro různé funkce

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Po migraci na úroveň služby s škálováním na úrovni služeb ztratíme žádné funkce ani možnosti

Ano. Některé funkce Azure SQL Database nejsou podporované v měřítku, mimo jiné i pro dlouhodobé uchovávání záloh. Po migraci databází do škálovatelného prostředí tyto funkce přestanou fungovat.

### <a name="will-polybase-work-with-hyperscale"></a>Bude základní práce s měřítkem

Ne. V Azure SQL Database není podporován základ.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Podporuje škálování pro R a Python

V tuto chvíli to není možné.

### <a name="are-compute-nodes-containerized"></a>Jsou výpočetní uzly kontejnery

Ne. Procesy s škálovatelným škálováním běží na [Service Fabricch](https://azure.microsoft.com/services/service-fabric/) uzlech (virtuálních počítačích), ne v kontejnerech.

## <a name="performance-questions"></a>Otázky týkající se výkonu

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Kolik propustnosti zápisu je možné nabízet v databázi s velkým škálováním

Hodnota propustnosti transakčního protokolu je nastavená na 100 MB/s pro libovolnou velikost výpočetního měřítka. Možnost dosažení této míry závisí na několika faktorech, mimo jiné typ úlohy, konfiguraci klienta a dostatek výpočetní kapacity na primární replice COMPUTE pro vytváření protokolů v této sazbě.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Kolik vstupně-výstupních operací se získá na největším výpočetním prostředí

Latence a vstupně-výstupní operace se budou lišit v závislosti na vzorech úloh. Pokud se přistupovaná data ukládají do mezipaměti ve výpočetní replice, zobrazí se podobné vstupně-výstupní operace jako s místními jednotkami SSD.

### <a name="does-my-throughput-get-affected-by-backups"></a>Bude mít tato propustnost vliv na zálohy

Ne. Výpočetní prostředky jsou oddělené od vrstvy úložiště. Tím se eliminuje dopad na výkon zálohování.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Získá tato propustnost při zřizování dalších výpočetních replik.

Protože je úložiště sdílené a mezi primárními a sekundárními výpočetními replikami nedochází k přímé fyzické replikaci, propustnost v primární replice nebude přímo ovlivněna přidáním sekundárních replik. Můžeme ale omezit nepřetržitě agresivní úlohy při psaní na primárním počítači, aby se protokol mohl použít na sekundárních replikách a stránkovacích serverech, aby se zabránilo špatnému výkonu čtení sekundárních replik.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Návody diagnostikovat a řešit problémy s výkonem v databázi s škálovatelným škálováním

U většiny problémů s výkonem, zejména u těch, které nejsou rootem v části výkon úložiště, platí běžné postupy diagnostiky SQL a odstraňování potíží. Diagnostiku úložiště pro konkrétní škálování na více procesorech najdete v tématu [Diagnostika řešení potíží s výkonným škálováním v jazyce SQL](hyperscale-performance-diagnostics.md)

## <a name="scalability-questions"></a>Otázky týkající se škálovatelnosti

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Jak dlouho by vyžadovalo horizontální navýšení a snížení kapacity výpočetní repliky

Škálování výpočtů nahoru nebo dolů obvykle trvá až 2 minuty bez ohledu na velikost dat.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Je moje databáze v režimu offline, zatímco probíhá operace škálování/snížení kapacity

Ne. Horizontální navýšení a snížení kapacity bude online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Při probíhajících operacích škálování očekávat přerušení připojení

Pokud dojde k převzetí služeb při selhání na konci operace škálování, nasměrujte nebo zmenšete výsledky v existujících připojeních, která se vynechává. Přidání sekundárních replik nevede k poklesu připojení.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>Slouží k horizontálnímu navýšení a snížení kapacity výpočetních replik a operace aktivované koncovým uživatelem.

Koncový uživatel. Nepoužívá se automaticky.  

### <a name="does-the-size-of-my-tempdb-database-and-rbpex-cache-also-grow-as-the-compute-is-scaled-up"></a>`tempdb`Roste velikost databáze a mezipaměti RBPEX i v případě, že se výpočetní výkon zmenší

Ano. `tempdb`Velikost databáze a [mezipaměti RBPEX](service-tier-hyperscale.md#distributed-functions-architecture) na výpočetních uzlech se budou automaticky škálovat, protože se zvýší počet jader.

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Můžu zřídit víc primárních výpočetních replik, jako je třeba vícenásobný hlavní systém, kde víc primárních výpočetních hlav může zvýšit úroveň souběžnosti.

Ne. Pouze primární replika COMPUTE přijímá požadavky na čtení a zápis. Sekundární výpočetní repliky akceptují pouze požadavky jen pro čtení.

## <a name="read-scale-out-questions"></a>Přečíst otázky ke škálování na více instancí

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Kolik sekundárních výpočetních replik se dá zřídit

Ve výchozím nastavení vytvoříme jednu sekundární repliku pro databáze s škálovatelnými škálováními. Pokud chcete upravit počet replik, můžete to udělat pomocí [Azure Portal](https://portal.azure.com) nebo [REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Návody se připojit k těmto sekundárním výpočetním replikám

K těmto dalším výpočetním replikám, které jsou jen pro čtení, se můžete připojit nastavením `ApplicationIntent` argumentu v připojovacím řetězci na `ReadOnly` . Všechna připojení označená pomocí `ReadOnly` jsou automaticky směrována na jednu z dalších výpočetních replik jen pro čtení. Podrobnosti najdete v tématu [použití replik jen pro čtení k převedení úloh dotazů jen pro čtení](read-scale-out.md).

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Návody ověřit, jestli se úspěšně připojili k sekundární výpočetní replice pomocí SSMS nebo jiných klientských nástrojů?

Můžete spustit následující dotaz T-SQL: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')` .
Výsledkem je `READ_ONLY` , že jste připojeni k sekundární replice jen pro čtení, a `READ_WRITE` Pokud jste připojeni k primární replice. Všimněte si, že kontext databáze musí být nastaven na název databáze škálování, nikoli na `master` databázi.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Můžu vytvořit vyhrazený koncový bod pro repliku s horizontálním načtením kapacity

Ne. Ke čtení replik na více instancí se můžete připojit pouze zadáním `ApplicationIntent=ReadOnly` .

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Vyrovnává inteligentní vyrovnávání zatížení úlohy čtení.

Ne. Nové připojení s záměrem jen pro čtení se přesměruje na libovolnou repliku s možností čtení na více instancí.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Můžu škálovat sekundární repliky výpočetních dat nezávisle na primární replice.

Ne. Sekundární replika COMPUTE se používá také jako cíle převzetí služeb při selhání s vysokou dostupností, takže musí mít stejnou konfiguraci jako primární, aby poskytovala očekávaný výkon po převzetí služeb při selhání.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Získám různou `tempdb` velikost pro primární výpočetní prostředky a další sekundární výpočetní repliky

Ne. Vaše `tempdb` databáze je nakonfigurovaná na základě zřizování velikosti služby COMPUTE, sekundární výpočetní repliky mají stejnou velikost jako primární výpočetní prostředí.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Můžu přidat indexy a zobrazení na mých sekundárních replikách COMPUTE

Ne. Databáze s škálovatelným škálováním mají sdílené úložiště, což znamená, že všechny výpočetní repliky uvidí stejné tabulky, indexy a zobrazení. Pokud chcete další indexy optimalizované pro čtení na sekundárních počítačích, musíte je přidat na primární.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Kolik zpoždění mezi primárními a sekundárními výpočetními replikami nastane

Latence dat z doby, kdy je transakce potvrzena na primárním čase na sekundární, závisí na aktuální míře generování protokolu, velikosti transakce, zatížení repliky a dalších faktorech. Typická latence dat pro malé transakce je v desítkových milisekundách, ale neexistují horní meze latence dat. Data v dané sekundární replice jsou vždycky transakční konzistentní. Latence dat v daném bodě v čase se ale může lišit u různých sekundárních replik. Úlohy, které musí okamžitě číst potvrzená data, by měly být spuštěny na primární replice.

## <a name="next-steps"></a>Další kroky

Další informace o úrovni služby škálování na úrovni služeb najdete v tématu [úroveň služby pro škálování na úrovni služby](service-tier-hyperscale.md).
