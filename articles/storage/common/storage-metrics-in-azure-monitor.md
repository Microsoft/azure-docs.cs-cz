---
title: Metriky Azure Storage ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Přečtěte si o nových metrikách nabízených z Azure Monitoru.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 73181222bf3f15bbbac24fc253eddfea1c57bc6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247091"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Metriky Azure Storage na platformě Azure Monitor

Pomocí metrik ve službě Azure Storage můžete analyzovat trendy využití, požadavky na trasování a diagnostikovat problémy s účtem úložiště.

Azure Monitor poskytuje jednotná uživatelská rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Azure Storage integruje Azure Monitor odesláním dat metriky na platformu Azure Monitor.

## <a name="access-metrics"></a>Metriky přístupu

Azure Monitor poskytuje několik způsobů, jak získat přístup k metrikám. Přístup k nim můžete z [portálu Azure,](https://portal.azure.com)rozhraní API Azure Monitor (REST a .NET) a řešení analýzy, jako jsou centra událostí. Další informace najdete v tématu [Metriky azure monitoru](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Metriky jsou ve výchozím nastavení povoleny a máte přístup k datům za posledních 93 dní. Pokud potřebujete uchovávat data po delší dobu, můžete archivovat data metriky do účtu Azure Storage. To se nakonfiguruje v [nastavení diagnostiky](../../azure-monitor/platform/platform-logs-overview.md) v Azure Monitoru.

### <a name="access-metrics-in-the-azure-portal"></a>Přístup k metrikám na webu Azure Portal

Metriky v průběhu času můžete sledovat na webu Azure Portal. Následující příklad ukazuje, jak zobrazit **transakce** na úrovni účtu.

![snímek obrazovky s přístupem k metrikám na webu Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

U metrik podporujících dimenze můžete filtrovat metriku s požadovanou hodnotou dimenze. Následující příklad ukazuje, jak zobrazit **transakce** na úrovni účtu na konkrétní operaci výběrem hodnot pro dimenzi **Název rozhraní API.**

![Snímek obrazovky s přístupem k metrikám s dimenzí na webu Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Přístup k metrikám pomocí rozhraní REST API

Azure Monitor poskytuje [REST API](/rest/api/monitor/) pro čtení definice metriky a hodnoty. V této části se zobrazí, jak číst metriky úložiště. ID prostředku se používá ve všech souborech REST APIS. Další informace najdete v článek Principy ID prostředků pro služby ve službě.

Následující příklad ukazuje, jak používat [ArmClient](https://github.com/projectkudu/ARMClient) na příkazovém řádku pro zjednodušení testování pomocí rozhraní REST API.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Definice metriky na úrovni účtu s rozhraním REST API

Následující příklad ukazuje, jak vypsat definici metriky na úrovni účtu:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Pokud chcete vypsat definice metrik pro objekt blob, tabulku, soubor nebo frontu, musíte zadat různá ID prostředků pro každou službu s rozhraním API.

Odpověď obsahuje definici metriky ve formátu JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Čtení hodnot metrik na úrovni účtu pomocí rozhraní REST API

Následující příklad ukazuje, jak číst metrická data na úrovni účtu:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Ve výše uvedeném příkladu, pokud chcete číst hodnoty metrik pro objekt blob, tabulka, soubor nebo fronty, je nutné zadat různá ID prostředků pro každou službu s rozhraním API.

Následující odpověď obsahuje hodnoty metrik ve formátu JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Přístup k metrikám pomocí sady .NET SDK

Azure Monitor poskytuje [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) pro čtení definice metriky a hodnoty. [Ukázkový kód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) ukazuje, jak používat sadu SDK s různými parametry. Pro metriky `0.18.0-preview` úložiště je potřeba použít novější verzi. ID prostředku se používá v sdk .NET. Další informace najdete v článek Principy ID prostředků pro služby ve službě.

Následující příklad ukazuje, jak používat Azure Monitor .NET SDK ke čtení metrikúložiště.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Definice metriky na úrovni účtu seznamu pomocí sady .NET SDK

Následující příklad ukazuje, jak vypsat definici metriky na úrovni účtu:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Pokud chcete vypsat definice metrik pro objekt blob, tabulku, soubor nebo frontu, musíte zadat různá ID prostředků pro každou službu s rozhraním API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Čtení hodnot metrik pomocí sady .NET SDK

Následující příklad ukazuje, `UsedCapacity` jak číst data na úrovni účtu:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

Ve výše uvedeném příkladu, pokud chcete číst hodnoty metrik pro objekt blob, tabulka, soubor nebo fronty, je nutné zadat různá ID prostředků pro každou službu s rozhraním API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Čtení vícerozměrných hodnot metrik pomocí sady .NET SDK

Pro vícerozměrné metriky je třeba definovat meta datový filtr, pokud chcete číst metrická data o konkrétní hodnotě dimenze.

Následující příklad ukazuje, jak číst data metriky na metriku podporující více dimenzí:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Principy ID prostředků pro služby ve službě Azure Storage

ID prostředku je jedinečný identifikátor prostředku v Azure. Při použití rozhraní API Azure Monitor REST ke čtení definice metrik nebo hodnoty, musíte použít ID prostředku pro prostředek, na kterém chcete pracovat. Šablona ID prostředku se řídí tímto formátem:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Úložiště poskytuje metriky na úrovni účtu úložiště i na úrovni služeb pomocí Azure Monitoru. Například můžete načíst metriky pro úložiště objektů blob. Každá úroveň má své vlastní ID prostředku, který se používá k načtení metriky pouze pro tuto úroveň.

### <a name="resource-id-for-a-storage-account"></a>ID prostředku pro účet úložiště

V následujícím textu je uveden formát pro určení ID prostředku pro účet úložiště.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>ID prostředku pro služby úložiště

Následující text ukazuje formát pro určení ID prostředku pro každou službu úložiště.

* ID prostředku služby objektu blob
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* ID prostředku služby Table Service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* ID prostředku služby fronty
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* ID prostředku souborové služby
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID prostředků v rozhraní REST monitoru Azure Monitor

Následující ukazuje vzor používaný při volání rozhraní API Azure Monitor REST.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Metriky kapacity
Hodnoty metrik kapacity se zasílají do Azure Monitoru každou hodinu. Hodnoty jsou aktualizovány denně. Časové zrno definuje časový interval, pro který jsou zobrazeny hodnoty metrik. Podporované časové zrnitosti pro všechny metriky kapacity je jedna hodina (PT1H).

Azure Storage poskytuje následující metriky kapacity ve službě Azure Monitor.

### <a name="account-level"></a>Úroveň účtu

| Název metriky | Popis |
| ------------------- | ----------------- |
| Usedcapacity | Velikost úložiště používaného účtem úložiště. U standardních účtů úložiště je to součet kapacity využité objektem blob, tabulkou, souborem a frontou. U účtů úložiště úrovně Premium a účtů služby Blob se rovná hodnotě BlobCapacity. <br/><br/> Jednotka: Bajty <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| Možnost objektů blob | Celkové úložiště objektů Blob použité v účtu úložiště. <br/><br/> Jednotka: Bajty <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 <br/> Rozměry: **Objekt blobType**a **Objekt blobTier** ([definice](#metrics-dimensions)) |
| Počet objektů BlobCount    | Počet objektů objektů objektů blob uložených v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 <br/> Rozměry: **Objekt blobType**a **Objekt blobTier** ([definice](#metrics-dimensions)) |
| ContainerCount    | Počet kontejnerů v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| IndexCapacity     | Velikost úložiště používaného hierarchickým indexem ADLS Gen2 <br/><br/> Jednotka: Bajty <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |

### <a name="table-storage"></a>Úložiště Table

| Název metriky | Popis |
| ------------------- | ----------------- |
| TabulkaKapacita | Velikost table storage používaného účtem úložiště. <br/><br/> Jednotka: Bajty <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| Počet tabulek   | Počet tabulek v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| Počet tabulkových entií | Počet entit tabulky v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| QueueCapacity | Velikost úložiště fronty používaného účtem úložiště. <br/><br/> Jednotka: Bajty <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| Počet front   | Počet front v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| QueueMessageCount | Počet zpráv fronty, jejichž platnost nevypršela v účtu úložiště. <br/><br/>Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |

### <a name="file-storage"></a>File Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| Souborkapacita | Velikost úložiště souborů používaného účtem úložiště. <br/><br/> Jednotka: Bajty <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| Počet souborů   | Počet souborů v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |
| FileShareCount | Počet sdílených složek v účtu úložiště. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Příklad hodnoty: 1024 |

## <a name="transaction-metrics"></a>Transakční metriky

Metriky transakcí se vyzařují při každém požadavku na účet úložiště z Azure Storage do Azure Monitoru. V případě, že na vašem účtu úložiště není žádná aktivita, nebudou v období k dispozici žádné údaje o metrikách transakcí. Všechny metriky transakcí jsou k dispozici na úrovni účtu i služby (úložiště objektů blob, úložiště tabulek, soubory Azure a úložiště fronty). Časové zrno definuje časový interval, ve kterých jsou zobrazeny hodnoty metriky. Podporovaná časová zrna pro všechny metriky transakcí jsou PT1H a PT1M.

Azure Storage poskytuje následující metriky transakcí ve službě Azure Monitor.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Transakce | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. <br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Příslušné dimenze: ResponseType, GeoType, ApiName a Authentication ([Definice](#metrics-dimensions))<br/> Příklad hodnoty: 1024 |
| Příchozí přenos dat | Množství příchozích dat. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure. <br/><br/> Jednotka: Bajty <br/> Typ agregace: Celkem <br/> Příslušné dimenze: GeoType, ApiName a Authentication ([Definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Výchozí přenos dat | Množství výchozích dat. Toto číslo zahrnuje odchozí do externího klienta z Azure Storage, stejně jako odchozí v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat. <br/><br/> Jednotka: Bajty <br/> Typ agregace: Celkem <br/> Příslušné dimenze: GeoType, ApiName a Authentication ([Definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessServerLatency | Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu. <br/><br/> Jednotka: Milisekundy <br/> Typ agregace: Průměr <br/> Příslušné dimenze: GeoType, ApiName a Authentication ([Definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessE2ELatency | Průměrná celková latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. <br/><br/> Jednotka: Milisekundy <br/> Typ agregace: Průměr <br/> Příslušné dimenze: GeoType, ApiName a Authentication ([Definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Dostupnost | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota celkových fakturovatelných požadavků vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. <br/><br/> Jednotka: Procento <br/> Typ agregace: Průměr <br/> Příslušné dimenze: GeoType, ApiName a Authentication ([Definice](#metrics-dimensions)) <br/> Příklad hodnoty: 99,99 |

## <a name="metrics-dimensions"></a>Dimenze metrik

Azure Storage podporuje následující dimenze pro metriky v Azure Monitoru.

| Název dimenze | Popis |
| ------------------- | ----------------- |
| **Typ objektu blob** | Typ objektu blob pouze pro metriky objektu blob. Podporované hodnoty jsou **BlockBlob**, **PageBlob**a **Azure Data Lake Storage**. Připojit objekt blob je součástí BlockBlob. |
| **Objekt BlobTier** | Azure storage nabízí různé úrovně přístupu, které umožňují ukládat data objektů objektů objektů blob nákladově nejefektivnějším způsobem. Další informace najdete v [úrovni objektu blob azure storage](../blobs/storage-blob-storage-tiers.md). Mezi podporované hodnoty patří: <br/> <li>**Hot**: Horká úroveň</li> <li>**Cool:** Cool úroveň</li> <li>**Archiv**: Archivní vrstva</li> <li>**Premium:** Úroveň Premium pro objekt blob bloku</li> <li>**P4/P6/P10/P15/P20/P30/P50/P60**: Typy úrovní pro bitové blob stránky premium</li> <li>**Standard**: Typ úrovně pro standardní stránku Blob</li> <li>**Untiered**: Typ úrovně pro účet úložiště pro obecné účely v1</li> |
| **Geotype** | Transakce z primárního nebo sekundárního clusteru. Dostupné hodnoty zahrnují **primární** a **sekundární**. Platí pro geograficky redundantní úložiště pro čtení (RA-GRS) při čtení objektů ze sekundárního tenanta. |
| **Typ odpovědi** | Typ odpovědi transakce. Dostupné hodnoty zahrnují: <br/><br/> <li>**ServerOtherError**: Všechny ostatní chyby na straně serveru s výjimkou popsaných chyb </li> <li>**ServerBusyError**: Ověřený požadavek, který vrátil stavový kód HTTP 503. </li> <li>**ServerTimeoutError**: Časový čas ověřeného požadavku, který vrátil stavový kód HTTP 500. Časový limit vypršel kvůli chybě serveru. </li> <li>**AuthorizationError**: Ověřený požadavek, který se nezdařil z důvodu neoprávněného přístupu k datům nebo selhání autorizace. </li> <li>**NetworkError**: Ověřený požadavek, který se nezdařil z důvodu chyb v síti. K tomu nejčastěji dochází, když klient předčasně ukončí spojení před vypršením časového limitu. </li>  <li>**ClientAccountBandwidthTrottlingError**: Požadavek je omezen na šířku pásma pro překročení [limitů škálovatelnosti účtu úložiště](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: Požadavek je omezen na míru požadavku pro překročení [limitů škálovatelnosti účtu úložiště](scalability-targets-standard-account.md).<li>**ClientThrottlingError:** Další chyba omezení na straně klienta. ClientAccountBandwidthTrottlingError a ClientAccountRequestThrottlingError jsou vyloučeny.</li> <li>**ClientTimeoutError**: Časový čas ověřeného požadavku, který vrátil stavový kód HTTP 500. Pokud je časový limit sítě klienta nebo časový limit žádosti nastavený na hodnotu nižší, než služba úložiště očekávala, jde o očekávané vypršení časového limitu. V opačném případě bude ohlášeno jako ServerTimeoutError.</li> </li> <li>**ClientOtherError**: Všechny ostatní chyby na straně klienta s výjimkou popsaných chyb. </li> <li>**Úspěch**: Úspěšný požadavek</li> <li> **SuccessWithTrottling**: Úspěšný požadavek při klienta SMB získá omezení v první pokusy, ale úspěšné po opakování.</li> |
| **Název api** | Název operace. Například: <br/> <li>**Vytvořitkontejner**</li> <li>**Odstranit blob**</li> <li>**GetBlob**</li> Všechny názvy operací naleznete v [dokumentu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Ověřování** | Typ ověřování používaný v transakcích. Dostupné hodnoty zahrnují: <br/> <li>**AccountKey**: Transakce je ověřena pomocí klíče účtu úložiště.</li> <li>**SAS**: Transakce je ověřena pomocí sdílených přístupových podpisů.</li> <li>**OAuth**: Transakce je ověřena pomocí přístupových tokenů OAuth.</li> <li>**Anonymní**: Transakce je požadována anonymně. Neobsahuje požadavky na kontroly před výstupem.</li> <li>**AnonymousPreflight**: Transakce je požadavek kontroly před výstupem.</li> |

Pro metriky podporující dimenze je třeba zadat hodnotu dimenze, abyste viděli odpovídající hodnoty metrik. Pokud se například podíváte na hodnotu **Transakce** pro úspěšné odpovědi, je třeba filtrovat dimenzi **ResponseType** s **hodnotou Úspěch**. Nebo pokud se podíváte na **BlobCount** hodnotu pro objekt Blob bloku, je třeba filtrovat dimenzi **BlobType** s **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Kontinuita služeb starších metrik

Starší metriky jsou k dispozici paralelně se spravovanými metrikami Azure Monitoru. Podpora udržuje stejné, dokud Azure Storage ukončí službu na starší metriky.

## <a name="faq"></a>Nejčastější dotazy

**Podporují nové metriky účet klasického úložiště?**

Ne, nové metriky ve Službě Azure Monitor podporují jenom účty úložiště Azure Resource Manager. Pokud chcete používat metriky na účtech úložiště, musíte migrovat na účet úložiště Azure Resource Manager. Viz [Migrace do Správce prostředků Azure](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Podporuje Azure Storage metriky pro spravované disky nebo nespravované disky?**

Ne, Azure Compute podporuje metriky na discích. Další podrobnosti najdete v [článku.](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)

**Jak mapovat a migrovat klasické metriky pomocí nových metrik?**

Podrobné mapování mezi klasickými metrikami a novými metrikami najdete v [migraci metrik Azure Storage](./storage-metrics-migration.md).

## <a name="next-steps"></a>Další kroky

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
