---
title: Porovnání funkcí Azure SQL Database | Microsoft Docs
description: Tento článek obsahuje porovnání funkcí systému SQL Server, které jsou k dispozici v různých typů databáze SQL Azure.
services: sql-database
author: jovanpop-msft
ms.reviewer: bonova, carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: de85a555def31d164f2cda5c6c6bc9fae9fb5a7e
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309278"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Porovnání funkcí: Azure SQL Database a SQL Server 

Azure SQL Database sdílí společný kód základní s SQL serverem. Funkce systému SQL Server nepodporuje Azure SQL Database, závisí na typu databáze Azure SQL, který vytvoříte. S Azure SQL Database, můžete buď vytvořit databázi jako součást [spravované instance](sql-database-managed-instance.md) (momentálně ve verzi public preview) nebo můžete vytvořit databázi, která je součástí logického serveru a volitelně umístěný v Elastickém fondu. 

Microsoft dál pro přidání funkcí do Azure SQL Database. Naleznete na webové stránce služby aktualizace pro Azure nejnovější aktualizace pomocí tyto filtry:

* Filtrování na [službu SQL Database](https://azure.microsoft.com/updates/?service=sql-database)
* Filtrování na [oznámení všeobecné dostupnosti ](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) pro funkce služby SQL Database

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Podpora funkcí systému SQL Server v Azure SQL Database

V následující tabulce jsou uvedeny hlavní funkce systému SQL Server a poskytuje informace o tom, jestli tyto funkce částečně nebo zcela podporuje a odkaz na další informace o funkci. 

| **Funkce SQL** | **Podporované v databázi nebo logického serveru Azure SQL** | **Podporované v Azure SQL Database nebo spravované Instance (preview)** |
| --- | --- | --- |
| [Funkce Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Ano – viz [úložiště certifikátů](sql-database-always-encrypted.md) a [Key vault](sql-database-always-encrypted-azure-key-vault.md) | Ano – viz [úložiště certifikátů](sql-database-always-encrypted.md) a [Key vault](sql-database-always-encrypted-azure-key-vault.md) |
| [Always On skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Vysoká dostupnost](sql-database-high-availability.md) je součástí každé databázi. Zotavení po havárii je popsána v [přehled kontinuity podnikových procesů s Azure SQL Database](sql-database-business-continuity.md) | [Vysoká dostupnost](sql-database-high-availability.md) je součástí každé databázi. Zotavení po havárii je popsána v [přehled kontinuity podnikových procesů s Azure SQL Database](sql-database-business-continuity.md) |
| [Připojit databázi](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Ne | Ne |
| [Aplikační role](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Ano | Ano |
|[Auditování](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Ano](sql-database-auditing.md)| [Ano](sql-database-managed-instance-auditing.md) |
| [Automatické zálohování](sql-database-automated-backups.md) | Ano | Ano |
| [Automatické ladění (vynucení plánu)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ano](sql-database-automatic-tuning.md)| [Ano](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Automatické ladění (indexy)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ano](sql-database-automatic-tuning.md)| Ne |
| [Soubor souboru BACPAC (exportovat)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Ano – najdete v části [export databáze SQL](sql-database-export.md) | Ne |
| [Soubor souboru BACPAC (Importovat)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Ano – najdete v části [import databáze SQL](sql-database-import.md) | Ne |
| [Příkaz BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Ne, najdete v článku pouze spouštěná systému automatické zálohování - [automatizované zálohování](sql-database-automated-backups.md) | Spouštěná systému zálohy pro automatické a uživatel inicioval pouze kopie zálohy – viz [zálohování rozdíly](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Integrované funkce](https://docs.microsoft.com/sql/t-sql/functions/functions) | Většina - jednotlivých funkcí najdete v části | Ano – najdete v části [uložené procedury, funkce, aktivuje rozdíly](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Funkce Change data capture](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Ne | Ano |
| [Sledování změn](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Ano |Ano |
| [Kolace příkazy](https://docs.microsoft.com/sql/t-sql/statements/collations) | Ano | Ano |
| [Indexy Columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Ano – [úroveň Premium, úrovně Standard - S3 a vyšší úroveň obecné účely a kritické obchodní vrstvy](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Ano |
| [Modul common language runtime (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Ne | Ano – najdete v části [rozdíly CLR](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Databáze s omezením](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Ano | Ano |
| [Uživatelů s omezením](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Ano | Ano |
| [Řízení toku klíčová slova jazyka](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Ano | Ano |
| [Mezi databázové dotazy](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Ne – najdete v části [elastické dotazy](sql-database-elastic-query-overview.md) | Ano, plus [elastické dotazy](sql-database-elastic-query-overview.md) |
| [Transakce mezi databázemi](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Ne | Ano – najdete v části [propojené rozdíly serveru](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#linked-servers) |
| [Kurzory](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Ano |Ano | 
| [Komprese dat](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Ano |Ano |
| [Databázového e-mailu](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Ne | Ano |
| [Služba migrace dat (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Ano | Ano |
| [Zrcadlení databáze](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Ne | Ne |
| [Nastavení konfigurace databáze](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Ano | Ano |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Ne | Ne |
| [Snímky databáze](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Ne | Ne |
| [Datové typy](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Ano |Ano |
| [Příkaz DBCC příkazy](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Většina - najdete jednotlivé příkazy | Ano – najdete v části [DBCC rozdíly](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [Příkazy DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Většina - najdete jednotlivé příkazy | Ano – najdete v části [rozdíly T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Aktivační události DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Pouze pro databázi |  Ano |
| [Zobrazení distribuovaných oddílu](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Ne | Ano |
| [Distribuované transakce - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Ne – najdete v části [elastické transakce](sql-database-elastic-transactions-overview.md) |  Ne – najdete v části [elastické transakce](sql-database-elastic-transactions-overview.md) |
| [Příkazy DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Ano | Ano |
| [Aktivační události DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | Většina - najdete jednotlivé příkazy |  Ano |
| [Zobrazení dynamické správy](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Většina - najdete v části jednotlivých zobrazení dynamické správy |  Ano – najdete v části [rozdíly T-SQL](sql-database-managed-instance-transact-sql-information.md) |
|[Dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Ano](sql-database-dynamic-data-masking-get-started.md)| [Ano](sql-database-dynamic-data-masking-get-started.md) |
| [Elastické fondy](sql-database-elastic-pool.md) | Ano | Předdefinované – jedna Instance pro spravované může mít více databází, které sdílejí stejnou fondu prostředků |
| [Oznamování událostí](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Ne – najdete v části [výstrahy](sql-database-insights-alerts-portal.md) | Ano |
| [Výrazy](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Ano | Ano |
| [Rozšířené události](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Některé - najdete v části [rozšířené události v databázi SQL](sql-database-xevent-db-diff-from-svr.md) | Ano – najdete v části [rozšířené události rozdíly ](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Rozšířené uložené procedury](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Ne | Ne |
[Soubory a skupiny souborů](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Pouze skupiny primární soubor | Ano |
| [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Ne | Ne |
| [Fulltextové vyhledávání](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Dělení textu třetích stran nejsou podporovány. |Dělení textu třetích stran nejsou podporovány. |
| [Functions](https://docs.microsoft.com/sql/t-sql/functions/functions) | Většina - jednotlivých funkcí najdete v části | Ano – najdete v části [uložené procedury, funkce, aktivuje rozdíly](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [geografické obnovení](sql-database-recovery-using-backups.md#geo-restore) | Ano | Ne – můžete obnovit COPY_ONLY najdete úplné zálohy, které můžete provést pravidelně - [zálohování rozdíly](sql-database-managed-instance-transact-sql-information.md#backup) a [obnovit rozdíly](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Geografická replikace](sql-database-geo-replication-overview.md) | Ano | Ne |
| [Zpracování grafu](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Ano | Ano |
| [Optimalizace v paměti](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Ano – [Premium a kritické obchodní vrstvy](sql-database-in-memory.md) | Ne |
| [Podpora data JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Ano](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) | [Ano](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) |
| [Jazykové elementy](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Většina - najdete jednotlivé elementy |  Ano – najdete v části [rozdíly T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Odkazované servery](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Ne – najdete v části [elastické dotazu](sql-database-elastic-query-horizontal-partitioning.md) | Pouze pro SQL Server a databáze SQL |
| [Přesouvání protokolu](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Vysoká dostupnost](sql-database-high-availability.md) je součástí každé databázi. Zotavení po havárii je popsána v [přehled kontinuity podnikových procesů s Azure SQL Database](sql-database-business-continuity.md) |[Vysoká dostupnost](sql-database-high-availability.md) je součástí každé databázi. Zotavení po havárii je popsána v [přehled kontinuity podnikových procesů s Azure SQL Database](sql-database-business-continuity.md) |
| [Služby Master Data Services (služby MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Ne | Ne |
| [Minimální úroveň protokolování v hromadného importu](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Ne | Ne |
| [Úprava dat systému](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Ne | Ano |
| [Online operace indexu](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Ano | Ano |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Ne|Ano – najdete v části [rozdíly T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Ano|Ano|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Ne|Ano – najdete v části [rozdíly T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Ne|Ano – najdete v části [rozdíly T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Ano|Ano|
| [Operátory](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Většina - najdete jednotlivé operátory |Ano – najdete v části [rozdíly T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Dělení na oddíly](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Ano | Ano |
| [Obnovení bodu v čase databáze](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Ano – najdete v části [obnovení databáze SQL](sql-database-recovery-using-backups.md#point-in-time-restore) | Ano – najdete v části [obnovení databáze SQL](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Ne | Ne |
| [Na základě zásad správy](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Ne | Ne |
| [Predikáty](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Ano | Ano |
| [Služby R](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Verze Preview; v tématu [co je nového v machine learning](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Ne |
| [Správce zdrojů](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Ne | Ano |
| [OBNOVENÍ příkazů](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Ne | Ano – najdete v části [obnovit rozdíly](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Obnovit databázi ze zálohy](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Z automatizované zálohování jenom - najdete na stránce [obnovení databáze SQL](sql-database-recovery-using-backups.md) | Z automatizované zálohování - najdete na stránce [obnovení databáze SQL](sql-database-recovery-using-backups.md) a z úplné zálohy - najdete na stránce [zálohování rozdíly](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Ano | Ano |
| [Sémantické vyhledávání](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Ne | Ne |
| [Pořadová čísla](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Ano | Ano |
| [Služba Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Ne | Ano – najdete v části [rozdíly služby Service Broker](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Nastavení konfigurace serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Ne | Ano – najdete v části [rozdíly T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Set – příkazy](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Většina - najdete jednotlivé příkazy | Ano – najdete v části [rozdíly T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Ano | Ano |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Ano | Ano |
| [Synchronizaci dat SQL](sql-database-get-started-sql-data-sync.md) | Ano | Ne |
| [SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/what-is) | Ano | Ano |
| [Agent SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Ne – najdete v části [elastické úlohy](sql-database-elastic-jobs-getting-started.md) | Ano – najdete v části [rozdíly agenta systému SQL Server](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Ne – najdete v části [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Ne – najdete v části [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [Auditování SQL serveru](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Ne – najdete v části [auditování databáze SQL](sql-database-auditing.md) | Ano – najdete v části [auditování rozdíly](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)] (https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Ano | Ano |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Ano, s spravované SSIS v prostředí Azure Data Factory (ADF), kde jsou uložené balíčky v SSISDB hostované ve službě Azure SQL Database a spouštět v [Runtime integrace Azure SSIS](../data-factory/tutorial-deploy-ssis-packages-azure-powershell.md). | Ano, s spravované SSIS v prostředí Azure Data Factory (ADF), kde jsou uložené balíčky v SSISDB hostované spravované Instance a spouštět v [Runtime integrace Azure SSIS](../data-factory/tutorial-deploy-ssis-packages-azure-powershell.md). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Ano | Ano |
| [Prostředí PowerShell pro Server SQL](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Ano | Ano |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Ne – najdete v části [rozšířené události](sql-database-xevent-db-diff-from-svr.md) | Ano |
| [Replikace SQL serveru](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Jenom předplatitelé transakční replikace a replikace snímků](sql-database-cloud-migrate.md) | Ne |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Ne – [najdete v Power BI](https://docs.microsoft.com/power-bi/) | Ne – [najdete v Power BI](https://docs.microsoft.com/power-bi/) |
| [Uložené procedury](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Ano | Ano |
| [Systémové uložené funkce](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Většina - jednotlivých funkcí najdete v části | Ano – najdete v části [uložené procedury, funkce, aktivuje rozdíly](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Systémové uložené procedury](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Některé - najdete v části jednotlivých uložené procedury | Ano – najdete v části [uložené procedury, funkce, aktivuje rozdíly](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Systémové tabulky](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Některé - najdete v části jednotlivé tabulky | Ano – najdete v části [rozdíly T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Zobrazení katalogu systému](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Některé – viz jednotlivých zobrazení | Ano – najdete v části [rozdíly T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Dočasné tabulky](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Místní a obor databáze globální dočasné tabulky. | Místní a obor instance globální dočasné tabulky. |
| [Dočasné tabulky](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Ano](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) | [Ano](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) |
|Detekce hrozeb|  [Ano](sql-database-threat-detection.md)|[Ano](sql-database-managed-instance-threat-detection.md)|
| [Příznaky trasování](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Ne | Ne |
| [Proměnné](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Ano | Ano |
| [Transparentní šifrování dat (šifrování TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Ano | Partial pouze s službu spravovat šifrování |
[Virtuální síť](../virtual-network/virtual-networks-overview.md) | Částečné - najdete v části [koncové body virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) | Ano, pouze modelu Resource Manager |
| [Windows Server Failover Clustering](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [Vysoká dostupnost](sql-database-high-availability.md) je součástí každé databázi. Zotavení po havárii je popsána v [přehled kontinuity podnikových procesů s Azure SQL Database](sql-database-business-continuity.md) | [Vysoká dostupnost](sql-database-high-availability.md) je součástí každé databázi. Zotavení po havárii je popsána v [přehled kontinuity podnikových procesů s Azure SQL Database](sql-database-business-continuity.md) |
| [Indexy XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Ano | Ano |

## <a name="next-steps"></a>Další postup

- Informace o službě Azure SQL Database najdete v tématu [Co je SQL Database?](sql-database-technical-overview.md)
- Informace o instanci spravované najdete v tématu [co je Instance spravované?](sql-database-managed-instance.md).
