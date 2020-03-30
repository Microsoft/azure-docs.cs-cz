---
title: Nejčastější dotazy k hyperškálování databáze Azure SQL
description: Odpovědi na běžné otázky, které zákazníci kladou na databázi Azure SQL na úrovni služby Hyperscale – běžně se nazývá databáze Hyperscale.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 9f518df02b1923513fd014be53646a9a1be8465e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268623"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Nejčastější dotazy k hyperškálování databáze Azure SQL

Tento článek obsahuje odpovědi na nejčastější dotazy pro zákazníky, kteří zvažují databázi na úrovni služby Azure SQL Database Hyperscale, která se ve zbývající části těchto častých otázek označuje jako pouze Hyperscale. Tento článek popisuje scénáře, které podporuje Hyperscale a funkce, které jsou kompatibilní s Hyperscale.

- Tyto nejčastější dotazy jsou určeny pro čtenáře, kteří mají stručné znalosti o úrovni služby Hyperscale a chtějí, aby byly zodpovězeny jejich konkrétní otázky a obavy.
- Tyto nejčastější dotazy nemají být průvodcem ani odpovídat na otázky týkající se používání databáze Hyperscale. Pro úvod do Hyperscale doporučujeme odkazovat na [Azure SQL Database Hyperscale](sql-database-service-tier-hyperscale.md) dokumentace.

## <a name="general-questions"></a>Obecné dotazy

### <a name="what-is-a-hyperscale-database"></a>Co je databáze Hyperscale

Databáze Hyperscale je databáze Azure SQL na úrovni služby Hyperscale, která je zálohována technologií úložiště škálování na škálování. Databáze Hyperscale podporuje až 100 TB dat a poskytuje vysokou propustnost a výkon, stejně jako rychlé škálování přizpůsobit požadavkům na pracovní vytížení. Škálování je transparentní pro aplikaci – připojení, zpracování dotazů atd.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Jaké typy prostředků a nákupní modely podporují hyperškálování

Úroveň služby Hyperscale je k dispozici jenom pro jednotlivé databáze pomocí nákupního modelu založeného na virtuálních jádrech v Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Jak se úroveň služby Hyperscale liší od úrovní služeb obecné účely a důležité pro podniky

Úrovně služeb založené na virtuálních jádrech jsou rozlišeny podle dostupnosti databáze a typu úložiště, výkonu a maximální velikosti, jak je popsáno v následující tabulce.

| | Typ prostředku | Pro obecné účely |  Hyperškálování | Kritické pro podnikání |
|:---:|:---:|:---:|:---:|:---:|
| **Nejlepší pro** |Všechny|Nabízí rozpočtově orientované vyvážené výpočetní a úložné možnosti.|Většina obchodních úloh. Automatické škálování velikosti úložiště až 100 TB, rychlé vertikální a horizontální výpočetní škálování, rychlé obnovení databáze.|OLTP aplikace s vysokou transakční frekvencí a nízkou latencí vi. Nabízí nejvyšší odolnost proti selhání a rychlé převzetí služeb při selhání pomocí více synchronně aktualizovaných replik.|
|  **Typ zdroje** ||Jedna databáze / elastický fond / spravovaná instance | Izolovaná databáze | Jedna databáze / elastický fond / spravovaná instance |
| **Velikost výpočetních prostředků**|Jednotná databáze / elastický fond * | 1 až 80 virtuálních jader | 1 až 80 virtuálních jader* | 1 až 80 virtuálních jader |
| |Spravovaná instance | 8, 16, 24, 32, 40, 64, 80 virtuálních jader | Není dostupné. | 8, 16, 24, 32, 40, 64, 80 virtuálních jader |
| **Typ úložiště** | Všechny |Vzdálené úložiště Premium (na instanci) | Odstranění spřažené úložiště s místní mezipamětí SSD (na instanci) | Super rychlé místní úložiště SSD (na instanci) |
| **Velikost úložiště** | Jednotná databáze / elastický fond *| 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| | Spravovaná instance  | 32 GB – 8 TB | Není dostupné. | 32 GB – 4 TB |
| **IOPS** | Izolovaná databáze | 500 IOPS na virtuální jádro s maximálním počtem iOPS 7000 | Hyperscale je vícevrstvá architektura s ukládáním do mezipaměti na více úrovních. Efektivní vipos bude záviset na zatížení. | 5000 IOPS s maximálním počtem iOPS 200 000|
| | Spravovaná instance | Závisí na velikosti souboru | Není dostupné. | 1375 IOPS/virtuální jádro |
|**Dostupnost**|Všechny|1 replika, bez horizontálního navýšení kapacity pro čtení, žádná místní mezipaměť | Více replik, až 4 čtení horizontálnínavýšení kapacity, částečné místní mezipaměti | 3 repliky, 1 horizontální navýšení kapacity pro čtení, zónově redundantní HA, úplné místní úložiště |
|**Zálohování**|Všechny|RA-GRS, 7-35 denní uchovávání (ve výchozím nastavení 7 dní)| RA-GRS, 7denní retence, konstantní zotavení v čase (PITR) | RA-GRS, 7-35 denní uchovávání (ve výchozím nastavení 7 dní) |

\*Elastické fondy nejsou podporovány ve vrstvě služby Hyperscale

### <a name="who-should-use-the-hyperscale-service-tier"></a>Kdo by měl používat úroveň služby Hyperscale

Úroveň služeb Hyperscale je určena pro zákazníky, kteří mají velké místní databáze SQL Serveru a chtějí modernizovat své aplikace přesunutím do cloudu, nebo pro zákazníky, kteří už používají Azure SQL Database a chtějí výrazně rozšířit potenciál pro růst databáze. Hyperscale je také určen pro zákazníky, kteří hledají vysoký výkon a vysokou škálovatelnost. S Hyperscale získáte:

- Velikost databáze až 100 TB
- Rychlé zálohování databáze bez ohledu na velikost databáze (zálohy jsou založeny na snímcích úložiště)
- Rychlé obnovení databáze bez ohledu na velikost databáze (obnovení jsou ze snímků úložiště)
- Vyšší propustnost protokolu bez ohledu na velikost databáze a počet virtuálních jader
- Přečtěte si horizontální navýšení kapacity pomocí jedné nebo více replik jen pro čtení, které se používají pro snižování zátěže pro čtení a jako horké pohotovostní režimy.
- Rychlé škálování výpočetních prostředků v konstantním čase, aby bylo silnější, aby vyhovovalo těžké pracovní zátěži a pak se škálování v konstantním čase. To je podobné škálování nahoru a dolů mezi P6 a P11, například, ale mnohem rychleji, protože to není velikost datové operace.

### <a name="what-regions-currently-support-hyperscale"></a>Oblasti, které v současné době podporují hyperškálování

Úroveň služby Hyperscale je momentálně dostupná v oblastech uvedených v části [Přehled hyperškálování databáze Azure SQL](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Mohu vytvořit více databází Hyperscale na logický server.

Ano. Další informace a omezení počtu databází Hyperscale na logický server naleznete v [tématu limity prostředků databáze SQL pro jednu a sdružené databáze na logickém serveru](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Jaké jsou charakteristiky výkonu databáze Hyperscale

Architektura Hyperscale poskytuje vysoký výkon a propustnost při podpoře velkých velikostí databáze. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Jaká je škálovatelnost databáze Hyperscale

Hyperscale poskytuje rychlou škálovatelnost na základě vaší pracovní hodové poptávky.

- **Škálování nahoru/dolů**

  S Hyperscale, můžete vertikálně navýšit kapacitu primární výpočetní velikost z hlediska prostředků, jako je procesor a paměť a potom vertikálně snížit kapacitu, v konstantním čase. Vzhledem k tomu, že úložiště je sdílené, škálování nahoru a škálování dolů není velikost operace dat.  
- **Změna velikosti dovnitř/ven**

  S Hyperscale získáte také možnost zřídit jednu nebo více dalších výpočetních replik, které můžete použít k zobrazení vašich požadavků na čtení. To znamená, že tyto další výpočetní repliky můžete použít jako repliky jen pro čtení k vyložení úlohy čtení z primárního výpočetního prostředku. Kromě jen pro čtení tyto repliky slouží také jako hot pohotovostní režimy v případě převzetí služeb při selhání z primární.

  Zřizování každé z těchto dalších výpočetních replik lze provést v konstantním čase a je operace online. K těmto dalším výpočetním replikám jen pro `ApplicationIntent` čtení se můžete `ReadOnly`připojit nastavením argumentu v připojovacím řetězci na . Všechna připojení `ReadOnly` se záměrem aplikace jsou automaticky směrována do jedné z dalších výpočetních replik jen pro čtení.

## <a name="deep-dive-questions"></a>Hloubkové otázky

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Mohu kombinovat hyperscale a jednotlivé databáze v jednom logickém serveru

Ano, můžete.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Vyžaduje technologie Hyperscale ke změně aplikačního programovacího modelu

Ne, programovací model aplikace zůstane tak, jak je. Připojovací řetězec používáte obvyklým způsobem a další běžné způsoby interakce s databází Hyperscale.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Jaká úroveň izolace transakcí je výchozí v databázi Hyperscale

Na primární replice výchozí úroveň izolace transakce je RCSI (Read Potvrzený snímek izolace). Na sekundární repliky škálování na horizontální navýšení kapacity pro čtení výchozí úroveň izolace je snímek.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Můžu si do Hyperscale přinést místní licenci nebo licenci IaaS SQL Server.

Ano, [hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) jsou dostupné pro Hyperscale. Každé jádro SQL Server Standard může mapovat na 1 virtuální jádra hyperškálování. Každé jádro SQL Server Enterprise může mapovat na 4 virtuální jádra Hyperscale. Nepotřebujete licenci SQL pro sekundární repliky. Cena hybridní výhody Azure se automaticky použije na repliky horizontálního navýšení kapacity pro čtení (sekundární).

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Jaký druh úloh je Hyperscale určen pro

Hyperscale podporuje všechny úlohy serveru SQL Server, ale je primárně optimalizován pro OLTP. Můžete také přinést hybridní (HTAP) a analytické (datové mart) úlohy.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Jak si můžu vybrat mezi Azure SQL Data Warehouse a Azure SQL Database Hyperscale

Pokud aktuálně používáte interaktivní analytické dotazy pomocí serveru SQL Server jako datového skladu, je hyperscale skvělou volbou, protože můžete hostovat malé a středně velké datové sklady (například několik TB až 100 TB) za nižší cenu a můžete migrovat data serveru SQL Server úlohy skladu do hyperškálování s minimálními změnami kódu T-SQL.

Pokud spouštějíte analýzu dat ve velkém měřítku se složitými dotazy a trvalými rychlostmi ingestování vyššími než 100 MB/s nebo používáte paralelní datový sklad (PDW), Teradata nebo jiné datové sklady masivně paralelního zpracování (MPP), může být sql data warehouse nejlepší volbou.
  
## <a name="hyperscale-compute-questions"></a>Výpočetní otázky hyperškálování

### <a name="can-i-pause-my-compute-at-any-time"></a>Mohu svůj výpočetní výkon kdykoli pozastavit

V tuto chvíli ne, ale můžete škálovat výpočetní prostředky a počet replik dolů snížit náklady v době mimo špičku.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Můžu zřídit výpočetní repliku s extra RAM pro mé úlohy náročné na paměť

Ne. Chcete-li získat více paměti RAM, musíte upgradovat na vyšší výpočetní velikost. Další informace najdete v tématu [Hyperscale úložiště a výpočetní velikosti](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Můžu zřídit více výpočetních replik různých velikostí

Ne.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Kolik replik horizontálního navýšení kapacity pro čtení je podporováno

Databáze Hyperscale jsou ve výchozím nastavení vytvořeny pomocí jedné repliky pro horizontální navýšení kapacity pro čtení (dvě repliky včetně primárních). Můžete škálovat počet replik jen pro čtení mezi 0 a 4 pomocí [portálu Azure](https://portal.azure.com) nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Z důvodu vysoké dostupnosti je nutné zřídit další výpočetní repliky

V databázích Hyperscale je odolnost proti chybám dat k dispozici na úrovni úložiště. K zajištění odolnosti proti chybám potřebujete pouze jednu repliku. Když je výpočetní replika vypnutá, nová replika se vytvoří automaticky bez ztráty dat.

Pokud však existuje pouze jedna replika, může nějakou dobu trvat, než se v nové replice po převzetí služeb při selhání vytvoří místní mezipaměť. Během fáze opětovného sestavení mezipaměti databáze načte data přímo ze stránkových serverů, což vede k vyšší latenci úložiště a snížení výkonu dotazů.

Pro kritické aplikace, které vyžadují vysokou dostupnost s minimálním dopadem převzetí služeb při selhání, byste měli zřídit alespoň 2 výpočetní repliky včetně primární výpočetní repliky. Toto je výchozí konfigurace. Tímto způsobem je k dispozici hot pohotovostní replika, která slouží jako cíl převzetí služeb při selhání.

## <a name="data-size-and-storage-questions"></a>Otázky týkající se velikosti dat a úložiště

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Jaká je maximální velikost databáze podporovaná pomocí hyperškálování

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Jaká je velikost transakčního protokolu s Hyperscale

Transakční protokol s Hyperscale je prakticky nekonečný. Nemusíte se starat o vyčerpání místa v protokolu v systému, který má vysokou propustnost protokolu. Rychlost generování protokolu však může být omezena pro nepřetržité agresivně zápisúloh. Maximální míra trvalého generování protokolu je 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Má `tempdb` moje měřítko jako moje databáze roste

Vaše `tempdb` databáze je umístěna v místním úložišti SSD a je dimenzována úměrně k velikosti výpočetních prostředků, které zřídíte. Váš `tempdb` je optimalizován tak, aby poskytoval maximální výhody výkonu. `tempdb`velikost není konfigurovatelná a je spravována za vás.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Zvětšuje se velikost databáze automaticky nebo je třeba spravovat velikost datových souborů

Velikost databáze se automaticky zvětšuje při vkládání nebo ingestování dalších dat.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Jaká je nejmenší velikost databáze, kterou Hyperscale podporuje nebo začíná

40 GB. Hyperscale databáze je vytvořena s počáteční velikost 10 GB. Poté začne růst o 10 GB každých 10 minut, dokud nedosáhne velikosti 40 GB. Každý z těchto 10 GB sklíčidla je přidělena v jiném stránkovacím serveru s cílem poskytnout více VOPS a vyšší vstupně-v/o paralelismu. Z důvodu této optimalizace i v případě, že zvolíte počáteční velikost databáze menší než 40 GB, databáze se automaticky zvětší na nejméně 40 GB.

### <a name="in-what-increments-does-my-database-size-grow"></a>V jakých přírůstcích se velikost databáze zvětšuje

Každý datový soubor se zvětšuje o 10 GB. Více datových souborů může růst ve stejnou dobu.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>Je úložiště v místním nebo vzdáleném měřítku Hyperscale

V Hyperscale se datové soubory ukládají ve standardním úložišti Azure. Data jsou plně uložena v mezipaměti v místním úložišti SSD, na stránkových serverech, které jsou blízko výpočetních replik. Kromě toho výpočetní repliky mají mezipaměti dat na místním ssd a v paměti, aby se snížila frekvence načítání dat ze vzdálených stránkových serverů.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Můžu spravovat nebo definovat soubory nebo skupiny souborů pomocí stupně Hyperscale.

Ne. Datové soubory jsou přidávány automaticky. Běžné důvody pro vytváření dalších skupin souborů neplatí v architektuře úložiště Hyperscale.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Mohu stanovit pevný strop pro růst dat pro mou databázi

Ne.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Jak jsou datové soubory rozloženy s Hyperscale

Datové soubory jsou řízeny stránkovacími servery s jedním stránkovacím serverem na datový soubor. S růstem velikosti dat se přidávají datové soubory a přidružené stránkové servery.

### <a name="is-database-shrink-supported"></a>Je podporováno zmenšování databáze

Ne.

### <a name="is-data-compression-supported"></a>Je podporována komprese dat

Ano, včetně komprese řádků, stránek a úložiště sloupců.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Pokud mám obrovskou tabulku, jsou data tabulky rozložena do více datových souborů

Ano. Datové stránky přidružené k dané tabulce mohou skončit ve více datových souborech, které jsou součástí stejné skupiny souborů. SQL Server používá [proporcionální výplň strategie](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) k distribuci dat přes datové soubory.

## <a name="data-migration-questions"></a>Otázky k migraci dat

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Můžu přesunout své stávající databáze Azure SQL na úroveň služby Hyperscale.

Ano. Existující databáze Azure SQL můžete přesunout do hyperškálování. Jedná se o jednosměrnou migraci. Databáze z Hyperscale nelze přesunout do jiné úrovně služby. Pro doklady o konceptu (POC) doporučujeme vytvořit kopii databáze a migrovat kopii do hyperškálování.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Mohu přesunout databáze Hyperscale na jiné úrovně služeb.

Ne. V tuto chvíli nelze přesunout hyperškálovat databázi na jinou úroveň služby.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Ztratil(a) bych po migraci na úroveň služby Hyperscale žádné funkce nebo možnosti

Ano. Některé funkce Azure SQL Database ještě nejsou ve Velkém měřítku podporované, včetně dlouhodobého uchovávání záloh. Po migraci databází do hyperškálování přestanou tyto funkce fungovat.  Očekáváme, že tato omezení budou dočasná.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Můžu přesunout místní databázi SQL Serveru nebo databázi SQL Serveru v cloudovém virtuálním počítači do Hyperscale.

Ano. Všechny existující technologie migrace můžete použít k migraci do hyperškálování, včetně transakční replikace, a všech dalších technologií přesunu dat (hromadné kopírování, Azure Data Factory, Azure Databricks, SSIS). Viz také [služba Migrace databáze Azure](../dms/dms-overview.md), která podporuje mnoho scénářů migrace.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Jaké jsou prostoje během migrace z místního prostředí nebo prostředí virtuálních strojů do hyperškálování a jak je lze minimalizovat

Prostoje pro migraci do Hyperscale je stejná jako prostoje při migraci databází do jiných úrovní služby Azure SQL Database. [Transakční replikaci](replication-to-sql-database.md#data-migration-scenario
) můžete použít k minimalizaci migrace prostojů pro databáze o velikosti až několika TB. U velmi rozsáhlých databází (10+ TB) můžete zvážit migraci dat pomocí adf, spark nebo jiných technologií pro přesun dat.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Kolik času by trvalo, než by se do hyperškálování vneslo množství dat x

Hyperscale je schopná spotřebovat 100 MB/s nových/změněných dat, ale čas potřebný k přesunu dat do databází Azure SQL je také ovlivněn dostupnou propustností sítě, rychlostí čtení zdroje a cílem cílové databázové služby.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>Můžu číst data z úložiště objektů blob a rychle se načítat (například Polybase v datovém skladu SQL)

Můžete mít klientské aplikace číst data z Azure Storage a načíst načtení dat do hyperškálovací databáze (stejně jako u jakékoli jiné databáze Azure SQL). Polybase není aktuálně podporována v Azure SQL Database. Jako alternativu k zajištění rychlého načtení můžete použít [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)nebo použít úlohu Spark v Azure [Databricks](https://docs.microsoft.com/azure/azure-databricks/) s [konektorem Spark pro SQL](sql-database-spark-connector.md). Konektor Spark do SQL podporuje hromadné vkládání.

Je také možné hromadně číst data z úložiště objektů blob Azure pomocí BULK INSERT nebo OPENROWSET: [Příklady hromadného přístupu k datům ve službě Azure Blob Storage](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

Jednoduchý model obnovení nebo hromadné protokolování není v hyperškálování podporován. Úplný model obnovení je nutné poskytnout vysokou dostupnost a bod-in-time obnovení. Architektura protokolu Hyperscale však poskytuje lepší rychlost ingestování dat ve srovnání s jinými úrovněmi služby Azure SQL Database.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>Umožňuje hyperškálování zřizování více uzlů pro paralelní ingestování velkého množství dat

Ne. Hyperscale je symetrická architektura s více násobným zpracováním (SMP) a není masivně paralelní zpracování (MPP) nebo architektura více hlavních. Můžete vytvořit pouze více replik pro horizontální navýšení kapacity úloh jen pro čtení.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Jaká je nejstarší verze serveru SQL Server podporovaná pro migraci do hyperškálování

SQL Server 2005. Další informace naleznete [v tématu Migrace do jedné databáze nebo sdružené databáze](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Problémy s kompatibilitou naleznete [v tématu Řešení problémů s kompatibilitou migrace databáze](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Podporuje hyperscale migraci z jiných zdrojů dat, jako jsou Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 a další databázové platformy

Ano. [Služba migrace databáze Azure](../dms/dms-overview.md) podporuje mnoho scénářů migrace.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Otázky kontinuity provozu a zotavení po havárii

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Jaké služby SLA jsou k dispozici pro databázi Hyperscale

Viz [SLA pro Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Další sekundární výpočetní repliky zvyšují dostupnost, až 99,99 % pro databázi se dvěma nebo více sekundárními výpočetními replikami.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Jsou zálohy databáze spravovány službou Azure SQL Database

Ano.

### <a name="how-often-are-the-database-backups-taken"></a>Jak často jsou zálohy databáze přijímána

Neexistují žádné tradiční úplné, rozdílové a protokolové zálohy pro hyperškálovací databáze. Místo toho existují pravidelné snímky úložiště datových souborů. Protokol, který je generován je jednoduše zachována jako-je pro nakonfigurované období uchování, což umožňuje obnovení do libovolného bodu v čase v rámci období uchování.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Obnoví se bod podpory hyperškálování v čase

Ano.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Co je cíl bodu obnovení (RPO) /Cíl doby obnovení (RTO) pro obnovení databáze v hyperměřítku

RPO je 0 min. Cíl RTO je kratší než 10 minut, bez ohledu na velikost databáze. 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Má zálohování databáze vliv na výpočetní výkon na primární nebo sekundární repliky

Ne. Zálohy jsou spravovány podsystémem úložiště a využívají snímky úložiště. Nemají vliv na zatížení uživatelů.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Mohu provést geografické obnovení pomocí databáze Hyperscale

Ano. Geografické obnovení je plně podporováno. Na rozdíl od obnovení bodu v čase vyžaduje geografické obnovení operaci velikosti dat. Datové soubory jsou kopírovány paralelně, takže doba trvání této operace závisí především na velikosti největšího souboru v databázi, nikoli na celkové velikosti databáze. Čas geografického obnovení bude výrazně kratší, pokud je databáze obnovena v oblasti Azure, která je [spárována](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) s oblastí zdrojové databáze.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Mohu nastavit geografickou replikaci pomocí databáze Hyperscale

V tuto chvíli to není možné.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Můžu vzít zálohu databáze Hyperscale a obnovit ji na můj místní server nebo na SQL Server ve virtuálním počítači

Ne. Formát úložiště pro hyperškálované databáze se liší od jakékoli vydané verze serveru SQL Server a nemáte řízení záloh y ani k nim nemáte přístup. Chcete-li data vyjmout z databáze Hyperscale, můžete extrahovat data pomocí všech technologií přesunu dat, tj.

## <a name="cross-feature-questions"></a>Otázky napříč funkcemi

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Ztratil(a) bych po migraci na úroveň služby Hyperscale žádné funkce nebo možnosti

Ano. Některé funkce Azure SQL Database nejsou ve velkém měřítku podporované, včetně dlouhodobého uchovávání záloh. Po migraci databází do hyperškálování přestanou tyto funkce fungovat.

### <a name="will-polybase-work-with-hyperscale"></a>Bude Polybase pracovat s Hyperscale

Ne. Polybase není v Azure SQL Database podporována.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Má Hyperscale podporu pro R a Python

V tuto chvíli to není možné.

### <a name="are-compute-nodes-containerized"></a>Jsou výpočetní uzly kontejnerizované

Ne. Hyperškálované procesy se spouštějí na uzlech [Service Fabric](https://azure.microsoft.com/services/service-fabric/) (VM), ne v kontejnerech.

## <a name="performance-questions"></a>Otázky týkající se výkonu

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Kolik propustnost zápisu mohu tlačit v databázi Hyperscale

Limit propustnost transakční protokol je nastavena na 100 MB/s pro všechny výpočetní velikost i hyperškálování. Schopnost dosáhnout této rychlosti závisí na více faktorech, včetně, ale bez omezení na typ pracovního vytížení, konfigurace klienta a mají dostatečnou výpočetní kapacitu na primární výpočetní repliky k vytvoření protokolu touto rychlostí.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Kolik vipos dostanu na největší výpočetní

Latence VOPS a VO se bude lišit v závislosti na vzorcích pracovního vytížení. Pokud jsou data, ke kterým se přistupuje, uložena do mezipaměti v výpočetní replice, uvidíte podobný výkon vi.

### <a name="does-my-throughput-get-affected-by-backups"></a>Ovlivnily zálohování propustnost

Ne. Výpočetní výkon se odvrství od vrstvy úložiště. To eliminuje dopad zálohování na výkon.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Má moje propustnost získat vliv, jak zřizovat další výpočetní repliky

Vzhledem k tomu, že úložiště je sdílené a mezi primární a sekundární výpočetní repliky neprobíhá žádná přímá fyzická replika, nebude propustnost primární repliky přímo ovlivněna přidáním sekundárních replik. Můžeme však omezit nepřetržitý agresivně zápis úlohy na primární povolit protokol použít na sekundární repliky a stránkovací servery dohnat, aby se zabránilo nízký výkon čtení na sekundární repliky.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Jak diagnostikovat a řešit problémy s výkonem v databázi Hyperscale

Pro většinu problémů s výkonem, zejména ty, které nejsou zakořeněny ve výkonu úložiště, platí běžné sql server diagnostické a řešení potíží kroky. Diagnostika úložiště specifická pro hyperškálování najdete v tématu [diagnostika řešení potíží s výkonem v oblasti výkonu sql hyperškálování](sql-database-hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Otázky škálovatelnosti

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Jak dlouho by trvalo vertikálně navýšit a snížit kapacitu výpočetní repliky

Škálování výpočetních prostředků nahoru nebo dolů by mělo trvat 5 až 10 minut bez ohledu na velikost dat.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Je moje databáze offline, zatímco operace rozšiřování nahoru/dolů probíhá

Ne. Škálování nahoru a dolů bude online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Mám očekávat, že dojde k přetažení připojení, když probíhají operace škálování

Škálování nahoru nebo dolů má za následek existující připojení jsou vynechány, když dojde k převzetí služeb při selhání na konci operace škálování. Přidání sekundárnírepliky nemá za následek připojení kapky.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>Je škálování nahoru a dolů výpočetních replik spouštěné operace nebo operace koncového uživatele

Koncových uživatelů. Ne automaticky.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>Má velikost databáze `tempdb` také růst jako výpočetní je navýšit

Ano. Databáze `tempdb` se automaticky zvětší, jak výpočetní prostředky zvětšují.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Zřizovat více primárních výpočetních replik, jako je například systém s více hlavními servery, kde více primárních výpočetních hlav může řídit vyšší úroveň souběžnosti

Ne. Pouze primární výpočetní replika přijímá požadavky na čtení a zápis. Sekundární výpočetní repliky přijímají pouze požadavky jen pro čtení.

## <a name="read-scale-out-questions"></a>Přečtěte si otázky horizontálního navýšení kapacity

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Kolik sekundárních výpočetních replik mohu zřídit

Ve výchozím nastavení vytváříme jednu sekundární repliku pro hyperškálované databáze. Pokud chcete upravit počet replik, můžete tak učinit pomocí [portálu Azure nebo](https://portal.azure.com) [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Jak se lze připojit k těmto sekundárním výpočetním replikám

K těmto dalším výpočetním replikám jen pro `ApplicationIntent` čtení se můžete `ReadOnly`připojit nastavením argumentu v připojovacím řetězci na . Všechna připojení `ReadOnly` označená jsou automaticky směrována do jedné z dalších výpočetních replik jen pro čtení.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Jak lze ověřit, zda jsem se úspěšně připojil k sekundární výpočetní repliky pomocí SSMS nebo jiných klientských nástrojů?

Můžete spustit následující dotaz T-SQL: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`.
Výsledkem `READ_ONLY` je, pokud jste připojeni k sekundární `READ_WRITE` replice jen pro čtení a pokud jste připojeni k primární replice. Všimněte si, že kontext databáze musí být nastavena na `master` název databáze Hyperscale, nikoli do databáze.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Je možné vytvořit vyhrazený koncový bod pro repliku s horizontálním navýšením kapacity pro čtení

Ne. K replikám s horizontálním navýšením kapacity pro čtení se můžete připojit pouze zadáním `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Provádí systém inteligentní vyrovnávání zatížení úlohy čtení

Ne. Nové připojení s záměrem jen pro čtení je přesměrován na libovolnou repliku škálování na více než pro čtení.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Můžu vertikálně navýšit/snížit sekundární výpočetní repliky nezávisle na primární replice

Ne. Sekundární výpočetní replika se také používá jako cíle s vysokou dostupností převzetí služeb při selhání, takže musí mít stejnou konfiguraci jako primární, aby poskytovaly očekávaný výkon po převzetí služeb při selhání.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Mám pro `tempdb` primární výpočty a další sekundární výpočetní repliky různé velikosti

Ne. Vaše `tempdb` databáze je nakonfigurována na základě zřizování velikosti výpočetních prostředků, sekundární výpočetní repliky mají stejnou velikost jako primární výpočetní prostředky.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Lze přidat indexy a zobrazení na mé sekundární výpočetní repliky

Ne. Hyperškálované databáze mají sdílené úložiště, což znamená, že všechny výpočetní repliky vidí stejné tabulky, indexy a zobrazení. Pokud chcete další indexy optimalizované pro čtení na sekundární, je nutné je přidat na primární.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Kolik zpoždění bude mezi primární a sekundární výpočetní repliky

Latence dat od okamžiku, kdy je transakce potvrzena na primární do doby, kdy je viditelná na sekundární závisí na aktuální rychlost generování protokolu. Typická latence dat je v nízkých milisekundách.

## <a name="next-steps"></a>Další kroky

Další informace o úrovni služby Hyperscale naleznete v [tématu Vrstva služby Hyperscale](sql-database-service-tier-hyperscale.md).
