---
title: Kontrolovat stav, nastavit protokolování a získat výstrahy – Azure Logic Apps | Microsoft Docs
description: Monitorování stavu, protokolování dat diagnostiky a nastavení výstrah pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.date: 07/21/2017
ms.openlocfilehash: 840124a35ed4389699757e011aaf5d05a9400836
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385527"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Stav monitorování, nastavení protokolování diagnostiky a zapnutí výstrah pro Azure Logic Apps

Po [vytvoření a spuštění aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) můžete zkontrolovat její historii spuštění, historii triggerů, stav a výkon. Pro monitorování událostí v reálném čase a bohatší ladění nastavte [diagnostické protokolování](#azure-diagnostics) pro vaši aplikaci logiky. Díky tomu budete moct [vyhledat a zobrazit události](#find-events), jako jsou aktivační události, události spuštění nebo události akcí. Tato [diagnostická data můžete využít také s dalšími službami](#extend-diagnostic-data), jako jsou Azure Storage nebo Azure Event Hubs. 

Pokud chcete dostávat oznámení o selháních nebo jiných možných problémech, nastavte [výstrahy](#add-azure-alerts). Můžete například vytvořit výstrahu, která detekuje "Pokud více než pět běhů selže za hodinu." Monitorování, sledování a protokolování můžete také nastavit programově pomocí [Azure Diagnostics nastavení a vlastnosti události](#diagnostic-event-properties).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Zobrazení spuštění a historie triggerů aplikace logiky

1. Pokud chcete aplikaci logiky najít v [Azure Portal](https://portal.azure.com), v hlavní nabídce Azure vyberte **všechny služby**. Do vyhledávacího pole zadejte "Logic Apps" a vyberte **Logic Apps**.

   ![Vyhledání aplikace logiky](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Azure Portal zobrazí všechny aplikace logiky, které jsou přidruženy k vašemu předplatnému Azure. 

2. Vyberte svou aplikaci logiky a pak zvolte **Přehled**.

   Azure Portal zobrazuje historii spuštění a historii triggerů pro vaši aplikaci logiky. Příklad:

   ![Historie spuštění aplikace logiky a historie aktivačních událostí](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Historie spuštění** zobrazuje všechna spuštění aplikace logiky. 
   * V **historii aktivační události** se zobrazí všechny aktivity triggeru pro vaši aplikaci logiky.

   Popisy stavů najdete v tématu [řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Pokud nenajdete očekávaná data, na panelu nástrojů klikněte na tlačítko **aktualizovat**.

3. Chcete-li zobrazit kroky z konkrétního spuštění, vyberte v části **historie spuštění**možnost spustit. 

   Zobrazení monitorování ukazuje každý krok v tomto spuštění. Příklad:

   ![Akce pro konkrétní spuštění](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Chcete-li získat další podrobnosti o spuštění, klikněte na tlačítko **Spustit podrobnosti**. Tyto informace shrnují kroky, stav, vstupy a výstupy pro spuštění. 

   ![Výběr možnosti Spustit podrobnosti](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Můžete například získat **ID korelace**spuštění, které může být nutné při použití [REST API pro Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Pokud chcete získat podrobnosti o konkrétním kroku, vyberte tento krok. Teď si můžete prohlédnout podrobnosti, jako jsou vstupy, výstupy a případné chyby, ke kterým došlo v tomto kroku. Příklad:

   ![Podrobnosti o kroku](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Všechny podrobnosti a události modulu runtime jsou v rámci Logic Apps služby šifrované. Šifrují se pouze v případě, že uživatel požaduje zobrazení těchto dat. Přístup k těmto událostem můžete také řídit pomocí [Access Control založeného na rolích Azure (RBAC)](../role-based-access-control/overview.md).

6. Chcete-li získat podrobnosti o konkrétní události triggeru, vraťte se do podokna **Přehled** . V části **Historie aktivační**události vyberte aktivační událost. Teď si můžete prohlédnout podrobnosti, jako jsou vstupy a výstupy, například:

   ![Podrobnosti výstupu aktivační události](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Zapnutí protokolování diagnostiky pro vaši aplikaci logiky

Pro rozsáhlejší ladění s podrobnostmi a událostmi za běhu můžete nastavit protokolování diagnostiky s [protokoly Azure monitor](../log-analytics/log-analytics-overview.md). Azure Monitor je služba v Azure, která monitoruje cloudové a místní prostředí, které vám pomůžou zachovat jejich dostupnost a výkon. 

Než začnete, musíte mít Log Analytics pracovní prostor. Naučte [se, jak vytvořit pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte svou aplikaci logiky. 

2. V nabídce okna aplikace logiky v části **sledování** **Vyberte** > **nastavení diagnostické diagnostiky**.

   ![Přejít na monitorování, diagnostiku, nastavení diagnostiky](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. V části **nastavení diagnostiky**vyberte **zapnuto**.

   ![Zapnout diagnostické protokoly](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Nyní vyberte pracovní prostor Log Analytics a kategorii událostí pro protokolování, jak je znázorněno níže:

   1. Vyberte **odeslat Log Analytics**. 
   2. V části **Log Analytics**vyberte **Konfigurovat**. 
   3. V části **pracovní prostory OMS**vyberte pracovní prostor, který se má použít pro protokolování.
      > [!NOTE]
      > Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.
   4. V části **protokol**vyberte kategorii **WorkflowRuntime** .
   5. Vyberte interval metriky.
   6. Jakmile budete hotoví, vyberte **Uložit**.

   ![Vyberte Log Analytics pracovní prostor a data pro protokolování](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Nyní můžete najít události a další data pro události triggeru, události spuštění a akce.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Hledání událostí a dat pro aplikaci logiky

Pokud chcete v aplikaci logiky najít a zobrazit události, jako jsou triggery událostí, události spuštění a události akcí, postupujte podle těchto kroků.

1. V [Azure Portal](https://portal.azure.com)vyberte **všechny služby**. Vyhledejte "Log Analytics" a pak zvolte **Log Analytics** , jak je znázorněno zde:

   ![Vyberte "Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. V části **Log Analytics**Najděte pracovní prostor Log Analytics a vyberte ho. 

   ![Vyberte svůj pracovní prostor Log Analytics](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. V části **Správa**vyberte možnost **prohledávání protokolu**.

   ![Volba "prohledávání protokolu"](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. Do vyhledávacího pole zadejte pole, které chcete najít, a stiskněte klávesu **ENTER**. Když začnete psát, uvidíte možné shody a operace, které můžete použít. 

   Pokud například chcete najít prvních 10 událostí, ke kterým došlo, zadejte a vyberte tento vyhledávací dotaz: **kategorie vyhledávání = = "WorkflowRuntime" | omezení 10**

   ![Zadejte hledaný řetězec.](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Přečtěte si další informace o [tom, jak najít data v protokolech Azure monitor](../log-analytics/log-analytics-log-searches.md).

5. Na stránce výsledky na levém panelu vyberte časový rámec, který chcete zobrazit.
Pokud chcete dotaz upřesnit přidáním filtru, vyberte **+ Přidat**.

   ![Zvolit časový rámec pro výsledky dotazu](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. V části **Přidat filtry**zadejte název filtru, abyste mohli najít požadovaný filtr. Vyberte filtr a zvolte **+ Přidat**.

   Tento příklad používá slovo status (stav) k vyhledání neúspěšných událostí v **AzureDiagnostics**.
   Zde je již vybrán filtr pro **status_s** .

   ![Vyberte filtr.](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. Na levém panelu vyberte hodnotu filtru, kterou chcete použít, a zvolte **použít**.

   ![Vyberte hodnota filtru, zvolte použít.](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Nyní se vraťte k dotazu, který vytváříte. Dotaz se aktualizuje o vybraný filtr a hodnotu. Předchozí výsledky jsou teď filtrované.

   ![Vrátit se k dotazu pomocí filtrovaných výsledků](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Pokud chcete dotaz uložit pro budoucí použití, klikněte na **Uložit**. Naučte [se, jak dotaz uložit](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Jak a kde používat diagnostická data s ostatními službami

Společně s protokoly Azure Monitor můžete pomocí diagnostických dat aplikace logiky navzájem využít jiné služby Azure, například: 

* [Archivace Azure Diagnostics protokolů v Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Streamování protokolů Azure Diagnostics do Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Sledování v reálném čase můžete získat pomocí telemetrie a analýz z jiných služeb, jako je [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a [Power BI](../azure-monitor/platform/powerbi.md). Příklad:

* [Streamování dat z Event Hubs do Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyzujte streamovaná data pomocí Stream Analytics a vytvořte řídicí panel analýzy v reálném čase v Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na základě možností, které chcete nastavit, se ujistěte, že jste nejdřív [vytvořili účet úložiště Azure](../storage/common/storage-create-storage-account.md) , nebo [vytvoříte centrum událostí Azure](../event-hubs/event-hubs-create.md). Pak vyberte možnosti, pro které chcete odeslat diagnostická data:

![Odeslání dat do účtu služby Azure Storage nebo centra událostí](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Doba uchování platí jenom v případě, že se rozhodnete použít účet úložiště.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Nastavení upozornění pro aplikaci logiky

Pokud chcete monitorovat konkrétní metriky nebo přesáhli prahové hodnoty pro vaši aplikaci logiky, nastavte [výstrahy v Azure](../azure-monitor/platform/alerts-overview.md). Seznamte [se s metrikami v Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Pokud chcete nastavit výstrahy bez [Azure monitor protokolů](../log-analytics/log-analytics-overview.md), postupujte podle těchto kroků. K pokročilejším kritériím a akcím výstrah [nastavte Azure monitor protokoly](#azure-diagnostics) .

1. V nabídce okna aplikace logiky v části **monitorování**vyberte**pravidla** >  **diagnostické** > výstrahy**Přidat výstrahu** , jak je znázorněno zde:

   ![Přidání upozornění pro aplikaci logiky](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. V okně **Přidat pravidlo výstrahy** vytvořte výstrahu, jak je znázorněno níže:

   1. V části **prostředek**vyberte svou aplikaci logiky, pokud ještě není vybraná. 
   2. Zadejte název a popis výstrahy.
   3. Vyberte **metriku** nebo událost, kterou chcete sledovat.
   4. Vyberte **podmínku**, zadejte prahovou **hodnotu** pro metriku a vyberte **období** monitorování této metriky.
   5. Vyberte, jestli se má pro výstraha odeslat e-mail. 
   6. Zadejte všechny další e-mailové adresy pro odeslání výstrahy. 
   Můžete také zadat adresu URL Webhooku, kam chcete odeslat výstrahu.

   Toto pravidlo například pošle výstrahu, když pět nebo více spuštění selže za hodinu:

   ![Vytvořit pravidlo upozornění na metriku](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Pokud chcete z výstrahy spustit aplikaci logiky, můžete do svého pracovního postupu zahrnout [aktivační událost žádosti](../connectors/connectors-native-reqres.md) , která vám umožní provádět následující úlohy, jako jsou tyto příklady:
> 
> * [Odeslat do časové rezervy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Odeslat text](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Přidat zprávu do fronty](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Nastavení a podrobnosti Azure Diagnostics události

Každá událost diagnostiky obsahuje podrobnosti o vaší aplikaci logiky a o tom, že událost je například stav, čas spuštění, čas ukončení a tak dále. Chcete-li programově nastavit monitorování, sledování a protokolování, můžete použít tyto podrobnosti s [REST API Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) a [REST API pro Azure Diagnostics](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows).

`ActionCompleted` Událost `trackedProperties` má například vlastnosti a,kterémůžetepoužít`clientTrackingId` ke sledování a monitorování:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
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
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
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
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: Pokud není zadaný, Azure automaticky vygeneruje toto ID a koreluje události v rámci spuštění aplikace logiky, včetně všech vnořených pracovních postupů, které se volají z aplikace logiky. Toto ID můžete zadat ručně z triggeru tak, že v `x-ms-client-tracking-id` žádosti triggeru předáte hlavičku s hodnotou vlastního ID. Můžete použít Trigger žádosti, Trigger HTTP nebo Trigger Webhooku.

* `trackedProperties`: Chcete-li sledovat vstupy nebo výstupy v diagnostických datech, můžete přidat sledované vlastnosti do akcí v definici JSON vaší aplikace logiky. Sledované vlastnosti mohou sledovat pouze vstupy a výstupy jedné akce, ale můžete použít `correlation` vlastnosti událostí ke korelaci mezi akcemi v běhu.

  Chcete-li sledovat jednu nebo více vlastností, `trackedProperties` přidejte část a vlastnosti, které chcete do definice akce. Předpokládejme například, že chcete sledovat data jako "ID objednávky" v telemetrii:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```
  Tady je další příklad, který používá akci **inicializovat proměnnou** . Tento příklad přidá sledované vlastnosti z vstupu akce, kde input je pole, nikoli záznam.  

  ``` json
  "actions": { 
   "Initialize_variable": { 
      "inputs": { 
         "variables": [{ 
            "name": "ConnectorName", 
            "type": "String", 
            "value": "SFTP-SSH" 
         }]
      },
      "runAfter": {},
      "trackedProperties": { 
         "Track1": "@action().inputs.variables[0].value"
      },
      "type": "InitializeVariable"
   } 
  }
  ```

## <a name="next-steps"></a>Další postup

* [Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Scénáře B2B s Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Monitorování zpráv B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
