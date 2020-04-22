---
title: Použití diagnostických nastavení ke shromažďování metrik a protokolů platformy a v Azure
description: Posílejte metriky a protokoly platformy Azure Monitor do protokolů monitorování Azure, úložiště Azure nebo centra událostí Azure pomocí diagnostického nastavení.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681182"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Vytvoření diagnostického nastavení pro shromažďování protokolů prostředků a metrik v Azure

[Protokoly platformy](platform-logs-overview.md) v Azure, včetně protokolu aktivit Azure a protokolů prostředků, poskytují podrobné diagnostické a auditující informace pro prostředky Azure a platformu Azure, na které závisí. [Metriky platformy](data-platform-metrics.md) se shromažďují ve výchozím nastavení a obvykle se ukládají v databázi metrik Azure Monitoru.

Tento článek obsahuje podrobnosti o vytváření a konfiguraci diagnostických nastavení pro odesílání metrik platformy a protokolů platformy do různých cílů.

> [!IMPORTANT]
> Před vytvořením diagnostické nastavení pro shromažďování protokolu aktivit, měli byste nejprve zakázat všechny starší konfigurace. Podrobnosti [najdete v tématu Shromažďování protokolu aktivit Azure se staršími nastaveními.](diagnostic-settings-legacy.md)

Každý prostředek Azure vyžaduje vlastní diagnostické nastavení, které definuje následující kritéria:

- Kategorie protokolů a metrických dat odeslaných do cílů definovaných v nastavení. Dostupné kategorie se budou lišit pro různé typy prostředků.
- Jeden nebo více cílů pro odeslání protokolů. Mezi aktuální cíle patří pracovní prostor Log Analytics, centra událostí a Azure Storage.

Jediné diagnostické nastavení může definovat maximálně jeden z každého cíle. Pokud chcete odeslat data do více než jednoho určitého cílového typu (například do dvou různých pracovních prostorů Analýzy protokolů), vytvořte více nastavení. Každý prostředek může mít až 5 diagnostických nastavení.

> [!NOTE]
> [Metriky platformy](metrics-supported.md) se shromažďují automaticky do [metrik Azure Monitor .](data-platform-metrics.md) Diagnostická nastavení lze použít ke shromažďování metrik pro určité služby Azure do protokolů monitorování Azure pro analýzu s jinými daty monitorování pomocí [dotazů protokolu](../log-query/log-query-overview.md).

## <a name="destinations"></a>Cíle

Protokoly platformy a metriky lze odeslat do cílů v následující tabulce. Podrobnosti o odesílání dat do tohoto cíle naleznete v každé tabulce v následující tabulce.

| Cíl | Popis |
|:---|:---|
| [Pracovní prostor služby Log Analytics](resource-logs-collect-workspace.md) | Shromažďování protokolů a metrik do pracovního prostoru Log Analytics umožňuje analyzovat je s dalšími daty monitorování shromážděnými službou Azure Monitor pomocí výkonných dotazů protokolu a také využít další funkce Azure Monitoru, jako jsou výstrahy a vizualizace. |
| [Centra událostí](resource-logs-stream-event-hubs.md) | Odesílání protokolů a metrik do centra událostí umožňuje streamovat data do externích systémů, jako jsou siem třetích stran a další řešení analýzy protokolů. |
| [Účet služby Azure Storage](resource-logs-collect-storage.md) | Archivace protokolů a metrik do účtu úložiště Azure je užitečná pro auditování, statickou analýzu nebo zálohování. Ve srovnání s protokoly monitorování Azure a pracovní prostor Log Analytics, úložiště Azure je levnější a protokoly lze uchovávat tam po neomezenou dobu. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Vytvoření diagnostických nastavení na webu Azure Portal

Nastavení diagnostiky na webu Azure Portal můžete nakonfigurovat buď z nabídky Azure Monitor, nebo z nabídky pro prostředek.

1. Kde nakonfigurujete nastavení diagnostiky na webu Azure Portal, závisí na prostředku.

   - U jednoho prostředku klikněte v nabídce zdroje na **diagnostické nastavení** v části **Sledování.**

        ![Nastavení diagnostiky](media/diagnostic-settings/menu-resource.png)

   - U jednoho nebo více prostředků klikněte v části **Nastavení** v nabídce Azure Monitor na **nastavení diagnostiky** a potom klikněte na prostředek.

      ![Nastavení diagnostiky](media/diagnostic-settings/menu-monitor.png)

   - V protokolu aktivit klikněte na **protokol aktivit** v nabídce **Azure Monitor** a potom na **Nastavení diagnostiky**. Ujistěte se, že zakázat všechny starší konfigurace pro protokol aktivit. Podrobnosti [najdete v tématu Zakázání existujících nastavení.](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log)

        ![Nastavení diagnostiky](media/diagnostic-settings/menu-activity-log.png)

2. Pokud u vybraného prostředku neexistují žádná nastavení, budete vyzváni k vytvoření nastavení. Klepněte na **tlačítko Přidat diagnostické nastavení**.

   ![Přidat diagnostické nastavení - žádné existující nastavení](media/diagnostic-settings/add-setting.png)

   Pokud jsou na prostředku existující nastavení, zobrazí se seznam nastavení, která jsou již nakonfigurována. Chcete-li přidat nové nastavení, klepněte buď na **Přidat diagnostické nastavení,** nebo upravit stávající **nastavení.** Každé nastavení může mít maximálně jeden z jednotlivých typů cílů.

   ![Přidat diagnostické nastavení - existující nastavení](media/diagnostic-settings/edit-setting.png)

3. Zadejte název nastavení, pokud ho ještě nemá.

    ![Přidat diagnostické nastavení](media/diagnostic-settings/setting-new-blank.png)

4. **Podrobnosti kategorie (co trasovat)** – Zaškrtněte políčko pro každou kategorii dat, která chcete odeslat do cílů určených později. Seznam kategorií se liší pro každou službu Azure.

     - **AllMetrics** směruje metriky platformy prostředků do úložiště protokolů Azure, ale ve formě protokolu. Tyto metriky se obvykle posílají jenom do databáze časových řad metrik Azure Monitoru. Jejich odesláním do úložiště protokolů azure monitoru (které lze prohledávat prostřednictvím analýzy protokolů) můžete je integrovat do dotazů, které prohledávají ostatní protokoly. Tato možnost nemusí být k dispozici pro všechny typy prostředků. Když je podporována, [Azure Monitor podporované metriky](metrics-supported.md) uvádí, jaké metriky jsou shromažďovány pro jaké typy prostředků.

       > [!NOTE]
       > Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
       >
       > *Například*: Metrika IOReadBytes na blockchainu může být prozkoumána a zmapována na úrovni na uzl. Při exportu prostřednictvím nastavení diagnostiky však exportovaná metrika představuje všechny čtení bajtů pro všechny uzly.

     - **Protokoly** uvádí různé kategorie, které jsou k dispozici v závislosti na typu prostředku. Zkontrolujte všechny kategorie, které chcete směrovat k cíli.

5. **Podrobnosti o cíli** – Zaškrtněte políčko pro každý cíl. Když zaškrtnete každé políčko, zobrazí se možnosti, které vám umožní přidat další informace.

      ![Odeslat do analýzy protokolů nebo centra událostí](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** – zadejte předplatné a pracovní prostor.  Pokud pracovní prostor nemáte, musíte jej [vytvořit, než budete pokračovat](../learn/quick-create-workspace.md).

    1. **Centra událostí** – Zadejte následující kritéria:
       - Předplatné, jehož je centrum událostí součástí
       - Obor názvů centra událostí – pokud ho ještě nemáte, budete [ho muset vytvořit.](../../event-hubs/event-hubs-create.md)
       - Název centra událostí (nepovinné) pro odeslání všech dat. Pokud nezadáte název, centrum událostí se vytvoří pro každou kategorii protokolu. Pokud odesíláte více kategorií, můžete zadat název pro omezení počtu vytvořených center událostí. Podrobnosti najdete v [tématu kvóty a limity centra událostí Azure.](../../event-hubs/event-hubs-quotas.md)
       - Zásada Centra událostí (volitelné) Zásada definuje oprávnění, která má mechanismus streamování. Další informace naleznete v [tématu Event-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Úložiště** – zvolte předplatné, účet úložiště a zásady uchovávání informací.

        ![Odeslat do úložiště](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Zvažte nastavení zásad uchovávání informací na 0 a ruční odstranění dat z úložiště pomocí naplánované úlohy, aby se zabránilo možnému nejasnostem v budoucnu.
        >
        > Za prvé, pokud používáte úložiště pro archivaci, obecně chcete, aby vaše data kolem více než 365 dnů. Za druhé, pokud zvolíte zásady uchovávání informací, která je větší než 0, datum vypršení platnosti je připojen k protokolům v době úložiště. Po uložení nelze změnit datum těchto protokolů.
        >
        > Pokud například nastavíte zásady uchovávání informací pro *WorkflowRuntime* na 180 dní a o 24 hodin později ji nastavíte na 365 dní, protokoly uložené během těchto prvních 24 hodin budou automaticky odstraněny po 180 dnech, zatímco všechny následné protokoly tohoto typu budou automaticky odstraněny po 365 dnech. Pozdější změna zásad uchovávání informací neznamená, že prvních 24 hodin protokolů zůstane po dobu 365 dnů.

6. Klikněte na **Uložit**.

Po chvíli se nové nastavení zobrazí v seznamu nastavení pro tento prostředek a protokoly jsou přenášeny datovým proudem do zadaných cílů při generování nových dat událostí. Může trvat až 15 minut mezi vyzařování události a když [se zobrazí v pracovním prostoru Log Analytics](data-ingestion-time.md).

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

- [Další informace o protokolech platformy Azure](platform-logs-overview.md)
