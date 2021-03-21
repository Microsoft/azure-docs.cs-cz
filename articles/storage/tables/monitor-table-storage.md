---
title: Monitorování úložiště Azure Table | Microsoft Docs
description: Naučte se monitorovat výkon a dostupnost služby Azure Table Storage. Monitorujte data služby Azure Table Storage, zjistěte informace o konfiguraci a analyzujte data metrik a protokolů.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: d2973d95cf797123e02fd48dc2dfab1b172e05f5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499458"
---
# <a name="monitoring-azure-table-storage"></a>Monitorování úložiště tabulek v Azure

Pokud máte důležité aplikace a obchodní procesy, které spoléhají na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování vygenerovaná službou Azure Table Storage a způsob použití funkcí Azure Monitor k analýze výstrah na těchto datech.

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek. Tato funkce je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Viz [Přehled účtu úložiště](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Přehled monitorování

Stránka **Přehled** v Azure Portal pro každý prostředek úložiště tabulek zahrnuje stručný přehled využití prostředků, jako jsou požadavky a hodinová fakturace. Tyto informace jsou užitečné, ale k dispozici je jenom malé množství dat monitorování. Některá z těchto dat jsou shromažďována automaticky a jsou k dispozici pro analýzu ihned po vytvoření prostředku. Můžete povolit další typy shromažďování dat s určitou konfigurací.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
Azure Table Storage vytváří data monitorování pomocí [Azure monitor](../../azure-monitor/overview.md), což je plná služba monitorování zásobníku v Azure. Azure Monitor poskytuje kompletní sadu funkcí pro monitorování prostředků a prostředků Azure v jiných cloudech a v místním prostředí. 

Začněte s článkem [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md) , který popisuje následující informace:

- Co je Azure Monitor?
- Náklady spojené s monitorováním
- Monitorování dat shromážděných v Azure
- Konfigurace shromažďování dat
- Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části jsou uvedené v tomto článku, které popisují konkrétní data získaná z Azure Storage. Příklady ukazují, jak nakonfigurovat shromažďování dat a analyzovat tato data pomocí nástrojů Azure.

## <a name="monitoring-data"></a>Data monitorování

Azure Table Storage shromažďuje stejné typy dat monitorování jako jiné prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Podrobné informace o metrikách a protokolech, které vytváří služba Azure Table Storage, najdete v referenčních informacích k [datům monitorování Azure Table Storage](monitor-table-storage-reference.md) .

Metriky a protokoly v Azure Monitor podporují jenom účty úložiště Azure Resource Manager. Azure Monitor nepodporuje účty klasického úložiště. Pokud chcete používat metriky nebo protokoly v klasickém účtu úložiště, musíte migrovat na účet úložiště Azure Resource Manager. Viz [migrace na Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

Pokud chcete, můžete i nadále používat klasické metriky a protokoly. Klasické metriky a protokoly jsou ve skutečnosti k dispozici paralelně s metrikami a protokoly v Azure Monitor. Podpora zůstane v platnosti, dokud Azure Storage neukončí službu ve starších metrikách a protokolech.

## <a name="collection-and-routing"></a>Shromažďování a směrování

Metriky platforem a protokol aktivit jsou shromažďovány automaticky, ale mohou být směrovány do jiných umístění pomocí diagnostického nastavení. 

Chcete-li shromáždit protokoly prostředků, je nutné vytvořit nastavení diagnostiky. Když vytvoříte nastavení, vyberte **tabulka** jako typ úložiště, pro který chcete povolit protokoly. Pak zadejte jednu z následujících kategorií operací, pro které chcete shromažďovat protokoly. 

| Kategorie | Popis |
|:---|:---|
| StorageRead | Operace čtení u objektů |
| StorageWrite | Operace zápisu pro objekty. |
| StorageDelete | Odstraní operace s objekty. |

## <a name="creating-a-diagnostic-setting"></a>Vytvoření nastavení diagnostiky

Nastavení diagnostiky můžete vytvořit pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo šablony Azure Resource Manager. 

Obecné pokyny najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](../../azure-monitor/essentials/diagnostic-settings.md).

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek. Tato funkce je k dispozici pro všechny účty úložiště, které jsou vytvořeny pomocí modelu nasazení Azure Resource Manager. Viz [Přehled účtu úložiště](../common/storage-account-overview.md).

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k webu Azure Portal.

2. Přejděte na svůj účet úložiště.

3. V části **monitorování** klikněte na **nastavení diagnostiky (Preview)**.

   > [!div class="mx-imgBorder"]
   > ![portál – diagnostické protokoly](media/monitor-table-storage/diagnostic-logs-settings-pane.png)   

4. Jako typ úložiště, pro který chcete povolit protokoly, vyberte **tabulka** .

5. Klikněte na **Přidat nastavení diagnostiky**.

   > [!div class="mx-imgBorder"]
   > ![portál – protokoly prostředků – přidání nastavení diagnostiky](media/monitor-table-storage/diagnostic-logs-settings-pane-2.png)

   Zobrazí se stránka **nastavení diagnostiky** .

   > [!div class="mx-imgBorder"]
   > ![Stránka protokoly prostředků](media/monitor-table-storage/diagnostic-logs-page.png)

6. Do pole **název** na stránce zadejte název pro toto nastavení protokolu prostředků. Pak vyberte, které operace chcete protokolovat (operace čtení, zápisu a odstranění) a kam chcete protokoly odeslat.

#### <a name="archive-logs-to-a-storage-account"></a>Archivace protokolů do účtu úložiště

Pokud se rozhodnete archivovat protokoly do účtu úložiště, platíte za objem protokolů, které se odesílají do účtu úložiště. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Zaškrtněte políčko **archivovat do účtu úložiště** a pak klikněte na tlačítko **Konfigurovat** .

   > [!div class="mx-imgBorder"]   
   > ![Archivní úložiště stránky nastavení diagnostiky](media/monitor-table-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. V rozevíracím seznamu **účet úložiště** vyberte účet úložiště, do kterého chcete archivovat protokoly, klikněte na tlačítko **OK** a potom klikněte na tlačítko **Uložit** .

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Než zvolíte účet úložiště jako cíl exportu, přečtěte si téma [archivace protokolů prostředků Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) a pochopení požadavků v účtu úložiště.

#### <a name="stream-logs-to-azure-event-hubs"></a>Streamování protokolů do Azure Event Hubs

Pokud se rozhodnete streamovat protokoly do centra událostí, platíte za objem protokolů, které se odesílají do centra událostí. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Zaškrtněte políčko **datový proud do centra událostí** a pak klikněte na tlačítko **Konfigurovat** .

2. V podokně **Vyberte centrum událostí** zvolte obor názvů, název a název zásady centra událostí, do kterého chcete protokoly streamovat. 

   > [!div class="mx-imgBorder"]
   > ![Centrum událostí stránky nastavení diagnostiky](media/monitor-table-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **Uložit** .

#### <a name="send-logs-to-azure-log-analytics"></a>Odeslat protokoly do Azure Log Analytics

1. Zaškrtněte políčko **Odeslat do Log Analytics** , vyberte pracovní prostor Log Analytics a pak klikněte na tlačítko **Uložit** .

   > [!div class="mx-imgBorder"]   
   > ![Stránka nastavení diagnostiky Log Analytics](media/monitor-table-storage/diagnostic-logs-settings-pane-log-analytics.png)

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

`<storage-service-resource--id>`V tomto fragmentu kódu nahraďte zástupný symbol ID prostředku služby Table Service. ID prostředku můžete najít v Azure Portal tak, že otevřete stránku **vlastností** svého účtu úložiště.

Můžete použít `StorageRead` , `StorageWrite` a `StorageDelete` pro hodnotu parametru **kategorie** .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Tady je příklad:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Další informace o archivačních protokolech prostředků do Azure Storage najdete v tématu [protokoly prostředků Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Streamování protokolů do centra událostí

Pokud se rozhodnete streamovat protokoly do centra událostí, platíte za objem protokolů, které se odesílají do centra událostí. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Povolte protokoly pomocí rutiny [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) prostředí PowerShell s `EventHubAuthorizationRuleId` parametrem.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Tady je příklad:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Další informace o odesílání protokolů prostředků do Center událostí najdete v tématu [protokoly prostředků Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Odesílání protokolů do Log Analytics

Povolte protokoly pomocí rutiny [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) prostředí PowerShell s `WorkspaceId` parametrem.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Tady je příklad:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

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
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

`<storage-service-resource--id>`Zástupný symbol v tomto fragmentu kódu nahraďte službou úložiště ID prostředků. ID prostředku můžete najít v Azure Portal tak, že otevřete stránku **vlastností** svého účtu úložiště.

Můžete použít `StorageRead` , `StorageWrite` a `StorageDelete` pro hodnotu parametru **kategorie** .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Tady je příklad:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/tableServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

#### <a name="stream-logs-to-an-event-hub"></a>Streamování protokolů do centra událostí

Pokud se rozhodnete streamovat protokoly do centra událostí, platíte za objem protokolů, které se odesílají do centra událostí. Konkrétní ceny najdete v části **protokoly platformy** na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Protokol povolte pomocí příkazu [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Tady je příklad:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/tableServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

#### <a name="send-logs-to-log-analytics"></a>Odesílání protokolů do Log Analytics

Protokol povolte pomocí příkazu [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Tady je příklad:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/tableServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Další informace najdete v tématu [streamování protokolů prostředků Azure do Log Analytics pracovního prostoru v Azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Šablona](#tab/template)

Chcete-li zobrazit šablonu Azure Resource Manager, která vytvoří nastavení diagnostiky, přečtěte si téma [nastavení diagnostiky pro Azure Storage](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analýza metrik

Metriky můžete analyzovat pro Azure Storage s využitím metrik z jiných služeb Azure pomocí Průzkumník metrik. Otevřete Průzkumník metrik tím, že v nabídce **Azure monitor** vyberete **metriky** . Podrobnosti o používání tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../../azure-monitor/essentials/metrics-getting-started.md). 

Tento příklad ukazuje, jak zobrazit **transakce** na úrovni účtu.

![Snímek obrazovky s přístupem k metrikám v Azure Portal](./media/monitor-table-storage/access-metrics-portal.png)

Pro metriky, které podporují dimenze, můžete metriku filtrovat pomocí požadované hodnoty dimenze. Tento příklad ukazuje, jak zobrazit **transakce** na úrovni účtu u konkrétní operace výběrem hodnot pro dimenzi **název rozhraní API** .

![Snímek obrazovky s přístupem k metrikám s dimenzí v Azure Portal](./media/monitor-table-storage/access-metrics-portal-with-dimension.png)

Úplný seznam dimenzí, které Azure Storage podporuje, najdete v tématu věnovaném [dimenzím metrik](monitor-table-storage-reference.md#metrics-dimensions).

Metriky pro službu Azure Table Storage jsou v těchto oborech názvů: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/tableServices

Seznam Azure Monitor všech metrik podpory, které obsahují službu Azure Table Storage, najdete v článku [Azure monitor podporované metriky](../../azure-monitor/essentials/metrics-supported.md).


### <a name="accessing-metrics"></a>Přístup k metrikám

> [!TIP]
> Pokud chcete zobrazit příklady rozhraní příkazového řádku Azure CLI nebo .NET, vyberte odpovídající karty, které jsou tady uvedené.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Výpis definice metriky

Můžete vypsat definici metriky svého účtu úložiště nebo služby Table Storage. Použijte rutinu [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) .

V tomto příkladu nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo ID prostředku služby Table Storage.  Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Čtení hodnot metriky

Můžete číst hodnoty metriky na úrovni účtu účtu úložiště nebo služby Table Storage. Použijte rutinu [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Výpis definice metriky na úrovni účtu

Můžete vypsat definici metriky svého účtu úložiště nebo služby Table Storage. Použijte příkaz [AZ monitor Metrics list-definitions](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) .
 
V tomto příkladu nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo ID prostředku služby Table Storage. Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Číst hodnoty metrik na úrovni účtu

Můžete si přečíst hodnoty metrik svého účtu úložiště nebo služby Table Storage. Použijte příkaz [AZ monitor Metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor poskytuje [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) pro čtení definice metrik a hodnot. [Vzorový kód](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) ukazuje, jak použít sadu SDK s různými parametry. `0.18.0-preview`Pro metriky úložiště je nutné použít nebo novější verzi.
 
V těchto příkladech nahraďte `<resource-ID>` zástupný symbol ID prostředku celého účtu úložiště nebo služby Table Storage. Tato ID prostředků najdete na stránkách **vlastností** svého účtu úložiště v Azure Portal.

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
        // Resource ID for table storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default";
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

### <a name="template"></a>[Šablona](#tab/template)

Není k dispozici.

---

## <a name="analyzing-logs"></a>Analýza protokolů

Protokoly prostředků můžete přistupovat buď jako objekt BLOB v účtu úložiště, jako data události, nebo prostřednictvím analytických dotazů log.

Podrobné informace o polích, která se zobrazují v těchto protokolech, najdete v tématu Referenční informace k [datům monitorování služby Azure Table Storage](monitor-table-storage-reference.md).

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly pro objekty BLOB (včetně Azure Data Lake Storage Gen2), soubory, fronty, tabulky, účty Premium Storage pro obecné účely V1 a účty úložiště pro obecné účely v2. Klasické účty úložiště se nepodporují.

Položky protokolu jsou vytvořeny pouze v případě, že jsou zadány požadavky na koncový bod služby. Pokud například účet úložiště obsahuje aktivitu v koncovém bodu tabulky, ale ne v koncových bodech objektu BLOB nebo fronty, vytvoří se pouze protokoly, které se týkají služby Table Service. Protokoly Azure Storage obsahují podrobné informace o úspěšných a neúspěšných požadavcích služby úložiště. Tyto informace je možné použít k monitorování jednotlivých požadavků a diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí.

### <a name="log-authenticated-requests"></a>Protokolovat ověřené požadavky

 Protokolují se následující typy ověřovaných požadavků:

- Úspěšné požadavky
- Neúspěšné požadavky, včetně vypršení časového limitu, omezování, chyb sítě, selhání autorizace a dalších chyb
- Požadavky, které používají sdílený přístupový podpis (SAS) nebo OAuth, včetně neúspěšných a úspěšných požadavků
- Požadavky na analytické údaje (data protokolu Classic v kontejneru **$logs** a data metriky třídy v **$metricch** tabulkách)

Požadavky samotné službou Table Storage, jako je vytvoření nebo odstranění protokolu, nejsou protokolovány. Úplný seznam protokolovaných dat naleznete v tématu [operace protokolované úložiště a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Formát protokolu úložiště](monitor-table-storage-reference.md).

### <a name="log-anonymous-requests"></a>Protokolování anonymních požadavků

 Protokolují se tyto typy anonymních požadavků:

- Úspěšné požadavky
- Chyby serveru
- Chyby vypršení časového limitu pro klienta i server
- Neúspěšné žádosti o získání s kódem chyby 304 (nezměněno)

Všechny ostatní neúspěšné anonymní požadavky nejsou protokolovány. Úplný seznam protokolovaných dat naleznete v tématu [operace protokolované úložiště a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Formát protokolu úložiště](monitor-table-storage-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Přístup k protokolům v účtu úložiště

Protokoly se zobrazí jako objekty blob uložené do kontejneru v cílovém účtu úložiště. Data se shromažďují a ukládají v jednom objektu BLOB jako datová část JSON s oddělenými řádky. Název objektu BLOB se řídí těmito zásadami vytváření názvů:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/tableServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Tady je příklad:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Přístup k protokolům v centru událostí

Protokoly odeslané do centra událostí nejsou uloženy jako soubor, ale můžete ověřit, že centrum událostí obdrželo informace protokolu. V Azure Portal přejdete do centra událostí a ověříte, že počet **příchozích zpráv** je větší než nula. 

![Protokoly auditu](media/monitor-table-storage/event-hub-log.png)

Data protokolu, která se odesílají do centra událostí, můžete otevřít a číst pomocí nástrojů pro správu a správu událostí a monitorování událostí a informací o zabezpečení. Další informace najdete v tématu [protokoly prostředků Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Přístup k protokolům v pracovním prostoru Log Analytics

K protokolům odesílaným do Log Analytics pracovního prostoru můžete přistupovat pomocí dotazů protokolu Azure Monitor.

Další informace najdete v tématu [streamování dat monitorování Azure do centra událostí a externích partnerů](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

Data se ukládají do tabulky **StorageTableLogs** . 

#### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Tady jsou některé dotazy, které můžete zadat do panelu **hledání v protokolu** , abyste mohli monitorovat úložiště tabulek. Tyto dotazy fungují s [novým jazykem](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> Když vyberete **protokoly** z nabídky Skupina prostředků účtu úložiště, Log Analytics se otevře s oborem dotazu nastaveným na aktuální skupinu prostředků. To znamená, že dotazy protokolu budou zahrnovat jenom data z dané skupiny prostředků. Pokud chcete spustit dotaz, který zahrnuje data z jiných prostředků nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](../../azure-monitor/logs/scope.md) .

Tyto dotazy vám pomůžou monitorovat účty Azure Storage:

* K vypsání deseti nejběžnějších chyb za poslední tři dny.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* K vypsání prvních 10 operací, které způsobily nejvíce chyb za poslední tři dny.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* K vypsání prvních 10 operací s nejdelší koncovou latencí za poslední tři dny.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* K vypsání všech operací, které v posledních třech dnech způsobily chyby omezení na straně serveru.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* K vypsání všech žádostí s anonymním přístupem za poslední tři dny.

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* K vytvoření výsečového grafu operací používaných za poslední tři dny.
    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Časté otázky

**Podporuje Azure Storage metriky pro Managed Disks nebo nespravované disky?**

No. Azure COMPUTE podporuje metriky na discích. Další informace najdete v tématu [metriky jednotlivých disků pro spravované a nespravované disky](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Další kroky

- Odkaz na protokoly a metriky vytvořené službou Azure Table Storage najdete v referenčních informacích o [monitorování Azure Table Storage](monitor-table-storage-reference.md).
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
- Další informace o migraci metrik najdete v tématu [Azure Storage migrace metrik](../common/storage-metrics-migration.md).