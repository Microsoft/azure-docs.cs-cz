---
title: Porovnání funkcí služby Azure SQL Database | Dokumentace Microsoftu
description: Tento článek porovnává funkce serveru SQL Server, které jsou k dispozici v různých verzí Azure SQL Database.
services: sql-database
author: jovanpop-msft
ms.reviewer: bonova, carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 08/14/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: 75bd73b16d3e8d2e6e66cf518ab9694c945526cc
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604684"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Porovnání funkcí: Azure SQL Database a SQL Server 

Azure SQL Database sdílí společný základ kódu s využitím SQL serveru. Funkce SQL serveru podporovanou nástrojem Azure SQL Database závisí na typu Azure SQL database, která vytvoříte. S Azure SQL Database, můžete buď vytvořit databázi jako součást [spravovanou instanci](sql-database-managed-instance.md) (aktuálně ve verzi public preview) nebo můžete vytvořit databázi, která je součástí logického serveru a volitelně umístěný v Elastickém fondu. 

Microsoft nadále přidává funkce do služby Azure SQL Database. Přejděte na webovou stránku aktualizace služeb pro Azure pro nejnovější aktualizace přes tyto filtry:

* Filtrování na [službu SQL Database](https://azure.microsoft.com/updates/?service=sql-database)
* Filtrování na [oznámení všeobecné dostupnosti ](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) pro funkce služby SQL Database

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Podpora pro funkce systému SQL Server ve službě Azure SQL Database

Následující tabulky uvádějí hlavní funkce systému SQL Server a poskytuje informace o tom, jestli tato funkce je částečně nebo zcela podporovaná a odkaz na další informace o funkci. 

| **Funkce SQL** | **Podporované v Azure SQL Database/logického serveru** | **Podporované v Azure SQL Database/spravované Instance (preview)** |
| --- | --- | --- |
| [Funkce Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Ano – viz [úložiště certifikátů](sql-database-always-encrypted.md) a [služby Key vault](sql-database-always-encrypted-azure-key-vault.md) | Ano – viz [úložiště certifikátů](sql-database-always-encrypted.md) a [služby Key vault](sql-database-always-encrypted-azure-key-vault.md) |
| [Skupiny dostupnosti AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Vysoká dostupnost](sql-database-high-availability.md) je zahrnuta v každé databázi. Zotavení po havárii je podrobněji popsána [přehled kontinuity obchodních procesů ve službě Azure SQL Database](sql-database-business-continuity.md) | [Vysoká dostupnost](sql-database-high-availability.md) je zahrnuta v každé databázi. Zotavení po havárii je podrobněji popsána [přehled kontinuity obchodních procesů ve službě Azure SQL Database](sql-database-business-continuity.md) |
| [Připojení databáze](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Ne | Ne |
| [Aplikační role](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Ano | Ano |
|[Auditování](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Ano](sql-database-auditing.md)| [Ano](sql-database-managed-instance-auditing.md) |
| [Automatické zálohování](sql-database-automated-backups.md) | Ano | Ano |
| [Automatické ladění (vynucení plánu)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ano](sql-database-automatic-tuning.md)| [Ano](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Automatické ladění (indexy)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ano](sql-database-automatic-tuning.md)| Ne |
| [Soubor BACPAC (export)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Ano – viz [export databáze SQL](sql-database-export.md) | Ne |
| [Soubor BACPAC (import)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Ano – viz [import databáze SQL](sql-database-import.md) | Ne |
| [Příkaz BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Ne, najdete v článku pouze systém automatických záloh spouštěných - [automatické zálohování](sql-database-automated-backups.md) | Systémem vyvolaného automatizovaných záloh a uživatel inicioval pouze kopie zálohy – viz [zálohování rozdíly](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Předdefinované funkce](https://docs.microsoft.com/sql/t-sql/functions/functions) | Většina – viz jednotlivých funkcí | Ano – viz [uložené procedury, funkce, aktivační události rozdíly](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Funkce Change data capture](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Ne | Ano |
| [Sledování změn](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Ano |Ano |
| [Příkazy kolace](https://docs.microsoft.com/sql/t-sql/statements/collations) | Ano | Ano |
| [Indexy Columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Ano – [úroveň Premium, úrovně Standard – S3 a vyšší úrovni General Purpose a pro důležité obchodní informace úrovně](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Ano |
| [Common language runtime (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Ne | Ano – viz [rozdíly CLR](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Databáze s omezením](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Ano | Ano |
| [Uživatelé s omezením](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Ano | Ano |
| [Ovládací prvek klíčových slov toku jazyka](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Ano | Ano |
| [Mezidatabázové dotazy](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Ne – viz [elastické dotazy](sql-database-elastic-query-overview.md) | Ano, plus [elastické dotazy](sql-database-elastic-query-overview.md) |
| [Transakce mezi databázemi](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Ne | Ano – viz [propojený server rozdíly](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#linked-servers) |
| [Kurzory](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Ano |Ano | 
| [Komprese dat](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Ano |Ano |
| [Databázového e-mailu](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Ne | Ano |
| [Data Migration Service (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Ano | Ano |
| [Zrcadlení databáze](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Ne | Ne |
| [Nastavení konfigurace databáze](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Ano | Ano |
| [Služby data Quality Services (DQS –)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Ne | Ne |
| [Snímky databáze](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Ne | Ne |
| [Datové typy](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Ano |Ano |
| [Příkazy DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Většina – viz jednotlivé příkazy | Ano – viz [DBCC rozdíly](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [Příkazy DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Většina – viz jednotlivé příkazy | Ano – viz [rozdíly v jazyce T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Triggery DDS](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Jenom databáze |  Ano |
| [Oddíl distribuovaných zobrazení](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Ne | Ano |
| [Distribuované transakce - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Ne – viz [elastické transakce](sql-database-elastic-transactions-overview.md) |  Ne – viz [elastické transakce](sql-database-elastic-transactions-overview.md) |
| [Příkazy DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Ano | Ano |
| [Triggery DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | Většina – viz jednotlivé příkazy |  Ano |
| [Zobrazení dynamické správy](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Většina – viz jednotlivá zobrazení DMV |  Ano – viz [rozdíly v jazyce T-SQL](sql-database-managed-instance-transact-sql-information.md) |
|[Dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Ano](sql-database-dynamic-data-masking-get-started.md)| [Ano](sql-database-dynamic-data-masking-get-started.md) |
| [Elastické fondy](sql-database-elastic-pool.md) | Ano | Předdefinované – jeden Managed Instance může mít víc databází, které sdílejí stejný fond prostředků |
| [Oznamování událostí](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Ne – viz [výstrahy](sql-database-insights-alerts-portal.md) | Ano |
| [Výrazy](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Ano | Ano |
| [Rozšířené události](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Některé – viz [rozšířených událostí ve službě SQL Database](sql-database-xevent-db-diff-from-svr.md) | Ano – viz [rozšířené události rozdíly ](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Rozšířené uložené procedury](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Ne | Ne |
[Soubory a skupiny souborů](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Jenom primární soubor skupiny | Ano |
| [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Ne | Ne |
| [Fulltextové vyhledávání](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Moduly pro dělení na slova třetích stran nejsou podporovány. |Moduly pro dělení na slova třetích stran nejsou podporovány. |
| [Functions](https://docs.microsoft.com/sql/t-sql/functions/functions) | Většina – viz jednotlivých funkcí | Ano – viz [uložené procedury, funkce, aktivační události rozdíly](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Geografické obnovení](sql-database-recovery-using-backups.md#geo-restore) | Ano | Ne – obnovení COPY_ONLY naleznete v tématu úplné zálohy, které pravidelně – provádí [zálohování rozdíly](sql-database-managed-instance-transact-sql-information.md#backup) a [obnovení rozdíly](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Geografická replikace](sql-database-geo-replication-overview.md) | Ano | Ne |
| [Zpracování grafů](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Ano | Ano |
| [Optimalizace v paměti](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Ano – [jenom úrovně Premium a pro důležité obchodní informace](sql-database-in-memory.md) | Ne |
| [Podpora dat JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Ano](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) | [Ano](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) |
| [Elementy jazyka](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Většina – viz jednotlivé elementy |  Ano – viz [rozdíly v jazyce T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Propojené servery](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Ne - naleznete v tématu [elastického dotazu](sql-database-elastic-query-horizontal-partitioning.md) | Pouze na serveru SQL Server a SQL Database |
| [Přesouvání protokolu](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Vysoká dostupnost](sql-database-high-availability.md) je zahrnuta v každé databázi. Zotavení po havárii je podrobněji popsána [přehled kontinuity obchodních procesů ve službě Azure SQL Database](sql-database-business-continuity.md) |[Vysoká dostupnost](sql-database-high-availability.md) je zahrnuta v každé databázi. Zotavení po havárii je podrobněji popsána [přehled kontinuity obchodních procesů ve službě Azure SQL Database](sql-database-business-continuity.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Ne | Ne |
| [Minimální protokolování v hromadném importu](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Ne | Ne |
| [Úprava dat systému](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Ne | Ano |
| [Online indexovací operace](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Ano | Ano |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Ne|Ano – viz [rozdíly v jazyce T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Ano|Ano|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Ne|Ano – viz [rozdíly v jazyce T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Ne|Ano – viz [rozdíly v jazyce T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Ano|Ano|
| [Operátory](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Většina – viz jednotlivé operátory |Ano – viz [rozdíly v jazyce T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Vytváření oddílů](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Ano | Ano |
| [Obnovení bodu v čase databáze](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Ano – viz [obnovení databáze SQL](sql-database-recovery-using-backups.md#point-in-time-restore) | Ano – viz [obnovení databáze SQL](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Ne | Ne |
| [Na základě zásad správy](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Ne | Ne |
| [Predikáty](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Ano | Ano |
| [Služby R](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Ve verzi Preview; Zobrazit [co je nového ve službě machine learning](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Ne |
| [Správce zdrojů](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Ne | Ano |
| [Příkazy RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Ne | Ano – viz [obnovení rozdíly](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Obnovit databázi ze zálohy](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Z automatizovaných záloh pouze – viz [obnovení databáze SQL](sql-database-recovery-using-backups.md) | Z automatizovaných záloh – viz [obnovení databáze SQL](sql-database-recovery-using-backups.md) a podívejte se, z úplné zálohy - [zálohování rozdíly](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Ano | Ano |
| [Sémantické vyhledávání](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Ne | Ne |
| [Pořadová čísla](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Ano | Ano |
| [Služba Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Ne | Ano – viz [rozdíly Service Broker](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Nastavení konfigurace serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Ne | Ano – viz [rozdíly v jazyce T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Příkazy Set](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Většina – viz jednotlivé příkazy | Ano – viz [rozdíly v jazyce T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Ano | Ano |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Ano | Ano |
| [Synchronizace dat SQL](sql-database-get-started-sql-data-sync.md) | Ano | Ne |
| [SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/what-is) | Ano | Ano |
| [Agent SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Ne - naleznete v tématu [Elastických úloh](sql-database-elastic-jobs-getting-started.md) | Ano – viz [rozdíly agenta systému SQL Server](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Ne – viz [služby Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Ne – viz [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [Auditování služby SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Ne – viz [auditování služby SQL Database](sql-database-auditing.md) | Ano – viz [auditování rozdíly](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)] (https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Ano | Ano |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Ano, pomocí spravované služby SSIS v Azure Data Factory (ADF) prostředí, kde balíčky ukládají do databáze SSISDB hostitelem je Azure SQL Database a spustit v Azure SSIS Integration Runtime (IR), najdete v článku [vytvořit prostředí Azure-SSIS IR ve službě ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Porovnání funkcí služby SSIS v SQL Database a Managed Instance, najdete v článku [porovnání SQL Database a spravované Instance (Preview)](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview). | Ano, pomocí spravované služby SSIS v Azure Data Factory (ADF) prostředí, kde balíčky ukládají do databáze SSISDB hostitelem Managed Instance a spustit v Azure SSIS Integration Runtime (IR), najdete v článku [vytvořit prostředí Azure-SSIS IR ve službě ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Porovnání funkcí služby SSIS v SQL Database a Managed Instance, najdete v článku [porovnání SQL Database a spravované Instance (Preview)](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Ano | Ano |
| [Prostředí PowerShell pro SQL Server](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Ano | Ano |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Ne – viz [rozšířené události](sql-database-xevent-db-diff-from-svr.md) | Ano |
| [Replikace systému SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Jenom předplatitelé transakční replikace a replikace snímků](sql-database-cloud-migrate.md) | Ano (Public preview) – [replikace pomocí služby SQL Database Managed Instance (Public preview)](http://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Ne - [podívat se na Power BI](https://docs.microsoft.com/power-bi/) | Ne - [podívat se na Power BI](https://docs.microsoft.com/power-bi/) |
| [Uložené procedury](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Ano | Ano |
| [Systémové uložené funkce](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Většina – viz jednotlivých funkcí | Ano – viz [uložené procedury, funkce, aktivační události rozdíly](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Systémové uložené procedury](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Některé – viz jednotlivé uložené procedury | Ano – viz [uložené procedury, funkce, aktivační události rozdíly](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Systémové tabulky](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Některé – viz jednotlivé tabulky | Ano – viz [rozdíly v jazyce T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Zobrazení systémového katalogu](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Některé – viz jednotlivá zobrazení | Ano – viz [rozdíly v jazyce T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Dočasné tabulky](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Místní a s rozsahem databáze globálních dočasných tabulek | Místní a rozsahem instanci globálních dočasných tabulek |
| [Dočasné tabulky](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Ano](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) | [Ano](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) |
|Detekce hrozeb|  [Ano](sql-database-threat-detection.md)|[Ano](sql-database-managed-instance-threat-detection.md)|
| [Příznaky trasování](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Ne | Ne |
| [Proměnné](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Ano | Ano |
| [Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Ano | Částečné pouze s šifrováním spravované služby |
[Virtuální síť](../virtual-network/virtual-networks-overview.md) | Částečně – viz [koncových bodů virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) | Ano, pouze model Resource Manageru |
| [Windows Server Failover Clustering s](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [Vysoká dostupnost](sql-database-high-availability.md) je zahrnuta v každé databázi. Zotavení po havárii je podrobněji popsána [přehled kontinuity obchodních procesů ve službě Azure SQL Database](sql-database-business-continuity.md) | [Vysoká dostupnost](sql-database-high-availability.md) je zahrnuta v každé databázi. Zotavení po havárii je podrobněji popsána [přehled kontinuity obchodních procesů ve službě Azure SQL Database](sql-database-business-continuity.md) |
| [XML indexy](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Ano | Ano |

## <a name="next-steps"></a>Další postup

- Informace o službě Azure SQL Database najdete v tématu [Co je SQL Database?](sql-database-technical-overview.md)
- Informace o Managed Instance najdete v tématu [co je Managed Instance?](sql-database-managed-instance.md).
