---
title: Data Factory – protokol změn rozhraní .NET API
description: Popisuje zásadní změny, přidané funkce, opravy chyb a tak dále, v konkrétní verzi rozhraní .NET API pro Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jonburchel
ms.service: data-factory
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 2d292bbc820574d4360be51e93588f8f908f468d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785593"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory – protokol změn rozhraní .NET API
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Tento článek poskytuje informace o změnách Azure Data Factory SDK v konkrétní verzi. Nejnovější balíček NuGet pro Azure Data Factory najdete [tady](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) .

## <a name="version-4110"></a>4.11.0 verze
Přidání funkcí:

* Přidali jsme následující typy propojených služeb:
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* Přidali jsme následující typy datových sad:
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* Byly přidány následující typy zdrojů kopie:
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>4.10.0 verze
* Do TextFormat byly přidány následující volitelné vlastnosti:
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* Přidali jsme následující typy propojených služeb:
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* Přidali jsme následující typy datových sad:
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* Byly přidány následující typy zdrojů kopie:
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* Přidání vlastnosti [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) do AzureMLBatchExecutionActivity
  * Povolení předávání více vstupů webové služby do Azure Machine Learning experimentu

## <a name="version-491"></a>4.9.1 verze
### <a name="bug-fix"></a>Oprava chyby
* Vyřazení ověřování založeného na WebApi pro [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)

## <a name="version-490"></a>4.9.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Přidejte vlastnosti [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) a [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) do CopyActivity. Podrobnosti o této funkci najdete v tématu [připravené kopírování](data-factory-copy-activity-performance.md#staged-copy) .

### <a name="bug-fix"></a>Oprava chyby
* Zaveďte přetížení metody [ActivityWindowOperationExtensions. list](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) , která přebírá instanci [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters) .
* Označte [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) a [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) jako volitelné v CopySink.

## <a name="version-480"></a>4.8.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Do typu aktivity kopírování byly přidány následující volitelné vlastnosti, aby bylo možné vyladit výkon kopírování:
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>4.7.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Byl přidán nový typ StorageFormat typu [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) ke kopírování souborů ve formátu optimalizované řádky (Orc).
* Přidejte vlastnosti [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) a PolyBaseSettings do SqlDWSink.
  * Umožňuje použití základny ke kopírování dat do služby Azure synapse Analytics.

## <a name="version-461"></a>Verze 4.6.1
### <a name="bug-fixes"></a>Opravy chyb
* Opraví požadavek HTTP na výpis oken aktivity.
  * Odebere název skupiny prostředků a název datové továrny z datové části požadavku.

## <a name="version-460"></a>4.6.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Do [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)byly přidány následující vlastnosti:
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [Datové sady](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* Do [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)byly přidány následující vlastnosti:
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* Byl přidán nový typ [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) typu [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) pro definování datových sad, jejichž data jsou ve formátu JSON.

## <a name="version-450"></a>4.5.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Byly přidány [operace seznamu pro okno aktivity](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions).
  * Přidali jsme metody pro načtení oken aktivity s filtry na základě typů entit (to znamená datových továrn, datových sad, kanálů a aktivit).
* Přidali jsme následující typy propojených služeb:
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice), [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* Přidali jsme následující typy datových sad:
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset), [WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* Byly přidány následující typy zdrojů kopie:     
  * [Websource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>4.4.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Jako zdroj dat a jímky pro aktivity kopírování se přidal následující typ propojené služby:
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). Koncepční informace a příklady najdete v tématu [Azure Storage propojená služba SAS](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>4.3.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Následující typy propojených služeb některé byly přidány jako zdroje dat pro aktivity kopírování:
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). Koncepční informace a příklady najdete v tématu [přesun dat z HDFS pomocí Data Factory](data-factory-hdfs-connector.md) .
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). Koncepční informace a příklady najdete v tématu [přesun dat z úložišť dat ODBC pomocí Azure Data Factory](data-factory-odbc-connector.md) .

## <a name="version-420"></a>4.2.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Byl přidán následující nový typ aktivity: [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity). Podrobnosti o aktivitě najdete v tématu [aktualizace modelů Azure ml pomocí aktivity aktualizovat prostředek](data-factory-azure-ml-batch-execution-activity.md).
* Do [třídy AzureMLLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice)byla přidána nová volitelná vlastnost [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) .
* Do třídy [DataFactoryManagementClient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) se přidaly vlastnosti [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) a [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) .
* Povolte konfiguraci časových limitů pro volání klientů služby Data Factory.

## <a name="version-410"></a>4.1.0 verze
### <a name="feature-additions"></a>Přidání funkcí
* Přidali jsme následující typy propojených služeb:
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* Byly přidány následující typy aktivit:
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* Přidali jsme následující typy datových sad:
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* Přidali jsme následující typy zdrojů a jímky pro aktivitu kopírování:
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>4.0.1 verze
### <a name="breaking-changes"></a>Změny způsobující chyby
Následující třídy byly přejmenovány. Nové názvy byly původní názvy tříd před verzí 4.0.0.

| Název v 4.0.0 | Název v 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>Verze 4.0.0
### <a name="breaking-changes"></a>Změny způsobující chyby
* Následující třídy/rozhraní byly přejmenovány.

| Starý název | Nové jméno |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Tabulka |[Integrován](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

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
* Třída [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) podporuje dvě nové vlastnosti, **SliceIdentifierColumnName** a **SqlWriterCleanupScript**, aby podporovaly Idempotentní kopírování do Azure Azure synapse Analytics. Podrobnosti o těchto vlastnostech najdete v článku o [službě Azure synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) .
* Nyní podporujeme provoz uloženou proceduru pro Azure SQL Database a zdroje analýzy Azure synapse jako součást aktivity kopírování. Třídy [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) a [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) mají následující vlastnosti: **SqlReaderStoredProcedureName** a **StoredProcedureParameters**. Podrobnosti o těchto vlastnostech najdete v článcích [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) a [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) na Azure.com.