---
title: Objekt pro vytváření dat – protokol změn rozhraní API .NET | Microsoft Docs
description: Popisuje nejnovější změny, přidání funkce a opravy chyb atd... v konkrétní verzi rozhraní .NET API pro vytváření dat Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 110297fd60b4a25f959b451f2952f32d66cb8c3e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory – protokol změn rozhraní API .NET
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). 

Tento článek obsahuje informace o změnách SDK služby Azure Data Factory v konkrétní verzi. Můžete najít nejnovější balíček NuGet pro Azure Data Factory [sem](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Verze 4.11.0
Přidání funkce:

* Byly přidány následující typy propojené služby:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Byly přidány následující typy datové sady:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Byly přidány následující typy zdrojů kopie:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Verze 4.10.0
* Následující volitelné vlastnosti Přibyla TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Byly přidány následující typy propojené služby:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Byly přidány následující typy datové sady:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Byly přidány následující typy zdrojů kopie:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Přidat [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) vlastnost AzureMLBatchExecutionActivity
  * Povolit předání více webové služby vstupů pro Azure Machine Learning experimentu

## <a name="version-491"></a>Verze 4.9.1
### <a name="bug-fix"></a>Oprava chyby
* Přestat používat ověřování na základě WebApi pro [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Verze 4.9.0
### <a name="feature-additions"></a>Přidání funkce
* Přidat [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) a [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) vlastnosti, které chcete CopyActivity. V tématu [připravený kopie](data-factory-copy-activity-performance.md#staged-copy) informace o funkci.

### <a name="bug-fix"></a>Oprava chyby
* Zavést přetížení [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) metoda, která přebírá [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) instance.
* Označit [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) a [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) jako volitelný v CopySink.

## <a name="version-480"></a>Verze 4.8.0
### <a name="feature-additions"></a>Přidání funkce
* Byly přidány následující volitelné vlastnosti na typ aktivity kopírování povolit ladění výkonu kopie:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Verze 4.7.0
### <a name="feature-additions"></a>Přidání funkce
* Přidat nový typ StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) typ kopírovat soubory ve sloupcovém formátu (ORC) optimalizované řádek.
* Přidat [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) PolyBaseSettings vlastnosti, které SqlDWSink.
  * Umožňuje používání funkce PolyBase ke zkopírování dat do SQL Data Warehouse.

## <a name="version-461"></a>Verze 4.6.1
### <a name="bug-fixes"></a>Opravy chyb
* Opravy požadavku HTTP pro výpis okna aktivity.
  * Název skupiny prostředků a název objektu pro vytváření dat odebere datová část požadavku.

## <a name="version-460"></a>Verze 4.6.0
### <a name="feature-additions"></a>Přidání funkce
* Následující vlastnosti jsou přidané do [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [pipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Datové sady](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Následující vlastnosti jsou přidané do [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Přidat nové [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) typ [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) typ k definování datových sad, jejichž data jsou ve formátu JSON.

## <a name="version-450"></a>Verze 4.5.0
### <a name="feature-additions"></a>Přidání funkce
* Přidat [seznam operací pro okna aktivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Přidání metody k načtení windows aktivity s filtry na základě typů entit (tedy datové továrny, datové sady, kanály a aktivity).
* Byly přidány následující typy propojené služby:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Byly přidány následující typy datové sady:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Byly přidány následující typy zdrojů kopie:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Verze 4.4.0
### <a name="feature-additions"></a>Přidání funkce
* Následující typu propojené služby se přidal jako zdroje dat a jímky kopie aktivity:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). V tématu [propojená služba Azure úložiště SAS](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) koncepční informace a příklady.

## <a name="version-430"></a>Verze 4.3.0
### <a name="feature-additions"></a>Přidání funkce
* Následující nebyla typy některá pro propojenou službu byl přidán jako zdroje dat pro kopírování aktivity:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). V tématu [přesun dat z HDFS pomocí služby Data Factory](data-factory-hdfs-connector.md) koncepční informace a příklady.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). V tématu [přesunout data z ODBC datová úložiště pomocí Azure Data Factory](data-factory-odbc-connector.md) koncepční informace a příklady.

## <a name="version-420"></a>Verze 4.2.0
### <a name="feature-additions"></a>Přidání funkce
* Následující nového typu aktivity se přidal: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Podrobnosti o aktivitě najdete v tématu [aktualizace Azure ML modelů pomocí aktivita prostředku aktualizace](data-factory-azure-ml-batch-execution-activity.md).
* Nové volitelná vlastnost [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) byl přidán do [AzureMLLinkedService třída](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) a [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) přidané vlastnosti [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) třídy.
* Rutiny umožňují konfiguraci časových limitů pro volání klienta ke službě Data Factory.

## <a name="version-410"></a>Verze 4.1.0
### <a name="feature-additions"></a>Přidání funkce
* Byly přidány následující typy propojené služby:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Byly přidány následující typy aktivit:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Byly přidány následující typy datové sady:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Byly přidány následující typy zdroj a jímka pro aktivitu kopírování:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Verze 4.0.1
### <a name="breaking-changes"></a>Nejnovější změny
Následující třídy byly přejmenovány. Nové názvy byly původní názvy tříd před 4.0.0 verzi.

| Název v 4.0.0 | Název v 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Verze 4.0.0
### <a name="breaking-changes"></a>Nejnovější změny
* Následující třídy nebo rozhraní byla přejmenovaná.

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

* **Seznamu** metody teď vrací stránkových výsledků. Pokud odpověď obsahuje neprázdný **NextLink** vlastnost, klientská aplikace musí pokračovat v načítání na další stránku, dokud jsou vráceny všechny stránky.  Zde naleznete příklad:

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
* **Seznam** kanálu rozhraní API Vrátí souhrn kanálu místo úplné podrobnosti. Například aktivity v kanálu souhrn obsahovat jenom název a typ.

### <a name="feature-additions"></a>Přidání funkce
* [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) třída podporuje dva nové vlastnosti **SliceIdentifierColumnName** a **SqlWriterCleanupScript**, pro podporu idempotent kopírovat do Azure SQL Data Warehouse. Najdete v článku [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) podrobnosti o těchto vlastnostech najdete v článku.
* Teď podporují spuštěným uložené procedury pro Azure SQL Database a Azure SQL Data Warehouse zdroje v rámci aktivity kopírování. [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) a [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) třídy mají následující vlastnosti: **SqlReaderStoredProcedureName** a **StoredProcedureParameters**. Najdete v článku [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) a [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) články na Azure.com. Podrobnosti o těchto vlastnostech.  
