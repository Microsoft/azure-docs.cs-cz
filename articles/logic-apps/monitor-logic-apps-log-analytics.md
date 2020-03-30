---
title: Monitorování aplikací logiky pomocí protokolů Azure Monitoru
description: Poradce při potížích s aplikacemi logiky nastavením protokolů Azure Monitoru a shromažďováním diagnostických dat pro Aplikace Logika Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270235"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Nastavení protokolů Azure Monitoru a shromažďování diagnostických dat pro aplikace Azure Logic Apps

Chcete-li získat bohatší ladění informací o aplikacích logiky během běhu, můžete nastavit a použít [protokoly Azure Monitor](../azure-monitor/platform/data-platform-logs.md) pro záznam a ukládání informací o datech a událostech za běhu, jako jsou události aktivační události, události spuštění a události akcí v [pracovním prostoru Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). [Azure Monitor](../azure-monitor/overview.md) vám pomůže monitorovat cloudová a místní prostředí, takže můžete snadněji udržovat jejich dostupnost a výkon. Pomocí protokolů Azure Monitor můžete vytvářet [dotazy protokolu,](../azure-monitor/log-query/log-query-overview.md) které vám pomohou shromažďovat a kontrolovat tyto informace. Tato diagnostická data můžete [taky použít s jinými službami Azure](#extend-data), jako je Azure Storage a Azure Event Hubs.

Chcete-li nastavit protokolování pro aplikaci [logiky, můžete povolit Log Analytics při vytváření aplikace logiky](#logging-for-new-logic-apps), nebo můžete [nainstalovat řešení Logic Apps Management](#install-management-solution) v pracovním prostoru Log Analytics pro existující aplikace logiky. Toto řešení poskytuje agregované informace pro spuštění aplikace logiky a obsahuje konkrétní podrobnosti, jako je stav, doba provádění, stav opětovného odeslání a ID korelace. Chcete-li povolit protokolování a vytváření dotazů na tyto informace, [nastavte protokoly Azure Monitor .](#set-up-resource-logs)

Tento článek ukazuje, jak povolit Analýzu protokolů při vytváření aplikací logiky, jak nainstalovat a nastavit řešení Správy logických aplikací a jak nastavit a vytvořit dotazy pro protokoly Azure Monitor.

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete [pracovní prostor Analýzy protokolů](../azure-monitor/platform/resource-logs-collect-workspace.md). Pokud pracovní prostor nemáte, přečtěte si, [jak vytvořit pracovní prostor Analýzy protokolů](../azure-monitor/learn/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Povolení analýzy protokolů pro nové aplikace logiky

Při vytváření aplikace logiky můžete zapnout Analýzu protokolů.

1. Na [webu Azure Portal](https://portal.azure.com)v podokně, kde poskytujete informace o vytvoření aplikace logiky, postupujte takto:

   1. V části **Log Analytics**vyberte **Možnost Zapnuto**.

   1. Ze seznamu **pracovního prostoru Log Analytics** vyberte pracovní prostor, do kterého chcete odesílat data ze spuštění aplikace logiky.

      ![Zadání informací o aplikaci logiky](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Po dokončení tohoto kroku Azure vytvoří aplikaci logiky, která je teď přidružená k pracovnímu prostoru Analýzy protokolů. Tento krok také automaticky nainstaluje řešení Správy logických aplikací do pracovního prostoru.

1. Až to budete mít, vyberte **Vytvořit**.

1. Po spuštění aplikace logiky, chcete-li zobrazit spuštění aplikace logiky, [pokračujte v těchto krocích](#view-logic-app-runs).

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Instalace řešení správy aplikací logiky

Pokud jste při vytváření aplikace logiky zapnuli Analýzu protokolů, tento krok přeskočte. V pracovním prostoru Analýzy protokolů už máte nainstalované řešení Správy logických aplikací.

1. Do vyhledávacího pole [portálu](https://portal.azure.com) `log analytics workspaces`Azure Portal zadejte a vyberte **pracovní prostory Analýzy protokolů**.

   ![Vyberte možnost "Pracovní prostory analýzy protokolů"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. V části **Pracovní prostory Log Analytics**vyberte pracovní prostor.

   ![Vyberte pracovní prostor Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. V podokně **Přehled** vyberte v části **Začínáme s řešeními** > monitorování**Konfigurace protokolů**vyberte **Zobrazit řešení**.

   ![V podokně přehledu vyberte "Zobrazit řešení"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. V části **Přehled**vyberte **Přidat**.

   ![V podokně přehledu přidejte nové řešení](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Po otevření **marketplace** zadejte do `logic apps management`vyhledávacího pole a vyberte Logic Apps **Management**.

   ![Na marketplace vyberte "Správa logických aplikací"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. V podokně popis řešení vyberte **Vytvořit**.

   ![Výběrem možnosti "Vytvořit" přidáte řešení "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Zkontrolujte a potvrďte pracovní prostor Log Analytics, do kterého chcete nainstalovat řešení, a vyberte **Znovu vytvořit.**

   ![Vyberte možnost "Vytvořit" pro "Správu logických aplikací"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Poté, co Azure nasadí řešení do skupiny prostředků Azure, která obsahuje váš pracovní prostor Analýzy protokolů, řešení se zobrazí v podokně souhrnu vašeho pracovního prostoru.

   ![Podokno souhrnu pracovního prostoru](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Nastavení protokolů Azure Monitoru

Při ukládání informací o událostech za běhu a data v [protokolech Azure Monitor](../azure-monitor/platform/data-platform-logs.md), můžete vytvořit [protokol dotazy,](../azure-monitor/log-query/log-query-overview.md) které vám pomohou najít a zkontrolovat tyto informace.

1. Na [webu Azure Portal](https://portal.azure.com)najděte a vyberte aplikaci logiky.

1. V nabídce aplikace logiky vyberte v části **Sledování**možnost **Nastavení** > diagnostiky**Přidat diagnostické nastavení**.

   ![V části "Monitorování" vyberte "Diagnostic settings" > "Add diagnostic setting"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Chcete-li nastavení vytvořit, postupujte takto:

   1. Zadejte název nastavení.

   1. Vyberte **odeslat do analýzy protokolů**.

   1. V **části Předplatné**vyberte předplatné Azure, které je přidružené k vašemu pracovnímu prostoru Analýzy protokolů.

   1. V **části Pracovní prostor Analýzy protokolů**vyberte pracovní prostor, který chcete použít.

   1. V **části log**vyberte kategorii **WorkflowRuntime,** která určuje kategorii událostí, kterou chcete zaznamenat.

   1. Chcete-li vybrat všechny metriky, vyberte v části **metric**, vyberte **AllMetrics**.

   1. Jakmile budete mít hotovo, vyberte **Uložit**.

   Například:

   ![Vyberte pracovní prostor a data služby Log Analytics pro protokolování.](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Zobrazení stavu spuštění aplikace logiky

Po spuštění aplikace logiky můžete zobrazit data o těchto spuštěních v pracovním prostoru Analýzy protokolů.

1. Na [webu Azure Portal](https://portal.azure.com)najděte a otevřete pracovní prostor Log Analytics.

1. V nabídce pracovního prostoru vyberte **souhrnná správa** > **logických aplikací**pracovního prostoru .

   ![Stav a počet spuštění aplikace logiky](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Pokud se na dlaždici Správa logických aplikací po spuštění okamžitě nezobrazí výsledky, zkuste vybrat **možnost Aktualizovat** nebo počkat krátkou dobu, než to zkusíte znovu.

   Zde jsou spuštěny aplikace logiky seskupeny podle názvu nebo stavu spuštění. Tato stránka také zobrazuje podrobnosti o selhání v akcích nebo aktivačních událostech pro spuštění aplikace logiky.

   ![Souhrn stavu aplikace logiky se spustí](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Chcete-li zobrazit všechny spuštění pro konkrétní aplikaci logiky nebo stav, vyberte řádek pro tuto aplikaci logiky nebo stav.

   Zde je příklad, který zobrazuje všechny spuštění pro konkrétní aplikaci logiky:

   ![Zobrazení spuštění a stavu aplikace logiky](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   U akcí, ve kterých [nastavujete sledované vlastnosti](#extend-data), můžete tyto vlastnosti zobrazit také výběrem možnosti **Zobrazení** ve sloupci **Sledované vlastnosti.** Chcete-li prohledat sledované vlastnosti, použijte sloupcový filtr.

   ![Zobrazení sledovaných vlastností aplikace logiky](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > Sledované vlastnosti nebo dokončené události mohou zaznamenat 10 až 15 minut zpoždění, než se zobrazí v pracovním prostoru Log Analytics.
   > Funkce **Opětovné odeslání** na této stránce je také momentálně nedostupná.

1. Chcete-li filtrovat výsledky, můžete provádět filtrování na straně klienta i na serveru.

   * **Filtr na straně klienta**: Pro každý sloupec vyberte požadované filtry, například:

     ![Ukázkové filtry sloupců](./media/monitor-logic-apps-log-analytics/filters.png)

   * Filtr na straně serveru : **Chcete-li**vybrat určité časové okno nebo omezit počet spuštění, které se zobrazí, použijte ovládací prvek oboru v horní části stránky. Ve výchozím nastavení se zobrazí pouze 1 000 záznamů najednou.

     ![Změna časového okna](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Chcete-li zobrazit všechny akce a jejich podrobnosti pro konkrétní spuštění, vyberte řádek pro spuštění aplikace logiky.

   Zde je příklad, který zobrazuje všechny akce a aktivační události pro konkrétní aplikaci logiky spustit:

   ![Zobrazit akce pro spuštění aplikace logiky](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Odeslání diagnostických dat do Azure Storage a Azure Event Hubs

Spolu s protokoly Azure Monitor, můžete rozšířit způsob použití diagnostických dat aplikace logiky s jinými službami Azure, například:

* [Archivace protokolů prostředků Azure do účtu úložiště](../azure-monitor/platform/resource-logs-collect-storage.md)
* [Streamování protokolů platformy Azure do Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Monitorování v reálném čase pak můžete získat pomocí telemetrie a analýz y z jiných služeb, jako je [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a [Power BI](../azure-monitor/platform/powerbi.md). Například:

* [Streamování dat z centra událostí do Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyzujte streamovaná data pomocí Stream Analytics a vytvořte řídicí panel analýz y v reálném čase v Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na základě umístění, kam chcete odesílat diagnostická data, se ujistěte, že jste nejprve [vytvořili účet úložiště Azure](../storage/common/storage-create-storage-account.md) nebo [vytvořili centrum událostí Azure](../event-hubs/event-hubs-create.md). Poté můžete vybrat cíle, kam chcete tato data odeslat. Retenční období platí pouze v případě, že používáte účet úložiště.

![Odeslání dat do účtu úložiště Azure nebo centra událostí](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Události diagnostiky Azure Monitoru

Každá diagnostická událost má podrobnosti o vaší aplikaci logiky a této události, například stav, čas zahájení, čas ukončení a tak dále. Chcete-li programově nastavit monitorování, sledování a protokolování, můžete tyto informace použít s [rozhraním REST API pro azure logic apps](https://docs.microsoft.com/rest/api/logic) a rozhraní REST API pro Azure [Monitor](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows). Můžete také použít `clientTrackingId` `trackedProperties` vlastnosti a, které se zobrazují 

* `clientTrackingId`: Pokud není k dispozici, Azure automaticky generuje toto ID a koreluje události v rámci spuštění aplikace logiky, včetně všech vnořených pracovních postupů, které jsou volány z aplikace logiky. Toto ID můžete zadat ručně v `x-ms-client-tracking-id` aktivační události předáním záhlaví s vlastní hodnotou ID v požadavku na aktivační událost. Můžete použít aktivační událost požadavku, aktivační událost HTTP nebo webhooku.

* `trackedProperties`: Chcete-li sledovat vstupy nebo výstupy v `trackedProperties` diagnostických datech, můžete přidat oddíl do akce buď pomocí Návrháře aplikace logiky nebo přímo v definici JSON aplikace logiky. Sledované vlastnosti můžete sledovat pouze vstupy a výstupy jedné akce, ale můžete použít `correlation` vlastnosti událostí ke korelaci mezi akcemi v běhu. Chcete-li sledovat více než jednu vlastnost, jednu `trackedProperties` nebo více vlastností, přidejte oddíl a vlastnosti, které chcete do definice akce.

  Zde je příklad, který ukazuje, jak definice **akce Inicializovat proměnné** zahrnuje sledované vlastnosti ze vstupu akce, kde je vstupem pole, nikoli záznam.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  Tento příklad ukazuje více sledovaných vlastností:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Tento příklad ukazuje, `ActionCompleted` jak `clientTrackingId` `trackedProperties` událost obsahuje atributy a:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Další kroky

* [Vytváření monitorovacích a sledovacích dotazů](../logic-apps/create-monitoring-tracking-queries.md)
* [Monitorování zpráv B2B s využitím protokolů Azure Monitoru](../logic-apps/monitor-b2b-messages-log-analytics.md)
