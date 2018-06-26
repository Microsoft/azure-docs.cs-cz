---
title: Datový proud Azure diagnostických protokolů do centra událostí
description: Zjistěte, jak k vysílání datového proudu do centra událostí Azure diagnostické protokoly.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 806ebe80ad49f2f908bf46549bb4abc533a6d516
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936696"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Datový proud Azure diagnostických protokolů do centra událostí
**[Azure diagnostické protokoly](monitoring-overview-of-diagnostic-logs.md)**  Streamovat skoro v reálném čase pro žádnou aplikaci pomocí předdefinované možnosti "Export do služby Event Hubs" na portálu nebo povolením ID události rozbočovače autorizační pravidla v nastavení diagnostiky prostřednictvím Azure Rutiny prostředí PowerShell nebo Azure CLI 2.0.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Co můžete dělat s protokolů diagnostiky a Event Hubs
Můžete použít k diagnostickým protokolům streamování schopností několika způsoby:

* **Stream protokoluje události do 3. stran protokolování a telemetrie systémy** – dá Streamovat všechny k diagnostickým protokolům k rozbočovači jedna událost tak, aby data protokolu kanálu nástroje třetích stran SIEM nebo log analytics.
* **Zobrazit stav služby streamování "aktivní cesta" dat do PowerBI** – pomocí služby Event Hubs, Stream Analytics a PowerBI, můžete snadno transformovat data diagnostiky v poblíž přehledy v reálném čase na služeb Azure. [V tomto článku dokumentace podává přehled o tom, jak nastavit službu Event Hubs, zpracování dat pomocí služby Stream Analytics a použít PowerBI jako výstup](../stream-analytics/stream-analytics-power-bi-dashboard.md). Zde je několik tipů pro získání nastavení diagnostické protokoly.

  * Centra událostí pro kategorii diagnostické protokoly se vytvoří automaticky, když zaškrtnutí políčka na portálu nebo povolit pomocí prostředí PowerShell, kterou chcete vybrat centra událostí v oboru názvů s názvem, který začíná **insights -**.
  * Následující kód SQL je ukázkový dotaz služby Stream Analytics, který můžete použít k analýze všechna data protokolu v do PowerBI tabulky:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the PowerBI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Vytvoření vlastní telemetrii a protokolování platformy** – Pokud už máte uživatelské telemetrie platformy nebo jsou jenom přemýšlíte o vytváření jeden vysoce škálovatelné, publikování a odběru na povaze služby Event Hubs umožňuje flexibilně ingestování diagnostické protokoly. [Naleznete v Průvodci Dana Rosanova pomocí služby Event Hubs telemetrie platformy globálním měřítku zde](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Povolení diagnostických protokolů streamování

Můžete povolit vysílání datového proudu diagnostické protokoly prostřednictvím kódu programu, prostřednictvím portálu nebo pomocí [rozhraní REST API Azure monitorování](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). V obou případech vytvoříte nastavení diagnostiky v němž jsou uvedeny na obor názvů služby Event Hubs a protokolu kategorií a metriky, které se mají posílat do oboru názvů. Centra událostí je vytvořen v oboru názvů pro každou kategorii protokolu, které povolíte. Diagnostika **kategorie protokolu** je typ protokolu, který může shromažďovat prostředku.

> [!WARNING]
> Povolení a vysílání datového proudu diagnostických protokolů z výpočetní prostředky (například virtuální počítače nebo Service Fabric) [vyžaduje jinou sadu kroků](../event-hubs/event-hubs-streaming-azure-diags-data.md).

Obor názvů služby Event Hubs nemusí být ve stejném předplatném jako prostředek emitování protokoly tak dlouho, dokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC do oba odběry.

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Diagnostické protokoly datového proudu pomocí portálu

1. Na portálu, přejděte do monitorování Azure a klikněte na **nastavení diagnostiky**

    ![Monitorování části monitorování Azure](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. Volitelně můžete seznam filtrovat podle skupiny prostředků nebo typ prostředku, a potom klikněte na prostředek, pro kterou chcete provést nastavení diagnostiky.

3. Pokud neexistuje žádné nastavení na prostředku jste vybrali, zobrazí se výzva k vytvoření nastavení. Klikněte na tlačítko "Zapnout diagnostiky."

   ![Přidat nastavení diagnostiky - žádná existující nastavení](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   Pokud máte stávající nastavení na prostředek, zobrazí se seznam nastavení, které jsou již nakonfigurována na tomto prostředku. Klikněte na tlačítko "Přidat nastavení diagnostiky."

   ![Přidat nastavení diagnostiky - stávající nastavení](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Zadejte název nastavení a zaškrtněte políčko pro **datový proud do centra událostí**, pak vyberte obor názvů Event Hubs.

   ![Přidat nastavení diagnostiky - stávající nastavení](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)

   Obor názvů vybrané bude kde vytvoření (pokud to vaše první čas diagnostické protokoly streamování) nebo prostřednictvím datového proudu do centra událostí (Pokud již existují prostředky, které jsou streamování této kategorie protokolu na tento obor názvů), a definuje zásady oprávnění, streamování mechanismus má. V současné době streamování do centra událostí vyžadují oprávnění spravovat, odeslání a naslouchání. Můžete vytvářet nebo upravovat Event Hubs obor názvů sdílených zásad přístupu na portálu na kartě Konfigurace oboru názvů. Pokud chcete aktualizovat jednu z těchto nastavení diagnostiky, klient musí mít oprávnění ListKey na autorizační pravidlo Event Hubs. Volitelně můžete zadat název centra událostí. Pokud zadáte název centra událostí, protokoly jsou směrovány do tohoto centra událostí a nikoli k rozbočovači nově vytvořený událostí podle kategorie protokolu.

4. Klikněte na **Uložit**.

Po chvíli se nové nastavení se zobrazí v seznamu nastavení pro tento prostředek a diagnostické protokoly jsou datového proudu do tohoto centra událostí, také se vygeneruje nová data událostí.

### <a name="via-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell

Povolit vysílání datového proudu prostřednictvím [rutin prostředí Azure PowerShell](insights-powershell-samples.md), můžete použít `Set-AzureRmDiagnosticSetting` rutiny s těmito parametry:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

ID události rozbočovače autorizační pravidlo je řetězec s Tento formát: `{Event Hub namespace resource ID}/authorizationrules/{key name}`, například `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Nelze vybrat aktuálně název rozbočovače určitá událost pomocí prostředí PowerShell.

### <a name="via-azure-cli-20"></a>Via Azure CLI 2.0

Povolit vysílání datového proudu prostřednictvím [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/monitor?view=azure-cli-latest), můžete použít [vytvořit az monitorování diagnostiky – nastavení](https://docs.microsoft.com/en-us/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) příkaz.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Přidáním dalších kategorií do protokolů diagnostiky tak, že přidáte do pole JSON, který je předán jako slovník `--logs` parametr.

`--event-hub-rule` Argument používá stejný formát jako ID události rozbočovače autorizační pravidla, jak je popsáno pro rutinu prostředí PowerShell.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Způsob, jakým využívají data protokolu ze služby Event Hubs?

Zde je ukázka výstupní data ze služby Event Hubs:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Název elementu | Popis |
| --- | --- |
| záznamy |Pole všechny protokolu události v této datové části. |
| time |Čas, kdy došlo k události. |
| category |Kategorie protokolu pro tuto událost. |
| resourceId |ID prostředku prostředku, který vytvořil tuto událost. |
| operationName |Název operace |
| úroveň |Volitelné. Určuje úroveň protokolu událostí. |
| properties |Vlastnosti události. |

Můžete zobrazit seznam všech poskytovatelů prostředků, které podporují streamování Event Hubs [zde](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>Datový proud dat z výpočetní prostředky

Můžete také stream diagnostických protokolů z výpočetní prostředky pomocí agenta Windows Azure Diagnostics. [Najdete v článku](../event-hubs/event-hubs-streaming-azure-diags-data.md) jak k tomuto nastavení.

## <a name="next-steps"></a>Další postup

* [Další informace o diagnostických protokolů Azure.](monitoring-overview-of-diagnostic-logs.md)
* [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
