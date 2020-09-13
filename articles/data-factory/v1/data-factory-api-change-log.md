---
title: Data Factory – protokol změn rozhraní .NET API
description: Popisuje zásadní změny, přidané funkce, opravy chyb a tak dále, v konkrétní verzi rozhraní .NET API pro Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: b7936fcd1e4a629a813c4266920f6c34a15cf9b4
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438938"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory – protokol změn rozhraní .NET API
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Tento článek poskytuje informace o změnách Azure Data Factory SDK v konkrétní verzi. Nejnovější balíček NuGet pro Azure Data Factory najdete [tady](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) .

## <a name="version-4110"></a>4.11.0 verze
Přidání funkcí:

* Přidali jsme následující typy propojených služeb:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Přidali jsme následující typy datových sad:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Byly přidány následující typy zdrojů kopie:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>4.10.0 verze
* Do TextFormat byly přidány následující volitelné vlastnosti:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Přidali jsme následující typy propojených služeb:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Přidali jsme následující typy datových sad:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Byly přidány následující typy zdrojů kopie:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Přidání vlastnosti [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) do AzureMLBatchExecutionActivity
  * Povolení předávání více vstupů webové služby do Azure Machine Learning experimentu

## <a name="version-491"></a>4.9.1 verze
### <a name="bug-fix"></a>Oprava chyby
* Vyřazení ověřování založeného na WebApi pro [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx)

## <a name="version-490"></a>4.9.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Přidejte vlastnosti [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) a [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) do CopyActivity. Podrobnosti o této funkci najdete v tématu [připravené kopírování](data-factory-copy-activity-performance.md#staged-copy) .

### <a name="bug-fix"></a>Oprava chyby
* Zaveďte přetížení metody [ActivityWindowOperationExtensions. list](https://msdn.microsoft.com/library/mt767915.aspx) , která přebírá instanci [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) .
* Označte [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) a [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) jako volitelné v CopySink.

## <a name="version-480"></a>4.8.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Do typu aktivity kopírování byly přidány následující volitelné vlastnosti, aby bylo možné vyladit výkon kopírování:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>4.7.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Byl přidán nový typ StorageFormat typu [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) ke kopírování souborů ve formátu optimalizované řádky (Orc).
* Přidejte vlastnosti [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) a PolyBaseSettings do SqlDWSink.
  * Umožňuje použití základny ke kopírování dat do služby Azure synapse Analytics (dříve SQL Data Warehouse).

## <a name="version-461"></a>Verze 4.6.1
### <a name="bug-fixes"></a>Opravy chyb
* Opraví požadavek HTTP na výpis oken aktivity.
  * Odebere název skupiny prostředků a název datové továrny z datové části požadavku.

## <a name="version-460"></a>4.6.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Do [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)byly přidány následující vlastnosti:
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Datové sady](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Do [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)byly přidány následující vlastnosti:
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Byl přidán nový typ [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) typu [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) pro definování datových sad, jejichž data jsou ve formátu JSON.

## <a name="version-450"></a>4.5.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Byly přidány [operace seznamu pro okno aktivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Přidali jsme metody pro načtení oken aktivity s filtry na základě typů entit (to znamená datových továrn, datových sad, kanálů a aktivit).
* Přidali jsme následující typy propojených služeb:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Přidali jsme následující typy datových sad:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Byly přidány následující typy zdrojů kopie:     
  * [Websource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>4.4.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Jako zdroj dat a jímky pro aktivity kopírování se přidal následující typ propojené služby:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Koncepční informace a příklady najdete v tématu [Azure Storage propojená služba SAS](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>4.3.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Následující typy propojených služeb některé byly přidány jako zdroje dat pro aktivity kopírování:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Koncepční informace a příklady najdete v tématu [přesun dat z HDFS pomocí Data Factory](data-factory-hdfs-connector.md) .
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Koncepční informace a příklady najdete v tématu [přesun dat z úložišť dat ODBC pomocí Azure Data Factory](data-factory-odbc-connector.md) .

## <a name="version-420"></a>4.2.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Byl přidán následující nový typ aktivity: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Podrobnosti o aktivitě najdete v tématu [aktualizace modelů Azure ml pomocí aktivity aktualizovat prostředek](data-factory-azure-ml-batch-execution-activity.md).
* Do [třídy AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)byla přidána nová volitelná vlastnost [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) .
* Do třídy [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) se přidaly vlastnosti [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) a [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) .
* Povolte konfiguraci časových limitů pro volání klientů služby Data Factory.

## <a name="version-410"></a>4.1.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Přidali jsme následující typy propojených služeb:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Byly přidány následující typy aktivit:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Přidali jsme následující typy datových sad:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Přidali jsme následující typy zdrojů a jímky pro aktivitu kopírování:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>4.0.1 verze
### <a name="breaking-changes"></a>Změny způsobující chyby
Následující třídy byly přejmenovány. Nové názvy byly původní názvy tříd před verzí 4.0.0.

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

| Starý název | Nové jméno |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabulka |[Datová sada](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* Metody **seznamu** vrátí aktuálně stránkované výsledky. Pokud odpověď obsahuje neprázdnou vlastnost **NextLink** , musí klientská aplikace pokračovat v načítání další stránky, dokud nebudou vráceny všechny stránky.  Tady je příklad:

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
* API kanálu **seznamu** vrátí pouze souhrn kanálu namísto úplných podrobností. Například aktivity v souhrnu kanálu obsahují pouze název a typ.

### <a name="feature-additions"></a>Přidání funkcí
* Třída [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) podporuje dvě nové vlastnosti, **SliceIdentifierColumnName** a **SqlWriterCleanupScript**, aby podporovaly Idempotentní kopírování do Azure Azure synapse Analytics. Podrobnosti o těchto vlastnostech najdete v článku o [službě Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) .
* Nyní podporujeme provoz uloženou proceduru pro Azure SQL Database a zdroje analýzy Azure synapse jako součást aktivity kopírování. Třídy [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) a [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) mají následující vlastnosti: **SqlReaderStoredProcedureName** a **StoredProcedureParameters**. Podrobnosti o těchto vlastnostech najdete v článcích [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) a [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) na Azure.com.  
