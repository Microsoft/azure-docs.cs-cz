---
title: Monitorování souborů Azure | Microsoft Docs
description: Naučte se monitorovat výkon a dostupnost souborů Azure. Monitorujte data souborů Azure, zjistěte informace o konfiguraci a analyzujte metriky a data protokolů.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 16a899b7f0e40c2eee91d1dd445f0992572a9dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103418160"
---
# <a name="monitoring-azure-files"></a>Monitorování souborů Azure

Pokud máte důležité aplikace a obchodní procesy, které spoléhají na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování vygenerovaná soubory Azure a způsob použití funkcí Azure Monitor k analýze výstrah na těchto datech.

## <a name="monitor-overview"></a>Přehled monitorování

Stránka **Přehled** v Azure Portal pro každý prostředek služby soubory Azure zahrnuje stručný přehled využití prostředků, jako jsou třeba žádosti a hodinová fakturace. Tyto informace jsou užitečné, ale k dispozici je jenom malé množství dat monitorování. Některá z těchto dat jsou shromažďována automaticky a jsou k dispozici pro analýzu ihned po vytvoření prostředku. Můžete povolit další typy shromažďování dat s určitou konfigurací.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
Služba soubory Azure vytváří data monitorování pomocí [Azure monitor](../../azure-monitor/overview.md), což je plná služba monitorování zásobníku v Azure. Azure Monitor poskytuje kompletní sadu funkcí pro monitorování prostředků a prostředků Azure v jiných cloudech a v místním prostředí. 

Začněte s článkem [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md), který popisuje následující informace:

- Co je Azure Monitor?
- Náklady spojené s monitorováním
- Monitorování dat shromážděných v Azure
- Konfigurace shromažďování dat
- Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části jsou uvedené v tomto článku, které popisují konkrétní data získaná ze souborů Azure. Příklady ukazují, jak nakonfigurovat shromažďování dat a analyzovat tato data pomocí nástrojů Azure.

## <a name="monitoring-data"></a>Data monitorování

Soubory Azure shromažďují stejný druh dat monitorování jako jiné prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Podrobné informace o metrikách a protokolech, které vytváří služba soubory Azure, najdete v referenčních informacích k [datům monitorování souborů Azure](storage-files-monitoring-reference.md) .

Metriky a protokoly v Azure Monitor podporují jenom účty úložiště Azure Resource Manager. Azure Monitor nepodporuje účty klasického úložiště. Pokud chcete používat metriky nebo protokoly v klasickém účtu úložiště, musíte migrovat na účet úložiště Azure Resource Manager. Viz [migrace na Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

## <a name="collection-and-routing"></a>Shromažďování a směrování

Metriky platforem a protokol aktivit jsou shromažďovány automaticky, ale mohou být směrovány do jiných umístění pomocí diagnostického nastavení. 

Chcete-li shromáždit protokoly prostředků, je nutné vytvořit nastavení diagnostiky. Když vytvoříte nastavení, vyberte **soubor** jako typ úložiště, pro který chcete povolit protokoly. Pak zadejte jednu z následujících kategorií operací, pro které chcete shromažďovat protokoly. 

| Kategorie | Popis |
|:---|:---|
| StorageRead | Operace čtení u objektů |
| StorageWrite | Operace zápisu pro objekty. |
| StorageDelete | Odstraní operace s objekty. |

Seznam operací SMB a REST, které se protokolují, najdete v článku o [operacích protokolovaných úložiště a o stavových zprávách](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a referenčních informacích k [datům monitorování Azure Files](storage-files-monitoring-reference.md).

## <a name="creating-a-diagnostic-setting"></a>Vytvoření nastavení diagnostiky

Nastavení diagnostiky můžete vytvořit pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo šablony Azure Resource Manager.

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek. Tato funkce je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Viz [Přehled účtu úložiště](../common/storage-account-overview.md).

Obecné pokyny najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](../../azure-monitor/essentials/diagnostic-settings.md).

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k webu Azure Portal.

2. Přejděte na svůj účet úložiště.

3. V části **monitorování** klikněte na **nastavení diagnostiky (Preview)**.

   > [!div class="mx-imgBorder"]
   > ![portál – diagnostické protokoly](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. Jako typ úložiště, pro který chcete povolit protokoly, vyberte **soubor** .

5. Klikněte na **Přidat nastavení diagnostiky**.

   > [!div class="mx-imgBorder"]
   > ![portál – protokoly prostředků – přidání nastavení diagnostiky](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   Zobrazí se stránka **nastavení diagnostiky** .

   > [!div class="mx-imgBorder"]
   > ![Stránka protokoly prostředků](media/storage-files-monitoring/diagnostic-logs-page.png)

6. Do pole **název** na stránce zadejte název pro toto nastavení protokolu prostředků. Pak vyberte, které operace chcete protokolovat (operace čtení, zápisu a odstranění) a kam chcete protokoly odeslat.

#### <a name="archive-logs-to-a-storage-account"></a>Archivace protokolů do účtu úložiště

Pokud se rozhodnete archivovat protokoly do účtu úložiště, platíte za objem protokolů, které se odesílají do účtu úložiště. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Zaškrtněte políčko **archivovat do účtu úložiště** a pak klikněte na tlačítko **Konfigurovat** .

   > [!div class="mx-imgBorder"]   
   > ![Archivní úložiště stránky nastavení diagnostiky](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. V rozevíracím seznamu **účet úložiště** vyberte účet úložiště, do kterého chcete archivovat protokoly, klikněte na tlačítko **OK** a potom klikněte na tlačítko **Uložit** .

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Než zvolíte účet úložiště jako cíl exportu, přečtěte si téma [archivace protokolů prostředků Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) a pochopení požadavků v účtu úložiště.

#### <a name="stream-logs-to-azure-event-hubs"></a>Streamování protokolů do Azure Event Hubs

Pokud se rozhodnete streamovat protokoly do centra událostí, platíte za objem protokolů, které se odesílají do centra událostí. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Zaškrtněte políčko **datový proud do centra událostí** a pak klikněte na tlačítko **Konfigurovat** .

2. V podokně **Vyberte centrum událostí** zvolte obor názvů, název a název zásady centra událostí, do kterého chcete protokoly streamovat. 

   > [!div class="mx-imgBorder"]
   > ![Centrum událostí stránky nastavení diagnostiky](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **Uložit** .

#### <a name="send-logs-to-azure-log-analytics"></a>Odeslat protokoly do Azure Log Analytics

1. Zaškrtněte políčko **Odeslat do Log Analytics** , vyberte pracovní prostor Log Analytics a pak klikněte na tlačítko **Uložit** .

   > [!div class="mx-imgBorder"]   
   > ![Stránka nastavení diagnostiky Log Analytics](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

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
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> 
```

`<storage-service-resource--id>`V tomto fragmentu kódu nahraďte zástupný symbol ID prostředku souborové služby Azure. ID prostředku můžete najít v Azure Portal tak, že otevřete stránku **vlastností** svého účtu úložiště.

Můžete použít `StorageRead` , `StorageWrite` a `StorageDelete` pro hodnotu parametru **kategorie** .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Tady je příklad:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Popis jednotlivých parametrů najdete v tématu [archivace protokolů prostředků Azure prostřednictvím Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Streamování protokolů do centra událostí

Pokud se rozhodnete streamovat protokoly do centra událostí, platíte za objem protokolů, které se odesílají do centra událostí. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Povolte protokoly pomocí rutiny [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) prostředí PowerShell s `EventHubAuthorizationRuleId` parametrem.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Tady je příklad:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Popis jednotlivých parametrů najdete v tématu [streamovaná data, která se Event Hubs pomocí rutin PowerShellu](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Odesílání protokolů do Log Analytics

Povolte protokoly pomocí rutiny [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) prostředí PowerShell s `WorkspaceId` parametrem.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Tady je příklad:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Další informace najdete v tématu [streamování protokolů prostředků Azure do Log Analytics pracovního prostoru v Azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

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
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

`<storage-service-resource--id>`Zástupný symbol v tomto fragmentu textu nahraďte službou úložiště objektů BLOB ID prostředku. ID prostředku můžete najít v Azure Portal tak, že otevřete stránku **vlastností** svého účtu úložiště.

Můžete použít `StorageRead` , `StorageWrite` a `StorageDelete` pro hodnotu parametru **kategorie** .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Tady je příklad:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

Popis jednotlivých parametrů najdete v tématu [archivní protokoly prostředků přes Azure CLI](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Streamování protokolů do centra událostí

Pokud se rozhodnete streamovat protokoly do centra událostí, platíte za objem protokolů, které se odesílají do centra událostí. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Protokol povolte pomocí příkazu [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Tady je příklad:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Popis jednotlivých parametrů najdete v tématu [streamovaná data, která se Event Hubs přes rozhraní příkazového řádku Azure CLI](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Odesílání protokolů do Log Analytics

Protokol povolte pomocí příkazu [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Tady je příklad:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Další informace najdete v tématu [streamování protokolů prostředků Azure do Log Analytics pracovního prostoru v Azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Šablona](#tab/template)

Chcete-li zobrazit šablonu Azure Resource Manager, která vytvoří nastavení diagnostiky, přečtěte si téma [nastavení diagnostiky pro Azure Storage](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analýza metrik

Metriky můžete analyzovat pro Azure Storage s využitím metrik z jiných služeb Azure pomocí Průzkumník metrik. Otevřete Průzkumník metrik tím, že v nabídce **Azure monitor** vyberete **metriky** . Podrobnosti o používání tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../../azure-monitor/essentials/metrics-getting-started.md). 

Pro metriky, které podporují dimenze, můžete metriku filtrovat pomocí požadované hodnoty dimenze.  Úplný seznam dimenzí, které Azure Storage podporuje, najdete v tématu věnovaném [dimenzím metrik](storage-files-monitoring-reference.md#metrics-dimensions). Metriky pro soubory Azure jsou v těchto oborech názvů: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/služby

Seznam Azure Monitor všech metrik podpory, které obsahují soubory Azure, najdete v tématu [Azure monitor podporovaných metrik](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Přístup k metrikám

> [!TIP]
> Pokud chcete zobrazit příklady rozhraní příkazového řádku Azure CLI nebo .NET, vyberte odpovídající karty, které jsou tady uvedené.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Výpis definice metriky

Můžete vypsat definici metriky svého účtu úložiště nebo služby soubory Azure. Použijte rutinu [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) .

V tomto příkladu nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo ID prostředku služby soubory Azure.  Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Čtení hodnot metriky

Můžete číst hodnoty metriky na úrovni účtu účtu úložiště nebo služby soubory Azure. Použijte rutinu [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Výpis definice metriky na úrovni účtu

Můžete vypsat definici metriky svého účtu úložiště nebo služby soubory Azure. Použijte příkaz [AZ monitor Metrics list-definitions](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) .
 
V tomto příkladu nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo ID prostředku služby soubory Azure. Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Číst hodnoty metrik na úrovni účtu

Můžete si přečíst hodnoty metrik účtu úložiště nebo služby soubory Azure. Použijte příkaz [AZ monitor Metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor poskytuje [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) pro čtení definice metrik a hodnot. [Vzorový kód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) ukazuje, jak použít sadu SDK s různými parametry. `0.18.0-preview`Pro metriky úložiště je nutné použít nebo novější verzi.
 
V těchto příkladech nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo služby soubory Azure. Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

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

# <a name="template"></a>[Šablona](#tab/template)

Není k dispozici.

---

## <a name="analyzing-logs"></a>Analýza protokolů

Protokoly prostředků můžete přistupovat buď jako objekt BLOB v účtu úložiště, jako data události, nebo prostřednictvím analytických dotazů log.

Seznam operací SMB a REST, které se protokolují, najdete v článku o [operacích protokolovaných úložiště a o stavových zprávách](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a referenčních informacích k [datům monitorování Azure Files](storage-files-monitoring-reference.md).

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly pro objekty BLOB (včetně Azure Data Lake Storage Gen2), soubory, fronty, tabulky, účty Premium Storage pro obecné účely V1 a účty úložiště pro obecné účely v2. Klasické účty úložiště se nepodporují.

Položky protokolu jsou vytvořeny pouze v případě, že jsou zadány požadavky na koncový bod služby. Pokud například účet úložiště obsahuje aktivitu v koncovém bodu souboru, ale ne ve svých koncových bodech tabulky nebo fronty, vytvoří se pouze protokoly, které se týkají služby Azure File Service. Protokoly Azure Storage obsahují podrobné informace o úspěšných a neúspěšných požadavcích služby úložiště. Tyto informace je možné použít k monitorování jednotlivých požadavků a diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí.

### <a name="log-authenticated-requests"></a>Protokolovat ověřené požadavky

 Protokolují se následující typy ověřovaných požadavků:

- Úspěšné požadavky
- Neúspěšné požadavky, včetně vypršení časového limitu, omezování, chyb sítě, selhání autorizace a dalších chyb
- Požadavky, které používají protokol Kerberos, NTLM nebo sdílený přístupový podpis (SAS), včetně neúspěšných a úspěšných požadavků
- Požadavky na analytické údaje (data protokolu Classic v kontejneru **$logs** a data metriky classic v **$metricch** tabulkách)

Požadavky samotné službou soubory Azure, jako je vytvoření nebo odstranění protokolu, nejsou protokolovány. Úplný seznam protokolů SMB a REST, které jsou protokolovány, najdete v tématu [operace protokolované úložiště a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [referenční informace o monitorování dat sledování služby Azure Files](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Přístup k protokolům v účtu úložiště

Protokoly se zobrazí jako objekty blob uložené do kontejneru v cílovém účtu úložiště. Data se shromažďují a ukládají v jednom objektu BLOB jako datová část JSON s oddělenými řádky. Název objektu BLOB se řídí těmito zásadami vytváření názvů:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Tady je příklad:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Přístup k protokolům v centru událostí

Protokoly odeslané do centra událostí nejsou uloženy jako soubor, ale můžete ověřit, že centrum událostí obdrželo informace protokolu. V Azure Portal přejdete do centra událostí a ověříte, že počet **příchozích zpráv** je větší než nula. 

![Protokoly auditu](media/storage-files-monitoring/event-hub-log.png)

Data protokolu, která se odesílají do centra událostí, můžete otevřít a číst pomocí nástrojů pro správu a správu událostí a monitorování událostí a informací o zabezpečení. Další informace najdete v tématu [co se dá dělat s daty monitorování odesílanými do mého centra událostí?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Přístup k protokolům v pracovním prostoru Log Analytics

K protokolům odesílaným do Log Analytics pracovního prostoru můžete přistupovat pomocí dotazů protokolu Azure Monitor. Data se ukládají do tabulky **StorageFileLogs** . 

Další informace najdete v tématu [Log Analytics kurzu](../../azure-monitor/logs/log-analytics-tutorial.md).

#### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Tady jsou některé dotazy, které můžete zadat do panelu **hledání v protokolu** , abyste mohli monitorovat soubory Azure. Tyto dotazy fungují s [novým jazykem](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> Když vyberete **protokoly** z nabídky Skupina prostředků účtu úložiště, Log Analytics se otevře s oborem dotazu nastaveným na aktuální skupinu prostředků. To znamená, že dotazy protokolu budou zahrnovat jenom data z dané skupiny prostředků. Pokud chcete spustit dotaz, který zahrnuje data z jiných prostředků nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](../../azure-monitor/logs/scope.md) .

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

Seznam názvů sloupců a popisů pro soubory Azure najdete v tématu [StorageFileLogs](/azure/azure-monitor/reference/tables/storagefilelogs).

Další informace o tom, jak zapisovat dotazy, najdete v tématu [Log Analytics kurzu](../../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="alerts"></a>Výstrahy

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy v systému před tím, než si je vaši zákazníci všimnete. Můžete nastavit výstrahy na [metrikách](../../azure-monitor/alerts/alerts-metric-overview.md), [protokolech](../../azure-monitor/alerts/alerts-unified-log.md)a [protokolu aktivit](../../azure-monitor/alerts/activity-log-alerts.md). 

V následující tabulce jsou uvedeny příklady scénářů, které je třeba monitorovat, a správnou metriku pro použití výstrahy:

| Scenario | Metrika, která se má použít pro upozornění |
|-|-|
| Sdílení souborů je omezené. | Metrika: transakcí<br>Název dimenze: typ odpovědi <br>Název dimenze: sdílená složka (jenom pro sdílení souborů Premium) |
| Velikost sdílené složky je 80% kapacity. | Metrika: kapacita souboru<br>Název dimenze: sdílená složka (jenom pro sdílení souborů Premium) |
| Odchozí výstup sdílení souborů překročil 500 GiB za jeden den. | Metrika: odchozí data<br>Název dimenze: sdílená složka (jenom pro sdílení souborů Premium) |

### <a name="how-to-create-alerts-for-azure-files"></a>Jak vytvořit výstrahy pro soubory Azure

1. V **Azure Portal** přejít na svůj **účet úložiště** . 

2. Klikněte na **výstrahy** a pak klikněte na **+ nové pravidlo výstrahy**.

3. Klikněte na **Upravit prostředek**, vyberte **typ prostředku** a pak klikněte na **Hotovo**. 

4. Klikněte na **Přidat podmínku** a zadejte pro tuto výstrahu následující informace: 

    - **Metrika**
    - **Název dimenze**
    - **Logika výstrahy**

5. Klikněte na **Přidat skupiny akcí** a přidejte do výstrahy skupinu akcí (E-mail, SMS atd.), a to tak, že vyberete existující skupinu akcí nebo vytvoříte novou skupinu akcí.

6. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.

7. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.

> [!NOTE]  
> Pokud vytvoříte výstrahu a je příliš hlučná, upravte prahovou hodnotu a logiku výstrahy.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Postup vytvoření výstrahy, pokud je sdílená složka omezená

1. V **Azure Portal** přejít na svůj **účet úložiště** .
2. V části **monitorování** klikněte na **výstrahy** a pak klikněte na **+ nové pravidlo výstrahy**.
3. Klikněte na **Upravit prostředek**, vyberte **typ prostředku** pro účet úložiště a pak klikněte na **Hotovo**. Pokud je třeba název účtu úložiště `contoso` , vyberte `contoso/file` prostředek.
4. Kliknutím na **Přidat podmínku** přidejte podmínku.
5. Zobrazí se seznam signálů, které jsou pro účet úložiště podporované, vyberte metriku **transakcí** .
6. V okně **Konfigurovat logiku signálu** klikněte na rozevírací seznam **název dimenze** a vyberte **typ odpovědi**.
7. Klikněte na rozevírací seznam **hodnoty dimenze** a vyberte odpovídající typy odezvy pro sdílenou složku.

    Pro standardní sdílené složky vyberte následující typy odpovědí:

    - SuccessWithShareIopsThrottling
    - SuccessWithThrottling
    - ClientShareIopsThrottlingError

    Pro sdílené složky Premium vyberte následující typy odpovědí:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Pokud typy odezvy nejsou uvedeny v rozevíracím seznamu **hodnoty dimenze** , znamená to, že prostředek nebyl omezen. Chcete-li přidat hodnoty dimenze, klikněte vedle rozevíracího seznamu **hodnoty dimenze** na možnost **Přidat vlastní hodnotu**, zadejte typ respone (například **SuccessWithThrottling**), vyberte **OK** a pak opakováním těchto kroků přidejte všechny použitelné typy odpovědí pro sdílenou složku.

8. Pro **sdílené složky Premium** klikněte na rozevírací seznam **název dimenze** a vyberte **sdílená složka**. Pro **standardní sdílené složky** přejděte na **Krok #10**.

   > [!NOTE]
   > Pokud je sdílená složka standardní sdílenou složkou, dimenze **sdílené složky** nebude zobrazovat seznam sdílených složek, protože pro standardní sdílené složky nejsou k dispozici žádné metriky pro jednotlivé sdílené složky. Výstrahy omezování pro standardní sdílené složky se aktivují, pokud je omezená jakákoli sdílená složka v rámci účtu úložiště a výstraha neurčí, která sdílená složka byla omezená. Vzhledem k tomu, že pro standardní sdílené složky nejsou k dispozici metriky jednotlivých sdílených složek, doporučuje se mít pro každý účet úložiště jednu sdílenou složku.

9. Klikněte na rozevírací seznam **hodnoty dimenze** a vyberte sdílené složky, na kterých chcete upozornit.
10. Definujte **Parametry výstrahy** (prahová hodnota, operátor, členitost agregace a frekvence vyhodnocení) a klikněte na **Hotovo**.

    > [!TIP]
    > Pokud používáte statickou prahovou hodnotu, graf metriky může pomoci určit rozumnou prahovou hodnotu, pokud je sdílená složka momentálně omezená. Pokud používáte dynamickou prahovou hodnotu, v grafu metriky se zobrazí vypočtené prahové hodnoty na základě nedávných dat.

11. Kliknutím na **Přidat skupiny akcí** přidejte **skupinu akcí** (e-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
12. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
13. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Jak vytvořit výstrahu, pokud je velikost sdílené složky Azure 80% kapacity

1. V **Azure Portal** přejít na svůj **účet úložiště** .
2. V části **monitorování** klikněte na možnost **výstrahy** a potom klikněte na tlačítko **+ nové pravidlo výstrahy**.
3. Klikněte na **Upravit prostředek**, vyberte **typ prostředku** pro účet úložiště a pak klikněte na **Hotovo**. Pokud je třeba název účtu úložiště `contoso` , vyberte `contoso/file` prostředek.
4. Kliknutím na **Přidat podmínku** přidejte podmínku.
5. Zobrazí se seznam signálů, které jsou pro účet úložiště podporované, a vyberte metriku **kapacity souboru** .
6. Pro **sdílené složky Premium** klikněte na rozevírací seznam **název dimenze** a vyberte **sdílená složka**. Pro **standardní sdílené složky** přejděte na **Krok #8**.

   > [!NOTE]
   > Pokud je sdílená složka standardní sdílenou složkou, dimenze **sdílené složky** nebude zobrazovat seznam sdílených složek, protože pro standardní sdílené složky nejsou k dispozici žádné metriky pro jednotlivé sdílené složky. Výstrahy pro standardní sdílení souborů jsou založené na všech sdílených složkách v účtu úložiště. Vzhledem k tomu, že pro standardní sdílené složky nejsou k dispozici metriky jednotlivých sdílených složek, doporučuje se mít pro každý účet úložiště jednu sdílenou složku.

7. Klikněte na rozevírací seznam **hodnoty dimenze** a vyberte sdílené složky, na kterých chcete upozornit.
8. Zadejte **prahovou hodnotu** v bajtech. Pokud je například velikost sdílené složky 100 TiB a chcete dostat upozornění, když je velikost sdílené složky 80% kapacity, prahová hodnota v bajtech je 87960930222080.
9. Definujte zbytek **parametrů výstrahy** (členitost agregace a četnost vyhodnocení) a klikněte na **Hotovo**.
10. Kliknutím na **Přidat skupiny akcí** přidejte **skupinu akcí** (e-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
11. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
12. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Jak vytvořit výstrahu v případě, že výstup Azure File Share přesáhl 500 GiB za den

1. V **Azure Portal** přejít na svůj **účet úložiště** .
2. V části monitorování klikněte na možnost **výstrahy** a potom klikněte na tlačítko **+ nové pravidlo výstrahy**.
3. Klikněte na **Upravit prostředek**, vyberte **typ prostředku** pro účet úložiště a pak klikněte na **Hotovo**. Pokud je třeba název účtu úložiště contoso, vyberte prostředek contoso/File.
4. Kliknutím na **Přidat podmínku** přidejte podmínku.
5. Zobrazí se seznam signálů, které jsou pro účet úložiště podporované, a vyberte **výstupní** metriku.
6. Pro **sdílené složky Premium** klikněte na rozevírací seznam **název dimenze** a vyberte **sdílená složka**. Pro **standardní sdílené složky** přejděte na **Krok #8**.

   > [!NOTE]
   > Pokud je sdílená složka standardní sdílenou složkou, dimenze **sdílené složky** nebude zobrazovat seznam sdílených složek, protože pro standardní sdílené složky nejsou k dispozici žádné metriky pro jednotlivé sdílené složky. Výstrahy pro standardní sdílení souborů jsou založené na všech sdílených složkách v účtu úložiště. Vzhledem k tomu, že pro standardní sdílené složky nejsou k dispozici metriky jednotlivých sdílených složek, doporučuje se mít pro každý účet úložiště jednu sdílenou složku.

7. Klikněte na rozevírací seznam **hodnoty dimenze** a vyberte sdílené složky, na kterých chcete upozornit.
8. Jako prahovou hodnotu zadejte **536870912000** bajtů. 
9. Klikněte na rozevírací seznam **členitosti agregace** a vyberte **24 hodin**.
10. Vyberte **frekvenci hodnocení** a **klikněte na Hotovo**.
11. Kliknutím na **Přidat skupiny akcí** přidejte **skupinu akcí** (e-mail, SMS atd.) k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
12. Vyplňte **Podrobnosti výstrahy** , jako je **název pravidla výstrahy**, **Popis** a **závažnost**.
13. Kliknutím na **vytvořit pravidlo výstrahy** vytvořte výstrahu.

## <a name="next-steps"></a>Další kroky

- [Referenční informace o datech monitorování služby Azure Files](storage-files-monitoring-reference.md)
- [Monitorování prostředků Azure pomocí Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Azure Storage migrace metrik](../common/storage-metrics-migration.md)
- [Plánování nasazení Azure Files](./storage-files-planning.md)
- [Nasazení služby Azure Files](./storage-how-to-create-file-share.md)
- [Řešení problémů se Soubory Azure ve Windows](./storage-troubleshoot-windows-file-connection-problems.md)
- [Řešení problémů se Soubory Azure v Linuxu](./storage-troubleshoot-linux-file-connection-problems.md)
