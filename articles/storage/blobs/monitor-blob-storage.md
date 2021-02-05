---
title: Monitorování Azure Blob Storage | Microsoft Docs
description: Naučte se monitorovat výkon a dostupnost Azure Blob Storage. Monitorujte data Blob Storage Azure, Naučte se konfigurovat a analyzujte data metrik a protokolů.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: subject-monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 4ca74070c1b0d2cd4a5cf4225443465e080b518d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584885"
---
# <a name="monitoring-azure-blob-storage"></a>Monitorování Blob Storage Azure

Pokud máte důležité aplikace a obchodní procesy, které spoléhají na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování vygenerovaná službou Azure Blob Storage a způsob použití funkcí Azure Monitor k analýze výstrah na těchto datech.

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek. Tato funkce je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Viz [Přehled účtu úložiště](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Přehled monitorování

Stránka **Přehled** v Azure Portal pro každý prostředek úložiště objektů BLOB zahrnuje stručný přehled využití prostředků, jako jsou požadavky a hodinová fakturace. Tyto informace jsou užitečné, ale k dispozici je jenom malé množství dat monitorování. Některá z těchto dat jsou shromažďována automaticky a jsou k dispozici pro analýzu ihned po vytvoření prostředku. Můžete povolit další typy shromažďování dat s určitou konfigurací.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
Azure Blob Storage vytváří data monitorování pomocí [Azure monitor](../../azure-monitor/overview.md), což je plná služba monitorování zásobníku v Azure. Azure Monitor poskytuje kompletní sadu funkcí pro monitorování prostředků a prostředků Azure v jiných cloudech a v místním prostředí. 

Začněte s článkem [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) , který popisuje následující informace:

- Co je Azure Monitor?
- Náklady spojené s monitorováním
- Monitorování dat shromážděných v Azure
- Konfigurace shromažďování dat
- Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části jsou uvedené v tomto článku, které popisují konkrétní data získaná z Azure Storage. Příklady ukazují, jak nakonfigurovat shromažďování dat a analyzovat tato data pomocí nástrojů Azure.

## <a name="monitoring-data"></a>Data monitorování

Azure Blob Storage shromažďuje stejné typy dat monitorování jako jiné prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Podrobné informace o metrikách a protokolech, které vytvořila služba Azure Blob Storage, najdete v tématu Referenční informace o [službě azure BLOB Storage monitoring data](monitor-blob-storage-reference.md) .

Metriky a protokoly v Azure Monitor podporují jenom účty úložiště Azure Resource Manager. Azure Monitor nepodporuje účty klasického úložiště. Pokud chcete používat metriky nebo protokoly v klasickém účtu úložiště, musíte migrovat na účet úložiště Azure Resource Manager. Viz [migrace na Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

Pokud chcete, můžete i nadále používat klasické metriky a protokoly. Klasické metriky a protokoly jsou ve skutečnosti k dispozici paralelně s metrikami a protokoly v Azure Monitor. Podpora zůstane v platnosti, dokud Azure Storage neukončí službu ve starších metrikách a protokolech.

## <a name="collection-and-routing"></a>Shromažďování a směrování

Metriky platforem a protokol aktivit jsou shromažďovány automaticky, ale mohou být směrovány do jiných umístění pomocí diagnostického nastavení. 

Chcete-li shromáždit protokoly prostředků, je nutné vytvořit nastavení diagnostiky. Když vytvoříte nastavení, vyberte **objekt BLOB** jako typ úložiště, pro který chcete povolit protokoly. Pak zadejte jednu z následujících kategorií operací, pro které chcete shromažďovat protokoly. 

| Kategorie | Popis |
|:---|:---|
| StorageRead | Operace čtení u objektů |
| StorageWrite | Operace zápisu pro objekty. |
| StorageDelete | Odstraní operace s objekty. |

> [!NOTE]
> Data Lake Storage Gen2 se nezobrazuje jako typ úložiště. Důvodem je to, že Data Lake Storage Gen2 sadu funkcí dostupných pro úložiště objektů BLOB. 

## <a name="creating-a-diagnostic-setting"></a>Vytvoření nastavení diagnostiky

Nastavení diagnostiky můžete vytvořit pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo šablony Azure Resource Manager. 

Obecné pokyny najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](../../azure-monitor/platform/diagnostic-settings.md).

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek. Tato funkce je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Viz [Přehled účtu úložiště](../common/storage-account-overview.md).

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k portálu Azure.

2. Přejděte na svůj účet úložiště.

3. V části **monitorování** klikněte na **nastavení diagnostiky (Preview)**.

   > [!div class="mx-imgBorder"]
   > ![portál – diagnostické protokoly](media/monitor-blob-storage/diagnostic-logs-settings-pane.png)   

4. Jako typ úložiště, pro který chcete povolit protokoly, vyberte **objekt BLOB** .

5. Klikněte na **Přidat nastavení diagnostiky**.

   > [!div class="mx-imgBorder"]
   > ![portál – protokoly prostředků – přidání nastavení diagnostiky](media/monitor-blob-storage/diagnostic-logs-settings-pane-2.png)

   Zobrazí se stránka **nastavení diagnostiky** .

   > [!div class="mx-imgBorder"]
   > ![Stránka protokoly prostředků](media/monitor-blob-storage/diagnostic-logs-page.png)

6. Do pole **název** na stránce zadejte název pro toto nastavení protokolu prostředků. Pak vyberte, které operace chcete protokolovat (operace čtení, zápisu a odstranění) a kam chcete protokoly odeslat.

#### <a name="archive-logs-to-a-storage-account"></a>Archivace protokolů do účtu úložiště

Pokud se rozhodnete archivovat protokoly do účtu úložiště, platíte za objem protokolů, které se odesílají do účtu úložiště. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Zaškrtněte políčko **archivovat do účtu úložiště** a pak klikněte na tlačítko **Konfigurovat** .

   > [!div class="mx-imgBorder"]   
   > ![Archivní úložiště stránky nastavení diagnostiky](media/monitor-blob-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. V rozevíracím seznamu **účet úložiště** vyberte účet úložiště, do kterého chcete archivovat protokoly, klikněte na tlačítko **OK** a potom klikněte na tlačítko **Uložit** .

   > [!NOTE]
   > Než zvolíte účet úložiště jako cíl exportu, přečtěte si téma [archivace protokolů prostředků Azure](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage) a pochopení požadavků v účtu úložiště.

#### <a name="stream-logs-to-azure-event-hubs"></a>Streamování protokolů do Azure Event Hubs

Pokud se rozhodnete streamovat protokoly do centra událostí, platíte za objem protokolů, které se odesílají do centra událostí. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Zaškrtněte políčko **datový proud do centra událostí** a pak klikněte na tlačítko **Konfigurovat** .

2. V podokně **Vyberte centrum událostí** zvolte obor názvů, název a název zásady centra událostí, do kterého chcete protokoly streamovat. 

   > [!div class="mx-imgBorder"]
   > ![Centrum událostí stránky nastavení diagnostiky](media/monitor-blob-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **Uložit** .

#### <a name="send-logs-to-azure-log-analytics"></a>Odeslat protokoly do Azure Log Analytics

1. Zaškrtněte políčko **Odeslat do Log Analytics** , vyberte pracovní prostor Log Analytics a pak klikněte na tlačítko **Uložit** .

   > [!div class="mx-imgBorder"]   
   > ![Stránka nastavení diagnostiky Log Analytics](media/monitor-blob-storage/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Otevřete příkazové okno prostředí Windows PowerShell a přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkazu. Pak postupujte podle pokynů na obrazovce.

   ```powershell
   Connect-AzAccount
   ```

2. Nastavte aktivní předplatné na předplatné účtu úložiště, pro který chcete povolit protokolování.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Archivace protokolů do účtu úložiště

Pokud se rozhodnete archivovat protokoly do účtu úložiště, platíte za objem protokolů, které se odesílají do účtu úložiště. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Povolte protokoly pomocí rutiny [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) prostředí PowerShell spolu s `StorageAccountId` parametrem.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

`<storage-service-resource--id>`V tomto fragmentu kódu nahraďte zástupný symbol ID prostředku služby BLOB Service. ID prostředku můžete najít v Azure Portal tak, že otevřete stránku **vlastností** svého účtu úložiště.

Můžete použít `StorageRead` , `StorageWrite` a `StorageDelete` pro hodnotu parametru **kategorie** .

Tady je příklad:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Popis jednotlivých parametrů najdete v tématu [archivace protokolů prostředků Azure prostřednictvím Azure PowerShell](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Streamování protokolů do centra událostí

Pokud se rozhodnete streamovat protokoly do centra událostí, platíte za objem protokolů, které se odesílají do centra událostí. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Povolte protokoly pomocí rutiny [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) prostředí PowerShell s `EventHubAuthorizationRuleId` parametrem.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Tady je příklad:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Popis jednotlivých parametrů najdete v tématu [streamovaná data, která se Event Hubs pomocí rutin PowerShellu](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Odesílání protokolů do Log Analytics

Povolte protokoly pomocí rutiny [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) prostředí PowerShell s `WorkspaceId` parametrem.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Tady je příklad:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Další informace najdete v tématu [streamování protokolů prostředků Azure do Log Analytics pracovního prostoru v Azure monitor](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nejdřív otevřete [Azure Cloud Shell](../../cloud-shell/overview.md), nebo pokud jste rozhraní příkazového řádku Azure [nainstalovali](/cli/azure/install-azure-cli) místně, otevřete konzolovou aplikaci, například Windows PowerShell.

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, pro který chcete povolit protokoly.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

#### <a name="archive-logs-to-a-storage-account"></a>Archivace protokolů do účtu úložiště

Pokud se rozhodnete archivovat protokoly do účtu úložiště, platíte za objem protokolů, které se odesílají do účtu úložiště. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Protokol povolte pomocí příkazu [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

`<storage-service-resource--id>`Zástupný symbol v tomto fragmentu textu nahraďte službou úložiště objektů BLOB ID prostředku. ID prostředku můžete najít v Azure Portal tak, že otevřete stránku **vlastností** svého účtu úložiště.

Můžete použít `StorageRead` , `StorageWrite` a `StorageDelete` pro hodnotu parametru **kategorie** .

Tady je příklad:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true, "retentionPolicy": {"days": 90, "enabled": true}}]'`

Popis jednotlivých parametrů najdete v tématu [archivní protokoly prostředků přes Azure CLI](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Streamování protokolů do centra událostí

Pokud se rozhodnete streamovat protokoly do centra událostí, platíte za objem protokolů, které se odesílají do centra událostí. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Protokol povolte pomocí příkazu [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Tady je příklad:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Popis jednotlivých parametrů najdete v tématu [streamovaná data, která se Event Hubs přes rozhraní příkazového řádku Azure CLI](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Odesílání protokolů do Log Analytics

Protokol povolte pomocí příkazu [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Tady je příklad:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Další informace najdete v tématu [streamování protokolů prostředků Azure do Log Analytics pracovního prostoru v Azure monitor](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Šablona](#tab/template)

Chcete-li zobrazit šablonu Azure Resource Manager, která vytvoří nastavení diagnostiky, přečtěte si téma [nastavení diagnostiky pro Azure Storage](../../azure-monitor/samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analýza metrik

Metriky můžete analyzovat pro Azure Storage s využitím metrik z jiných služeb Azure pomocí Průzkumník metrik. Otevřete Průzkumník metrik tím, že v nabídce **Azure monitor** vyberete **metriky** . Podrobnosti o používání tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../../azure-monitor/platform/metrics-getting-started.md). 

Tento příklad ukazuje, jak zobrazit **transakce** na úrovni účtu.

![Snímek obrazovky s přístupem k metrikám v Azure Portal](./media/monitor-blob-storage/access-metrics-portal.png)

Pro metriky, které podporují dimenze, můžete metriku filtrovat pomocí požadované hodnoty dimenze. Tento příklad ukazuje, jak zobrazit **transakce** na úrovni účtu u konkrétní operace výběrem hodnot pro dimenzi **název rozhraní API** .

![Snímek obrazovky s přístupem k metrikám s dimenzí v Azure Portal](./media/monitor-blob-storage/access-metrics-portal-with-dimension.png)

Úplný seznam dimenzí, které Azure Storage podporuje, najdete v tématu věnovaném [dimenzím metrik](monitor-blob-storage-reference.md#metrics-dimensions).

Metriky pro Azure Blob Storage jsou v těchto oborech názvů: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/blobServices

Seznam Azure Monitor všech metrik podpory, které zahrnují službu Azure Blob Storage, najdete v článku [Azure monitor podporované metriky](../../azure-monitor/platform/metrics-supported.md).


### <a name="accessing-metrics"></a>Přístup k metrikám

> [!TIP]
> Pokud chcete zobrazit příklady rozhraní příkazového řádku Azure CLI nebo .NET, vyberte odpovídající karty, které jsou tady uvedené.

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor poskytuje [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) pro čtení definice metrik a hodnot. [Vzorový kód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) ukazuje, jak použít sadu SDK s různými parametry. `0.18.0-preview`Pro metriky úložiště je nutné použít nebo novější verzi.
 
V těchto příkladech nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo služby Blob Storage. Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

Nahraďte `<subscription-ID>` proměnnou číslem ID vašeho předplatného. Pokyny k získání hodnot pro `<tenant-ID>` , `<application-ID>` a `<AccessKey>` najdete v tématu [použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](../../active-directory/develop/howto-create-service-principal-portal.md). 

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Výpis definice metriky

Můžete vypsat definici metriky svého účtu úložiště nebo služby Blob Storage. Použijte rutinu [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) .

V tomto příkladu nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo ID prostředku služby Blob Storage.  Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Čtení hodnot metriky

Můžete číst hodnoty metriky na úrovni účtu účtu úložiště nebo služby Blob Storage. Použijte rutinu [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Výpis definice metriky na úrovni účtu

Můžete vypsat definici metriky svého účtu úložiště nebo služby Blob Storage. Použijte příkaz [AZ monitor Metrics list-definitions](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) .
 
V tomto příkladu nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo ID prostředku služby Blob Storage. Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Číst hodnoty metrik na úrovni účtu

Můžete si přečíst hodnoty metrik účtu úložiště nebo služby Blob Storage. Použijte příkaz [AZ monitor Metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```
### <a name="template"></a>[Šablona](#tab/template)

Není k dispozici.

---

## <a name="analyzing-logs"></a>Analýza protokolů

Protokoly prostředků můžete přistupovat buď jako objekt BLOB v účtu úložiště, jako data události, nebo prostřednictvím analytických dotazů log.

Podrobný referenční informace o polích, která se zobrazují v těchto protokolech, najdete v tématu Referenční informace ke [službě Azure Blob Storage monitoring data](monitor-blob-storage-reference.md).

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly pro objekty BLOB (včetně Azure Data Lake Storage Gen2), soubory, fronty, tabulky, účty Premium Storage pro obecné účely V1 a účty úložiště pro obecné účely v2. Klasické účty úložiště se nepodporují.

Položky protokolu jsou vytvořeny pouze v případě, že jsou zadány požadavky na koncový bod služby. Pokud například účet úložiště obsahuje aktivitu v koncovém bodu objektu blob, ale ne ve svých koncových bodech tabulky nebo fronty, vytvoří se jenom protokoly, které se týkají služby BLOB Service. Protokoly Azure Storage obsahují podrobné informace o úspěšných a neúspěšných požadavcích služby úložiště. Tyto informace je možné použít k monitorování jednotlivých požadavků a diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí.

### <a name="log-authenticated-requests"></a>Protokolovat ověřené požadavky

 Protokolují se následující typy ověřovaných požadavků:

- Úspěšné požadavky
- Neúspěšné požadavky, včetně vypršení časového limitu, omezování, chyb sítě, selhání autorizace a dalších chyb
- Požadavky, které používají sdílený přístupový podpis (SAS) nebo OAuth, včetně neúspěšných a úspěšných požadavků
- Požadavky na analytické údaje (data protokolu Classic v kontejneru **$logs** a data metriky třídy v **$metricch** tabulkách)

Požadavky samotné službou BLOB Storage, jako je vytvoření nebo odstranění protokolu, nejsou protokolovány. Úplný seznam protokolovaných dat naleznete v tématu [operace protokolované úložiště a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Formát protokolu úložiště](monitor-blob-storage-reference.md).

### <a name="log-anonymous-requests"></a>Protokolování anonymních požadavků

 Protokolují se tyto typy anonymních požadavků:

- Úspěšné požadavky
- Chyby serveru
- Chyby vypršení časového limitu pro klienta i server
- Neúspěšné žádosti o získání s kódem chyby 304 (nezměněno)

Všechny ostatní neúspěšné anonymní požadavky nejsou protokolovány. Úplný seznam protokolovaných dat naleznete v tématu [operace protokolované úložiště a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Formát protokolu úložiště](monitor-blob-storage-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Přístup k protokolům v účtu úložiště

Protokoly se zobrazí jako objekty blob uložené do kontejneru v cílovém účtu úložiště. Data se shromažďují a ukládají v jednom objektu BLOB jako datová část JSON s oddělenými řádky. Název objektu BLOB se řídí těmito zásadami vytváření názvů:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Tady je příklad:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Přístup k protokolům v centru událostí

Protokoly odeslané do centra událostí nejsou uloženy jako soubor, ale můžete ověřit, že centrum událostí obdrželo informace protokolu. V Azure Portal přejdete do centra událostí a ověříte, že počet **příchozích zpráv** je větší než nula. 

![Protokoly auditu](media/monitor-blob-storage/event-hub-log.png)

Data protokolu, která se odesílají do centra událostí, můžete otevřít a číst pomocí nástrojů pro správu a správu událostí a monitorování událostí a informací o zabezpečení. Další informace najdete v tématu [co se dá dělat s daty monitorování odesílanými do mého centra událostí?](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Přístup k protokolům v pracovním prostoru Log Analytics

K protokolům odesílaným do Log Analytics pracovního prostoru můžete přistupovat pomocí dotazů protokolu Azure Monitor.

Další informace najdete v tématu [Začínáme s Log Analytics v Azure monitor](../../azure-monitor/log-query/log-analytics-tutorial.md).

Data se ukládají do tabulky **StorageBlobLog** . Protokoly pro Data Lake Storage Gen2 se neobjevují ve vyhrazené tabulce. Důvodem je, že Data Lake Storage Gen2 není služba. Je to sada funkcí, které můžete ve svém účtu úložiště povolit. Pokud jste tyto možnosti povolili, protokoly se budou dál zobrazovat v tabulce StorageBlobLogs. 

#### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Tady jsou některé dotazy, které můžete zadat do panelu **hledání protokolu** , abyste mohli monitorovat úložiště objektů BLOB. Tyto dotazy fungují s [novým jazykem](../../azure-monitor/log-query/log-query-overview.md).

> [!IMPORTANT]
> Když vyberete **protokoly** z nabídky Skupina prostředků účtu úložiště, Log Analytics se otevře s oborem dotazu nastaveným na aktuální skupinu prostředků. To znamená, že dotazy protokolu budou zahrnovat jenom data z dané skupiny prostředků. Pokud chcete spustit dotaz, který zahrnuje data z jiných prostředků nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](../../azure-monitor/log-query/scope.md) .

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
## <a name="faq"></a>Časté otázky

**Podporuje Azure Storage metriky pro Managed Disks nebo nespravované disky?**

No. Azure COMPUTE podporuje metriky na discích. Další informace najdete v tématu [metriky jednotlivých disků pro spravované a nespravované disky](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Další kroky

- Odkaz na protokoly a metriky, které vytvořila služba Azure Blob Storage, najdete v tématu Referenční informace ke [službě azure BLOB Storage monitoring data](monitor-blob-storage-reference.md).
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md).
- Další informace o migraci metrik najdete v tématu [Azure Storage migrace metrik](../common/storage-metrics-migration.md).
