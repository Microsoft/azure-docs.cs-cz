---
title: Azure SQL Database Nejčastější dotazy k škálování | Microsoft Docs
description: Odpovědi na nejčastější dotazy, které zákazníci žádají o databázi SQL Azure v úrovni služby technologie škálování, která se běžně označuje jako databáze v rámci škálování.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/02/2019
ms.openlocfilehash: 6f2ef181e7f61696245a4413d7a28d84801f2838
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72032884"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Nejčastější dotazy k databázím Azure SQL s škálovatelným škálováním

Tento článek obsahuje odpovědi na nejčastější dotazy pro zákazníky, kteří berou v úvahách o databázi na úrovni služby Azure SQL Database úrovně služeb, která se běžně označuje jako databáze s měřítkem. Tento článek popisuje scénáře, které podporuje škálovatelné, a služby mezi funkcemi jsou kompatibilní se SQL Databasem škálováním obecně.

- Tyto nejčastější dotazy jsou určené pro čtenáře, kteří mají stručný přehled o úrovni služby úrovně služeb a hledají jejich konkrétní otázky a obavy o zodpovězení.
- Tato Nejčastější dotazy se netýkají Guidebook nebo odpovědí na dotazy týkající se použití SQL Database databáze s měřítkem. V takovém případě doporučujeme, abyste odkazovali na Azure SQL Database dokumentaci ke škálování na více [míst](sql-database-service-tier-hyperscale.md) .

## <a name="general-questions"></a>Obecné otázky

### <a name="what-is-a-hyperscale-database"></a>Co je databáze s škálovatelným škálováním

Databáze s technologií škálování je databáze SQL Azure v úrovni služby technologie škálování, která je založená na technologii úložiště s horizontálním škálováním na více instancí. Databáze ve velkém měřítku podporuje až 100 TB dat a poskytuje vysokou propustnost a výkon a také rychlé škálování pro přizpůsobení požadavků úloh. Škálování je transparentní pro aplikaci – připojení, zpracování dotazů atd. funguje stejně jako jakákoli jiná databáze SQL.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Jaké typy prostředků a nákupní modely podporují škálování

Úroveň služby Vcore je k dispozici pouze pro izolované databáze pomocí nákupního modelu založeného na v Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Jak se úroveň služby škálování na úrovni služeb liší od Pro obecné účely a Pro důležité obchodní informace úrovní služeb

Úrovně služeb založené na vCore se primárně liší v závislosti na dostupnosti, typu úložiště a IOPS.

- Úroveň služby Pro obecné účely je vhodná pro většinu obchodních úloh a nabízí vyváženou sadu výpočetních a úložných možností, u kterých není priorita vstupně-výstupních operací nebo doba převzetí služeb při selhání.
- Úroveň služby s technologií webscale je optimalizovaná pro velmi velké databázové úlohy.
- Úroveň služby Pro důležité obchodní informace je vhodná pro obchodní úlohy, u kterých je vstupně-výstupní latence v/v prioritou.

| | Typ prostředku | Obecné použití |  Hyperškálování | Pro důležité obchodní informace |
|:---:|:---:|:---:|:---:|:---:|
| **Nejlepší pro** |Všechno|Nabízí možnosti pro vyvážené výpočty a úložiště s vyrovnanou rozpočtem.|Většina obchodních úloh. Automatické škálování velikosti úložiště až na 100 TB, škálování kapalinových vertikálních a horizontálních výpočetních škálování a rychlé obnovení databáze.|OLTP aplikace s vysokou mírou transakcí a nízkou latencí v/v. Nabízí nejvyšší odolnost proti chybám a rychlé převzetí služeb při selhání s využitím několika synchronně aktualizovaných replik.|
|  **Typ prostředku** ||Jedna databáze/elastický fond/spravovaná instance | Izolovaná databáze | Jedna databáze/elastický fond/spravovaná instance |
| **Velikost výpočetního prostředí**|Jedna databáze/elastický fond * | 1 až 80 virtuální jádra | 1 až 80 virtuální jádra * | 1 až 80 virtuální jádra |
| |Spravovaná instance | 8, 16, 24, 32, 40, 64, 80 virtuální jádra | Nevztahuje se | 8, 16, 24, 32, 40, 64, 80 virtuální jádra |
| **Typ úložiště** | Všechno |Premium Remote Storage (na instanci) | Oddělené úložiště s místní mezipamětí SSD (na instanci) | Vysoce rychlé místní SSD úložiště (na instanci) |
| **Velikost úložiště** | Izolovaná databáze/elastický fond | 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| | Spravovaná instance  | 32 GB – 8 TB | Nevztahuje se | 32 GB – 4 TB |
| **IOPS** | Samostatná databáze * * | 500 IOPS na vCore s maximálním počtem vstupně-výstupních operací 7000 | Škálovatelná architektura je Vícevrstvá architektura s ukládáním do mezipaměti na více úrovních. Platnost IOPS bude záviset na zatížení. | 5000 IOPS s 200 000m maximálním IOPS|
| | Spravovaná instance | Závisí na velikosti souboru | Nevztahuje se | Spravovaná instance: závisí na velikosti souboru.|
|**Dostupnost**|Všechno|1 replika bez měřítka pro čtení, žádná místní mezipaměť | Více replik, až 4 škálování pro čtení, částečná místní mezipaměť | 3 repliky, 1 škálování pro čtení, zóna – redundantní HA, úplná místní mezipaměť |
|**Vytvářet**|Všechno|RA-GRS, 7-35 dní (ve výchozím nastavení 7 dnů)| RA-GRS, 7 dní, časový interval pro obnovení v čase konstanty (PITR) | RA-GRS, 7-35 dní (ve výchozím nastavení 7 dnů) |

@no__t – 0 elastické fondy nejsou podporované v úrovni služby škálování na úrovni služeb.

### <a name="who-should-use-the-hyperscale-service-tier"></a>Kdo má používat úroveň služby pro škálování na úrovni služeb

Úroveň služby pro škálování na úrovni služeb je určená pro zákazníky, kteří mají velké místní SQL Server databáze a chtějí modernizovat své aplikace tím, že se přesunou do cloudu nebo pro zákazníky, kteří už používají Azure SQL Database a chtějí významně rozšířit potenciál pro nárůst databáze. Škálování je také určené pro zákazníky, kteří hledají vysoký výkon i vysokou škálovatelnost. S škálovatelným škálováním získáte:

- Podpora až 100 TB velikosti databáze
- Rychlé zálohování databáze bez ohledu na velikost databáze (zálohy jsou založené na snímcích souborů)
- Rychlá databáze se obnoví bez ohledu na velikost databáze (obnovení ze snímků souborů).
- Vyšší propustnost protokolu má za následek rychlou dobu potvrzení transakcí bez ohledu na velikost databáze.
- Přečtěte si horizontální navýšení kapacity na jeden nebo více uzlů jen pro čtení, které vám umožní přesměrovat úlohy čtení a v Hot-Standby.
- Rychlé škálování výpočetních prostředků v konstantním čase, aby bylo lépe náročné na to, aby vyhovovalo velkým úlohám a pak horizontální navýšení kapacity v konstantním čase. To se podobá vertikálnímu vertikálnímu navýšení a snížení kapacity mezi P6 a P11, například mnohem rychleji, protože se nejedná o velikost datové operace.

### <a name="what-regions-currently-support-hyperscale"></a>Jaké oblasti v současné době podporují škálování

Azure SQL Database úroveň škálování je aktuálně dostupná v oblastech uvedených v části [Azure SQL Database Přehled škálování](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Je možné vytvořit více databází v rámci škálování na logický Server

Ano. Další informace a omezení počtu databází v rámci škálování na logický Server najdete v tématu [SQL Database omezení prostředků pro jednu a sdruženou databázi na logickém serveru](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Jaké jsou výkonnostní charakteristiky databáze v rámci škálování

SQL Database architektura škálovatelného škálování poskytuje vysoký výkon a propustnost při podpoře rozsáhlých velikostí databází. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Jaká je škálovatelnost databáze v rámci škálování

SQL Database škálování poskytuje rychlou škálovatelnost na základě požadavků vašich úloh.

- **Horizontální navýšení nebo snížení kapacity**

  S měřítkem můžete škálovat primární výpočetní velikost z hlediska prostředků, jako je například CPU, paměť a pak snížit kapacitu v konstantním čase. Vzhledem k tomu, že je úložiště sdílené, nefunguje horizontální navýšení kapacity a zmenšování velikosti datové operace.  
- **Horizontální navýšení nebo zmenšení kapacity**

  S vlastním škálováním získáte také možnost zřídit jeden nebo více dalších výpočetních uzlů, které můžete použít k obsluze svých žádostí o čtení. To znamená, že tyto dodatečné výpočetní uzly můžete použít jako uzly jen pro čtení k přesměrování úlohy čtení z primární výpočetní služby. Kromě jen pro čtení tyto uzly také slouží jako aktivní pohotovostní režim v případě převzetí služeb při selhání z primární služby.

  Zřizování každého z těchto dalších výpočetních uzlů se dá provést v konstantním čase a je to online operace. K těmto dalším výpočetním uzlům, které jsou jen pro čtení, se můžete připojit nastavením argumentu `ApplicationIntent` v připojovacím řetězci na hodnotu `readonly`. Všechna připojení označená `readonly` jsou automaticky směrována na jeden z dalších výpočetních uzlů jen pro čtení.

## <a name="deep-dive-questions"></a>Podrobné dotazy k podrobně

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Můžu kombinovat škálovatelné a izolované databáze na jednom logickém serveru

Ano, je to možné.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Vyžaduje se, aby se změnila velikost aplikačního programovacího modelu

Ne, programovací model vaší aplikace zůstane tak, jak je. Připojovací řetězec se používá jako obvykle a jiné běžné režimy pro interakci se službou Azure SQL Database.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Jaké úrovně izolace transakcí budou ve SQL Database databázi s měřítkem výchozí.

Na primárním uzlu je úroveň izolace transakce RCSI (čtení potvrzené izolace snímku). Na sekundárních uzlech se škálováním pro čtení je úroveň izolace snímek.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Můžu přinášet místní nebo IaaS SQL Server licenci SQL Database škálování

Ano, [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) je k dispozici pro škálování. Každý SQL Server Standard Core se může namapovat na 1 virtuální jádra s měřítkem. Každý SQL Server Enterprise Core se může namapovat na 4 škálovatelné virtuální jádra. Pro sekundární repliky nepotřebujete licenci SQL. Zvýhodněné hybridní využití Azureová cena se automaticky použije pro repliky se škálováním na úrovni čtení (sekundární).

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Jaký druh zatížení je SQL Database škálováním navrženým pro

SQL Database škálování podporuje všechny úlohy SQL Server, ale je primárně optimalizovaná pro OLTP. Můžete přinést i hybridní úlohy (HTAP) a analytické (datové tržiště).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Jak si můžu vybrat mezi Azure SQL Data Warehouse a SQL Database škálováním

Pokud aktuálně spouštíte interaktivní analytické dotazy pomocí SQL Server jako datový sklad, SQL Database škálování je skvělým způsobem, protože můžete hostovat relativně malé datové sklady (například pár TB až desítkách TB) za nižší náklady a můžete migrovat data w. arehouse úlohy pro SQL Database škálování bez změny kódu T-SQL.

Pokud pracujete s analýzou dat ve velkém měřítku s využitím složitých dotazů a používáte PDW (Parallel Data Warehouse), Teradata nebo jiné hromadné paralelní procesory (MPP) SQL Data Warehouse, může být nejlepší volbou.
  
## <a name="sql-database-hyperscale-compute-questions"></a>SQL Database dotazy na výpočetní výkon s škálovatelným škálováním

### <a name="can-i-pause-my-compute-at-any-time"></a>Můžu kdykoli pozastavit výpočetní výkon

V tuto chvíli ale můžete škálovat výpočetní výkon a počet replik dolů a snížit tak náklady v době mimo špičku.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Můžu zřídit výpočetní prostředky s dodatečnou pamětí RAM pro úlohy náročné na paměť

Ne. Pokud chcete získat větší velikost paměti RAM, musíte upgradovat na vyšší výpočetní kapacitu. Další informace najdete v tématu [velikosti úložiště a výpočetních](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute)prostředků ve škálování na úrovni.

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Můžu zřídit několik výpočetních uzlů různých velikostí.

Ne.

### <a name="how-many-read-scale-replicas-are-supported"></a>Kolik replik se škálováním pro čtení je podporovaných

Databáze s škálovatelným škálováním jsou ve výchozím nastavení vytvářeny s jednou replikou se škálováním na úrovni čtení (celkem dvě repliky). Počet replik, které jsou jen pro čtení, můžete škálovat mezi 0 a 4 pomocí [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) nebo rozhraní příkazového [řádku](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Pro zajištění vysoké dostupnosti potřebuji zřídit další výpočetní uzly.

V databázích s škálovatelnou úrovní je Odolnost zajištěna na úrovni úložiště. Pro zajištění odolnosti potřebujete jenom jednu repliku. Pokud je výpočetní replika vypnutá, vytvoří se automaticky nová replika bez ztráty dat.

Pokud ale existuje jenom jedna replika, může trvat nějakou dobu, než se po převzetí služeb při selhání sestaví místní mezipaměť v nové replice. Během fáze opětovného sestavování mezipaměti databáze načte data přímo ze stránek stránky, což má za následek snížený počet IOPS a výkon dotazů.

Pro klíčové aplikace, které vyžadují vysokou dostupnost, byste měli zřídit aspoň 2 výpočetní uzly, včetně primárního výpočetního uzlu (výchozí). Díky tomu je v případě převzetí služeb při selhání dostupný pohotovostní úsporný režim.

## <a name="data-size-and-storage-questions"></a>Otázky velikosti a úložiště dat

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Jaká je maximální velikost databáze podporovaná SQL Database škálováním

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Jaká je velikost transakčního protokolu s měřítkem

Transakční protokol s měřítkem je prakticky nekonečný. Nemusíte se starat o nedostatek místa v protokolu v systému, který má vysokou propustnost protokolu. Nicméně je možné omezit rychlost generování protokolu pro nepřetržitě agresivní úlohy. Maximální počet trvalých generování protokolů je přibližně 100 MB/s.

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Roste velikost dočasná databáze při zvětšování databáze

Vaše databáze `tempdb` je umístěná v místním úložišti SSD a je nakonfigurovaná na základě výpočetní velikosti, kterou zřídíte. Vaše `tempdb` je optimalizováno a stanoveno tak, aby poskytovalo maximální výkonnostní výhody. Velikost `tempdb` není konfigurovatelná a je spravována za vás subsystémem úložiště.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Roste velikost databáze automaticky nebo je nutné spravovat velikost datových souborů

Velikost databáze se automaticky zvětšuje při vkládání nebo přijímání dalších dat.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Jaká je nejmenší velikost databáze, kterou SQL Database podporuje nebo začíná na

10 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>V jakých přírůstcích roste velikost databáze

Každý datový soubor roste o 10 GB. Současně může růst více datových souborů.

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>Je úložiště v SQL Database místním nebo vzdáleném měřítku

V měřítku jsou datové soubory uložené ve službě Azure Storage úrovně Standard. Data jsou ve velkém mezipaměti v místním úložišti SSD, v počítačích blízko výpočetních uzlů. Kromě toho výpočetní uzly mají mezipaměť na místních jednotkách SSD a v paměti (fond vyrovnávacích pamětí atd.), aby se snížila frekvence načítání dat ze vzdálených uzlů.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Můžu spravovat nebo definovat soubory nebo skupiny souborů s měřítkem

Ne
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Můžu zřídit pevný limit růstu dat pro moji databázi

Ne

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Jak jsou datové soubory vymezeny s SQL Database škálováním

Datové soubory jsou ovládány pomocí stránkových serverů. Jak roste velikost dat, přidají se datové soubory a přidružené uzly stránkového serveru.

### <a name="is-database-shrink-supported"></a>Je podporováno zmenšení databáze

Ne

### <a name="is-database-compression-supported"></a>Je podporována komprese databáze

Ano

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Pokud mám obrovský stůl, data z tabulky se rozšíří do několika datových souborů.

Ano. Datové stránky přidružené k dané tabulce mohou končit více datovými soubory, které jsou součástí stejné skupiny souborů. SQL Server používá [strategii proporcionálního plnění](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) k distribuci dat nad datovými soubory.

## <a name="data-migration-questions"></a>Otázky k migraci dat

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Můžu přesunout moje existující databáze SQL Azure do vrstvy služeb s osobním škálováním

Ano. Stávající databáze SQL Azure můžete přesunout do škálování na velká. Toto je jednosměrná migrace. Databáze nemůžete přesouvat z velkého měřítka do jiné úrovně služeb. Doporučujeme vytvořit kopii produkčních databází a migrovat ji do škálování pro účely POCsí konceptů.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Můžu přesunout databáze v osobním měřítku na jiné edice

Ne. V tuto chvíli nemůžete přesunout databázi v rámci škálování na jinou úroveň služby.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Po migraci na úroveň služby s škálováním na úrovni služeb ztratíme žádné funkce ani možnosti

Ano. Některé funkce Azure SQL Database se zatím nepodporují, mezi které patří ale ne omezené dlouhodobé zálohování uchovávání. Po migraci databází do škálovatelného prostředí tyto funkce přestanou fungovat.  Očekáváme, že tato omezení budou dočasná.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Můžu přesunout místní SQL Server databázi nebo databázi virtuálního počítače SQL Server do škálování

Ano. Můžete použít všechny existující technologie migrace k migraci do škálování, včetně transakční replikace a dalších technologií pro přesun dat (hromadné kopírování, Azure Data Factory, Azure Databricks, SSIS). Viz také [Azure Database Migration Service](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Co je během migrace z místního prostředí nebo z prostředí virtuálních počítačů na škálovatelné a jak můžu minimalizovat

Výpadek je stejný jako výpadek při migraci databází do izolované databáze v Azure SQL Database. [Transakční replikaci](replication-to-sql-database.md#data-migration-scenario
) můžete použít k minimalizaci výpadku výpadku databáze o velikosti až několika TB. U velmi rozsáhlých databází (10 + TB) můžete zvážit migraci dat pomocí ADF, Sparku nebo jiných technologií pro přesun dat.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Kolik času by vyžadovalo převedení X množství dat na SQL Database škálování

Škálovatelné škálování je schopné spotřebovávat 100 MB/s nových nebo změněných dat, ale čas potřebný k přesunu dat do databází SQL Azure taky ovlivňuje dostupnou propustnost sítě, rychlost čtení zdroje a cílový cíl na úrovni služby databáze v rámci škálování na úrovni služby.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Můžu číst data z úložiště objektů BLOB a rychle je načíst (například základnu a SQL Data Warehouse).

Můžete číst data z Azure Storage a načíst zatížení dat do databáze v rámci škálování (stejně jako v případě běžné izolované databáze). V Azure SQL Database se v tuto chvíli nepodporuje základ. Pomocí [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) nebo spuštění úlohy Sparku v [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) pomocí [konektoru Spark pro SQL](sql-database-spark-connector.md)můžete použít základ. Konektor Spark pro SQL podporuje hromadné vložení.

Je také možné hromadně číst data z úložiště objektů BLOB v Azure pomocí BULK INSERT nebo OPENROWSET: [Příklady hromadného přístupu k datům v azure BLOB Storage](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

Jednoduché obnovení nebo model hromadného protokolování není v měřítku podporován. K zajištění vysoké dostupnosti je vyžadován model úplného obnovení. Ale škálovatelná škála nabízí lepší rychlost ingestování dat v porovnání s jednou databází Azure SQL z důvodu nové architektury protokolů.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>Umožňuje zřizování více uzlů pro ingestování velkých objemů dat SQL Database škálováním.

Ne. SQL Database s měřítkem se nejedná o architekturu SMP a nejedná se o architekturu asymetrickou pro více procesů nebo pro více hlavních serverů. Pro horizontální navýšení kapacity úloh jen pro čtení můžete vytvořit jenom několik replik.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Jaká je nejstarší SQL Serverá verze, SQL Database se migrace podpory na úrovni škálování z

SQL Server 2005. Další informace najdete v tématu [migrace do izolované databáze nebo do databáze ve fondu](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Problémy s kompatibilitou najdete v tématu [řešení problémů s kompatibilitou migrace databáze](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>Podporuje SQL Database migraci z jiných zdrojů dat, jako jsou Aurora, MySQL, Oracle, DB2 a další databázové platformy.

Ano. Pocházejí z různých zdrojů dat, než je SQL Server, vyžadují logickou migraci. [Azure Database Migration Service](../dms/dms-overview.md) můžete použít pro logickou migraci.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Otázky pro provozní kontinuitu a zotavení po havárii

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Jaké smlouvy SLA jsou k dispozici pro databázi s škálovatelným škálováním

S výchozím primárním a 1 čitelným sekundárním objektem SLA je 99,95% dostupnost.  S více replikami, smlouva SLA bude až 99,99%.  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Jsou zálohy databáze spravované pro mě službou Azure SQL Database.

Ano

### <a name="how-often-are-the-database-backups-taken"></a>Jak často jsou zálohy databáze podniknuty

Neexistují žádné tradiční zálohy úplného, rozdílového a protokolu pro SQL Database databáze v rozsahu. Místo toho existují běžné snímky datových souborů a generovaných protokolů, které jsou zachovány, stejně jako pro dobu uchování nakonfigurovanou nebo dostupnou pro vás.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>SQL Database obnovení v časovém bodě podpory na úrovni škálování

Ano

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Jaký je cíl bodu obnovení (RPO)/Recovery Time (RTO) se zálohováním a obnovením v SQL Database škálování

CÍL bodu obnovení je 0 min. Cílem RTO je méně než 10 minut, bez ohledu na velikost databáze. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Vliv zálohování velkých databází na výpočetní výkon na primárním

Ne. Zálohy jsou spravovány subsystémem úložiště a využívají snímky souborů. Neovlivňují úlohu uživatele na primárním počítači.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Je možné provést geografickou obnovu s SQL Database databází s měřítkem

Ano.  Geografické obnovení je plně podporované.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Můžu nastavit geografickou replikaci s využitím SQL Database databáze s škálovatelným škálováním

V současnosti ne.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Získají moje sekundární výpočetní uzly geograficky replikovanou SQL Database škálováním

V současnosti ne.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Můžu SQL Database zálohu databáze na úrovni a obnovit ji na místním serveru nebo SQL Server na virtuálním počítači.

Ne. Formát úložiště pro databáze s škálovatelným škálováním se liší od tradičních SQL Server a Vy neřídíte zálohy nebo k nim máte přístup. Pokud chcete získat data z SQL Database databáze s škálovatelným škálováním, buď použijte službu exportu, nebo použijte skriptování plus BCP.

## <a name="cross-feature-questions"></a>Otázky pro různé funkce

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Po migraci na úroveň služby s škálováním na úrovni služeb ztratíme žádné funkce ani možnosti

Ano. Některé funkce Azure SQL Database nejsou podporované v měřítku, mezi které patří ale ne omezené dlouhodobé zálohování uchovávání. Po migraci databází do škálovatelného prostředí tyto funkce přestanou fungovat.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Bude základní práce s SQL Database škálováním na úrovni

Ne. Základová databáze není na Azure SQL Database podporována.

### <a name="does-the-compute-have-support-for-r-and-python"></a>Má COMPUTE podporu pro R a Python

Ne. R a Python nejsou podporované v Azure SQL Database.

### <a name="are-the-compute-nodes-containerized"></a>Jsou výpočetní uzly kontejnery

Ne. Vaše databáze se nachází na výpočetním VIRTUÁLNÍm počítači, nikoli v kontejneru.

## <a name="performance-questions"></a>Otázky týkající se výkonu

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Kolik propustnosti mám nabízet na největším SQL Database výpočetní výkon

Zjistili jsme konzistentní 100 MB/s dat změn (generování dat protokolu transakcí).

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Kolik vstupně-výstupních operací se mi získá na nejvyšší SQL Database výpočetního rozsahu

Latence a vstupně-výstupní operace se budou lišit v závislosti na vzorech úloh.  Pokud jsou data, která je potřeba přistupovat, místní pro výpočetní mezipaměť, budou to stejné vzory v/v jako místní jednotka SSD.   

### <a name="does-my-throughput-get-affected-by-backups"></a>Bude mít tato propustnost vliv na zálohy

Ne. Výpočetní prostředky jsou oddělené od vrstvy úložiště, aby se zabránilo dopadu na výpočetní prostředky.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Získá tato propustnost při zřizování dalších výpočetních uzlů.

Vzhledem k tomu, že je úložiště sdílené a mezi primárními a sekundárními výpočetními uzly nedochází k přímé fyzické replikaci, neovlivní propustnost na primárním uzlu uzly pro čtení. Můžeme ale omezit nepřetržitě agresivní úlohy, aby se protokol mohl použít na sekundárních uzlech a stránkovacích serverech a vyhnout se špatnému výkonu čtení na sekundárních uzlech.

## <a name="scalability-questions"></a>Otázky škálovatelnosti

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Jak dlouho by pomohly škálovat výpočetní uzel směrem nahoru a dolů

Škálování výpočetních prostředků nahoru nebo dolů by mělo trvat 5-10 minut bez ohledu na velikost dat.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Je moje databáze v režimu offline, zatímco probíhá operace škálování/snížení kapacity

Ne. Horizontální navýšení a snížení kapacity bude online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Při probíhajících operacích škálování očekávat přerušení připojení

Pokud dojde k převzetí služeb při selhání na výpočetním uzlu s cílovou velikostí, dojde k jejich vertikálnímu navýšení nebo snížení kapacity v existujících připojeních. Přidání replik pro čtení nevede k poklesu připojení.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>Probíhá horizontální navýšení a snížení počtu výpočetních uzlů a operace aktivované koncovým uživatelem.

Koncový uživatel. Nepoužívá se automaticky.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>Zvětšuje se `tempb` také při horizontálním navýšení kapacity

Ano. Temp DB se při nárůstu kapacity automaticky nasadí.  

### <a name="can-i-provision-multiple-primary-compute-nodes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Můžu zřídit několik primárních výpočetních uzlů, jako je například vícenásobný hlavní systém, ve kterém více primárních výpočetních hlav může zvýšit úroveň souběžnosti.

Ne. Pouze primární výpočetní uzel přijímá požadavky na čtení a zápis. Sekundární výpočetní uzly akceptují pouze požadavky jen pro čtení.

## <a name="read-scale-questions"></a>Přečíst otázky ke škálování

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Kolik sekundárních výpočetních uzlů je možné zřídit

Ve výchozím nastavení vytvoříme 2 repliky pro databáze s škálovatelnými škálováními. Pokud chcete upravit počet replik, můžete to udělat pomocí [Azure Portal](https://portal.azure.com).

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Návody se připojit k těmto sekundárním výpočetním uzlům

K těmto dalším výpočetním uzlům, které jsou jen pro čtení, se můžete připojit nastavením argumentu `ApplicationIntent` v připojovacím řetězci na hodnotu `readonly`. Všechna připojení označená `readonly` jsou automaticky směrována na jeden z dalších výpočetních uzlů jen pro čtení.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-node-using-ssms--other-client-tools"></a>Návody ověřit, jestli se mám úspěšně připojit k sekundárnímu výpočetnímu uzlu pomocí SSMS/jiných klientských nástrojů?

Pomocí SSMS/jiných klientských nástrojů můžete spustit následující dotaz T-SQL: `SELECT DATABASEPROPERTYEX ( '<database_name>' , 'updateability' )`.
Výsledek je `READ_ONLY`, pokud vaše připojení odkazuje na sekundární uzel jen pro čtení nebo `READ_WRITE`, pokud vaše připojení odkazuje na primární uzel.

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Můžu vytvořit vyhrazený koncový bod pro repliku pro čtení a škálování.

Ne. K replice se škálováním na úrovni čtení se můžete připojit pouze zadáním `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Vyrovnává inteligentní vyrovnávání zatížení úlohy čtení.

Ne. Úloha jen pro čtení se přesměruje na náhodnou repliku na úrovni čtení.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Můžu navýšit nebo snížit kapacitu sekundárních výpočetních uzlů nezávisle na primárním výpočetním prostředí.

Ne. Sekundární výpočetní uzly se používají také pro HA, takže v případě převzetí služeb při selhání musí být stejná konfigurace jako primární.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Získám různou velikost tempa databáze pro primární výpočetní prostředky a moje další sekundární výpočetní uzly

Ne. Vaše `tempdb` se konfiguruje na základě zřizování velikosti výpočtů, sekundární výpočetní uzly mají stejnou velikost jako primární výpočetní prostředí.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Můžu přidat indexy a zobrazení na mých sekundárních výpočetních uzlech

Ne. Databáze s škálovatelným škálováním mají sdílené úložiště, což znamená, že všechny výpočetní uzly uvidí stejné tabulky, indexy a zobrazení. Pokud chcete, aby byly další indexy optimalizované pro čtení na sekundárních počítačích, musíte je nejdřív přidat na primární.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Kolik zpoždění uplyne mezi primárním a sekundárním výpočetním uzlem

Od okamžiku, kdy se transakce na primárním počítači potvrdí v závislosti na rychlosti generování protokolu, může být buď okamžitá, nebo v nižších milisekundách.

## <a name="next-steps"></a>Další kroky

Další informace o úrovni služby škálování na úrovni služeb najdete v tématu [úroveň služby pro škálování na úrovni služby](sql-database-service-tier-hyperscale.md).
