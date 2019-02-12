---
title: Azure SQL Database Hyperškálovatelného – nejčastější dotazy | Dokumentace Microsoftu
description: Odpovědi na běžné dotazy zákazníků požádat o Azure SQL database ve vrstvě služeb Hyperškálovatelného - říká Hyperškálovatelného databáze.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/06/2019
ms.openlocfilehash: 16d536ff5bd1f7d9a3427877a93036d9f711564e
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099768"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Nejčastější dotazy týkající se databáze Azure SQL Hyperškálováním

Tento článek obsahuje odpovědi na nejčastější dotazy pro zákazníky, kteří uvažují databáze v rámci úrovně služeb Azure SQL Database Hyperškálovatelného, říká Hyperškálovatelného databáze (aktuálně ve verzi public preview). Tento článek popisuje scénáře, které podporuje Velkokapacitní a různé funkce služby jsou obecně kompatibilní s Hyperškálováním databáze SQL.

- Tyto nejčastější dotazy je určená pro čtenáře, kteří mají stručné vysvětlení vrstvy služby s Hyperškálováním a chtějí mít jejich konkrétní otázky a připomínky odpovědi.
- Tyto nejčastější dotazy neměl být aby se nebo odpovídejte na tom, jak používat velkokapacitní databáze SQL database. K tomu, doporučujeme odkazujete i [mírou škálování Azure SQL Database](sql-database-service-tier-hyperscale.md) dokumentaci.

## <a name="general-questions"></a>Obecné otázky

### <a name="what-is-a-hyperscale-database"></a>Co je databáze hyperškálovatelný systém

Velkokapacitní databáze je databáze Azure SQL v rámci úrovně služeb Hyperškálovatelného, která je založená na technologii Hyperškálovatelného horizontální navýšení kapacity úložiště. Velkokapacitní databáze podporuje až 100 TB dat a poskytuje vysokou propustnost a výkonu, a také Rychlé škálování umožní reagovat na požadavky na úlohy. Škálování je transparentní pro aplikace, připojení, pracovní zpracování dotazů a tak dále, podobně jako jakékoli jiné databáze SQL.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Jaké typy prostředků a tyto modely nákupu podporují Hyperškálováním

Úroveň služby Hyperškálovatelného dostupná jenom pro izolované databáze pomocí nákupní model založený na virtuálních jádrech v Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Jak na úrovni služby hyperškálovatelný systém se liší od úrovně služeb pro obecné účely a pro důležité obchodní informace

Založený na virtuálních jádrech služby, které úrovně se liší především na základě dostupnosti, typ úložiště a vstupně-výstupních operací.

- Služba úrovni General Purpose je vhodné pro většinu obchodních úloh nabídky vyrovnáváním výpočetní výkon a úložiště možnosti, kde vstupně-výstupních operací doby latence nebo převzetí služeb při selhání nejsou prioritu.
- Úroveň služby Hyperškálovatelného je optimalizována pro úlohy velmi rozsáhlé databáze.
- Úroveň pro důležité obchodní informace služby jsou vhodné pro firemní úlohy kde vstupně-výstupní latence je prioritou.

| | Typ prostředku | Obecné použití |  Hyperškálování | Pro důležité obchodní informace |
|:---|:---:|:---:|:---:|:---:|:---:|
| **Nejlepší pro** |Vše|  Většinu obchodních úloh. Nabídky rozpočtu orientované vyvážené výpočetní a úložné možnosti. | Datové aplikace s požadavky na kapacitu velkých objemů dat a možnost automatického škálování úložiště a škálování výpočetní plynule. | OLTP aplikace s vysokou četností transakcí a nejnižší latenci vstupně-výstupních operací. Nabízí nejvyšší odolnost proti selhání s využitím několika, izolovaných replik.|
|  **Typ prostředku** ||Izolované databáze nebo elastického fondu / spravované instance | Izolovaná databáze | Izolované databáze nebo elastického fondu / spravované instance |
| **Vypočítat velikost**|Izolovanou databázi nebo elastický fond * | 1 až 80 virtuálních jader | 1 až 80 virtuálních jader * | 1 až 80 virtuálních jader |
| |Spravovaná instance | 8, 16, 24, 32, 40, 64, 80 virtuálních jader | neuvedeno | 8, 16, 24, 32, 40, 64, 80 virtuálních jader |
| **Typ úložiště** | Vše |Vzdálené úložiště úrovně Premium (za instanci) | Zrušit propojených úložiště místní mezipaměti SSD (za instanci) | Mimořádně rychlým místní SSD storage (za instanci) |
| **Velikost úložiště** | Izolované databáze nebo elastického fondu | 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| | Spravovaná instance  | 32 GB – 8 TB | neuvedeno | 32 GB – 4 TB |
| **Vstupně-výstupních operací** | Jednu databázi ** | 500 IOPS na vCore s 7000 maximální IOPS | Zatím neznámý | 5000 vstupně-výstupních operací s 200 000 maximální IOPS|
| | Spravovaná instance | Závisí na velikosti souboru | neuvedeno | Spravovanou instanci: Závisí na velikosti souboru|
|**Dostupnost**|Vše|1 repliky, žádné škálování pro čtení, ne v místní mezipaměti | Víc replik, až 15 škálování pro čtení, částečné místní mezipaměti | 3 repliky, 1 škálování pro čtení, zónově redundantní vysokou dostupnost, úplná místní mezipaměti |
|**Zálohování**|Vše|RA-GRS, 7 – 35 dní (7 dní ve výchozím nastavení)| RA-GRS, 7 – 35 dní (7 dní ve výchozím nastavení), konstantní čas in0time bodu obnovení (PITR) | RA-GRS, 7 – 35 dní (7 dní ve výchozím nastavení) |

\* Elastické fondy nejsou podporovány v rámci úrovně služeb hyperškálovatelný systém

### <a name="who-should-use-the-hyperscale-service-tier"></a>Kdo by měl používat velkokapacitní úroveň služby

Úroveň služby Hyperškálovatelného je primárně určena pro zákazníky, kteří mají rozsáhlých místních databází SQL serveru a chtějí modernizovat svých aplikací díky přesunu do cloudu nebo pro zákazníky, kteří už používáte Azure SQL Database a chcete rozšířit výrazně možnosti pro růst databáze. Velkokapacitní je také určená pro zákazníky, kteří hledají vysoký výkon a vysokou škálovatelnost. S Hyperškálováním získáte:

- Podpora pro až 100 TB velikosti databáze
- Rychlé zálohy bez ohledu na velikost databáze (zálohy jsou založeny na snímky)
- Rychlé databáze obnoví bez ohledu na velikost databáze (obnoví se ze souboru snímků)
- Větší propustnost v protokolu výsledků v potvrzení časy rychlé transakce bez ohledu na velikost databáze
- Čtení horizontální navýšení kapacity na jeden nebo více uzlů jen pro čtení k přesměrování zpracování úlohy čtení a pro horkou – možnosti pro případ potřeby.
- Rychlého vertikálního navýšení výpočetního výkonu, takže v konstantním času, aby výkonnější podle náročné úlohy a potom kapacitu zase v konstantním času. To se podobá vertikální navýšení a snížení mezi P6 pro P11, například ale mnohem rychlejší, protože není velikost datové operace.

### <a name="what-regions-currently-support-hyperscale"></a>Jaké oblasti v současné době podporují Hyperškálováním

Velkokapacitní je aktuálně dostupné pro izolované databáze v těchto oblastech:  Západní US1, západní USA 2, východní US1, střed USA, západní Evropa, Severní Evropa, Velká Británie – Západ, jihovýchodní Asie, Japonsko – východ, Korea – střed, Austrálie – jihovýchod a Austrálie – východ.

### <a name="can-i-create-multiple-hyperscale-databases-per-sql-database-server"></a>Můžete vytvořit více databází hyperškálovatelný systém na serveru služby SQL Database

Ano. Další informace a omezení počtu databází hyperškálovatelný systém na serveru služby SQL Database najdete v tématu [limity prostředků SQL Database pro jeden a ve fondu databází na serveru služby SQL Database](sql-database-resource-limits-database-server.md).

### <a name="what-are-the-performance-characteristic-of-a-hyperscale-database"></a>Co jsou charakteristické výkonu databáze hyperškálovatelný systém

Architektura Hyperškálovatelného SQL Database poskytuje vysoký výkon a propustnost při současné podpoře velikosti velké databáze. Charakteristiky a přesný výkon profil není ve verzi public preview k dispozici.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Jaká je škálovatelnost Hyperškálovatelného databáze

Velkokapacitní SQL Database nabízí rychlou škálovatelnost podle potřeb vašich úloh.

- **Škálování směrem nahoru nebo dolů**

  S Hyperškálováním můžete škálovat kapacitu primární výpočetního prostředí z hlediska prostředkům, jako je procesor, paměť a potom kapacitu zase v konstantním času. Vzhledem k tomu, že je sdílené úložiště, vertikální navýšení a snížení kapacity není velikost datové operace.  
- **Měřítko Zvětšit/zmenšit**

  S Hyperškálováním budete mít také možnost zřizování jeden nebo více dalších výpočetních uzlů, které můžete použít k obsluze žádostí o čtení. To znamená, že vám pomůže těchto dalších výpočetních uzlů jako jen pro čtení uzly přesměrování zpracování úlohy čtení od primární výpočetních prostředků. Kromě jen pro čtení, že tyto uzly slouží také jako hot standby je v případě převzetí přes z primárního serveru.

  Zřizování každé z těchto dalších výpočetních uzlů můžete udělat v konstantním čase a je online operace. Můžete připojit k tyto uzly další výpočetní prostředky jen pro čtení tak, že nastavíte `ApplicationIntent` argument u připojovacího řetězce k `read_only`. Všechna připojení označené `read-only` se automaticky směrují na jednom z uzlů další výpočetní prostředky jen pro čtení.

## <a name="deep-dive-questions"></a>Podrobné informace o dotazy

### <a name="can-i-mix-hyperscale-and-single-databases-a-my-sql-database-server"></a>Můžete používat kombinaci Velkokapacitní a izolované databáze serveru SQL Database

Ano, je to možné.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Vyžaduje Hyperškálovatelného Moje aplikace programovací model, chcete-li změnit

Ne, zůstane aplikační programovací model je. Používáte připojovací řetězec jako obvykle a jiné regulární režimy pro interakci s Azure SQL database.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Co jsou úrovně izolace transakce má být výchozí v Hyperškálovacím databáze SQL database

Na primárním uzlu je úroveň izolace transakce RCSI (čtení potvrzené izolaci snímku). Na sekundární uzly škálování čtení je úroveň izolace snímku.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Možné přenést své místní nebo licenci na IaaS SQL Server do SQL Database Hyperškálovatelného

Ano, [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) je k dispozici pro mírou škálování. Každé jádro SQL Server Standard můžete namapovat na 1 virtuální jádra mírou škálování. Každé jádro SQL Server Enterprise můžete namapovat na 4 virtuální jádra mírou škálování. Není nutné licenci SQL pro sekundární repliky. Cena zvýhodněné hybridní využití Azure se automaticky použijí, repliky (sekundárních) škálování pro čtení.

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Jaký druh úloh je velkokapacitní SQL databáze určená pro

Velkokapacitní SQL Database podporuje všechny úlohy SQL serveru, ale je primárně optimalizováno pro OLTP. Je možné přenést hybridní data a analytické (datové Tržiště) procesy.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Jak můžu si zvolit mezi Azure SQL Data Warehouse a velkokapacitní databáze SQL

Pokud v tuto chvíli se spouštění interaktivních analýz dotazů pomocí SQL serveru jako datový sklad, Hyperškálovatelného databáze SQL je skvělou možností vzhledem k tomu může hostovat relativně malé datové sklady (například několik TB až vyšplhat až TB) s nižšími náklady a můžete migrovat data  úlohy skladu k SQL Database Hyperškálovatelného beze změny kódu jazyka T-SQL.

Pokud jsou spuštěné analýzy dat ve velkém měřítku s složitých dotazů a pomocí Parallel Data Warehouse (PDW), Teradata nebo jiných masivně paralelní procesoru (MPP)) datové sklady SQL Data Warehouse může být nejlepší volbou.
  
## <a name="sql-database-hyperscale-compute-questions"></a>SQL Database Hyperškálovatelný výpočetní dotazy

### <a name="can-i-pause-my-compute-at-any-time"></a>Můžete pozastavit výpočetní operace Moje kdykoli

Ne.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Můžete zřídit výpočetní prostředí s další paměti RAM pro úlohy náročné na paměť

Ne. Chcete-li získat více paměti RAM, budete muset upgradovat na vyšší výpočetní velikost. Gen4 hardware poskytuje víc paměti RAM ve srovnání s Gen5 hardwaru. Další informace najdete v tématu [velkokapacitní úložiště a objem výpočtů velikostí](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-preview).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Můžete zřídit několika výpočetních uzlech různých velikostí

Ne.

### <a name="how-many-read-scale-replicas-are-supported"></a>Počet replik škálování pro čtení jsou podporované.

Ve verzi public preview jsou vytvořeny databáze Hyperškálovatelného s jedna replika škálování pro čtení (dvě repliky celkem) ve výchozím nastavení. Pokud chcete přidat nebo odebrat repliky škálování pro čtení, požádejte prosím prostřednictvím žádosti o podporu pomocí webu Azure portal.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Pro zajištění vysoké dostupnosti můžu potřebujete ke zřízení dalších výpočetních uzlů

V databázích hyperškálovatelný systém vysoké dostupnosti najdete na úrovni úložiště. Potřebujete jenom jednu repliku pro zajištění vysoké dostupnosti. Když výpočetní repliky je vypnutý, novou repliku se vytvoří automaticky bez ztráty.

Pokud existuje pouze jedna replika, ale může trvat nějakou dobu sestavení místní mezipaměti v nové repliky po převzetí služeb při selhání. Během fáze opětovné sestavení mezipaměti databáze načte data přímo ze stránky servery, výsledkem je důvodem sníženého výkonu vstupně-výstupních operací a dotazu.

Pro kritické aplikace, které vyžadují vysokou dostupnost měli byste zřídit aspoň 2 výpočetních uzlů, včetně primární výpočetního uzlu (výchozí). Tímto způsobem hot standby není k dispozici v případě selhání.

## <a name="data-size-and-storage-questions"></a>Otázky ohledně velikosti a skladování dat

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Co je podporovaná s Hyperškálováním databáze SQL db maximální velikost

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Jaká je velikost protokolu transakcí s Hyperškálováním

Transakční protokol s Hyperškálováním je téměř nekonečné. Nemusíte starat o nedostatku místa protokolu na systému, který má protokol vysokou propustnost. Frekvence generování protokolu může však omezit průběžné agresivní úlohy. Maximální a průměrná protokolu generování kurzu není ještě známý (stále ve verzi preview).

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Moje dočasné databáze škálovat s růstem databáze

Vaše `tempdb` databáze se nachází na místní úložiště SSD a je nakonfigurován na základě velikosti výpočetní prostředky, který zřídíte. Vaše `tempdb` je optimalizované a rozloží zajištění maximálně zvýšit efektivitu. `tempdb` Velikost není Konfigurovatelný a je pro vás spravuje služba subsystémů úložiště.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Provede svoji velikost databáze automaticky zvětšovat a muset spravovat velikost datových souborů

Velikost vaší databáze automaticky rozšíří, jak můžete vložit/ingestovat další data.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Co je nejmenší velikost databáze, která podporuje Hyperškálovatelného databáze SQL nebo začíná

5 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>Jaké přírůstky svoji velikost databáze zvětšit

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>Místní nebo vzdálený, je úložiště v SQL Database Hyperškálovatelného

V Hyperškálovacím jsou datové soubory uložené v Azure storage úrovně standard. Data je často ukládat do mezipaměti na místní úložiště SSD, na počítačích blízko výpočetních uzlů. Kromě toho mají výpočetní uzly mezipaměť na místní SSD a v paměti (fond vyrovnávacích pamětí a tak dále), abyste snížili četnost načítání dat ze vzdáleného uzlů.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Můžete spravovat nebo definovat soubory nebo skupiny souborů s Hyperškálováním

Ne
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Můžete zřídit pevný limit na nárůst dat pro moje databáze

Ne

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Jak jsou datové soubory rozloženy s Hyperškálováním databáze SQL

Datové soubory jsou ovládaná stránky servery. S růstem velikost dat, se přidají datové soubory a související stránky uzly serveru.

### <a name="is-database-shrink-supported"></a>Je zmenšit databázi podporováno

Ne

### <a name="is-database-compression-supported"></a>Je podporována komprese databáze

Ano

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Pokud mám velké tabulky, data tabulky získat rozprostřete napříč několika datových souborů

Ano. Stránky data související s danou tabulku můžete skončit ve více datových souborů, které jsou všechny jsou součástí stejné skupiny souborů. Používá systém SQL Server [proporčního vyplnění strategie](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) distribuovat data přes datových souborů.

## <a name="data-migration-questions"></a>Otázky ohledně migrace dat

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Můžete přesunout mé existující databáze Azure SQL na vrstvu služby hyperškálovatelný systém

Ano. Můžete přesunout vaše stávající databáze Azure SQL na hyperškálovatelný systém. Ve verzi public preview Toto je jednosměrná migrace. Nelze přesunout databáze z Hyperškálovatelného na jinou úroveň služby. Doporučujeme vytvořit kopii provozní databáze a migrace na Hyperškálovatelného pro testování konceptů (upozorníme).
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Můžete přesunout Moje Hyperškálovatelného databáze do jiné edice

Ne. Ve verzi public preview nejde přesunout databázi Hyperškálovatelného na jinou vrstvu služby.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Ztratit všechny možnosti nebo funkce po migraci na vrstvu služby hyperškálovatelný systém

Ano. Některé funkce služby Azure SQL Database nepodporuje Hyperškálovatelného ve verzi public preview, včetně, ale nikoli výhradně transparentní šifrování dat a dlouho období uchování zálohy. Po dokončení migrace databází do Hyperškálovatelného tyto funkce přestanou fungovat.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Můžu přesunout místní databázi systému SQL Server nebo virtuální počítač databázi systému SQL Server na hyperškálovatelný systém

Ano. Všechny existující technologie, které migrace můžete použít k migraci do hyperškálovatelný systém, včetně souboru BACPAC, transakční replikace, načítání dat logické. Viz také [Azure Database Migration Service](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Co je Moje prostoje během migrace z místní nebo v prostředí virtuálního počítače na hyperškálovatelný systém a jak ho můžete minimalizovat

Výpadek je stejný jako výpadek při migraci databází do jediné databáze ve službě Azure SQL Database. Můžete použít [transakční replikace](replication-to-sql-database.md#data-migration-scenario
) minimalizovat prostoje migrace databází až po několik TB velikosti. Pro velmi rozsáhlé databáze (více než 10 TB), můžete zvážit k migraci dat pomocí ADF, Spark nebo jiné technologie pro přesun dat.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Kolik času by ho zkuste umožňuje přinést si X množství dat do SQL Database Hyperškálovatelného

Ještě není známý (stále ve verzi preview)

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Může číst data z úložiště objektů blob a také rychlé zatížení (jako je Polybase a SQL Data Warehouse)

Můžete číst data ze služby Azure Storage a načíst načtení dat do databáze Hyperškálovatelného (stejně jako vám pomůžou s regulární izolované databáze). Polybase se aktuálně nepodporuje v Azure SQL Database. Můžete provést pomocí Polybase [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) nebo spuštění úlohy Spark job [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) s [konektor Spark pro SQL](sql-database-spark-connector.md). Konektor Spark SQL podporuje příkaz bulk insert.

Jednoduché obnovení nebo hromadné protokolování modelu se nepodporuje v Hyperškálovacím. Úplný model obnovení se vyžaduje pro zajištění vysoké dostupnosti. Však nabízí lepší hyperškálovatelný systém pro příjem rychlost v porovnání s izolovanou databázi z důvodu novou architekturu protokolu.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>Umožňuje SQL Database Hyperškálovatelného zřizování více uzlů pro příjem velkých objemů dat

Ne. Velkokapacitní databáze SQL je architektura SMP a není asymetrického multiprocesingem nebo architekturu s několika hlavními databázemi. Můžete vytvořit pouze několik replik pro horizontální navýšení kapacity úloh jen pro čtení.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Co je nejstarší SQL Server se SQL Database Hyperškálovatelného nepodporují migraci z verze

SQL Server 2005. Další informace najdete v tématu [migrovat izolovanou databázi nebo databázi ve fondu](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Problémy s kompatibilitou, naleznete v tématu [řešení problémů s kompatibilitou databáze migrace](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>Velkokapacitní SQL Database podporuje migraci z jiných zdrojů dat, jako je například programu Aurora, MySQL, Oracle, DB2 a další platformy databáze

Ano. Pocházejí z různých zdrojů dat jiné než SQL Server vyžaduje logickou migrace. Můžete použít [Azure Database Migration Service](../dms/dms-overview.md) logické migrace.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Obchodní kontinuity podnikových procesů a po havárii obnovení otázky

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Co smlouvy SLA jsou k dispozici pro mírou škálování databáze

Obecně platí Smlouva SLA není k dispozici ve verzi public preview. Velkokapacitní však nabízí stejnou úroveň Vysoká dostupnost s aktuálně dostupných nabídek SQL DB. Zobrazit [SLA](https://azure.microsoft.com/support/legal/sla/).

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Zálohování databáze spravují mi ve službě Azure SQL Database

Ano

### <a name="how-often-are-the-database-backups-taken"></a>Jak často jsou přijata zálohy databáze

Nejsou žádné tradiční úplného, rozdílového a zálohy protokolů pro databáze SQL Database Hyperškálovatelného. Místo toho jsou pravidelné snímky datových souborů a protokolu, který je generován zůstane jednoduše je pro dobu uchování nakonfigurované nebo k dispozici.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>Obnovení k časovému okamžiku podporu Hyperškálovatelného databáze SQL

Ano

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Co je cíl bodu obnovení (RPO) a cíl času obnovení (RTO) pomocí zálohování a obnovení v Hyperškálovacím databáze SQL

Plánovaný bod obnovení je 0 min. Cílem RTO je menší než 10 minut, bez ohledu na velikost databáze. Ve verzi public preview, ale může docházet delší dobu obnovení.

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Vliv zálohováním databází velký výpočetní výkon na mé primární

Ne. Zálohy se spravují přes subsystému úložiště a využívat snímky. Uživatelské zatížení na primární, nemají vliv.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Můžete provést geografické obnovení s Hyperškálovacím databáze SQL database

Ne, není ve verzi public preview.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Můžete nastavit geografickou replikaci s Hyperškálováním databáze SQL database

Ne, není ve verzi public preview.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Moje sekundární výpočetních uzlů můžete získat geograficky replikované s Hyperškálováním databáze SQL

Ne, není ve verzi public preview.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Můžete vytvořit zálohu databáze SQL Database Velkokapacitní a obnovíte své místní server nebo SQL Server na virtuálním počítači

Ne. Formát úložiště pro databáze hyperškálovatelný systém se liší od tradiční SQL serveru a nemáte řídit zálohy nebo k nim měli přístup. Abyste mohli data z databáze SQL Database Hyperškálovatelného, používat službu export nebo skriptování plus BCP.

## <a name="cross-feature-questions"></a>Různé funkce otázky

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Ztratit všechny možnosti nebo funkce po migraci na vrstvu služby hyperškálovatelný systém

Ano. Některé funkce služby Azure SQL Database nepodporuje Hyperškálovatelného ve verzi public preview, včetně, ale nikoli výhradně transparentní šifrování dat a dlouho období uchování zálohy. Po dokončení migrace databází do Hyperškálovatelného tyto funkce přestanou fungovat.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Polybase se práce s Hyperškálováním databáze SQL

Ne. Polybase nepodporuje v Azure SQL Database.

### <a name="does-the-compute-have-support-for-r-and-python"></a>Má výpočetní podpoře R a pythonu

Ne. R a Python nejsou podporovány ve službě Azure SQL Database.

### <a name="are-the-compute-nodes-containerized"></a>Jsou výpočetní uzly kontejnerizovaných

Ne. Vaše databáze se nachází na výpočetní výkon virtuálních počítačů a není kontejner.

## <a name="performance-questions"></a>Otázky výkonu

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Jakou propustnost můžete odeslat na největší výpočetní Hyperškálovatelného databáze SQL

Ještě není známý (stále ve verzi preview)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Kolik vstupně-výstupních operací získat největší výpočtově Hyperškálovatelného databáze SQL

Ještě není známý (stále ve verzi preview)

### <a name="does-my-throughput-get-affected-by-backups"></a>Moje propustnost získat ovlivněny zálohy

Ne. Výpočetní prostředky, je oddělený od úložné vrstvy, aby se zabránilo dopadu na výpočetní prostředky.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Moje propustnost získat ovlivněna jsem zřízení dalších výpočetních uzlů

Protože sdílené úložiště a neexistuje žádné přímé fyzické replikace děje mezi primárním a sekundárním výpočetních uzlů, technicky vzato propustnosti na primárním uzlu ovlivní tak, že přidáte uzly škálování pro čtení. Ale jsme může omezit průběžné agresivní pracovního vytížení k povolení přihlášení použít na servery stránky dohnat a sekundární uzly a vyhnout se chybné výkonu při čtení na sekundární uzly.

## <a name="scalability-questions"></a>Otázky týkající se škálovatelnosti

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Jak dlouho bude trvat škálování nahoru a dolů výpočetním uzlu

Několik minut

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Probíhá škálování nahoru/dolů operace je Moje databáze v režimu offline

Ne. Škálování nahoru a dolů bude online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>By měla očekávat přímé připojení, když probíhají operace škálování

Škálování směrem nahoru nebo dolů výsledky v existující připojení probíhá vyřazování při převzetí služeb při selhání se stane s výpočetním uzlu s cílovou velikost. Přidání další repliky nemá za následek připojení.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>Je škálování nahoru a dolů automatické výpočetních uzlů nebo koncový uživatel aktivuje operace

Koncový uživatel. Nepodporuje automatické.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>Nemá Moje `tempb` také růst výpočetní výkon je škálovaný

Ano. Dočasné databáze se škálují automaticky, roste tak výpočetní prostředky.  

### <a name="can-i-provision-multiple-primary-computes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Můžete zřídit několik primární výpočetní prostředí, jako je například více hlavních systému, kde můžete několika vedoucích primární výpočetní jednotka vyšší úroveň souběžnosti

Ne. Jenom primární výpočetním uzlu přijímá požadavky na čtení/zápis. Sekundární výpočetních uzlů přijímat pouze požadavky jen pro čtení.

## <a name="read-scale-questions"></a>Dotazy škálování pro čtení

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Kolik sekundární výpočetních uzlů můžete zřídit

Ve verzi public preview vytvoříme 2 repliky pro databáze Hyperškálovatelného ve výchozím nastavení. Pokud chcete nastavit počet replik, požádejte prosím prostřednictvím žádosti o podporu pomocí webu Azure portal.

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Jak připojit k těmto sekundární výpočetních uzlů

Můžete připojit k tyto uzly další výpočetní prostředky jen pro čtení tak, že nastavíte `ApplicationIntent` argument u připojovacího řetězce k `read_only`. Všechna připojení označené `read-only` se automaticky směrují na jednom z uzlů další výpočetní prostředky jen pro čtení.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Můžete vytvořit vyhrazený koncový bod pro repliku škálování pro čtení

Ne. Ve verzi public preview, můžete připojit jenom k replice škálování pro čtení tak, že zadáte `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Dělá systém Vyrovnávání zatížení inteligentní čtení úloh

Ne. Čtení pouze úloha je ve verzi preview, znovu orientovaného na náhodných repliku škálování pro čtení.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Můžete škálovat nahoru/dolů sekundární výpočetních uzlů bez ohledu na jejich primárních výpočetní

Ne, není ve verzi public preview.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Pro můj primární výpočty a Moje sekundární dalších výpočetních uzlů získat velikosti různé dočasné databáze

Ne. Vaše `tempdb` je nakonfigurovaný podle zřizování výpočetních velikost, ve verzi public preview, sekundární výpočetních uzlů mají stejnou velikost jako primární výpočty.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Můžete přidat zobrazení na můj sekundární indexy a výpočetních uzlů

Ne. Hyperškálovatelného databáze měla sdílené úložiště, což znamená, že všechny výpočetní uzly najdete stejné tabulky, indexy a zobrazení. Pokud chcete další indexy, které jsou optimalizované pro operace čtení na sekundární – je nutné přidat je na primárním nejprve.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Kolik zpoždění se chystají se mezi primárním a sekundárním výpočetního uzlu

Od doby, kdy je transakce potvrzeny při primární, v závislosti na míru generování protokolu může jí být buď zajistí okamžité vynucování nebo s nízkou milisekund.

## <a name="next-steps"></a>Další postup

Další informace o Hyperškálovatelného úroveň služby, najdete v části [Hyperškálovatelného úroveň služby (preview)](sql-database-service-tier-hyperscale.md).
