---
title: Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik v Azure | Microsoft Docs
description: Vytvořte nastavení diagnostiky pro přeposílání protokolů platformy Azure, abyste mohli protokoly Azure Monitor, úložiště Azure nebo Azure Event Hubs.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c1b7f81c62217d9e113f3293a8f351d908a6a576
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887272"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure
[Protokoly platforem](resource-logs-overview.md) v Azure poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé. Tento článek poskytuje podrobné informace o vytváření a konfiguraci nastavení diagnostiky pro shromažďování protokolů platforem do různých umístění.

Každý prostředek Azure vyžaduje vlastní nastavení diagnostiky. Nastavení diagnostiky definuje pro tento prostředek následující:

- Kategorie protokolů a data metriky, které jsou odesílány do cílů definovaných v nastavení. Dostupné kategorie se budou lišit pro různé typy prostředků.
- Jeden nebo více cílů pro odeslání protokolů. Aktuální cíle zahrnují Log Analytics pracovní prostor, Event Hubs a Azure Storage.
- Zásady uchovávání informací pro data uložená v Azure Storage.
 
Jedno diagnostické nastavení může definovat jedno ze všech cílů. Pokud chcete odesílat data do více než jednoho konkrétního cílového typu (například dvou různých Log Analytics pracovních prostorů), vytvořte více nastavení. Každý prostředek může mít až 5 nastavení diagnostiky.

> [!NOTE]
> Protokol aktivit je možné přeslat do stejného umístění jako jiné protokoly platformy, ale není nakonfigurovaný s nastavením diagnostiky. Podrobnosti najdete [v tématu Přehled protokolů platforem v Azure](platform-logs-overview.md#destinations) .

> [!NOTE]
> [Metriky platformy](metrics-supported.md) se shromažďují automaticky, aby se [Azure monitor metriky](data-platform-metrics.md). Nastavení diagnostiky lze použít ke shromáždění metrik pro určité služby Azure do protokolů Azure Monitor k analýze s ostatními daty monitorování pomocí [dotazů protokolu](../log-query/log-query-overview.md).

## <a name="destinations"></a>Cíle 
Protokoly platforem lze odeslat do cílových umístění v následující tabulce. Konfigurace pro každý cíl se provádí pomocí stejného procesu pro vytváření nastavení diagnostiky popsaných v tomto článku. Podrobnosti o odesílání dat do tohoto cíle získáte podle každého odkazu v následující tabulce.

| Cíl | Popis |
|:---|:---|
| [Pracovní prostor služby Log Analytics](resource-logs-collect-workspace.md) | Shromažďování protokolů do pracovního prostoru Log Analytics umožňuje jejich analýzu s dalšími daty monitorování shromážděnými pomocí Azure Monitor pomocí výkonných dotazů protokolu a také k využití jiných Azure Monitor funkcí, jako jsou výstrahy a vizualizace. |
| [Centra událostí](resource-logs-stream-event-hubs.md) | Odesílání protokolů do Event Hubs umožňuje streamovat data do externích systémů, jako jsou systémů Siem třetích stran a další řešení Log Analytics. |
| [Účet služby Azure Storage](resource-logs-collect-storage.md) | Archivace protokolů na účet služby Azure Storage je užitečná pro audit, statickou analýzu nebo zálohování. |




## <a name="create-diagnostic-settings-in-azure-portal"></a>Vytvořit nastavení diagnostiky v Azure Portal
Nastavení diagnostiky můžete nakonfigurovat v Azure Portal, a to buď z nabídky Azure Monitor, nebo z nabídky pro daný prostředek.

1. V nabídce Azure Monitor v Azure Portal klikněte na **nastavení diagnostiky** v části **Nastavení** a potom klikněte na prostředek.

    ![Nastavení diagnostiky](media/diagnostic-settings/menu-monitor.png)

    Nebo v nabídce prostředků v Azure Portal klikněte na **nastavení diagnostiky** pod položkou **monitor**.

    ![Nastavení diagnostiky](media/diagnostic-settings/menu-resource.png)

2. Pokud u vybraného prostředku neexistuje žádné nastavení, zobrazí se výzva k vytvoření nastavení. Klikněte na **Zapnout diagnostiku**.

   ![Přidat nastavení diagnostiky – žádná existující nastavení](media/diagnostic-settings/add-setting.png)

   Pokud je u prostředku k dispozici existující nastavení, zobrazí se seznam nastavení, která jsou už nakonfigurovaná. Kliknutím na **Přidat nastavení diagnostiky** můžete přidat nové nastavení nebo **Upravit nastavení** a upravit existující. Každé nastavení nemůže mít více než jeden z cílových typů.

   ![Přidat nastavení diagnostiky – existující nastavení](media/diagnostic-settings/edit-setting.png)

3. Dejte nastavení název, pokud ho ještě nikdo nemá.
4. Zaškrtněte políčko pro všechny cílové umístění pro odeslání protokolů. Klikněte na **Konfigurovat** a určete jejich nastavení, jak je popsáno v následující tabulce.

    | Nastavení | Popis |
    |:---|:---|
    | Pracovní prostor Log Analytics | Název pracovního prostoru |
    | Účet úložiště | Název účtu úložiště |
    | Obor názvů centra událostí | Obor názvů, ve kterém se vytvoří centrum událostí (Pokud se jedná o vaše první přihlášení ke streamování protokolů) nebo streamování do služby (Pokud už existují prostředky streamující do tohoto oboru názvů do kategorie log).
    | Název centra událostí | Volitelně můžete zadat název centra událostí, který bude odesílat všechna data v nastavení. Pokud název nezadáte, vytvoří se v každé kategorii protokolu centrum událostí. Pokud posíláte více kategorií, možná budete chtít zadat název pro omezení počtu vytvořených Center událostí. Podrobnosti najdete v tématu [kvóty a omezení pro Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) . |
    | Název zásad centra událostí | Definuje oprávnění, která má mechanismus streamování. |

    ![Přidat nastavení diagnostiky – existující nastavení](media/diagnostic-settings/setting-details.png)

5. Zaškrtněte políčko u každé kategorie dat, která se mají odeslat do určených cílů. Pokud jste vybrali možnost **archivovat do účtu úložiště**, budete také muset zadat [dobu uchování](resource-logs-collect-storage.md#data-retention).



> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.

4. Klikněte na **Uložit**.

Po chvíli se nové nastavení objeví v seznamu nastavení tohoto prostředku a protokoly se streamují do zadaných cílů, protože se generují nová data události. Všimněte si, že mezi při vygenerování události a jejím [zobrazením v Log Analytics pracovním prostoru](data-ingestion-time.md)může trvat až 15 minut.



## <a name="create-diagnostic-settings-using-powershell"></a>Vytvoření nastavení diagnostiky pomocí PowerShellu
Pomocí rutiny [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) vytvořte nastavení diagnostiky s [Azure PowerShell](powershell-quickstart-samples.md). Popis jeho parametrů naleznete v dokumentaci k této rutině.

Následuje příklad rutiny prostředí PowerShell pro vytvoření nastavení diagnostiky pomocí všech tří cílů.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Vytvoření nastavení diagnostiky pomocí Azure CLI
Pomocí příkazu [AZ monitor Diagnostic-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) vytvořte nastavení diagnostiky pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Popis jeho parametrů naleznete v dokumentaci k tomuto příkazu.

Následuje příklad příkazu CLI pro vytvoření nastavení diagnostiky pomocí všech tří cílů.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Konfigurace nastavení diagnostiky pomocí REST API
Pokud chcete vytvořit nebo aktualizovat nastavení diagnostiky pomocí [REST API Azure monitor](https://docs.microsoft.com/rest/api/monitor/), přečtěte si téma [nastavení diagnostiky](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) .


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Konfigurace nastavení diagnostiky pomocí šablony Správce prostředků
Pokud chcete vytvořit nebo aktualizovat nastavení diagnostiky pomocí šablony Správce prostředků, přečtěte si téma [Automatické povolení nastavení diagnostiky při vytváření prostředků pomocí šablony Správce prostředků](diagnostic-settings-template.md) .

## <a name="next-steps"></a>Další kroky

* [Další informace o protokolech platforem Azure](resource-logs-overview.md)