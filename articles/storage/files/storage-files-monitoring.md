---
title: Monitorování souborů Azure | Microsoft Docs
description: Naučte se monitorovat výkon a dostupnost souborů Azure. Monitorujte data souborů Azure, zjistěte informace o konfiguraci a analyzujte metriky a data protokolů.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: e0c5e6041da933b4a42bc438900f8c4c91cc6dbc
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711366"
---
# <a name="monitoring-azure-files"></a>Monitorování souborů Azure

Pokud máte důležité aplikace a obchodní procesy, které spoléhají na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování vygenerovaná soubory Azure a způsob použití funkcí Azure Monitor k analýze výstrah na těchto datech.

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [tuto stránku](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek. Tato funkce je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Viz [Přehled účtu úložiště](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Přehled monitorování

Stránka **Přehled** v Azure Portal pro každý prostředek služby soubory Azure zahrnuje stručný přehled využití prostředků, jako jsou třeba žádosti a hodinová fakturace. Tyto informace jsou užitečné, ale k dispozici je jenom malé množství dat monitorování. Některá z těchto dat jsou shromažďována automaticky a jsou k dispozici pro analýzu ihned po vytvoření prostředku. Můžete povolit další typy shromažďování dat s určitou konfigurací.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
Služba soubory Azure vytváří data monitorování pomocí [Azure monitor](../../azure-monitor/overview.md), což je plná služba monitorování zásobníku v Azure. Azure Monitor poskytuje kompletní sadu funkcí pro monitorování prostředků a prostředků Azure v jiných cloudech a v místním prostředí. 

Začněte s článkem [monitorování prostředků Azure pomocí Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), který popisuje následující informace:

- Co je Azure Monitor?
- Náklady spojené s monitorováním
- Monitorování dat shromážděných v Azure
- Konfigurace shromažďování dat
- Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části jsou uvedené v tomto článku, které popisují konkrétní data získaná ze souborů Azure. Příklady ukazují, jak nakonfigurovat shromažďování dat a analyzovat tato data pomocí nástrojů Azure.

## <a name="monitoring-data"></a>Data monitorování

Soubory Azure shromažďují stejný druh dat monitorování jako jiné prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Podrobné informace o metrikách a protokolech, které vytváří služba soubory Azure, najdete v referenčních informacích k [datům monitorování souborů Azure](storage-files-monitoring-reference.md) .

Metriky a protokoly v Azure Monitor podporují jenom účty úložiště Azure Resource Manager. Azure Monitor nepodporuje účty klasického úložiště. Pokud chcete používat metriky nebo protokoly v klasickém účtu úložiště, musíte migrovat na účet úložiště Azure Resource Manager. Viz [migrace na Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

## <a name="collection-and-routing"></a>Shromažďování a směrování

Metriky platforem a protokol aktivit jsou shromažďovány automaticky, ale mohou být směrovány do jiných umístění pomocí diagnostického nastavení. Je nutné vytvořit nastavení diagnostiky pro shromáždění protokolů prostředků. 

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [tuto stránku](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Tato verze Preview umožňuje protokoly pro objekty BLOB (včetně Azure Data Lake Storage Gen2), soubory, fronty, tabulky, účty Premium Storage pro obecné účely V1 a účty úložiště pro obecné účely v2. Klasické účty úložiště se nepodporují.

Pokud chcete vytvořit nastavení diagnostiky pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu, přečtěte si téma [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](../../azure-monitor/platform/diagnostic-settings.md). 

Chcete-li zobrazit šablonu Azure Resource Manager, která vytvoří nastavení diagnostiky, přečtěte si téma [nastavení diagnostiky pro Azure Storage](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage).

Při vytváření nastavení diagnostiky vyberte typ úložiště, pro které chcete povolit protokoly, jako je například objekt blob, fronta, tabulka nebo soubor. V případě služby soubory Azure vyberte **soubor**. 

Pokud vytvoříte nastavení diagnostiky v Azure Portal, můžete prostředek vybrat ze seznamu. Pokud používáte PowerShell nebo rozhraní příkazového řádku Azure, musíte použít ID prostředku koncového bodu služby soubory Azure. ID prostředku můžete najít v Azure Portal tak, že otevřete stránku **vlastností** svého účtu úložiště.

Musíte také zadat jednu z následujících kategorií operací, pro které chcete shromažďovat protokoly. 

| Kategorie | Popis |
|:---|:---|
| StorageRead | Operace čtení u objektů |
| StorageWrite | Operace zápisu pro objekty. |
| StorageDelete | Odstraní operace s objekty. |

## <a name="analyzing-metrics"></a>Analýza metrik

Metriky můžete analyzovat pro Azure Storage s využitím metrik z jiných služeb Azure pomocí Průzkumník metrik. Otevřete Průzkumník metrik tím, že v nabídce **Azure monitor** vyberete **metriky** . Podrobnosti o používání tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../../azure-monitor/platform/metrics-getting-started.md). 

Pro metriky, které podporují dimenze, můžete metriku filtrovat pomocí požadované hodnoty dimenze.  Úplný seznam dimenzí, které Azure Storage podporuje, najdete v tématu věnovaném [dimenzím metrik](storage-files-monitoring-reference.md#metrics-dimensions). Metriky pro soubory Azure jsou v těchto oborech názvů: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/služby

Seznam Azure Monitor všech metrik podpory, které obsahují soubory Azure, najdete v tématu [Azure monitor podporovaných metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Přístup k metrikám

> [!TIP]
> Pokud chcete zobrazit příklady rozhraní příkazového řádku Azure CLI nebo .NET, vyberte odpovídající karty, které jsou tady uvedené.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Výpis definice metriky

Můžete vypsat definici metriky svého účtu úložiště nebo služby soubory Azure. Použijte rutinu [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition) .

V tomto příkladu nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo ID prostředku služby soubory Azure.  Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Čtení hodnot metriky

Můžete číst hodnoty metriky na úrovni účtu účtu úložiště nebo služby soubory Azure. Použijte rutinu [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Výpis definice metriky na úrovni účtu

Můžete vypsat definici metriky svého účtu úložiště nebo služby soubory Azure. Použijte příkaz [AZ monitor Metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) .
 
V tomto příkladu nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo ID prostředku služby soubory Azure. Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Číst hodnoty metrik na úrovni účtu

Můžete si přečíst hodnoty metrik účtu úložiště nebo služby soubory Azure. Použijte příkaz [AZ monitor Metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor poskytuje [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) pro čtení definice metrik a hodnot. [Vzorový kód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) ukazuje, jak použít sadu SDK s různými parametry. `0.18.0-preview`Pro metriky úložiště je nutné použít nebo novější verzi.
 
V těchto příkladech nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo služby soubory Azure. Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

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
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
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

## <a name="analyzing-logs"></a>Analýza protokolů

Protokoly prostředků můžete přistupovat buď jako objekt BLOB v účtu úložiště, jako data události, nebo prostřednictvím analytických dotazů log.

Podrobné informace o polích, která se zobrazují v těchto protokolech, najdete v tématu Referenční informace k [datům monitorování Azure Azure Files](storage-files-monitoring-reference.md).

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [tuto stránku](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Tato verze Preview umožňuje protokoly pro objekty BLOB (včetně Azure Data Lake Storage Gen2), soubory, fronty, tabulky, účty Premium Storage pro obecné účely V1 a účty úložiště pro obecné účely v2. Klasické účty úložiště se nepodporují.

Položky protokolu jsou vytvořeny pouze v případě, že jsou zadány požadavky na koncový bod služby. Pokud například účet úložiště obsahuje aktivitu v koncovém bodu souboru, ale ne ve svých koncových bodech tabulky nebo fronty, vytvoří se pouze protokoly, které se týkají této souborové služby. Protokoly Azure Storage obsahují podrobné informace o úspěšných a neúspěšných požadavcích služby úložiště. Tyto informace je možné použít k monitorování jednotlivých požadavků a diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí.

### <a name="log-authenticated-requests"></a>Protokolovat ověřené požadavky

 Protokolují se následující typy ověřovaných požadavků:

- Úspěšné požadavky
- Neúspěšné požadavky, včetně vypršení časového limitu, omezování, chyb sítě, selhání autorizace a dalších chyb
- Požadavky, které používají sdílený přístupový podpis (SAS) nebo OAuth, včetně neúspěšných a úspěšných požadavků
- Požadavky na analytické údaje (data protokolu Classic v kontejneru **$logs** a data metriky třídy v **$metricch** tabulkách)

Požadavky samotné službou soubory Azure, jako je vytvoření nebo odstranění protokolu, nejsou protokolovány. Úplný seznam protokolovaných dat naleznete v tématu [operace protokolované úložiště a stavové zprávy](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Formát protokolu úložiště](storage-files-monitoring-reference.md).

### <a name="log-anonymous-requests"></a>Protokolování anonymních požadavků

 Protokolují se tyto typy anonymních požadavků:

- Úspěšné požadavky
- Chyby serveru
- Chyby vypršení časového limitu u klientů i serveru
- Neúspěšné žádosti o získání s kódem chyby 304 (nezměněno)

Všechny ostatní neúspěšné anonymní požadavky nejsou protokolovány. Úplný seznam protokolovaných dat naleznete v tématu [operace protokolované úložiště a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Formát protokolu úložiště](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Přístup k protokolům v účtu úložiště

Protokoly se zobrazí jako objekty blob uložené do kontejneru v cílovém účtu úložiště. Data se shromažďují a ukládají v jednom objektu BLOB jako datová část JSON s oddělenými řádky. Název objektu BLOB se řídí těmito zásadami vytváření názvů:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Tady je příklad:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Přístup k protokolům v centru událostí

Protokoly odeslané do centra událostí nejsou uloženy jako soubor, ale můžete ověřit, že centrum událostí obdrželo informace protokolu. V Azure Portal přejdete do centra událostí a ověříte, že počet **příchozích zpráv** je větší než nula. 

![Protokoly auditu](media/storage-files-monitoring/event-hub-log.png)

Data protokolu, která se odesílají do centra událostí, můžete otevřít a číst pomocí nástrojů pro správu a správu událostí a monitorování událostí a informací o zabezpečení. Další informace najdete v tématu [co se dá dělat s daty monitorování odesílanými do mého centra událostí?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Přístup k protokolům v pracovním prostoru Log Analytics

K protokolům odesílaným do Log Analytics pracovního prostoru můžete přistupovat pomocí dotazů protokolu Azure Monitor. Data se ukládají do tabulky **StorageFileLogs** . 

Další informace najdete v tématu [Začínáme s Log Analytics v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

#### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Tady jsou některé dotazy, které můžete zadat do panelu **hledání v protokolu** , abyste mohli monitorovat soubory Azure. Tyto dotazy fungují s [novým jazykem](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

> [!IMPORTANT]
> Když vyberete **protokoly** z nabídky Skupina prostředků účtu úložiště, Log Analytics se otevře s oborem dotazu nastaveným na aktuální skupinu prostředků. To znamená, že dotazy protokolu budou zahrnovat jenom data z dané skupiny prostředků. Pokud chcete spustit dotaz, který zahrnuje data z jiných prostředků nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) .

Pomocí těchto dotazů vám pomůžou monitorovat sdílené složky Azure:

- Zobrazit chyby protokolu SMB za poslední týden

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Vytvoření výsečového grafu operací SMB za poslední týden

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Zobrazit chyby REST za poslední týden

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Vytvoření výsečového grafu operací REST za poslední týden

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Seznam názvů sloupců a popisů pro soubory Azure najdete v tématu [StorageFileLogs](https://docs.microsoft.com/azure/azure-monitor/reference/tables/storagefilelogs).

Další informace o tom, jak zapisovat dotazy, najdete v tématu [kurz: Začínáme s Log Analytics dotazy](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

## <a name="alerts"></a>Upozornění

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy v systému před tím, než si je vaši zákazníci všimnete. Můžete nastavit výstrahy na [metrikách](/azure/azure-monitor/platform/alerts-metric-overview), [protokolech](/azure/azure-monitor/platform/alerts-unified-log)a [protokolu aktivit](/azure/azure-monitor/platform/activity-log-alerts). 

V následující tabulce jsou uvedeny příklady scénářů, které je třeba monitorovat, a správnou metriku pro použití výstrahy:

| Scénář | Metrika, která se má použít pro upozornění |
|-|-|
| Sdílení souborů je omezené. | Metrika: transakcí<br>Název dimenze: typ odpovědi <br>Název dimenze: sdílená složka (jenom pro sdílení souborů Premium) |
| Velikost sdílené složky je 80% kapacity. | Metrika: kapacita souboru<br>Název dimenze: sdílená složka (jenom pro sdílení souborů Premium) |
| Odchozí výstup sdílení souborů překročil 500 GiB za jeden den. | Metrika: odchozí data<br>Název dimenze: sdílená složka (jenom pro sdílení souborů Premium) |

### <a name="how-to-create-alerts-for-azure-files"></a>Jak vytvořit výstrahy pro soubory Azure

1. V **Azure Portal**přejít na svůj **účet úložiště** . 

2. Klikněte na **výstrahy** a pak klikněte na **+ nové pravidlo výstrahy**.

3. Klikněte na **Upravit prostředek**, vyberte **typ prostředku** a pak klikněte na **Hotovo**. 

4. Klikněte na **vybrat podmínku** a zadejte pro tuto výstrahu následující informace: 

    - **Metrika**
    - **Název dimenze**
    - **Logika výstrahy**

5. Klikněte na **Vybrat skupinu akcí** a přidejte do ní skupinu akcí (E-mail, SMS atd.), a to buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.

6. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis**a **závažnost**.

7. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.

> [!NOTE]  
> Pokud vytvoříte výstrahu a je příliš hlučná, upravte prahovou hodnotu a logiku výstrahy.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Postup vytvoření výstrahy, pokud je sdílená složka omezená

1. V **Azure Portal**přejít na svůj **účet úložiště** .
2. V části **monitorování** klikněte na **výstrahy**a pak klikněte na **+ nové pravidlo výstrahy**.
3. Klikněte na **Upravit prostředek**, vyberte **typ prostředku** pro účet úložiště a pak klikněte na **Hotovo**. Pokud je třeba název účtu úložiště `contoso` , vyberte `contoso/file` prostředek.
4. Kliknutím na **vybrat podmínku** přidáte podmínku.
5. Zobrazí se seznam signálů, které jsou pro účet úložiště podporované, vyberte metriku **transakcí** .
6. V okně **Konfigurovat logiku signálu** klikněte na rozevírací seznam **název dimenze** a vyberte **typ odpovědi**.
7. Klikněte na rozevírací seznam **hodnoty dimenze** a vyberte **SUCCESSWITHTHROTTLING** (pro SMB) nebo **ClientThrottlingError** (pro REST).

   > [!NOTE]
   > Pokud není uvedená hodnota dimenze SuccessWithThrottling nebo ClientThrottlingError, znamená to, že prostředek nebyl omezen. Chcete-li přidat hodnotu dimenze, klikněte na tlačítko **Přidat vlastní hodnotu** vedle rozevíracího seznamu **hodnoty dimenzí** , zadejte **SuccessWithThrottling** nebo **ClientThrottlingError**, klikněte na tlačítko **OK** a poté opakujte krok #7.

8. Klikněte na rozevírací seznam **název dimenze** a vyberte **sdílení souborů**.
9. Klikněte na rozevírací seznam **hodnoty dimenze** a vyberte sdílené složky, na kterých chcete upozornit.

   > [!NOTE]
   > Pokud je sdílená složka standardní sdílená složka, vyberte **všechny aktuální a budoucí hodnoty**. Rozevírací seznam hodnoty dimenze nebude zobrazovat seznam sdílených složek, protože pro standardní sdílené složky nejsou k dispozici metriky pro jednotlivé sdílené složky. Výstrahy omezování pro standardní sdílené složky se aktivují, pokud je omezená jakákoli sdílená složka v rámci účtu úložiště a výstraha neurčí, která sdílená složka byla omezená. Vzhledem k tomu, že pro standardní sdílené složky nejsou k dispozici metriky jednotlivých sdílených složek, doporučuje se mít pro každý účet úložiště jednu sdílenou složku.

10. Definujte **Parametry výstrahy** (prahová hodnota, operátor, členitost agregace a frekvence vyhodnocení) a klikněte na **Hotovo**.

    > [!TIP]
    > Pokud používáte statickou prahovou hodnotu, graf metriky může pomoci určit rozumnou prahovou hodnotu, pokud je sdílená složka momentálně omezená. Pokud používáte dynamickou prahovou hodnotu, v grafu metriky se zobrazí vypočtené prahové hodnoty na základě nedávných dat.

11. Kliknutím na **Vybrat skupinu akcí** přidejte **skupinu akcí** (e-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
12. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, * * popis a **závažnost**.
13. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Jak vytvořit výstrahu, pokud je velikost sdílené složky Azure 80% kapacity

1. V **Azure Portal**přejít na svůj **účet úložiště** .
2. V části **monitorování** klikněte na možnost **výstrahy** a potom klikněte na tlačítko **+ nové pravidlo výstrahy**.
3. Klikněte na **Upravit prostředek**, vyberte **typ prostředku** pro účet úložiště a pak klikněte na **Hotovo**. Pokud je třeba název účtu úložiště `contoso` , vyberte `contoso/file` prostředek.
4. Kliknutím na **vybrat podmínku** přidáte podmínku.
5. Zobrazí se seznam signálů, které jsou pro účet úložiště podporované, a vyberte metriku **kapacity souboru** .
6. V okně **Konfigurovat logiku signálu** klikněte na rozevírací seznam **název dimenze** a vyberte **sdílení souborů**.
7. Klikněte na rozevírací seznam **hodnoty dimenze** a vyberte sdílené složky, na kterých chcete upozornit.

   > [!NOTE]
   > Pokud je sdílená složka standardní sdílená složka, vyberte **všechny aktuální a budoucí hodnoty**. Rozevírací seznam hodnoty dimenze nebude zobrazovat seznam sdílených složek, protože pro standardní sdílené složky nejsou k dispozici metriky pro jednotlivé sdílené složky. Výstrahy pro standardní sdílení souborů jsou založené na všech sdílených složkách v účtu úložiště. Vzhledem k tomu, že pro standardní sdílené složky nejsou k dispozici metriky jednotlivých sdílených složek, doporučuje se mít pro každý účet úložiště jednu sdílenou složku.

8. Zadejte **prahovou hodnotu** v bajtech. Pokud je například velikost sdílené složky 100 TiB a chcete dostat upozornění, když je velikost sdílené složky 80% kapacity, prahová hodnota v bajtech je 87960930222080.
9. Definujte zbytek **parametrů výstrahy** (členitost agregace a četnost vyhodnocení) a klikněte na **Hotovo**.
10. Kliknutím na vybrat skupinu akcí přidejte skupinu akcí (e-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
11. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, * * popis a **závažnost**.
12. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Jak vytvořit výstrahu v případě, že výstup Azure File Share přesáhl 500 GiB za den

1. V **Azure Portal**přejít na svůj **účet úložiště** .
2. V části monitorování klikněte na možnost **výstrahy** a potom klikněte na tlačítko **+ nové pravidlo výstrahy**.
3. Klikněte na **Upravit prostředek**, vyberte **typ prostředku** pro účet úložiště a pak klikněte na **Hotovo**. Pokud je třeba název účtu úložiště contoso, vyberte prostředek contoso/File.
4. Kliknutím na **vybrat podmínku** přidáte podmínku.
5. Zobrazí se seznam signálů, které jsou pro účet úložiště podporované, a vyberte **výstupní** metriku.
6. V okně **Konfigurovat logiku signálu** klikněte na rozevírací seznam **název dimenze** a vyberte **sdílení souborů**.
7. Klikněte na rozevírací seznam **hodnoty dimenze** a vyberte sdílené složky, na kterých chcete upozornit.

   > [!NOTE]
   > Pokud je sdílená složka standardní sdílená složka, vyberte **všechny aktuální a budoucí hodnoty**. Rozevírací seznam hodnoty dimenze nebude zobrazovat seznam sdílených složek, protože pro standardní sdílené složky nejsou k dispozici metriky pro jednotlivé sdílené složky. Výstrahy pro standardní sdílení souborů jsou založené na všech sdílených složkách v účtu úložiště. Vzhledem k tomu, že pro standardní sdílené složky nejsou k dispozici metriky jednotlivých sdílených složek, doporučuje se mít pro každý účet úložiště jednu sdílenou složku.

8. Jako prahovou hodnotu zadejte **536870912000** bajtů. 
9. Klikněte na rozevírací seznam **členitosti agregace** a vyberte **24 hodin**.
10. Vyberte **frekvenci hodnocení** a **klikněte na Hotovo**.
11. Kliknutím na **Vybrat skupinu akcí** přidejte **skupinu akcí** (e-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
12. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, * * popis a **závažnost**.
13. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.

## <a name="next-steps"></a>Další kroky

- [Referenční informace o datech monitorování služby Azure Files](storage-files-monitoring.md)
- [Monitorování prostředků Azure pomocí Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md)
- [Azure Storage migrace metrik](../common/storage-metrics-migration.md)
- [Plánování nasazení Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning)
- [Nasazení služby Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)
- [Řešení problémů se Soubory Azure ve Windows](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)
- [Řešení problémů se Soubory Azure v Linuxu](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-linux-file-connection-problems)
