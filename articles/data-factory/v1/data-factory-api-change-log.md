---
title: Datové továrny – protokol změn rozhraní API .NET | Dokumentace Microsoftu
description: Popisuje změny způsobující chyby, aktualizací obsahujících nové funkce, opravy atd... v konkrétní verzi rozhraní .NET API pro Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 863f3500c84eeab1c3dac19141cd334fc6961694
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015912"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory – protokol změn rozhraní API .NET
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Tento článek obsahuje informace o změnách na sadu SDK pro Azure Data Factory v konkrétní verzi. Můžete najít nejnovější balíček NuGet pro Azure Data Factory [zde](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Verze 4.11.0
Aktualizací obsahujících nové funkce:

* Byly přidány následující typy propojené služby:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Byly přidány následující typy datové sady:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Byly přidány následující typy zdrojů kopírování:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Verze 4.10.0
* Následující volitelné vlastnosti Přibyla TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [firstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [treatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Byly přidány následující typy propojené služby:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Byly přidány následující typy datové sady:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Byly přidány následující typy zdrojů kopírování:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Přidat [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) vlastnost AzureMLBatchExecutionActivity
  * Povolit předání několik webových služeb vstupy do experimentu Azure Machine Learning

## <a name="version-491"></a>Verze 4.9.1
### <a name="bug-fix"></a>Oprava chyby
* Přestat používat ověřování na základě WebApi pro [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Verze 4.9.0
### <a name="feature-additions"></a>Aktualizací obsahujících nové funkce
* Přidat [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) a [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) vlastností CopyActivity. Zobrazit [fázovaného kopírování](data-factory-copy-activity-performance.md#staged-copy) podrobnosti o funkci.

### <a name="bug-fix"></a>Oprava chyby
* Zavést přetížení [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) metoda, která přebírá [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) instance.
* Označit [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) a [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) v CopySink jako volitelné.

## <a name="version-480"></a>Verze 4.8.0
### <a name="feature-additions"></a>Aktualizací obsahujících nové funkce
* Byly přidány následující volitelné vlastnosti pro typ aktivity kopírování k povolení ladění výkonu kopírování:
  * [parallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Verze 4.7.0
### <a name="feature-additions"></a>Aktualizací obsahujících nové funkce
* Přidat nový typ StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) typu kopírování souborů ve formátu (ORC) úložiště se sloupcovou strukturou optimalizované řádek.
* Přidat [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) PolyBaseSettings vlastnosti, které SqlDWSink.
  * Umožňuje použití technologie PolyBase můžete kopírovat data do SQL Data Warehouse.

## <a name="version-461"></a>Verze 4.6.1
### <a name="bug-fixes"></a>Opravy chyb
* Opravuje požadavku HTTP pro zobrazení seznamu okna aktivit.
  * Odebere název skupiny prostředků a název objektu pro vytváření dat z datové části požadavku.

## <a name="version-460"></a>Verze 4.6.0
### <a name="feature-additions"></a>Aktualizací obsahujících nové funkce
* Následující vlastnosti byly přidány do [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [pipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [expirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Datové sady](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Následující vlastnosti byly přidány do [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Přidána nové [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) typ [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) typu pro definování datové sady, jejichž data jsou ve formátu JSON.

## <a name="version-450"></a>Verze 4.5.0
### <a name="feature-additions"></a>Aktualizací obsahujících nové funkce
* Přidání [seznam operací pro okna aktivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Přidání metody k načtení okna aktivit s filtry na základě typů entit (to znamená, že objekty pro vytváření dat, datové sady, kanály a aktivity).
* Byly přidány následující typy propojené služby:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Byly přidány následující typy datové sady:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Byly přidány následující typy zdrojů kopírování:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Verze 4.4.0
### <a name="feature-additions"></a>Aktualizací obsahujících nové funkce
* Následující typ propojené služby se přidala jako zdroje a jímky aktivity kopírování:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Zobrazit [propojená služba Azure Storage SAS](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) koncepční informace a příklady.

## <a name="version-430"></a>Verze 4.3.0
### <a name="feature-additions"></a>Aktualizací obsahujících nové funkce
* Následující typy haven propojené služby byl přidán jako zdroj dat pro aktivity kopírování:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Zobrazit [přesunout data z HDFS pomocí služby Data Factory](data-factory-hdfs-connector.md) koncepční informace a příklady.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Zobrazit [přesunout úložiště dat z rozhraní ODBC dat pomocí Azure Data Factory](data-factory-odbc-connector.md) koncepční informace a příklady.

## <a name="version-420"></a>Verze 4.2.0
### <a name="feature-additions"></a>Aktualizací obsahujících nové funkce
* Byla přidána následující nového typu aktivity: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Podrobnosti o aktivitě najdete v tématu [modely aktualizace Azure ML s využitím aktivita prostředku aktualizace](data-factory-azure-ml-batch-execution-activity.md).
* Nové vlastnosti volitelné [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) byl přidán do [AzureMLLinkedService třídy](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) a [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) vlastnosti byly přidány do [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) třídy.
* Povolit konfiguraci vypršení časových limitů u volání klienta do služby Data Factory.

## <a name="version-410"></a>Verze 4.1.0
### <a name="feature-additions"></a>Aktualizací obsahujících nové funkce
* Byly přidány následující typy propojené služby:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Byly přidány následující typy aktivit:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Byly přidány následující typy datové sady:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Byly přidány následující typy zdroje a jímky pro aktivitu kopírování:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Verze 4.0.1
### <a name="breaking-changes"></a>Změny způsobující chyby
Následující třídy byly přejmenovány. Nové názvy byly původní názvy tříd, než verze 4.0.0.

| Název v 4.0.0 | Název v 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Verze 4.0.0
### <a name="breaking-changes"></a>Změny způsobující chyby
* Následující třídy/rozhraní byly přejmenovány.

| Starý název | Nový název |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Table |[Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* **Seznamu** metody nyní vrací stránkových výsledků. Pokud odpověď obsahuje neprázdný **NextLink** vlastnost, klientská aplikace potřebuje pokračovat v načítání na další stránku, dokud jsou vráceny všechny stránky.  Zde naleznete příklad:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **Seznam** kanálu rozhraní API Vrátí souhrn kanálu místo úplné podrobnosti. Například aktivity v kanálu souhrnu obsahovat pouze názvem a typem.

### <a name="feature-additions"></a>Aktualizací obsahujících nové funkce
* [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) třída podporuje dvě nové vlastnosti **SliceIdentifierColumnName** a **SqlWriterCleanupScript**pro zajištění podpory idempotentní kopírovat do Azure SQL Data Sklad. Zobrazit [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) , kde najdete podrobnosti o těchto vlastnostech.
* Nyní podporujeme provoz uloženou proceduru nad zdroji, Azure SQL Database a Azure SQL Data Warehouse jako součást aktivitu kopírování. [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) a [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) třídy mají následující vlastnosti: **SqlReaderStoredProcedureName** a **StoredProcedureParameters**. Zobrazit [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) a [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) článků na webu Azure.com najdete podrobnosti o těchto vlastnostech.  
