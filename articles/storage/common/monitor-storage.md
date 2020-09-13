---
title: Azure Storage monitorování | Microsoft Docs
description: Naučte se monitorovat výkon a dostupnost Azure Storage. Monitorujte Azure Storage data, Naučte se konfiguraci a analyzujte data metrik a protokolů.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 0edb50fd72622d3d7d628e0e02ef2c3737f8713a
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500415"
---
# <a name="monitoring-azure-storage"></a>Azure Storage monitorování

Pokud máte důležité aplikace a obchodní procesy, které spoléhají na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování generovaná nástrojem Azure Storage a způsob použití funkcí Azure Monitor k analýze výstrah na těchto datech.

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [tuto stránku](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek. Tato funkce je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Viz [Přehled účtu úložiště](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Přehled monitorování

Stránka **Přehled** v Azure Portal pro každý prostředek úložiště zahrnuje stručný přehled využití prostředků, jako jsou požadavky a hodinová fakturace. Tyto informace jsou užitečné, ale k dispozici je jenom malé množství dat monitorování. Některá z těchto dat se shromažďují automaticky a jsou dostupná k analýze hned po vytvoření prostředku úložiště. Můžete povolit další typy shromažďování dat s určitou konfigurací.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
Azure Storage vytvoří data monitorování pomocí [Azure monitor](../../azure-monitor/overview.md), což je plná služba monitorování zásobníku v Azure. Azure Monitor poskytuje kompletní sadu funkcí pro monitorování prostředků a prostředků Azure v jiných cloudech a v místním prostředí. 

Začněte s článkem [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) , který popisuje následující informace:

- Co je Azure Monitor?
- Náklady spojené s monitorováním
- Monitorování dat shromážděných v Azure
- Konfigurace shromažďování dat
- Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části jsou uvedené v tomto článku, které popisují konkrétní data získaná z Azure Storage. Příklady ukazují, jak nakonfigurovat shromažďování dat a analyzovat tato data pomocí nástrojů Azure.

## <a name="monitoring-data-from-azure-storage"></a>Monitorování dat z Azure Storage

Azure Storage shromažďuje stejné typy dat monitorování jako jiné prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Další informace o protokolech a metrikách vytvořených pomocí Azure Storage naleznete v tématu [Azure Storage monitoring data reference](monitor-storage-reference.md).

Metriky a protokoly v Azure Monitor podporují jenom účty úložiště Azure Resource Manager. Azure Monitor nepodporuje účty klasického úložiště. Pokud chcete používat metriky nebo protokoly v klasickém účtu úložiště, musíte migrovat na účet úložiště Azure Resource Manager. Viz [migrace na Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Pokud chcete, můžete i nadále používat klasické metriky a protokoly. Klasické metriky a protokoly jsou ve skutečnosti k dispozici paralelně s metrikami a protokoly v Azure Monitor. Podpora zůstane v platnosti, dokud Azure Storage neukončí službu ve starších metrikách a protokolech.

### <a name="logs-in-azure-monitor-preview"></a>Protokoly v Azure Monitor (Preview)

Položky protokolu jsou vytvořeny pouze v případě, že jsou zadány požadavky na koncový bod služby. Pokud například účet úložiště obsahuje aktivitu v koncovém bodu objektu blob, ale ne ve svých koncových bodech tabulky nebo fronty, vytvoří se jenom protokoly, které se týkají služby BLOB Service. Protokoly Azure Storage obsahují podrobné informace o úspěšných a neúspěšných požadavcích služby úložiště. Tyto informace je možné použít k monitorování jednotlivých požadavků a diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí.

#### <a name="log-authenticated-requests"></a>Protokolovat ověřené požadavky

 Protokolují se následující typy ověřovaných požadavků:

- Úspěšné požadavky
- Neúspěšné požadavky, včetně vypršení časového limitu, omezování, chyb sítě, selhání autorizace a dalších chyb
- Požadavky, které používají sdílený přístupový podpis (SAS) nebo OAuth, včetně neúspěšných a úspěšných požadavků
- Požadavky na analytické údaje (data protokolu Classic v kontejneru **$logs** a data metriky třídy v **$metricch** tabulkách)

Požadavky samotné službou úložiště, jako je vytvoření nebo odstranění protokolu, nejsou protokolovány. Úplný seznam protokolovaných dat naleznete v tématu [operace protokolované úložiště a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Formát protokolu úložiště](monitor-storage-reference.md).

#### <a name="log-anonymous-requests"></a>Protokolování anonymních požadavků

 Protokolují se tyto typy anonymních požadavků:

- Úspěšné požadavky
- Chyby serveru
- Chyby vypršení časového limitu pro klienta i server
- Neúspěšné žádosti o získání s kódem chyby 304 (nezměněno)

Všechny ostatní neúspěšné anonymní požadavky nejsou protokolovány. Úplný seznam protokolovaných dat naleznete v tématu [operace protokolované úložiště a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Formát protokolu úložiště](monitor-storage-reference.md).

## <a name="configuration"></a>Konfigurace

Metriky platforem a protokol aktivit jsou shromažďovány automaticky, ale je nutné vytvořit nastavení diagnostiky pro shromáždění protokolů prostředků nebo jejich přeposílání mimo Azure Monitor. Postup vytvoření nastavení diagnostiky pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](../../azure-monitor/platform/diagnostic-settings.md).

Při vytváření nastavení diagnostiky vyberte typ úložiště, pro které chcete povolit protokoly, jako je například objekt blob, fronta, tabulka nebo soubor. Data Lake Storage Gen2 se nezobrazuje jako typ úložiště. Důvodem je to, že Data Lake Storage Gen2 sadu funkcí dostupných pro úložiště objektů BLOB. 

Pokud vytvoříte nastavení diagnostiky v Azure Portal, můžete prostředek vybrat ze seznamu. Pokud používáte PowerShell nebo rozhraní příkazového řádku Azure, musíte použít ID prostředku typu úložiště. ID prostředku můžete najít v Azure Portal tak, že otevřete stránku **vlastností** svého účtu úložiště.

Musíte také zadat kategorie operací, pro které chcete shromažďovat protokoly. Kategorie pro Azure Storage jsou uvedeny v této tabulce.

| Kategorie | Popis |
|:---|:---|
| StorageRead | Operace čtení u objektů |
| StorageWrite | Operace zápisu pro objekty. |
| StorageDelete | Odstraní operace s objekty. |

## <a name="analyzing-metric-data"></a>Analýza dat metriky

Metriky můžete analyzovat pro Azure Storage s využitím metrik z jiných služeb Azure pomocí Průzkumník metrik. Otevřete Průzkumník metrik tím, že v nabídce **Azure monitor** vyberete **metriky** . Podrobnosti o používání tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../../azure-monitor/platform/metrics-getting-started.md). 

Tento příklad ukazuje, jak zobrazit **transakce** na úrovni účtu.

![Snímek obrazovky s přístupem k metrikám v Azure Portal](./media/monitor-storage/access-metrics-portal.png)

Pro metriky, které podporují dimenze, můžete metriku filtrovat pomocí požadované hodnoty dimenze. Tento příklad ukazuje, jak zobrazit **transakce** na úrovni účtu u konkrétní operace výběrem hodnot pro dimenzi **název rozhraní API** .

![Snímek obrazovky s přístupem k metrikám s dimenzí v Azure Portal](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Úplný seznam dimenzí, které Azure Storage podporuje, najdete v tématu věnovaném [dimenzím metrik](monitor-storage-reference.md#metrics-dimensions).

Všechny metriky pro Azure Storage jsou umístěny v těchto oborech názvů:

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/blobServices
- Microsoft. Storage/storageAccounts/služby
- Microsoft. Storage/storageAccounts/queueServices
- Microsoft. Storage/storageAccounts/tableServices

Seznam Azure Monitor všech metrik podpory, které zahrnují Azure Storage, najdete v článku [Azure monitor podporované metriky](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="accessing-metrics"></a>Přístup k metrikám

> [!TIP]
> Pokud chcete zobrazit příklady rozhraní příkazového řádku Azure CLI nebo .NET, vyberte odpovídající karty, které jsou tady uvedené.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Výpis definice metriky

Můžete vypsat definici metriky svého účtu úložiště nebo jednotlivé služby úložiště, jako je například objekt blob, soubor, tabulka nebo služba front. Použijte rutinu [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) .

V tomto příkladu nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo ID prostředku jednotlivé služby úložiště, jako je například objekt blob, soubor, tabulka nebo služba front. Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Čtení hodnot metriky

Můžete číst hodnoty metriky na úrovni účtu účtu úložiště nebo jednotlivé služby úložiště, jako je například objekt blob, soubor, tabulka nebo služba front. Použijte rutinu [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Výpis definice metriky na úrovni účtu

Můžete vypsat definici metriky svého účtu úložiště nebo jednotlivé služby úložiště, jako je například objekt blob, soubor, tabulka nebo služba front. Použijte příkaz [AZ monitor Metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) .
 
V tomto příkladu nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo ID prostředku jednotlivé služby úložiště, jako je například objekt blob, soubor, tabulka nebo služba front. Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Číst hodnoty metrik na úrovni účtu

Můžete si přečíst hodnoty metrik účtu úložiště nebo jednotlivé služby úložiště, jako je například objekt blob, soubor, tabulka nebo služba front. Použijte příkaz [AZ monitor Metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor poskytuje [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) pro čtení definice metrik a hodnot. [Vzorový kód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) ukazuje, jak použít sadu SDK s různými parametry. `0.18.0-preview`Pro metriky úložiště je nutné použít nebo novější verzi.
 
V těchto příkladech nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo ID prostředku jednotlivé služby úložiště, jako je například objekt blob, soubor, tabulka nebo služba front. Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

Nahraďte `<subscription-ID>` proměnnou číslem ID vašeho předplatného. Pokyny k získání hodnot pro `<tenant-ID>` , `<application-ID>` a `<AccessKey>` najdete v tématu [použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-the-account-level-metric-definition"></a>Výpis definice metriky na úrovni účtu

Následující příklad ukazuje, jak zobrazit definici metriky na úrovni účtu:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
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

#### <a name="reading-account-level-metric-values"></a>Čtení hodnot metrik na úrovni účtu

Následující příklad ukazuje, jak číst `UsedCapacity` data na úrovni účtu:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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
            // Enumrate metric value
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

#### <a name="reading-multidimensional-metric-values"></a>Čtení hodnot multidimenzionální metriky

Pro multidimenzionální metriky je nutné definovat filtry metadat, pokud chcete číst data metriky pro konkrétní hodnoty dimenze.

Následující příklad ukazuje, jak číst data metriky v metrikě podporující více dimenzí:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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

---

## <a name="analyzing-log-data"></a>Analýza dat protokolu

Protokoly prostředků můžete přistupovat buď jako objekt BLOB v účtu úložiště, jako data události, nebo prostřednictvím analytických dotazů log.

Podrobný referenční informace o polích, která se zobrazují v těchto protokolech, najdete v tématu [Azure Storage – referenční informace k datům monitorování](monitor-storage-reference.md).

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [tuto stránku](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Tato verze Preview umožňuje protokoly pro objekty BLOB (včetně Azure Data Lake Storage Gen2), soubory, fronty, tabulky, účty Premium Storage pro obecné účely V1 a účty úložiště pro obecné účely v2. Klasické účty úložiště se nepodporují.

### <a name="accessing-logs-in-a-storage-account"></a>Přístup k protokolům v účtu úložiště

Protokoly se zobrazí jako objekty blob uložené do kontejneru v cílovém účtu úložiště. Data se shromažďují a ukládají v jednom objektu BLOB jako datová část JSON s oddělenými řádky. Název objektu BLOB se řídí těmito zásadami vytváření názvů:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Tady je příklad:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Přístup k protokolům v centru událostí

Protokoly odeslané do centra událostí nejsou uloženy jako soubor, ale můžete ověřit, že centrum událostí obdrželo informace protokolu. V Azure Portal přejdete do centra událostí a ověříte, že počet **příchozích zpráv** je větší než nula. 

![Protokoly auditu](media/monitor-storage/event-hub-log.png)

Data protokolu, která se odesílají do centra událostí, můžete otevřít a číst pomocí nástrojů pro správu a správu událostí a monitorování událostí a informací o zabezpečení. Další informace najdete v tématu [co se dá dělat s daty monitorování odesílanými do mého centra událostí?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Přístup k protokolům v pracovním prostoru Log Analytics

K protokolům odesílaným do Log Analytics pracovního prostoru můžete přistupovat pomocí dotazů protokolu Azure Monitor.

Další informace najdete v tématu [Začínáme s Log Analytics v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Data jsou uložena v těchto tabulkách.

| Tabulka | Popis |
|:---|:---|
|StorageBlobLogs | Protokoly, které popisují činnost v úložišti objektů BLOB. |
|StorageFileLogs | Protokoly, které popisují činnost ve sdílených složkách souborů. |
|StorageQueueLogs | Protokoly, které popisují aktivity ve frontách.|
|StorageTableLogs| Protokoly, které popisují aktivity v tabulkách.|

Protokoly pro Data Lake Storage Gen2 se neobjevují ve vyhrazené tabulce. Důvodem je, že Data Lake Storage Gen2 není služba. Jde o sadu funkcí, které můžete povolit v účtu služby Blob Storage. Pokud jste tyto možnosti povolili, protokoly se budou dál zobrazovat v tabulce StorageBlobLogs. 

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Azure Storage Log Analytics dotazy v Azure Monitor

Tady jsou některé dotazy, které můžete zadat do panelu **hledání protokolu** , abyste mohli monitorovat účty Azure Storage. Tyto dotazy fungují s [novým jazykem](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Tyto dotazy vám pomůžou monitorovat účty Azure Storage:

* K vypsání deseti nejběžnějších chyb za poslední tři dny.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* K vypsání prvních 10 operací, které způsobily nejvíce chyb za poslední tři dny.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* K vypsání prvních 10 operací s nejdelší koncovou latencí za poslední tři dny.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* K vypsání všech operací, které v posledních třech dnech způsobily chyby omezení na straně serveru.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* K vypsání všech žádostí s anonymním přístupem za poslední tři dny.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* K vytvoření výsečového grafu operací používaných za poslední tři dny.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Nejčastější dotazy

**Podporuje Azure Storage metriky pro Managed Disks nebo nespravované disky?**

No. Azure COMPUTE podporuje metriky na discích. Další informace najdete v tématu [metriky jednotlivých disků pro spravované a nespravované disky](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Další kroky

- Odkaz na protokoly a metriky vytvořené nástrojem Azure Storage najdete v tématu informace o [Azure Storage monitorování dat](monitor-storage-reference.md).
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md).
- Další informace o migraci metrik najdete v tématu [Azure Storage migrace metrik](./storage-metrics-migration.md).
