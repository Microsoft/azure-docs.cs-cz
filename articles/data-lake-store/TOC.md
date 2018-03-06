# [Dokumentace ke službě Data Lake Store](index.md)

# Přehled
## [Přehled Azure Data Lake Store](data-lake-store-overview.md)
## [Porovnání Azure Data Lake Store se službou Azure Storage](data-lake-store-comparison-with-blob-storage.md)
## [Azure Data Lake Store pro zpracování velkého objemu dat](data-lake-store-data-scenarios.md)
## [Open source aplikace využívající Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)
## [Osvědčené postupy při používání Data Lake Store](data-lake-store-best-practices.md)

# Začínáme
## [Pomocí portálu](data-lake-store-get-started-portal.md)
## [Použití služby Data Lake Store v průzkumníku služby Storage](data-lake-store-in-storage-explorer.md)
## [Pomocí PowerShellu](data-lake-store-get-started-powershell.md)
## [Použití Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)


# Postup
## Načtení a přesun dat
### [Pomocí služby Azure Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Použití služby Data Lake Store v průzkumníku služby Storage](data-lake-store-in-storage-explorer.md)
### [Pomocí AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
### [Pomocí DistCp](data-lake-store-copy-data-wasb-distcp.md)
### [Pomocí Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
### [Nahrávání dat z offline zdrojů](data-lake-store-offline-bulk-data-upload.md)
### [Migrace Azure Data Lake Store napříč oblastmi](data-lake-store-migration-cross-region.md)

## Zabezpečení dat
### [Přehled zabezpečení](data-lake-store-security-overview.md)
### [Řízení přístupu v Data Lake Store](data-lake-store-access-control.md)
### [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
### [Šifrování](data-lake-store-encryption.md)

## Ověřování s využitím služby Data Lake Store
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

## Práce s Data Lake Store
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
### [Průvodce laděním výkonu pro Azure Data Lake Store](data-lake-store-performance-tuning-guidance.md)
### [Průvodce laděním výkonu pro použití PowerShellu se službou Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
### [Průvodce laděním výkonu pro Spark ve službě HDInsight a Azure Data Lake Store](data-lake-store-performance-tuning-spark.md)
### [Průvodce laděním výkonu pro Hive ve službě HDInsight a Azure Data Lake Store](data-lake-store-performance-tuning-hive.md)
### [Průvodce laděním výkonu pro MapReduce ve službě HDInsight a Azure Data Lake Store](data-lake-store-performance-tuning-mapreduce.md)
### [Průvodce laděním výkonu pro Storm ve službě HDInsight a Azure Data Lake Store](data-lake-store-performance-tuning-storm.md)

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
## [Ukázky kódu](https://azure.microsoft.com/en-us/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python (správa účtů)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
## [Python (správa systému souborů)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [Azure CLI](https://docs.microsoft.com/cli/azure/dls)

# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/)
## [Blog služby Data Lake Store](https://blogs.msdn.microsoft.com/azuredatalake/)
## [Váš názor na UserVoice](https://feedback.azure.com/forums/327234-data-lake)
## [Fórum na webu MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [Ceny](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Fórum Stack Overflow](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
