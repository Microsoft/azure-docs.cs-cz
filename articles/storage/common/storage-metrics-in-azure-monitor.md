---
title: Azure Storage metriky v Azure Monitor | Microsoft Docs
description: Seznamte se s novými metrikami nabízenými z Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: e31ad78e24f329eb46cd85ba4a5962442a216779
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844828"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Metriky Azure Storage na platformě Azure Monitor

Díky metrikám na Azure Storage můžete analyzovat trendy využití, sledovat požadavky a diagnostikovat problémy s vaším účtem úložiště.

Azure Monitor poskytuje jednotná uživatelská rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [Azure monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Azure Storage integruje Azure Monitor tím, že odesílá data metriky platformě Azure Monitor.

## <a name="access-metrics"></a>Metriky přístup

Azure Monitor poskytuje několik způsobů přístupu metriky. K nim můžete přistupovat z [Azure Portal](https://portal.azure.com), rozhraní API Azure monitor (Rest a .NET) a analytických řešení, jako je například Event Hubs. Další informace najdete v tématu [Azure monitor metriky](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Metriky jsou ve výchozím nastavení povolené a můžete získat přístup k datům v posledních 93 dnech. Pokud je potřeba data uchovávat po delší dobu, můžete archivovat data metrik do účtu služby Azure Storage. Toto je nakonfigurováno v [nastavení diagnostiky](../../azure-monitor/platform/diagnostic-logs-overview.md) ve službě Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Přístup k metrikám v Azure Portal

Metriky můžete monitorovat v průběhu času v Azure Portal. Následující příklad ukazuje, jak zobrazit **transakce** na úrovni účtu.

![snímek obrazovky s přístupem k metrikám v Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Pro metriky podporující dimenze můžete metriku filtrovat pomocí požadované hodnoty dimenze. Následující příklad ukazuje, jak zobrazit **transakce** na úrovni účtu u konkrétní operace výběrem hodnot pro dimenzi **název rozhraní API** .

![snímek obrazovky s přístupem k metrikám s dimenzí v Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Přístup k metrikám pomocí REST API

Azure Monitor poskytuje [rozhraní REST API](/rest/api/monitor/) pro čtení definice metrik a hodnot. V této části se dozvíte, jak číst metriky úložiště. ID prostředku se používá ve všech rozhraních REST API. Další informace najdete v článku Principy ID prostředku pro služby v úložišti.

Následující příklad ukazuje, jak použít [ArmClient](https://github.com/projectkudu/ARMClient) na příkazovém řádku pro zjednodušení testování pomocí REST API.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Výpis definice metriky na úrovni účtu pomocí REST API

Následující příklad ukazuje, jak zobrazit definici metriky na úrovni účtu:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Pokud chcete zobrazit seznam definic metrik pro objekt blob, tabulku, soubor nebo frontu, musíte pro každou službu pomocí rozhraní API zadat různá ID prostředků.

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Číst hodnoty metrik na úrovni účtu pomocí REST API

Následující příklad ukazuje, jak číst data metrik na úrovni účtu:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Pokud například chcete číst hodnoty metrik pro objekt blob, tabulku, soubor nebo frontu, musíte pro každou službu pomocí rozhraní API zadat různá ID prostředků.

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

### <a name="access-metrics-with-the-net-sdk"></a>Přístup k metrikám pomocí .NET SDK

Azure Monitor poskytuje [sadu .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) pro čtení definice metrik a hodnot. [Vzorový kód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) ukazuje, jak použít sadu SDK s různými parametry. Pro metriky úložiště `0.18.0-preview` je nutné použít nebo novější verzi. ID prostředku se používá v sadě .NET SDK. Další informace najdete v článku Principy ID prostředku pro služby v úložišti.

Následující příklad ukazuje, jak použít sadu Azure Monitor .NET SDK ke čtení metrik úložiště.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Výpis definice metriky na úrovni účtu pomocí sady .NET SDK

Následující příklad ukazuje, jak zobrazit definici metriky na úrovni účtu:

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

Pokud chcete zobrazit seznam definic metrik pro objekt blob, tabulku, soubor nebo frontu, musíte pro každou službu pomocí rozhraní API zadat různá ID prostředků.

#### <a name="read-metric-values-with-the-net-sdk"></a>Čtení hodnot metriky pomocí sady .NET SDK

Následující příklad ukazuje, jak číst `UsedCapacity` data na úrovni účtu:

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

Pokud například chcete číst hodnoty metrik pro objekt blob, tabulku, soubor nebo frontu, musíte pro každou službu pomocí rozhraní API zadat různá ID prostředků.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Čtení hodnot multidimenzionální metriky pomocí sady .NET SDK

U multidimenzionálních metrik je nutné definovat filtr meta dat, pokud chcete číst data metriky pro konkrétní hodnotu dimenze.

Následující příklad ukazuje, jak číst data metriky v metrikě podporující více dimenzí:

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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Principy ID prostředku pro služby v Azure Storage

ID prostředku je jedinečný identifikátor prostředku v Azure. Při použití REST API Azure Monitor ke čtení definic metrik nebo hodnot je nutné použít ID prostředku pro prostředek, na kterém chcete pracovat. Šablona ID prostředku se řídí tímto formátem:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Storage poskytuje metriky na úrovni účtu úložiště i na úrovni služby s Azure Monitor. Můžete například načíst metriky pro pouze úložiště objektů BLOB. Každá úroveň má své vlastní ID prostředku, které se používá k načtení metrik jenom pro danou úroveň.

### <a name="resource-id-for-a-storage-account"></a>ID prostředku pro účet úložiště

Následující příklad uvádí formát pro zadání ID prostředku pro účet úložiště.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>ID prostředku pro služby úložiště

Následující příklad uvádí formát pro zadání ID prostředku pro každou službu úložiště.

* ID prostředku Blob service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* ID prostředku Table service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* ID prostředku Služba front
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* ID prostředku souborové služby
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID prostředku v Azure Monitor REST API

Následující příklad ukazuje vzor používaný při volání REST API Azure Monitor.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Metriky kapacity
Hodnoty metrik kapacity se odesílají do Azure Monitor každou hodinu. Hodnoty se aktualizují každý den. Časové rozpětí definuje časový interval, pro který jsou prezentovány hodnoty metrik. Podporovaná časová zrna pro všechny metriky kapacity je jedna hodina (PT1H).

Azure Storage poskytuje v Azure Monitor následující metriky kapacity.

### <a name="account-level"></a>Úroveň účtu

| Název metriky | Popis |
| ------------------- | ----------------- |
| UsedCapacity | Velikost úložiště využitého účtem úložiště U standardních účtů úložiště je to součet kapacity využité objektem blob, tabulkou, souborem a frontou. U účtů úložiště úrovně Premium a účtů služby Blob se rovná hodnotě BlobCapacity. <br/><br/> Jednotce B <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| BlobCapacity | Celkem úložiště objektů BLOB použité v účtu úložiště. <br/><br/> Jednotce B <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 <br/> Rozměry: **BlobType**a **BlobTier** ([definice](#metrics-dimensions)) |
| BlobCount    | Počet objektů BLOB uložených v účtu úložiště. <br/><br/> Jednotce Count <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 <br/> Rozměry: **BlobType**a **BlobTier** ([definice](#metrics-dimensions)) |
| ContainerCount    | Počet kontejnerů v účtu úložiště. <br/><br/> Jednotce Count <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |
| IndexCapacity     | Velikost úložiště, kterou používá ADLS Gen2 hierarchický index <br/><br/> Jednotce B <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |

### <a name="table-storage"></a>Úložiště tabulek

| Název metriky | Popis |
| ------------------- | ----------------- |
| TableCapacity | Velikost úložiště tabulek, kterou používá účet úložiště <br/><br/> Jednotce B <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |
| TableCount   | Počet tabulek v účtu úložiště <br/><br/> Jednotce Count <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |
| TableEntityCount | Počet entit tabulky v účtu úložiště <br/><br/> Jednotce Count <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| QueueCapacity | Velikost úložiště fronty, kterou účet úložiště používá <br/><br/> Jednotce B <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |
| QueueCount   | Počet front v účtu úložiště. <br/><br/> Jednotce Count <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |
| QueueMessageCount | Počet zpráv ve frontě s neplatným vypršením platnosti v účtu úložiště <br/><br/>Jednotce Count <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |

### <a name="file-storage"></a>File Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| Kapacita zařízení | Velikost úložiště souborů, kterou používá účet úložiště. <br/><br/> Jednotce B <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |
| FileCount   | Počet souborů v účtu úložiště. <br/><br/> Jednotce Count <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |
| FileShareCount | Počet sdílených složek v účtu úložiště. <br/><br/> Jednotce Count <br/> Typ agregace: Average <br/> Příklad hodnoty: 1024 |

## <a name="transaction-metrics"></a>Metriky transakcí

Metriky transakcí jsou vydávány při každém požadavku na účet úložiště z Azure Storage do Azure Monitor. V případě, že váš účet úložiště neobsahuje žádné aktivity, nebudou v obdobích metrik transakcí žádná data. Všechny transakční metriky jsou k dispozici na úrovni účtu i služby (úložiště objektů blob, úložiště tabulek, soubory Azure a úložiště front). Časové rozpětí definuje časový interval, po který jsou zobrazeny hodnoty metriky. Podporovaná časová zrna pro všechny transakční metriky jsou PT1H a PT1M.

Azure Storage poskytuje následující metriky transakcí v Azure Monitor.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Transakce | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. <br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Použitelné dimenze: ResponseType, typ, ApiName a ověřování ([definice](#metrics-dimensions))<br/> Příklad hodnoty: 1024 |
| Příchozí přenos dat | Množství příchozích dat. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure. <br/><br/> Jednotce B <br/> Typ agregace: Celkem <br/> Použitelné dimenze: Typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Výchozí přenos | Množství výchozích dat. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat. <br/><br/> Jednotce B <br/> Typ agregace: Celkem <br/> Použitelné dimenze: Typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessServerLatency | Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu. <br/><br/> Jednotce Milisekundy <br/> Typ agregace: Average <br/> Použitelné dimenze: Typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessE2ELatency | Průměrná celková latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. <br/><br/> Jednotce Milisekundy <br/> Typ agregace: Average <br/> Použitelné dimenze: Typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Dostupnost | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota celkových fakturovatelných požadavků vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API. <br/><br/> Jednotce Percent <br/> Typ agregace: Average <br/> Použitelné dimenze: Typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 99,99 |

## <a name="metrics-dimensions"></a>Dimenze metriky

Azure Storage podporuje pro metriky v Azure Monitor následující dimenze.

| Název dimenze | Popis |
| ------------------- | ----------------- |
| **BlobType** | Typ objektu BLOB pouze pro metriky objektů BLOB Podporované hodnoty jsou **BlockBlob**, **PageBlob**a **Azure Data Lake Storage**. Doplňovací objekt BLOB je zahrnutý v BlockBlob. |
| **BlobTier** | Služba Azure Storage nabízí různé úrovně přístupu, které umožňují ukládat data objektů BLOB nejefektivnějším způsobem. Další informace najdete v [Azure Storage úrovni objektů BLOB](../blobs/storage-blob-storage-tiers.md). Mezi podporované hodnoty patří: <br/> <li>**Horká**: Horká vrstva</li> <li>**Studená**: Studená vrstva</li> <li>**Archiv**: Úroveň archivu</li> <li>**Premium**: Úroveň Premium pro objekt blob bloku</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Typy vrstev pro objekt blob stránky úrovně Premium</li> <li>**Standardní**: Typ vrstvy pro objekt BLOB Standard Page</li> <li>Nevrstvený: Typ vrstvy pro účet úložiště pro obecné účely v1</li> |
| **GeoType** | Transakce z primárního nebo sekundárního clusteru. Dostupné hodnoty zahrnují **Primary** a **Secondary**. Vztahuje se na geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) při čtení objektů ze sekundárního tenanta. |
| **ResponseType** | Typ odpovědi transakce. Dostupné hodnoty zahrnují: <br/><br/> <li>**ServerOtherError**: Všechny ostatní chyby na straně serveru kromě zde popsaných </li> <li>**ServerBusyError**: Ověřená žádost, která vrátila stavový kód HTTP 503. </li> <li>**ServerTimeoutError**: Ověřená žádost s vypršeným časovým limitem, který vrátil stavový kód HTTP 500. Časový limit vypršel kvůli chybě serveru. </li> <li>**AuthorizationError**: Ověřená žádost, která selhala kvůli neoprávněnému přístupu k datům nebo chybě autorizace. </li> <li>**NetworkError**: Ověřená žádost, která selhala kvůli chybě sítě. K tomu nejčastěji dochází, když klient předčasně ukončí spojení před vypršením časového limitu. </li> <li>**ClientThrottlingError**: Chyba omezování využití sítě na straně klienta. </li> <li>**ClientTimeoutError**: Ověřená žádost s vypršeným časovým limitem, který vrátil stavový kód HTTP 500. Pokud je časový limit sítě klienta nebo časový limit žádosti nastavený na hodnotu nižší, než služba úložiště očekávala, jde o očekávané vypršení časového limitu. V opačném případě bude ohlášeno jako ServerTimeoutError. </li> <li>**ClientOtherError**: Všechny ostatní chyby na straně klienta kromě zde popsaných. </li> <li>**Success**: Úspěšná žádost</li> <li> **SuccessWithThrottling**: Úspěšná žádost, když se klient SMB omezí při prvním pokusu (s), ale po opakování proběhne úspěch.</li> |
| **ApiName** | Název operace. Příklad: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Všechny názvy operací naleznete v [dokumentu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Autentizace** | Typ ověřování používaný v transakcích. Dostupné hodnoty zahrnují: <br/> <li>**AccountKey**: Transakce se ověřuje pomocí klíče účtu úložiště.</li> <li>**SAS**: Transakce je ověřena pomocí sdílených přístupových podpisů.</li> <li>**OAuth**: Transakce se ověřuje pomocí přístupových tokenů OAuth.</li> <li>**Anonymní**: Transakce se požaduje anonymně. Neobsahuje požadavky na kontrolu před výstupem.</li> <li>**AnonymousPreflight**: Transakce je požadavek na kontrolu před výstupem.</li> |

Pro metriky podporující dimenze je nutné zadat hodnotu dimenze, aby se zobrazily odpovídající hodnoty metrik. Například pokud se podíváte na hodnotu **transakce** pro úspěšné odpovědi, je nutné filtrovat dimenzi **ResponseType** s úspěchem. Nebo pokud se podíváte na **BlobCount** hodnotu pro objekt blob bloku, musíte filtrovat dimenzi **BlobType** pomocí **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Kontinuita služeb zastaralých metrik

Starší metriky jsou k dispozici paralelně s Azure Monitor spravovanými metrikami. Podpora zůstává stejná, dokud Azure Storage neukončí službu ve starších metrikách.

## <a name="faq"></a>Nejčastější dotazy

**Podporují nové metriky klasický účet úložiště?**

Ne, nové metriky v Azure Monitor podporují jenom účty úložiště Azure Resource Manager. Pokud chcete použít metriky pro účty úložiště, je třeba migrovat na Azure Resource Manager účet úložiště. Viz [migrace na Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Podporuje Azure Storage metriky pro Managed Disks nebo nespravované disky?**

Ne, výpočetní prostředí Azure podporuje metriky na discích. Další podrobnosti najdete v [článku](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) .

**Jak namapovat a migrovat klasické metriky s novými metrikami?**

Podrobné mapování mezi klasickými metrikami a novými metrikami můžete najít v [Azure Storage migraci metrik](./storage-metrics-migration.md).

## <a name="next-steps"></a>Další kroky

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
