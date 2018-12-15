---
title: Kontrola stavu, nastavit protokolování a dostávat upozornění – Azure Logic Apps | Dokumentace Microsoftu
description: Monitorování stavu, můžete vytvářet protokoly dat diagnostiky a nastavení výstrah pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.date: 07/21/2017
ms.openlocfilehash: 3779260d2dd6036e65be39b4a59ceb1459d01b9e
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434252"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Monitorování stavu, nastavit protokolování diagnostiky a zapnutí výstrah pro Azure Logic Apps

Poté co [vytvoření a spuštění aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md), můžete zkontrolovat historii spouštění, historie aktivačních událostí, stavu a výkonu. Pro sledování událostí v reálném čase a rozsáhlejší ladění, nastavte [protokolování diagnostiky](#azure-diagnostics) pro vaši aplikaci logiky. Tímto způsobem můžete [vyhledejte a zobrazte události](#find-events), jako jsou aktivační události, spuštění události a akce události. Můžete také použít tuto [diagnostická data s jinými službami](#extend-diagnostic-data), jako je Azure Storage a Azure Event Hubs. 

Pokud chcete dostávat oznámení o selhání nebo jiné možné problémy, nastavte [výstrahy](#add-azure-alerts). Můžete například vytvořit výstrahu, která rozpozná "více než pět kterých došlo k selhání za hodinu." Můžete také nastavit monitorování, sledování a protokolování prostřednictvím kódu programu pomocí [nastavení Azure Diagnostics událostí a vlastností](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Zobrazit spuštění a historie triggeru aplikace logiky

1. K vyhledání aplikace logiky v [webu Azure portal](https://portal.azure.com), v hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte "aplikace logiky" a zvolte **Logic apps**.

   ![Vyhledání aplikace logiky](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Na webu Azure portal zobrazuje všechny aplikace logiky, které jsou spojené s předplatným Azure. 

2. Vyberte svou aplikaci logiky a pak zvolte **přehled**.

   Na webu Azure portal zobrazuje historii spouštění a historie triggeru aplikace logiky. Příklad:

   ![Spuštění aplikace logiky historie a historie spouštění](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Historie běhů** zobrazuje všechna spuštění pro vaši aplikaci logiky. 
   * **Historie spouštění** ukazuje všechny aktivity aktivační událost pro vaši aplikaci logiky.

   Popis stavu, najdete v části [řešení problémů aplikace logiky](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Pokud nenajdete data, která očekáváte, na panelu nástrojů zvolte **aktualizovat**.

3. Chcete-li zobrazit kroky z konkrétního spuštění, v části **historie běhů**, vyberte jej spusťte. 

   V přehledu monitorování se zobrazí každý krok v daném běhu. Příklad:

   ![Akce pro konkrétní spuštění](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Chcete-li získat další podrobnosti o daném spuštění, zvolte **detaily spuštění**. Tyto informace shrnuje kroky, stav, vstupy a výstupy pro spuštění. 

   ![Zvolte možnost "Spustit podrobnosti"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Například můžete získat spuštění **ID korelace**, což může být nutné při použití [rozhraní REST API pro Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Pokud chcete získat podrobnosti o konkrétním krokem, vyberte tento krok. Nyní můžete zkontrolovat podrobnosti jako vstupy, výstupy a všechny chyby, ke kterým došlo pro daný krok. Příklad:

   ![Podrobnosti kroku](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Všechny podrobnosti modulu CLR a události se šifrují ve službě Logic Apps. Že se dešifrují jenom v případě, že si uživatel vyžádá zobrazovat data. Můžete také kontrolovat přístup k těmto událostem s [Azure Role-Based řízení přístupu (RBAC)](../role-based-access-control/overview.md).

6. Chcete-li získat podrobné informace o konkrétní aktivační událost, přejděte zpět na **přehled** podokně. V části **historie spouštění**, vyberte aktivační událost. Můžete teď zkontrolovat podrobnosti jako vstupy a výstupy, například:

   ![Podrobnosti výstupu triggeru události](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Zapnout diagnostiku protokolování pro vaši aplikaci logiky

Pro širší ladění pomocí podrobnosti modulu CLR a události, můžete nastavit protokolování pomocí diagnostiky [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Log Analytics je služba v Azure, která monitoruje cloudové a místní prostředí můžete zachovat jejich dostupnost a výkon. 

Než začnete, musíte mít pracovní prostor Log Analytics. Přečtěte si [jak vytvořit pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. V [webu Azure portal](https://portal.azure.com)vyhledejte a vyberte svou aplikaci logiky. 

2. V nabídce okno aplikace logiky podle **monitorování**, zvolte **diagnostiky** > **nastavení diagnostiky**.

   ![Přejít na monitorování, Diagnostika, nastavení diagnostiky](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. V části **nastavení diagnostiky**, zvolte **na**.

   ![Zapnout diagnostické protokoly](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Teď vyberte Log Analytics pracovní prostor a událostí kategorie pro protokolování, jak je znázorněno:

   1. Vyberte **odesílat do Log Analytics**. 
   2. V části **Log Analytics**, zvolte **konfigurovat**. 
   3. V části **pracovních prostorů OMS**, vyberte pracovní prostor pro protokolování.
   > [!NOTE]
   > Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.
   4. V části **protokolu**, vyberte **WorkflowRuntime** kategorie.
   5. Zvolte metriky interval.
   6. Jakmile budete hotoví, vyberte **Uložit**.

   ![Vyberte pracovní prostor Log Analytics a data pro protokolování](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Nyní najít události a další data pro aktivační události, události a akce události.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Vyhledat události a data pro vaši aplikaci logiky

Najít a zobrazit události ve vaší aplikaci logiky, jako jsou aktivovat události, události, běhu a akce událostí, postupujte podle těchto kroků.

1. V [webu Azure portal](https://portal.azure.com), zvolte **všechny služby**. Vyhledejte "log analytics" a pak zvolte **Log Analytics** jak je znázorněno zde:

   ![Zvolte možnost "Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. V části **Log Analytics**vyhledejte a vyberte pracovní prostor Log Analytics. 

   ![Vyberte pracovní prostor Log Analytics](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. V části **správu**, zvolte **prohledávání protokolů**.

   ![Zvolte možnost "Prohledávání protokolů"](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. Do vyhledávacího pole zadejte pole, které chcete najít a stiskněte klávesu **Enter**. Když začnete psát, uvidíte možných shod a operace, které můžete použít. 

   Například pokud chcete najít prvních 10 události, ke kterým došlo, zadejte a vyberte tento vyhledávací dotaz: **hledat kategorie == "WorkflowRuntime" | limit 10**

   ![Zadejte hledaný řetězec](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Další informace o [jak najít data ve službě Log Analytics](../log-analytics/log-analytics-log-searches.md).

5. Na stránce výsledky na panelu vlevo vyberte časový rámec, který chcete zobrazit.
Chcete-li upřesněte svůj dotaz tak, že přidáte filtr, zvolte **+ přidat**.

   ![Vyberte časový rámec pro výsledky dotazu](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. V části **přidat filtry**, zadejte název filtru, abyste mohli vyhledat požadovaný filtr. Vyberte filtr a zvolte **+ přidat**.

   V tomto příkladu je slovo "stavu" najít neúspěšné události v rámci **AzureDiagnostics**.
   Tady filtr pro **status_s** je už vybraná.

   ![Vyberte filtr.](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. V levém panelu, vyberte hodnotu filtru, který chcete použít a zvolte **použít**.

   ![Vyberte hodnotu filtru, zvolte "Použít"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Nyní se vraťte na dotaz, který vytváříte. Vašemu dotazu je aktualizován vybraný filtr a hodnotu. Předchozí výsledky se teď filtrují příliš.

   ![Vraťte se do dotazu pomocí filtrované výsledky](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Chcete-li uložit dotaz pro budoucí použití, zvolte **Uložit**. Přečtěte si [uložení dotazu](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Rozšíření jak a kde použít diagnostických dat s jinými službami

Spolu s Azure Log Analytics můžete rozšířit použití diagnostických dat vaší aplikace logiky s ostatními službami Azure, například: 

* [Archiv, který protokoly diagnostiky Azure ve službě Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Stream protokoly diagnostiky Azure do služby Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Můžete získat v reálném čase sledování pomocí telemetrie a analýz z jiných služeb, jako jsou [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a [Power BI](../azure-monitor/platform/powerbi.md). Příklad:

* [Stream dat ze služby Event Hubs do služby Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyzujte data streamovaná službou Stream Analytics a vytvořit řídicí panel analýzy v reálném čase v Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Na základě možností, které chcete nastavit, ujistěte se, že jste první [vytvoření účtu služby Azure storage](../storage/common/storage-create-storage-account.md) nebo [vytvoření centra událostí Azure](../event-hubs/event-hubs-create.md). Vyberte požadované možnosti, pro které chcete posílat diagnostická data:

![Odesílání dat do služby Azure storage účet nebo event hub](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Období uchovávání platí jenom v případě, že budete chtít používat účet úložiště.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Nastavení výstrah pro aplikaci logiky

Pokud chcete sledovat určité metriky nebo došlo k překročení prahové hodnoty pro svou aplikaci logiky, nastavte [výstrah ve službě Azure](../azure-monitor/platform/alerts-overview.md). Další informace o [metrik v Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Postup nastavení výstrah bez [Azure Log Analytics](../log-analytics/log-analytics-overview.md), postupujte podle těchto kroků. Pro pokročilejší kritéria výstrahy a akce [nastavení Log Analytics](#azure-diagnostics) příliš.

1. V nabídce okno aplikace logiky podle **monitorování**, zvolte **diagnostiky** > **pravidla upozornění** > **přidat upozornění**jak je znázorněno zde:

   ![Přidat výstrahu pro vaši aplikaci logiky](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. Na **přidání pravidla výstrahy** okno, vytvořte upozornění, jak je uvedeno:

   1. V části **prostředků**, vyberte svou aplikaci logiky, pokud není zaškrtnuto. 
   2. Zadejte název a popis upozornění.
   3. Vyberte **metrika** nebo události, ke které chcete sledovat.
   4. Vyberte **podmínku**, zadejte **prahová hodnota** pro metriky a vyberte **období** pro monitorování tuto metriku.
   5. Vyberte, zda chcete odesílat e-mailu pro výstrahy. 
   6. Zadejte jiné e-mailové adresy pro odesílání oznámení. 
   Můžete také zadat adresu URL webhooku, ve které chcete odeslat výstrahu.

   Například toto pravidlo odešle výstrahu, když je pět nebo více spuštění selhání za hodinu:

   ![Vytvořit pravidlo upozornění metriky](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Ke spuštění aplikace logiky z výstrahy, můžete zahrnout [triggeru požadavku](../connectors/connectors-native-reqres.md) v pracovním postupu, který umožňuje provádět úkoly jako v těchto příkladech:
> 
> * [Příspěvek na Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Odeslala textová zpráva](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Přidání zprávy do fronty](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Nastavení Azure Diagnostics událostí a podrobnosti

Každý diagnostickou událost obsahuje podrobnosti o aplikaci logiky a události, například stav, čas spuštění, koncový čas a tak dále. Prostřednictvím kódu programu nastavit monitorování, sledování a protokolování, můžete tyto podrobnosti s [rozhraní REST API pro Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) a [rozhraní REST API pro Azure Diagnostics](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows).

Například `ActionCompleted` událost má `clientTrackingId` a `trackedProperties` vlastnosti, které můžete použít pro sledování a monitorování:

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

* `clientTrackingId`: Pokud není zadaný, Azure automaticky generuje toto ID a koreluje události napříč běh aplikace logiky, včetně všech vnořených pracovních postupech, které se volají z aplikace logiky. Pomocí aktivační události Toto ID můžete ručně zadat předáním `x-ms-client-tracking-id` záhlaví s vlastní hodnotou ID v triggeru požadavku. Můžete použít aktivační událost požadavek, HTTP trigger nebo trigger webhooku.

* `trackedProperties`: Ke sledování vstupů nebo výstupů v diagnostická data, můžete přidat sledované vlastnosti na akce v definici JSON vaší aplikace logiky. Sledované vlastnosti můžete sledovat pouze jednu akci vstupy a výstupy, ale můžete použít `correlation` vlastnosti události ke korelaci mezi akcí ve spuštění.

  Chcete-li sledovat jednu nebo více vlastností, přidejte `trackedProperties` oddílu a vlastnosti, které chcete definici akce. Předpokládejme například, že chcete sledovat daty, jako například "ID objednávky" v telemetrii:

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

## <a name="next-steps"></a>Další postup

* [Vytvoření šablony pro nasazení aplikace logiky a správa vydaných verzí](../logic-apps/logic-apps-create-deploy-template.md)
* [Scénáře B2B sadou Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Monitorování zpráv B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
