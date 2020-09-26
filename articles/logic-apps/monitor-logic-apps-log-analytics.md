---
title: Monitorování aplikací logiky pomocí protokolů Azure Monitor
description: Řešení potíží s Logic Apps nastavením protokolů Azure Monitor a shromažďování diagnostických dat pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 09/24/2020
ms.openlocfilehash: 5df596560e97ea9dba38dca4d4ca58e38caabd37
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333949"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Nastavení protokolů Azure Monitor a shromažďování diagnostických dat pro Azure Logic Apps

Pokud chcete získat bohatší ladicí informace o aplikacích logiky během běhu, můžete nastavit a použít [protokoly Azure monitor](../azure-monitor/platform/data-platform-logs.md) k zaznamenávání a ukládání informací o běhových datech a událostech, jako jsou události triggeru, události spuštění a události akcí v [pracovním prostoru Log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace). [Azure monitor](../azure-monitor/overview.md) vám pomůže monitorovat cloudová a místní prostředí, abyste mohli snadněji udržovat jejich dostupnost a výkon. Pomocí protokolů Azure Monitor můžete vytvářet [dotazy protokolů](../azure-monitor/log-query/log-query-overview.md) , které vám pomůžou shromáždit a zkontrolovat tyto informace. [Tato diagnostická data můžete také použít s jinými službami Azure](#extend-data), například Azure Storage a Azure Event Hubs.

Pokud chcete nastavit protokolování pro aplikaci logiky, můžete [povolit Log Analytics při vytváření aplikace logiky](#logging-for-new-logic-apps)nebo můžete [řešení pro správu Logic Apps nainstalovat](#install-management-solution) do svého pracovního prostoru Log Analytics pro existující aplikace logiky. Toto řešení poskytuje agregované informace pro vaše spuštění aplikace logiky a zahrnuje konkrétní podrobnosti, jako je stav, doba provádění, stav opětovného odeslání a ID korelace. Pokud pak chcete povolit protokolování a vytváření dotazů pro tyto informace, [nastavte protokoly Azure monitor](#set-up-resource-logs).

Tento článek ukazuje, jak povolit Log Analytics pro nové Logic Apps a stávající aplikace logiky, jak nainstalovat a nastavit řešení pro správu Logic Apps a jak nastavit a vytvářet dotazy na protokoly Azure Monitor.

## <a name="prerequisites"></a>Požadavky

Než začnete, potřebujete [Log Analytics pracovní prostor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace). Pokud nemáte pracovní prostor, přečtěte si, [jak vytvořit pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Povolit Log Analytics pro nové Logic Apps

Log Analytics můžete zapnout, když vytvoříte aplikaci logiky.

1. V [Azure Portal](https://portal.azure.com)v podokně, kde poskytnete informace k vytvoření vaší aplikace logiky, postupujte takto:

   1. V části **Log Analytics**vyberte **zapnuto**.

   1. V seznamu **pracovní prostor Log Analytics** vyberte pracovní prostor, ve kterém chcete poslat data z vaší aplikace logiky.

      ![Zadání informací o aplikaci logiky](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Po dokončení tohoto kroku Azure vytvoří aplikaci logiky, která je teď přidružená k vašemu pracovnímu prostoru Log Analytics. Tento krok také automaticky nainstaluje řešení pro správu Logic Apps do svého pracovního prostoru.

1. Po dokončení vyberte **Vytvořit**.

1. Po spuštění aplikace logiky pro zobrazení spuštění aplikace logiky [pokračujte následujícími kroky](#view-logic-app-runs).

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Instalace řešení pro správu Logic Apps

Pokud jste při vytváření aplikace logiky zapnuli Log Analytics, přeskočte tento krok. V pracovním prostoru Log Analytics už máte nainstalované řešení pro správu Logic Apps.

1. Do vyhledávacího pole [Azure Portal](https://portal.azure.com)zadejte `log analytics workspaces` a potom vyberte **Log Analytics pracovní prostory**.

   ![Vyberte Log Analytics pracovní prostory.](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. V části **Log Analytics pracovní prostory**vyberte svůj pracovní prostor.

   ![Vyberte svůj pracovní prostor Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. V podokně **Přehled** v části Začínáme **s Log Analytics**  >  **Konfigurace řešení monitorování**vyberte **Zobrazit řešení**.

   ![V podokně s přehledem vyberte možnost Zobrazit řešení.](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. V části **Přehled**vyberte **Přidat**.

   ![V podokně s přehledem přidejte nové řešení.](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Po otevření **Marketplace** do vyhledávacího pole zadejte `logic apps management` a vyberte **Logic Apps Management**.

   ![Z Marketplace vyberte Logic Apps Management.](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. V podokně s popisem řešení vyberte **vytvořit**.

   ![Pokud chcete přidat řešení Logic Apps Management, vyberte vytvořit.](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Zkontrolujte a potvrďte Log Analytics pracovní prostor, do kterého chcete řešení nainstalovat, a pak vyberte **vytvořit** znovu.

   ![Vyberte "vytvořit" pro "Logic Apps Management"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Jakmile Azure nasadí řešení do skupiny prostředků Azure, která obsahuje váš pracovní prostor Log Analytics, toto řešení se zobrazí v podokně Souhrn v pracovním prostoru.

   ![Podokno souhrnu pracovního prostoru](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Nastavení protokolů Azure Monitor

Když ukládáte informace o událostech a datech modulu runtime v [protokolech Azure monitor](../azure-monitor/platform/data-platform-logs.md), můžete vytvořit [dotazy protokolu](../azure-monitor/log-query/log-query-overview.md) , které vám pomůžou najít a zkontrolovat tyto informace.

> [!NOTE]
> Po povolení nastavení diagnostiky můžou data diagnostiky po dobu až 30 minut přecházet do protokolů v zadaném cíli, jako je Log Analytics, centrum událostí nebo účet úložiště. Tato prodleva znamená, že diagnostická data z tohoto časového období nemusí pro kontrolu existovat. Dokončené události a [sledované vlastnosti](#extend-data) se nemusí zobrazit v pracovním prostoru Log Analytics po dobu 10-15 minut.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte svou aplikaci logiky.

1. V nabídce aplikace logiky v části **monitorování**vyberte **nastavení diagnostiky**  >  **Přidat nastavení diagnostiky**.

   ![V části monitorování vyberte nastavení diagnostiky > přidat nastavení diagnostiky.](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Nastavení vytvoříte pomocí následujících kroků:

   1. Zadejte název nastavení.

   1. Vyberte **odeslat Log Analytics**.

   1. V poli **předplatné**vyberte předplatné Azure, které je přidružené k vašemu pracovnímu prostoru Log Analytics.

   1. V části **pracovní prostor Log Analytics**vyberte pracovní prostor, který chcete použít.

   1. V části **protokol**vyberte kategorii **aktivity typu WorkflowRuntime** , která určuje kategorii událostí, kterou chcete zaznamenat.

   1. Pokud chcete vybrat všechny metriky, vyberte v části **metrika**možnost **AllMetrics**.

   1. Jakmile budete mít hotovo, vyberte **Uložit**.

   Příklad:

   ![Vyberte Log Analytics pracovní prostor a data pro protokolování](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Zobrazit stav spuštění aplikace logiky

Po spuštění aplikace logiky můžete zobrazit data o těchto spuštěních v pracovním prostoru Log Analytics.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete Log Analytics pracovní prostor.

1. V nabídce pracovního prostoru vyberte možnost **Přehled pracovního prostoru**  >  **Logic Apps Správa**.

   ![Stav a počet spuštění aplikace logiky](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Pokud dlaždice pro správu Logic Apps po spuštění okamžitě nezobrazuje výsledky, zkuste vybrat možnost **aktualizovat** nebo počkat na krátkou dobu, než to zkusí znovu.

   Tady se vaše běhy aplikace logiky seskupují podle názvu nebo podle stavu spuštění. Tato stránka obsahuje také podrobnosti o selhání v akcích nebo aktivačních událostech pro spuštění aplikace logiky.

   ![Shrnutí stavu spuštění aplikace logiky](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Pokud chcete zobrazit všechna spuštění konkrétní aplikace logiky nebo stav, vyberte řádek pro tuto aplikaci logiky nebo stav.

   Tady je příklad, který ukazuje všechna spuštění pro konkrétní aplikaci logiky:

   ![Zobrazení běhu a stavu aplikace logiky](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   > [!NOTE]
   > Možnost opětovného **odeslání** na této stránce není momentálně k dispozici.

   V případě akcí, ve kterých jste [nastavili sledované vlastnosti](#extend-data), můžete tyto vlastnosti zobrazit také tak, že ve sloupci **sledované vlastnosti** vyberete **zobrazení** . Chcete-li vyhledat sledované vlastnosti, použijte filtr sloupce.

   ![Zobrazení sledovaných vlastností aplikace logiky](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

1. Pokud chcete filtrovat výsledky, můžete provést filtrování na straně klienta i na straně serveru.

   * **Filtr na straně klienta**: u každého sloupce vyberte filtry, které chcete, například:

     ![Příklady filtrů sloupců](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Filtr na straně serveru**: Pokud chcete vybrat konkrétní časový interval nebo omezit počet zobrazených běhů, použijte ovládací prvek rozsah v horní části stránky. Ve výchozím nastavení se zobrazují jenom 1 000 záznamů.

     ![Změna časového intervalu](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Pokud chcete zobrazit všechny akce a jejich podrobnosti pro konkrétní běh, vyberte řádek pro spuštění aplikace logiky.

   Tady je příklad, který zobrazuje všechny akce a triggery pro konkrétní spuštění aplikace logiky:

   ![Zobrazení akcí pro spuštění aplikace logiky](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Odeslání diagnostických dat do Azure Storage a Azure Event Hubs

Společně s protokoly Azure Monitor můžete pomocí diagnostických dat aplikace logiky navzájem využít jiné služby Azure, například:

* [Archivace protokolů prostředků Azure do účtu úložiště](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)
* [Streamování protokolů platformy Azure do Azure Event Hubs](../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs)

Sledování v reálném čase můžete získat pomocí telemetrie a analýz z jiných služeb, jako je [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a [Power BI](../azure-monitor/platform/powerbi.md). Příklad:

* [Streamování dat z Event Hubs do Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyzujte streamovaná data pomocí Stream Analytics a vytvořte řídicí panel analýzy v reálném čase v Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na základě umístění, kam chcete poslat diagnostická data, se ujistěte, že jste nejdřív [vytvořili účet Azure Storage](../storage/common/storage-account-create.md) , nebo [vytvoříte centrum událostí Azure](../event-hubs/event-hubs-create.md). Pak můžete vybrat cílové umístění, kam chcete tato data odeslat. Doba uchování platí jenom při použití účtu úložiště.

![Odeslání dat do účtu služby Azure Storage nebo centra událostí](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Události diagnostiky Azure Monitor

Každá událost diagnostiky obsahuje podrobnosti o vaší aplikaci logiky a o tom, že událost je například stav, čas spuštění, čas ukončení a tak dále. Chcete-li programově nastavit monitorování, sledování a protokolování, můžete tyto informace použít spolu s [REST API pro Azure Logic Apps](/rest/api/logic) a [REST API pro Azure monitor](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows). Můžete také použít `clientTrackingId` `trackedProperties` vlastnosti a, které se zobrazí v 

* `clientTrackingId`: Pokud není zadaný, Azure automaticky vygeneruje toto ID a koreluje události v rámci spuštění aplikace logiky, včetně všech vnořených pracovních postupů, které se volají z aplikace logiky. Toto ID můžete zadat ručně v triggeru tak, že `x-ms-client-tracking-id` v žádosti triggeru předáte hlavičku s hodnotou vlastního ID. Můžete použít Trigger žádosti, Trigger HTTP nebo Trigger Webhooku.

* `trackedProperties`: Chcete-li sledovat vstupy nebo výstupy v diagnostických datech, můžete přidat `trackedProperties` oddíl do akce buď pomocí návrháře aplikace logiky, nebo přímo v definici JSON vaší aplikace logiky. Sledované vlastnosti mohou sledovat pouze vstupy a výstupy jedné akce, ale můžete použít `correlation` vlastnosti událostí ke korelaci mezi akcemi v běhu. Chcete-li sledovat více než jednu vlastnost, jednu nebo více vlastností, přidejte `trackedProperties` část a vlastnosti, které chcete do definice akce.

  Tady je příklad, který ukazuje, jak definice akce **inicializovat proměnnou** obsahuje sledované vlastnosti z vstupu akce, kde input je pole, nikoli záznam.

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

  V tomto příkladu se zobrazí několik sledovaných vlastností:

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

Tento příklad ukazuje, jak `ActionCompleted` událost zahrnuje `clientTrackingId` atributy a `trackedProperties` :

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
