---
title: Azure Storage metrics v Azure monitorování | Microsoft Docs
description: Další informace o nové metriky nabízené z monitorování Azure.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/05/2017
ms.author: fryu
ms.openlocfilehash: 3c4c9a330286eb00aa6a0da6e95b56b49b6eaa6c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Metriky Azure Storage na platformě Azure Monitor

S metriky pro úložiště Azure můžete analyzovat trendy využití, požadavky trace a diagnostikovat problémy s vaším účtem úložiště.

Monitorování Azure poskytuje jednotné uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [Azure monitorování](../../monitoring-and-diagnostics/monitoring-overview.md). Úložiště Azure integruje Azure monitorování odesílá data metriky pro platformu Azure monitorování.

## <a name="access-metrics"></a>Metriky přístup

Azure monitorování poskytuje několik způsobů metriky přístup. Se dostanete z [portál Azure](https://portal.azure.com), rozhraní API Správce Azure monitorování (REST a rozhraní .net) a řešení pro analýzu například operace Management Suite a Event Hubs. Další informace najdete v tématu [Azure monitorování metriky](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Ve výchozím nastavení jsou povolené metriky a dostanete za posledních 30 dní data. Pokud potřebujete zachování dat pro delší časové období, můžete archivovat metriky dat do účtu Azure Storage. Toto je nakonfigurováno v [nastavení pro diagnostiku](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) v Azure monitorování.

### <a name="access-metrics-in-the-azure-portal"></a>Metriky přístup k portálu Azure

Metriky můžete sledovat v čase na portálu Azure. Následující příklad ukazuje, jak zobrazit **UsedCapacity** na úrovni účtu.

![snímek obrazovky přístup k metriky na portálu Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Pro podporu dimenze metriky musí filtrovat s hodnotou požadované dimenze. Následující příklad ukazuje, jak zobrazit **transakce** na úrovni účtu s **úspěch** typ odpovědi.

![snímek obrazovky přístup k metriky s dimenzí na portálu Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Metriky přístup pomocí rozhraní REST API

Poskytuje Azure monitorování [rozhraní REST API](/rest/api/monitor/) číst metriky definice a hodnoty. V této části se dozvíte, jak číst metriky pro úložiště. ID prostředku se používá v všechna rozhraní API REST. Další informace, přečtěte si prosím [seznámení s ID prostředku pro služby v úložišti](#understanding-resource-id-for-services-in-storage).

Následující příklad ukazuje, jak používat [ArmClient](https://github.com/projectkudu/ARMClient) na příkazovém řádku zjednodušit testování pomocí rozhraní REST API.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Účet úrovně metriky definice seznamu pomocí rozhraní REST API

Následující příklad ukazuje, jak seznam metriky definice na úrovni účtu:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2017-05-01-preview

```

Pokud chcete zobrazit seznam definice metrik pro objekt blob, table, soubor nebo fronty, zadejte jiné ID prostředku pro každou službu s rozhraním API.

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Čtení hodnot metriky úrovni účtu pomocí rozhraní REST API

Následující příklad ukazuje, jak číst metriky data na úrovni účtu:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metric=Availability&api-version=2017-05-01-preview&aggregation=Average&interval=PT1H"

```

V výše příklad, pokud chcete načíst hodnoty metriky pro objekt blob, table, soubor nebo fronty, je nutné zadat jiné ID prostředku pro každou službu s rozhraním API.

Následující odpověď obsahuje metriky hodnoty ve formátu JSON:

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

### <a name="access-metrics-with-the-net-sdk"></a>Metriky přístup pomocí .net SDK

Poskytuje Azure monitorování [.Net SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) číst metriky definice a hodnoty. [Ukázkový kód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) ukazuje způsob použití sady SDK s odlišnými parametry. Budete muset použít `0.18.0-preview` nebo novější verze pro úložiště metriky. ID prostředku se používá v rozhraní .net SDK. Další informace, přečtěte si prosím [seznámení s ID prostředku pro služby v úložišti](#understanding-resource-id-for-services-in-storage).

Následující příklad ukazuje, jak používat Azure Monitor .net SDK číst metriky pro úložiště.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Účet úrovně metriky definice seznamu pomocí .net SDK

Následující příklad ukazuje, jak seznam metriky definice na úrovni účtu:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
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

Pokud chcete zobrazit seznam definice metrik pro objekt blob, table, soubor nebo fronty, zadejte jiné ID prostředku pro každou službu s rozhraním API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Číst metriky hodnoty pomocí .net SDK

Následující příklad ukazuje, jak číst `UsedCapacity` data na úrovni účtu:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metric: "UsedCapacity",

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

V výše příklad, pokud chcete načíst hodnoty metriky pro objekt blob, table, soubor nebo fronty, je nutné zadat jiné ID prostředku pro každou službu s rozhraním API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Načíst vícerozměrných metriky hodnoty pomocí .net SDK

Multidimenzionální metrik budete muset definovat meta filtr dat, pokud si chcete přečíst metriky data na konkrétní hodnotu dimenze.

Následující příklad ukazuje, jak číst data metriky na metrika podpora více dimenze:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metric: "BlobCapacity",
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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Seznámení s ID prostředku pro služby ve službě Azure Storage

ID prostředku je jedinečný identifikátor prostředku v Azure. Pokud používáte rozhraní REST API Azure monitorování číst metriky definice nebo hodnoty, je nutné použít ID prostředku pro daný prostředek, na kterém chcete pracovat. ID šablony prostředků následující formát:

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

Úložiště poskytuje metriky na úrovni účtu úložiště a úrovně služby s monitorováním Azure. Například můžete načíst metriky pro jenom úložiště objektů Blob. Každá úroveň má vlastní ID prostředku, který se používá k načtení metriky pro právě této úrovni.

### <a name="resource-id-for-a-storage-account"></a>ID prostředku pro účet úložiště

Následující obrázek znázorňuje formát pro určení ID prostředku pro účet úložiště.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>ID prostředku pro služby úložiště

Následující obrázek znázorňuje formát pro určení ID prostředku pro jednotlivé služby úložiště.

* ID prostředku služby objektů BLOB `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* ID prostředku služby Table `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* ID prostředku služby fronty `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* ID prostředku služby souboru `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID prostředku v Azure monitorování rozhraní REST API

Na obrázku způsobem používaným při volání rozhraní REST API Azure monitorování.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>Metriky kapacity
Hodnoty metrik kapacity jsou odesílány monitorování Azure každou hodinu. Hodnoty se aktualizuje denně. Časový úsek definuje časový interval, pro které jsou uvedené hodnoty metrik. Podporované časovým intervalem pro všechny metriky kapacity je jedna hodina (PT1H).

Úložiště Azure poskytuje následující metriky kapacity v Azure monitorování.

### <a name="account-level"></a>Úrovni účtu

| Název metriky | Popis |
| ------------------- | ----------------- |
| UsedCapacity | Velikost úložiště používá účet úložiště. Pro účty úložiště standard storage je součet hodnot kapacity používá objekt blob, table, souboru a fronty. Pro prémiové účty úložiště a účty úložiště Blob je stejný jako BlobCapacity. <br/><br/> Jednotka: bajtů <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| BlobCapacity | Celkový počet využití úložiště objektů Blob v účtu úložiště. <br/><br/> Jednotka: bajtů <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 <br/> Dimenze: BlobType ([definice](#metrics-dimensions)) |
| BlobCount    | Počet objekty blob uložené v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 <br/> Dimenze: BlobType ([definice](#metrics-dimensions)) |
| ContainerCount    | Počet kontejnerů v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 |

### <a name="table-storage"></a>Úložiště Table

| Název metriky | Popis |
| ------------------- | ----------------- |
| TableCapacity | Velikost úložiště Table používá účet úložiště. <br/><br/> Jednotka: bajtů <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 |
| TableCount   | Počet tabulek v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 |
| TableEntityCount | Počet entit tabulky v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| QueueCapacity | Velikost fronty úložiště používá účet úložiště. <br/><br/> Jednotka: bajtů <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 |
| QueueCount   | Počet front v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 |
| QueueMessageCount | Počet neprošlé fronty zpráv v účtu úložiště. <br/><br/>Jednotka: počet <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 |

### <a name="file-storage"></a>File Storage

| Název metriky | Popis |
| ------------------- | ----------------- |
| FileCapacity | Velikost úložiště File používá účet úložiště. <br/><br/> Jednotka: bajtů <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 |
| FileCount   | Počet souborů v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 |
| FileShareCount | Počet souborů sdílených složek v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: Průměrná <br/> Příklad hodnoty: 1024 |

## <a name="transaction-metrics"></a>Metriky transakce

Transakce metriky odešlou ze služby Azure Storage do Azure monitorování každou minutu. Všechny metriky transakce jsou k dispozici na úrovni účtu a služby (úložiště objektů Blob, Table storage, Azure soubory a Queue storage). Časový úsek definuje časový interval, jsou uvedeny hodnoty metriky. Zrna podporované času pro všechny metriky transakce jsou PT1H a PT1M.

Úložiště Azure poskytuje následující metriky transakce v Azure monitorování.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Transakce | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. <br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Použít dimenze: ResponseType, GeoType, ApiName ([definice](#metrics-dimensions))<br/> Příklad hodnoty: 1024 |
| Příchozí přenos dat | Množství příchozích dat. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure. <br/><br/> Jednotka: bajtů <br/> Typ agregace: Celkový počet <br/> Použít dimenze: GeoType, ApiName ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Výchozí přenos | Množství výchozích dat. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat. <br/><br/> Jednotka: bajtů <br/> Typ agregace: Celkový počet <br/> Použít dimenze: GeoType, ApiName ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessServerLatency | Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu. <br/><br/> Jednotka: milisekundách <br/> Typ agregace: Průměrná <br/> Použít dimenze: GeoType, ApiName ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessE2ELatency | Průměrná celková latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. <br/><br/> Jednotka: milisekundách <br/> Typ agregace: Průměrná <br/> Použít dimenze: GeoType, ApiName ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Dostupnost | Procento dostupnosti pro službu úložiště nebo zadaná operace rozhraní API. Dostupnost se vypočítává tak, že hodnota celkový počet požadavků fakturovatelný dělení podle počtu příslušné požadavky, včetně těchto požadavků, které vytváří neočekávaným chybám. Všechny neočekávaným chybám výsledkem je omezená dostupnost pro službu úložiště nebo zadaná operace rozhraní API. <br/><br/> Jednotka: % <br/> Typ agregace: Průměrná <br/> Použít dimenze: GeoType, ApiName ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 99,99 |

## <a name="metrics-dimensions"></a>Dimenze metriky

Úložiště Azure podporuje následující dimenze pro metriky v Azure monitorování.

| Název dimenze | Popis |
| ------------------- | ----------------- |
| BlobType | Typ objektu blob pro pouze metriky objektů Blob. Podporované hodnoty jsou **BlockBlob** a **PageBlob**. Append – objekt Blob je součástí BlockBlob. |
| Hodnota ResponseType | Typ odpovědi transakce. Dostupné hodnoty: <br/><br/> <li>ServerOtherError: Popisuje ty, které jsou s výjimkou všechny ostatní chyby na straně serveru </li> <li> ServerBusyError: Ověřit požadavek, který vrátil kód stavu HTTP 503. </li> <li> ServerTimeoutError: Vypršení časového limitu ověřený požadavek, který vrátil kód stavu HTTP 500. Časový limit došlo k chybě serveru. </li> <li> AuthorizationError: Ověřený požadavek, který se nezdařilo z důvodu neoprávněného přístupu dat nebo k chybě autorizace. </li> <li> NetworkError: Ověřeného požadavku, která se nezdařila z důvodu chyby sítě. Nejčastěji dochází, když klient předčasně ukončí připojení, vypršení časového limitu. </li> <li>    ClientThrottlingError: Chyba omezení na straně klienta. </li> <li> ClientTimeoutError: Vypršení časového limitu ověřený požadavek, který vrátil kód stavu HTTP 500. Pokud časový limit sítě klienta nebo časový limit požadavku je nastavena na hodnotu nižší, než bylo očekáváno pomocí služby úložiště, je očekávané časový limit. Jinak jsou považovány za ServerTimeoutError. </li> <li> ClientOtherError: Všechny ostatní chyby klienta s výjimkou ty, které jsou popsány. </li> <li> Úspěch: Úspěšné žádosti|
| GeoType | Transakce z primární nebo sekundární clusteru. Dostupné hodnoty zahrnují primární i sekundární. Se vztahuje na přístup pro čtení geograficky redundantní Storage(RA-GRS) při čtení objektů ze sekundární klienta. |
| apiName | Název operace. Příklad: <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>Getblob –</li> Všechny názvy operací, najdete v části [dokumentu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages#logged-operations.md). |

Pro podpůrné dimenze metriky je třeba zadat hodnotu dimenze zobrazíte odpovídající hodnoty metrik. Například, pokud se podíváte na **transakce** hodnota pro úspěšné odpovědi, budete muset filtrovat **ResponseType** dimenze s **úspěch**. Nebo pokud se podíváte na **BlobCount** hodnotu pro objekt Blob bloku, budete muset filtrovat **BlobType** dimenze s **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Abyste zabránili výpadkům provozu starší verze metrik

Starší verze metriky jsou k dispozici souběžně s Azure monitorování spravované metriky. Podporu stejné uchová, dokud končí službu na starší verze metrik Azure Storage.

## <a name="next-steps"></a>Další postup

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
