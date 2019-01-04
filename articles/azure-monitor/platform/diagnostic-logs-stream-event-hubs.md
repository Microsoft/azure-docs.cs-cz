---
title: Diagnostické protokoly Azure Stream do centra událostí
description: Naučíte se Streamovat diagnostické protokoly Azure do centra událostí.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 2143ebdddc71973b64b8b77be55b4d0c2b84df64
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53578984"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Diagnostické protokoly Azure Stream do centra událostí
**[Diagnostické protokoly Azure](diagnostic-logs-overview.md)**  můžete streamování v reálném čase pro libovolné aplikace na portálu nebo tím, že ID pravidla autorizace centra událostí v nastavení diagnostiky Azure pomocí integrovaných možností "Export do služby Event Hubs" Rutiny Powershellu nebo Azure CLI.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Co můžete dělat s Event Hubs a protokoly diagnostiky
Můžete například použít funkci streamování pro diagnostické protokoly několika způsoby:

* **Stream protokolů systémy telemetrie a protokolování 3. stran** – můžete Streamovat všechny diagnostických protokolů do jednoho centra událostí do datového kanálu protokolu do nástrojů třetích stran SIEM nebo log analytics.
* **Zobrazit stav služby streamování "kritickou cestu" dat do Power BI** – pomocí služby Event Hubs, Stream Analytics a Power BI, můžete snadno transformovat vaše diagnostická data v k téměř v reálném čase přehledy o vašich službách Azure. [Tento článek dokumentace podává přehled o tom, jak nastavit službu Event Hubs, zpracování dat pomocí Stream Analytics a Power BI využívat jako výstup](../../stream-analytics/stream-analytics-power-bi-dashboard.md). Tady je pár tipů pro získání nastavení diagnostické protokoly:

  * Centra událostí pro kategorii diagnostické protokoly se vytvoří automaticky při zaškrtnutím možnosti na portálu nebo povolit prostřednictvím prostředí PowerShell, takže vyberte Centrum událostí v oboru názvů s názvem, který začíná **insights -**.
  * Následující kód SQL je ukázkového dotazu Stream Analytics, který můžete použít k analýze všechna data protokolu do tabulky Power BI:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Vytvářejte vlastní telemetrii a protokolování platformy** – Pokud už máte vlastními silami sestavených telemetrická data platform nebo se uvažujete o 1, vysoce škálovatelné sestavování publikování a odběru povaha služby Event Hubs umožňuje flexibilně ingestovat diagnostické protokoly. [Viz Dan Rosanova návod k použití služby Event Hubs v telemetrii platformě globální škálování zde](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Povolení streamování diagnostických protokolů

Streamování diagnostických protokolů prostřednictvím kódu programu, prostřednictvím portálu, nebo pomocí můžete povolit [REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). V obou případech můžete vytvořit nastavení diagnostiky ve kterém určíte, obor názvů služby Event Hubs a kategorie protokolů a metrik, které chcete odeslat do oboru názvů. Centra událostí je vytvořen v oboru názvů pro každou kategorii protokolů, které povolíte. Diagnostika **kategorie protokolu** je typ protokolu, který může shromažďovat prostředku.

> [!WARNING]
> Povolení a streamování diagnostických protokolů z výpočetních prostředků (například virtuální počítače nebo Service Fabric) [vyžaduje jinou sadu kroků](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

Event Hubs, který obor názvů nemusí být ve stejném předplatném jako prostředek, které vysílá protokoly za předpokladu, uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC pro předplatná a obě předplatná jsou součástí stejného tenanta služby AAD.

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Například*: Metrika 'Příchozí zprávy' v Centru událostí můžete prozkoumat a převést na graf úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Diagnostické protokoly Stream pomocí portálu

1. Na portálu přejděte do Azure monitoru a klikněte na **nastavení diagnostiky**

    ![Monitorování bodu služby Azure Monitor](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-blade.png)

2. Volitelně můžete filtrovat seznam podle skupiny prostředků nebo prostředek typu a potom klikněte na prostředek, pro kterou chcete nastavit nastavení diagnostiky.

3. Pokud neexistuje žádná nastavení pro prostředek jste vybrali, se zobrazí výzva k vytvoření nastavení. Klikněte na tlačítko "Zapnout diagnostiku."

   ![Přidejte nastavení diagnostiky – žádná existující nastavení](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-none.png)

   Pokud existuje stávající nastavení na prostředek, zobrazí se seznam nastavení, které jsou už nakonfigurovaná na tento prostředek. Klikněte na tlačítko "Přidat nastavení diagnostiky."

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-multiple.png)

3. Zadejte název nastavení a zaškrtněte políčko u **Stream do centra událostí**, vyberte obor názvů služby Event Hubs.

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-configure.png)

   Vybraný obor názvů bude kde vytvoření (pokud to vaše první čas streamování diagnostických protokolů) nebo Streamovat do centra událostí (Pokud již existují prostředky, které jsou streamování protokolů kategorie se tento obor názvů), a definuje zásady oprávnění, která streamování mechanismus má. Streamování do centra událostí v současné době vyžaduje oprávnění spravovat, odesílání a naslouchání. Můžete vytvořit nebo upravit zásady přístupu sdílený obor názvů služby Event Hubs na portálu na kartě Konfigurace pro váš obor názvů. Pokud chcete aktualizovat jeden z těchto nastavení diagnostiky, klient musí mít oprávnění ListKey na autorizační pravidlo Event Hubs. Můžete také v případě potřeby zadejte název centra událostí. Pokud zadáte název centra událostí, protokoly se směrují do tohoto centra událostí, nikoli do nově vytvořeného event hub podle jednotlivých kategorií protokolu.

4. Klikněte na **Uložit**.

Po chvíli se nové nastavení se zobrazí v seznamu nastavení pro tento prostředek a diagnostické protokoly se streamují do tohoto centra událostí, jakmile je vygenerována nová data události.

### <a name="via-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell

Pokud chcete povolit streamování prostřednictvím [rutin prostředí Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), můžete použít `Set-AzureRmDiagnosticSetting` rutiny s těmito parametry:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

ID pravidla autorizace centra událostí je řetězec v tomto formátu: `{Event Hub namespace resource ID}/authorizationrules/{key name}`, například `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Nelze aktuálně vyberte název určité události centra pomocí Powershellu.

### <a name="via-azure-cli"></a>Prostřednictvím rozhraní příkazového řádku Azure

Pokud chcete povolit streamování prostřednictvím [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest), můžete použít [az monitor diagnostiky – nastavení vytváření](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) příkazu.

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

Můžete přidat další kategorie pro protokol diagnostiky tak, že přidáte slovníky předané jako pole JSON `--logs` parametru.

`--event-hub-rule` Argument používá stejný formát jako ID pravidla autorizace centra událostí, jak je vysvětleno pro rutinu Powershellu.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Jak se využívají data protokolů ze služby Event Hubs?

Tady je ukázkový výstup dat z Event Hubs:

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
| záznamy |Pole všech protokolů událostí v této datové části. |
| time |Čas, kdy došlo k události. |
| category |Kategorie protokolu pro tuto událost. |
| resourceId |ID prostředku prostředku, který vytvořil tuto událost. |
| operationName |Název operace |
| úroveň |Volitelné. Určuje úroveň protokolu událostí. |
| properties |Vlastnosti události. |

Můžete zobrazit seznam všech poskytovatelů prostředků, které podporují streamování do Event Hubs [tady](diagnostic-logs-overview.md).

## <a name="stream-data-from-compute-resources"></a>Datový Stream z výpočetních prostředků

Také můžete Streamovat diagnostické protokoly z výpočetních prostředků pomocí agenta diagnostiky Windows Azure. [Najdete v článku](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md) jak k tomuto nastavení.

## <a name="next-steps"></a>Další postup

* [Stream protokolů služby Azure Active Directory pomocí Azure monitoru](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Další informace o diagnostických protokolech Azure](diagnostic-logs-overview.md)
* [Začínáme s Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
