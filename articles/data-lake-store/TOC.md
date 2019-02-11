# [Dokumentace ke službě Data Lake Storage Gen1](index.md)
# [Dokumentace k přechodu na Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction)

# Přehled
## [Přehled Data Lake Storage Gen1](data-lake-store-overview.md)
## [Porovnání se službou Azure Storage](data-lake-store-comparison-with-blob-storage.md)
## [Zpracování velkých objemů dat](data-lake-store-data-scenarios.md)
## [Práce s opensourcovými aplikacemi](data-lake-store-compatible-oss-other-applications.md)
## [Osvědčené postupy](data-lake-store-best-practices.md)

# Začínáme
## [Pomocí webu Azure Portal](data-lake-store-get-started-portal.md)
## [Použití Azure PowerShellu](data-lake-store-get-started-powershell.md)
## [Použití Azure CLI](data-lake-store-get-started-cli-2.0.md)

# Postup
## Načtení a přesun dat
### [Pomocí služby Azure Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Pomocí Průzkumníka služby Azure Storage](data-lake-store-in-storage-explorer.md)
### [Pomocí AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
### [Pomocí DistCp](data-lake-store-copy-data-wasb-distcp.md)
### [Pomocí Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
### [Nahrávání dat z offline zdrojů](data-lake-store-offline-bulk-data-upload.md)
### [Migrace Data Lake Storage Gen1 napříč oblastmi](data-lake-store-migration-cross-region.md)

## Zabezpečení dat
### [Přehled zabezpečení](data-lake-store-security-overview.md)
### [Řízení přístupu](data-lake-store-access-control.md)
### [Zabezpečení uložených dat](data-lake-store-secure-data.md)
### [Šifrování](data-lake-store-encryption.md)
### [Integrace virtuální sítě](data-lake-store-network-security.md)

## Ověřování s využitím služby Data Lake Storage Gen1
### [Možnosti ověřování](data-lakes-store-authentication-using-azure-active-directory.md)
### [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [Pomocí Javy](data-lake-store-end-user-authenticate-java-sdk.md)
#### [Pomocí sady .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
#### [Pomocí rozhraní REST API](data-lake-store-end-user-authenticate-rest-api.md)
#### [Pomocí Pythonu](data-lake-store-end-user-authenticate-python.md)
### [Ověřování služba-služba](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [Pomocí Javy](data-lake-store-service-to-service-authenticate-java.md)
#### [Pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [Pomocí rozhraní REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [Pomocí Pythonu](data-lake-store-service-to-service-authenticate-python.md)

## Práce se službou Data Lake Storage Gen1
### Operace správy účtů
#### [Pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
#### [Pomocí rozhraní REST API](data-lake-store-get-started-rest-api.md)
#### [Pomocí Pythonu](data-lake-store-get-started-python.md)
### Operace systému souborů
#### [Pomocí sady .NET SDK](data-lake-store-data-operations-net-sdk.md)
#### [Pomocí sady Java SDK](data-lake-store-get-started-java-sdk.md)
#### [Pomocí rozhraní REST API](data-lake-store-data-operations-rest-api.md)
#### [Pomocí Pythonu](data-lake-store-data-operations-python.md)

## Výkon
### [Přehled](data-lake-store-performance-tuning-guidance.md)
### [Použití Azure PowerShellu](data-lake-store-performance-tuning-powershell.md)
### [Použití Sparku v HDInsightu](data-lake-store-performance-tuning-spark.md)
### [Použití Hivu v HDInsightu](data-lake-store-performance-tuning-hive.md)
### [Použití MapReduce v HDInsightu](data-lake-store-performance-tuning-mapreduce.md)
### [Použití Stormu v HDInsightu](data-lake-store-performance-tuning-storm.md)

## Integrace se službami Azure
### Pomocí HDInsight
#### [Pomocí webu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [Pomocí Azure PowerShellu (výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [Pomocí Azure PowerShellu (další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [Pomocí šablony Azure](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [Přístup z virtuálních počítačů ve virtuální síti Azure](data-lake-store-connectivity-from-vnets.md)
### [Použití se službou Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [Použití s Azure Event Hubs](data-lake-store-archive-eventhub-capture.md)
### [Použití se službou Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Použití se službou Stream Analytics](data-lake-store-stream-analytics.md)
### [Použití s Power BI](data-lake-store-power-bi.md)
### [Použití se službou Data Catalog](data-lake-store-with-data-catalog.md)
### [Použití funkce PolyBase ve službě SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [Použití s integračními službami SQL Serveru](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [Další možnosti integrace Azure](data-lake-store-integrate-with-other-services.md)

## Spravovat
### [Přístup k diagnostickým protokolům](data-lake-store-diagnostic-logs.md)
### [Plánování pro vysokou dostupnost](data-lake-store-disaster-recovery-guidance.md)

# Referenční informace
## [Ukázky kódu](https://azure.microsoft.com/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python (správa účtů)](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python)
## [Python (správa systému souborů)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [Šablona Resource Manageru](/azure/templates/microsoft.datalakestore/allversions)
## [Azure CLI](https://docs.microsoft.com/cli/azure/dls)

# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Blog služby Data Lake Store](https://blogs.msdn.microsoft.com/azuredatalake/)
## [Váš názor na UserVoice](https://feedback.azure.com/forums/327234-data-lake)
## [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [Ceny](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Fórum Stack Overflow](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
