---
title: Vytvoření diagnostického nastavení pro shromažďování protokolů a metrik v Azure
description: Vytvořte diagnostická nastavení pro předávání protokolů platformy Azure do protokolů monitorování Azure, úložiště Azure nebo Centra událostí Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672408"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Vytvoření diagnostického nastavení pro shromažďování protokolů platformy a metrik v Azure
[Protokoly platformy](platform-logs-overview.md) v Azure, včetně protokolu aktivit Azure a protokolů prostředků, poskytují podrobné diagnostické a auditující informace pro prostředky Azure a platformu Azure, na které závisí. Tento článek obsahuje podrobnosti o vytváření a konfiguraci diagnostických nastavení pro odesílání protokolů platformy do různých cílů.

> [!IMPORTANT]
> Před vytvořením diagnostické nastavení pro shromažďování protokolu aktivit, měli byste nejprve zakázat všechny starší konfigurace. Podrobnosti [najdete v tématu Shromažďování protokolu aktivit Azure se staršími nastaveními.](diagnostic-settings-legacy.md)

Každý prostředek Azure vyžaduje vlastní diagnostické nastavení, které definuje následující:

- Kategorie protokolů a metrických dat odeslaných do cílů definovaných v nastavení. Dostupné kategorie se budou lišit pro různé typy prostředků.
- Jeden nebo více cílů pro odeslání protokolů. Mezi aktuální cíle patří pracovní prostor Log Analytics, centra událostí a Azure Storage.
 
Jediné diagnostické nastavení může definovat maximálně jeden z každého cíle. Pokud chcete odeslat data do více než jednoho určitého cílového typu (například do dvou různých pracovních prostorů Analýzy protokolů), vytvořte více nastavení. Každý prostředek může mít až 5 diagnostických nastavení.


> [!NOTE]
> [Metriky platformy](metrics-supported.md) se shromažďují automaticky do [metrik Azure Monitor .](data-platform-metrics.md) Diagnostická nastavení lze použít ke shromažďování metrik pro určité služby Azure do protokolů monitorování Azure pro analýzu s jinými daty monitorování pomocí [dotazů protokolu](../log-query/log-query-overview.md).

## <a name="destinations"></a>Cíle 
Protokoly platformy lze odeslat do cílů v následující tabulce. Konfigurace pro každý cíl se provádí pomocí stejného procesu pro vytváření diagnostických nastavení popsaných v tomto článku. Podrobnosti o odesílání dat do tohoto cíle naleznete v každé tabulce v následující tabulce.

| Cíl | Popis |
|:---|:---|
| [Pracovní prostor Log Analytics](resource-logs-collect-workspace.md) | Shromažďování protokolů do pracovního prostoru Log Analytics umožňuje analyzovat je s dalšími daty monitorování shromážděnými službou Azure Monitor pomocí výkonných dotazů protokolu a také využít další funkce Azure Monitoru, jako jsou výstrahy a vizualizace. |
| [Centrum událostí](resource-logs-stream-event-hubs.md) | Odesílání protokolů do centra událostí umožňuje streamovat data do externích systémů, jako jsou siem třetích stran a další řešení analýzy protokolů. |
| [Účet služby Azure Storage](resource-logs-collect-storage.md) | Archivace protokolů do účtu úložiště Azure je užitečná pro auditování, statickou analýzu nebo zálohování. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Vytvoření diagnostických nastavení na webu Azure Portal
Nastavení diagnostiky na webu Azure Portal můžete nakonfigurovat buď z nabídky Azure Monitor, nebo z nabídky pro prostředek.

1. Kde nakonfigurujete nastavení diagnostiky na webu Azure Portal, závisí na prostředku.

   - U jednoho prostředku klikněte v nabídce zdroje na **diagnostické nastavení** v části **Sledování.**

        ![Nastavení diagnostiky](media/diagnostic-settings/menu-resource.png)

    - U jednoho nebo více prostředků klikněte v části **Nastavení** v nabídce Azure Monitor na **nastavení diagnostiky** a potom klikněte na prostředek.
    
        ![Nastavení diagnostiky](media/diagnostic-settings/menu-monitor.png)

    - V protokolu aktivit klikněte na **protokol aktivit** v nabídce **Azure Monitor** a potom na **Nastavení diagnostiky**. Ujistěte se, že zakázat všechny starší konfigurace pro protokol aktivit. Podrobnosti [najdete v tématu Zakázání existujících nastavení.](diagnostic-settings-legacy.md#disable-existing-settings)

        ![Nastavení diagnostiky](media/diagnostic-settings/menu-activity-log.png)

2. Pokud u vybraného prostředku neexistují žádná nastavení, budete vyzváni k vytvoření nastavení. Klepněte na **tlačítko Přidat diagnostické nastavení**.

   ![Přidat diagnostické nastavení - žádné existující nastavení](media/diagnostic-settings/add-setting.png)

   Pokud jsou v prostředku existující nastavení, zobrazí se již nakonfigurovaný seznam nastavení. Chcete-li přidat nové nastavení, klepněte buď na **Přidat diagnostické nastavení,** nebo upravit stávající **nastavení.** Každé nastavení může mít maximálně jeden z jednotlivých typů cílů.

   ![Přidat diagnostické nastavení - existující nastavení](media/diagnostic-settings/edit-setting.png)

3. Zadejte název nastavení, pokud ho ještě nemá.
4. Chcete-li protokoly odeslat, zaškrtněte políčko pro každý cíl. Chcete-li zadat jejich nastavení, jak je popsáno v následující tabulce, klepněte na **tlačítko Konfigurovat.**

    | Nastavení | Popis |
    |:---|:---|
    | Pracovní prostor služby Log Analytics | Název pracovního prostoru. |
    | Účet úložiště | Název účtu úložiště. |
    | Obor názvů centra událostí | Obor názvů, kde je centrum událostí vytvořeno (pokud se jedná o první protokoly streamování) nebo do datového proudu (pokud již existují prostředky, které jsou datovým proudem této kategorie protokolu do tohoto oboru názvů).
    | Název centra událostí | Volitelně zadejte název centra událostí pro odeslání všech dat v nastavení. Pokud nezadáte název, centrum událostí se vytvoří pro každou kategorii protokolu. Pokud odesíláte více kategorií, můžete zadat název pro omezení počtu vytvořených center událostí. Podrobnosti najdete v [tématu kvóty a limity centra událostí Azure.](../../event-hubs/event-hubs-quotas.md) |
    | Název zásad centra událostí | Definuje oprávnění, která má mechanismus streamování. |

    ![Přidat diagnostické nastavení - existující nastavení](media/diagnostic-settings/setting-details.png)

5. Zaškrtněte políčko pro každou kategorii dat, která má být odeslána do určených cílů. Seznam kategorií se bude lišit pro každou službu Azure.

   > [!NOTE]
   > Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
   >
   > *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.

6. Klikněte na **Uložit**.

Po chvíli se nové nastavení zobrazí v seznamu nastavení pro tento prostředek a protokoly jsou přenášeny datovým proudem do zadaných cílů při generování nových dat událostí. Všimněte si, že může být až patnáct minut mezi při vyzařování události a když [se zobrazí v pracovním prostoru Log Analytics](data-ingestion-time.md).



## <a name="create-diagnostic-settings-using-powershell"></a>Vytvoření diagnostických nastavení pomocí PowerShellu
Pomocí rutiny [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) vytvořte diagnostické nastavení pomocí [prostředí Azure PowerShell](powershell-quickstart-samples.md). Popis jejích parametrů naleznete v dokumentaci k této rutině.

> [!IMPORTANT]
> Tuto metodu nelze použít pro protokol aktivit Azure. Místo toho použijte [použít nastavení vytvoření diagnostiky v Azure Monitorpomocí šablony Správce prostředků](diagnostic-settings-template.md) k vytvoření šablony Správce prostředků a nasadit ji pomocí PowerShellu.

Následuje příklad rutiny prostředí PowerShell k vytvoření diagnostického nastavení pomocí všech tří cílů.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Vytvoření diagnostických nastavení pomocí azure cli
Pomocí příkazu [az monitor u vytvoření nastavení diagnostiky](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) vytvořte diagnostické nastavení pomocí [příkazového příkazu Konstituci az](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Popis jeho parametrů naleznete v dokumentaci k tomuto příkazu.

> [!IMPORTANT]
> Tuto metodu nelze použít pro protokol aktivit Azure. Místo toho použijte [vytvořit diagnostické nastavení v Azure Monitorpomocí šablony Správce prostředků](diagnostic-settings-template.md) k vytvoření šablony Správce prostředků a nasadit ji pomocí příkazového příkazu k použití.

Následuje příklad příkazu příkazu příkazu PŘÍKAZ K lisování a vytváření diagnostických nastavení pomocí všech tří cílů.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Konfigurace nastavení diagnostiky pomocí rozhraní REST API
Viz [Diagnostic Káznastavení](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) a vytvořte nebo aktualizujte diagnostická nastavení pomocí [rozhraní Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Konfigurace nastavení diagnostiky pomocí šablony Správce prostředků
Viz [Vytvoření nastavení diagnostiky ve Službě Azure Monitor pomocí šablony Správce prostředků](diagnostic-settings-template.md) k vytvoření nebo aktualizaci diagnostických nastavení pomocí šablony Správce prostředků.

## <a name="next-steps"></a>Další kroky

* [Další informace o protokolech platformy Azure](platform-logs-overview.md)
