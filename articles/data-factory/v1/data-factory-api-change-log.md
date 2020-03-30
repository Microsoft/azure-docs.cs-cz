---
title: Factory – protokol změn rozhraní .NET API
description: Popisuje nejnovější změny, přidání funkcí, opravy chyb a tak dále v konkrétní verzi rozhraní .NET API pro Azure Data Factory.
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
ms.openlocfilehash: dbbbdebdcf1db7afe485166f5744f2291b757d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978998"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory – protokol změn rozhraní API .NET
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Tento článek obsahuje informace o změnách sady Azure Data Factory SDK v konkrétní verzi. Nejnovější balíček NuGet pro Azure Data Factory najdete [zde](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Verze 4.11.0
Přidání funkcí:

* Byly přidány následující propojené typy služeb:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [Služba AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Byly přidány následující typy datových sad:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Byly přidány následující typy zdrojů kopírování:
  * [MongoDbZdroj](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Verze 4.10.0
* Do formátu TextFormat byly přidány následující volitelné vlastnosti:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [První záhlaví](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Byly přidány následující propojené typy služeb:
  * [Služba OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [Služba SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Byly přidány následující typy datových sad:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Byly přidány následující typy zdrojů kopírování:
  * [CassandraZdroj](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Přidání [vlastnosti WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) do AzureMLBatchExecutionActivity
  * Povolení předávání více vstupů webové služby experimentu Azure Machine Learning

## <a name="version-491"></a>Verze 4.9.1
### <a name="bug-fix"></a>Oprava chyby
* Zamezet ověřování založené na webovém rozhraní pro [službu WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Verze 4.9.0
### <a name="feature-additions"></a>Dodatky funkcí
* Přidejte vlastnosti [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) a [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) do funkce CopyActivity. Podrobnosti o této funkci naleznete v [tématu Fázovaná kopie.](data-factory-copy-activity-performance.md#staged-copy)

### <a name="bug-fix"></a>Oprava chyby
* Zavést přetížení [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) metoda, která přebírá [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) instance.
* Označit [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) a [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) jako volitelné v CopySink.

## <a name="version-480"></a>Verze 4.8.0
### <a name="feature-additions"></a>Dodatky funkcí
* Do typu Kopírovat aktivitu byly přidány následující volitelné vlastnosti, které umožňují ladění výkonu kopírování:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Verze 4.7.0
### <a name="feature-additions"></a>Dodatky funkcí
* Přidán nový typ Formátu StorageFormat [Typu OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) pro kopírování souborů v optimalizovaném sloupcovém (ORC) formátu.
* Přidejte vlastnosti [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) a PolyBaseSettings do zařízení SqlDWSink.
  * Umožňuje použití PolyBase ke kopírování dat do datového skladu SQL.

## <a name="version-461"></a>Verze 4.6.1
### <a name="bug-fixes"></a>Opravy chyb
* Opravuje požadavek HTTP pro výpis aktivity windows.
  * Odebere název skupiny prostředků a název datové továrny z datové části požadavku.

## <a name="version-460"></a>Verze 4.6.0
### <a name="feature-additions"></a>Dodatky funkcí
* Do [vlastností PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)byly přidány následující vlastnosti :
  * [Mode kanálu](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Soubory](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Do [aplikace PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)byly přidány následující vlastnosti :
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Přidán nový typ [Formátu Úložiště](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) pro definování datových sad, jejichž data jsou ve formátu JSON.

## <a name="version-450"></a>Verze 4.5.0
### <a name="feature-additions"></a>Dodatky funkcí
* Byly přidány [operace seznamu pro okno aktivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Byly přidány metody pro načtení oken aktivit s filtry založenými na typech entit (tj. továrny na data, datové sady, kanály a aktivity).
* Byly přidány následující propojené typy služeb:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Byly přidány následující typy datových sad:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Byly přidány následující typy zdrojů kopírování:     
  * [Webový zdroj](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Verze 4.4.0
### <a name="feature-additions"></a>Přidání funkcí
* Následující typ propojené služby byl přidán jako zdroje dat a jímky pro aktivity kopírování:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Koncepční informace a příklady najdete v [tématu Propojené služby Azure Storage SAS.](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service)

## <a name="version-430"></a>Verze 4.3.0
### <a name="feature-additions"></a>Přidání funkcí
* Jako zdroje dat pro aktivity kopírování byly přidány následující propojené typy služeb:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Viz [Přesun dat z HDFS pomocí Data Factory](data-factory-hdfs-connector.md) pro koncepční informace a příklady.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Koncepční informace a příklady najdete v tématu [Přesun dat z úložišť dat ODBC pomocí Azure Data Factory.](data-factory-odbc-connector.md)

## <a name="version-420"></a>Verze 4.2.0
### <a name="feature-additions"></a>Přidání funkcí
* Byl přidán následující nový typ aktivity: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Podrobnosti o aktivitě najdete v [tématu Aktualizace modelů Azure ML pomocí aktualizace aktivity prostředků](data-factory-azure-ml-batch-execution-activity.md).
* Do [třídy AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)byla přidána nová [volitelná vlastnost updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) .
* Do třídy [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) byly přidány vlastnosti [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) a [LongRunningOperationRetryTimeout.](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx)
* Povolit konfiguraci časových časových opovenek pro volání klienta do služby Data Factory.

## <a name="version-410"></a>Verze 4.1.0
### <a name="feature-additions"></a>Přidání funkcí
* Byly přidány následující propojené typy služeb:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Byly přidány následující typy aktivit:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Byly přidány následující typy datových sad:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Byly přidány následující typy zdrojů a jímek pro aktivitu kopírování:
  * [AzureDataLakeStoreZdroj](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Verze 4.0.1
### <a name="breaking-changes"></a>Změny způsobující chyby
Následující třídy byly přejmenovány. Nové názvy byly původní názvy tříd před vydáním 4.0.0.

| Název v 4.0.0 | Název v 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| Sada OracleDataset |[Sada OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| Sada relačních dat |[Sada Relačních tabulek dat](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Verze 4.0.0
### <a name="breaking-changes"></a>Změny způsobující chyby
* Následující třídy/rozhraní byly přejmenovány.

| Starý název | Nové jméno |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Table |[Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| Vlastnosti tabulky |[Vlastnosti datové sady](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[Vlastnosti datasetType](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[Parametry DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[Odpověď datasetget](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| Odpověď na tablelist |[Odpověď datového seznamu](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| Parametry CreateOrUpdateWithRawJsonContentParameters |[Parametry DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* **List** metody vrátit stránkované výsledky nyní. Pokud odpověď obsahuje neprázdnou vlastnost **NextLink,** musí klientská aplikace pokračovat v načítání další stránky, dokud nebudou vráceny všechny stránky.  Zde naleznete příklad:

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
* **Seznam** potrubí rozhraní API vrátí pouze souhrn kanálu namísto úplné podrobnosti. Například aktivity v souhrnu kanálu obsahují pouze název a typ.

### <a name="feature-additions"></a>Přidání funkcí
* Třída [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) podporuje dvě nové **vlastnosti, SliceIdentifierColumnName** a **SqlWriterCleanupScript**, pro podporu idempotentní kopie do Azure SQL Data Warehouse. Podrobnosti o těchto vlastnostech najdete v článku [Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md)
* Teď podporujeme spuštění uložené procedury proti zdrojům Azure SQL Database a Azure SQL Data Warehouse jako součást aktivity kopírování. Třídy [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) a [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) mají následující vlastnosti: **SqlReaderStoredProcedureName** a **StoredProcedureParameters**. Podrobnosti o těchto vlastnostech najdete v článcích [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) a Azure SQL Data [Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) na Azure.com.  
