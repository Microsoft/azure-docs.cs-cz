# [Dokumentace ke službě SQL Data Warehouse](index.md)

# Přehled

## [Informace o službě SQL Data Warehouse](sql-data-warehouse-overview-what-is.md)
## [Tahák](cheat-sheet.md)

# Šablony Rychlý start

## [Vytvoření a připojení – portál](create-data-warehouse-portal.md)
## Pozastavení a obnovení výpočtů
### [Azure Portal](pause-and-resume-compute-portal.md)
### [PowerShell](pause-and-resume-compute-powershell.md)
## Škálování výpočetního výkonu
### [Azure Portal](quickstart-scale-compute-portal.md)
### [PowerShell](quickstart-scale-compute-powershell.md)
### [T-SQL](quickstart-scale-compute-tsql.md)


# Kurzy
## [1. Načtení dat z objektu blob](load-data-from-azure-blob-storage-using-polybase.md)

# Koncepty
## Funkce služby
### [Architektura MPP](massively-parallel-processing-mpp-architecture.md)
### [Úrovně výkonu](performance-tiers.md)
### [Jednotky datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [Škálování na více instancí, pozastavení, obnovení](sql-data-warehouse-manage-compute-overview.md)
### [Zálohy datového skladu](sql-data-warehouse-backups.md)
### [Auditování](sql-data-warehouse-auditing-overview.md)
### [Omezení kapacity](sql-data-warehouse-service-capacity-limits.md)
### [Nejčastější dotazy](sql-data-warehouse-overview-faq.md)

## Zabezpečení
### [Přehled](sql-data-warehouse-overview-manage-security.md)
### [Ověřování](sql-data-warehouse-authentication.md)


## Migrace do služby SQL Data Warehouse
### [Přehled](sql-data-warehouse-overview-migrate.md)
### [Migrace schématu](sql-data-warehouse-migrate-schema.md)
### [Migrace kódu](sql-data-warehouse-migrate-code.md)
### [Migrace dat](sql-data-warehouse-migrate-data.md)

## Načítání a přesun dat
### [Přehled](design-elt-data-loading.md)
### [Osvědčené postupy](guidance-for-loading-data.md)


## Integrace
### [Přehled](sql-data-warehouse-overview-integrate.md)
### [Elastický dotaz SQL Database](how-to-use-elastic-query-with-sql-data-warehouse.md)


## Monitorování a ladění
### [Správa úloh](resource-classes-for-workload-management.md)
### [Komprese columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Monitorování](sql-data-warehouse-manage-monitor.md)
### [Řešení problémů](sql-data-warehouse-troubleshoot.md)

## Vývoj datových skladů
### [Přehled](sql-data-warehouse-overview-develop.md)
### [Komponenty datového skladu](sql-data-warehouse-overview-workload.md)

### Tabulky
#### [Přehled](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Datové typy](sql-data-warehouse-tables-data-types.md)
#### [Distribuované tabulky](sql-data-warehouse-tables-distribute.md)
#### [Indexy](sql-data-warehouse-tables-index.md)
#### [Identita](sql-data-warehouse-tables-identity.md)
#### [Oddíly](sql-data-warehouse-tables-partition.md)
#### [Replikované tabulky](design-guidance-for-replicated-tables.md)
#### [Statistika](sql-data-warehouse-tables-statistics.md)
#### [Dočasné](sql-data-warehouse-tables-temporary.md)

### Dotazy
#### [Dynamické SQL](sql-data-warehouse-develop-dynamic-sql.md)
#### [Možnosti pro seskupení](sql-data-warehouse-develop-group-by-options.md)
#### [Popisky](sql-data-warehouse-develop-label.md)

### Elementy jazyka T-SQL
#### [Smyčky](sql-data-warehouse-develop-loops.md)
#### [Uložené procedury](sql-data-warehouse-develop-stored-procedures.md)
#### [Transakce](sql-data-warehouse-develop-transactions.md)
#### [Osvědčené postupy pro transakce](sql-data-warehouse-develop-best-practices-transactions.md)
#### [Schémata definovaná uživatelem](sql-data-warehouse-develop-user-defined-schemas.md)
#### [Přiřazení proměnné](sql-data-warehouse-develop-variable-assignment.md)
#### [Zobrazení](sql-data-warehouse-develop-views.md)

# Návody
## Funkce služby
### [Obnovení datového skladu – portál](sql-data-warehouse-restore-database-portal.md)
### [Obnovení datového skladu – PowerShell](sql-data-warehouse-restore-database-powershell.md)
### [Obnovení datového skladu – REST API](sql-data-warehouse-restore-database-rest-api.md)

## Zabezpečení
### [Povolení šifrování – portál](sql-data-warehouse-encryption-tde.md)
### [Povolení šifrování – T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Detekce hrozeb](sql-data-warehouse-security-threat-detection.md)


## Načítání a přesun dat
### [Veřejná data společnosti Contoso](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [AzCopy](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [Redgate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)


## Integrace
### [Konfigurace elastického dotazu SQL Database](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
### [Přidání úlohy služby Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)
### [Použití Machine Learningu](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Vizualizace pomocí Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)

## Monitorování a ladění
### [Analýza úloh](analyze-your-workload.md)

## Horizontální navýšení kapacity

### [Automatizace výpočetních úrovní](manage-compute-with-azure-functions.md)


# Referenční informace

## T-SQL
### [Úplný přehled](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [Elementy jazyka SQL DW](sql-data-warehouse-reference-tsql-language-elements.md)
### [Příkazy SQL DW](sql-data-warehouse-reference-tsql-statements.md)
## [Systémová zobrazení](sql-data-warehouse-reference-tsql-system-views.md)
## [Rutiny PowerShellu](sql-data-warehouse-reference-powershell-cmdlets.md)
## [Rozhraní REST API](sql-data-warehouse-manage-compute-rest-api.md)

# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/?category=databases)
## [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [Ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Žádosti o funkce](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Podpora](sql-data-warehouse-get-started-create-support-ticket.md)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Partneři
### [Business intelligence](sql-data-warehouse-partner-business-intelligence.md)
### [Integrace dat](sql-data-warehouse-partner-data-integration.md)
### [Správa dat](sql-data-warehouse-partner-data-management.md)
