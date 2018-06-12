---
title: Zkontrolujte stav, nastavení protokolování a získat upozornění – Azure Logic Apps | Microsoft Docs
description: Monitorovat stav a výkon pro aplikace logiky, protokolu diagnostická data a nastavení výstrah
author: jeffhollan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 061269050ad598e1877c3b7bc6745d4095816020
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301214"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Monitorování stavu, nastavit protokolování diagnostiky a zapnutí výstrah pro Azure Logic Apps

Po jste [vytvořte a spusťte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md), můžete zkontrolovat historii spustí, historie aktivační událost, stavu a výkonu. Nastavení pro sledování událostí v reálném čase a bohatší ladění, [protokolování diagnostiky](#azure-diagnostics) pro svou aplikaci logiky. Tímto způsobem můžete [najít a zobrazit události](#find-events), jako jsou aktivační události, spuštění události a události akce. Můžete také použít tuto [diagnostická data s jinými službami](#extend-diagnostic-data), jako jsou služby Azure Storage a Azure Event Hubs. 

Pokud chcete dostávat oznámení o selhání nebo jiné možné problémy, nastavte [výstrahy](#add-azure-alerts). Například můžete vytvořit výstrahu, která zjistí "při více než pět spustí nezdaří za hodinu." Můžete také nastavit monitorování, sledování a protokolování programově pomocí [Azure Diagnostics událostí nastavení a vlastností](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Spustí zobrazení a historii aktivační událost pro svou aplikaci logiky

1. K vyhledání aplikace logiky v [portál Azure](https://portal.azure.com), v hlavní nabídce Azure, zvolte **všechny služby**. Do vyhledávacího pole zadejte "aplikace logiky" a zvolte **Logic apps**.

   ![Najít aplikaci logiky](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Portálu Azure obsahuje všechny aplikace logiky, které jsou spojeny s předplatným Azure. 

2. Vyberte svou aplikaci logiky, a potom vyberte **přehled**.

   Portál Azure se zobrazuje historie spustí a historii aktivační událost pro svou aplikaci logiky. Příklad:

   ![Spuštění aplikace logiky historie historie a aktivační události](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Spustí historie** ukazuje všechny spuštění pro svou aplikaci logiky. 
   * **Aktivovat historie** zobrazuje všechny aktivity aktivační událost pro svou aplikaci logiky.

   Popis stavu najdete v tématu [řešení aplikace logiky](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Pokud nenajdete data, která očekáváte, na panelu nástrojů vyberte **aktualizovat**.

3. Chcete-li zobrazit kroky z konkrétní spuštění, v části **spouští historie**, vyberte, které používají. 

   Zobrazení monitorování uvádí každý krok v tom, že spustit. Příklad:

   ![Akce pro konkrétní spustit](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Chcete-li získat další informace o spuštění, zvolte **podrobnosti o spuštění**. Tyto informace shrnuje kroky, stav, vstupy a výstupy pro spuštění. 

   ![Zvolte "Spustit podrobnosti"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Například můžete získat spuštění **ID korelace**, což může být nutné při použití [REST API pro Logic Apps](https://docs.microsoft.com/rest/api/logic).

5. Chcete-li získat podrobnosti o konkrétní krok, zvolte tohoto kroku. Nyní můžete zkontrolovat podrobnosti, třeba vstupy, výstupy a všechny chyby, které pro tento krok se stalo. Příklad:

   ![Podrobnosti o krok](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Všechny podrobnosti o modulu runtime a události se šifrují v rámci služby Logic Apps. Budou se dešifrují jenom v případě, že uživatel požádá o zobrazovat data. Také můžete řídit přístup k těmto událostem s [řízení řízení přístupu (RBAC)](../role-based-access-control/overview.md).

6. Chcete-li získat podrobnosti o konkrétní aktivační událost, přejděte zpět na **přehled** podokně. V části **aktivovat historie**, vyberte aktivační událost. Můžete teď zkontrolujte podrobnosti jako vstupy a výstupy, například:

   ![Podrobnosti o aktivační události výstup](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Zapněte diagnostiku protokolování pro svou aplikaci logiky

Pro širší ladění s podrobnostmi runtime a události, můžete nastavit protokolování s diagnostiky [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Analýzy protokolů je služba v Azure, který monitoruje své cloudové a místní prostředí pro správu jejich dostupnost a výkon. 

Než začnete, musíte mít pracovní prostor analýzy protokolů. Další informace [jak vytvořit pracovní prostor analýzy protokolů](../log-analytics/log-analytics-quick-create-workspace.md).

1. V [portál Azure](https://portal.azure.com), najděte a vyberte svou aplikaci logiky. 

2. V nabídce okno aplikace logiky v rámci **monitorování**, zvolte **diagnostiky** > **nastavení pro diagnostiku**.

   ![Přejděte na monitorování, Diagnostika, nastavení pro diagnostiku](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. V části **nastavení diagnostiky**, zvolte **na**.

   ![Zapnout diagnostické protokoly](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Nyní vyberte kategorii pracovní prostor a událostí analýzy protokolů pro protokolování, jak je znázorněno:

   1. Vyberte **odeslat k analýze protokolů**. 
   2. V části **analýzy protokolů**, zvolte **konfigurace**. 
   3. V části **pracovních prostorů OMS**, vyberte pracovní prostor analýzy protokolů pro protokolování.
   4. V části **protokolu**, vyberte **modul runtime pracovního postupu** kategorie.
   5. Zvolte metriky interval.
   6. Jakmile budete hotoví, vyberte **Uložit**.

   ![Vyberte pracovní prostor analýzy protokolů a dat pro protokolování](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Teď můžete najít události a dalších dat pro aktivační události, spustit události a události akce.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Najít události a data pro svou aplikaci logiky

Vyhledat a zobrazit události ve vaší aplikaci logiky, jako například aktivovat události, spustit události a akci události, postupujte podle těchto kroků.

1. V [portál Azure](https://portal.azure.com), zvolte **všechny služby**. Vyhledejte "analýzy protokolů" a pak zvolte **analýzy protokolů** jak je vidět tady:

   ![Zvolte "Analýzy protokolů"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. V části **analýzy protokolů**, najděte a vyberte pracovní prostor analýzy protokolů. 

   ![Vyberte pracovní prostor analýzy protokolů](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. V části **správy**, zvolte **portálu OMS**.

   ![Zvolte "Portál OMS"](media/logic-apps-monitor-your-logic-apps/omsportalpage.png)

4. Na domovské stránce, zvolte **hledání protokolů**.

   ![Na domovské stránce zvolte "Hledání protokolů"](media/logic-apps-monitor-your-logic-apps/logsearch.png)

   -nebo-

   ![V nabídce zvolte "Hledání protokolů"](media/logic-apps-monitor-your-logic-apps/logsearch-2.png)

5. Do vyhledávacího pole zadejte pole, které chcete najít a stiskněte klávesu **Enter**. Když začnete psát, zobrazí možné shody a operací, které můžete použít. 

   Například pokud chcete najít prvních 10 události, které se stalo, zadejte a vyberte tento vyhledávací dotaz: **hledání kategorie == "Runtime pracovního postupu" | omezit 10**

   ![Zadejte hledaný řetězec](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Další informace o [vyhledávání dat v analýzy protokolů](../log-analytics/log-analytics-log-searches.md).

6. Na stránce výsledky v levém panelu, vyberte časový rámec, který chcete zobrazit.
Chcete-li upřesněte dotaz přidáním filtru, zvolte **+ přidat**.

   ![Vyberte časový rámec pro výsledky dotazu](media/logic-apps-monitor-your-logic-apps/query-results.png)

7. V části **přidat filtry**, zadejte název filtru filtr, který chcete vyhledávat. Vyberte filtr a vyberte **+ přidat**.

   Tento příklad používá slovo "status" k vyhledání chybných události **AzureDiagnostics**.
   Zde filtr pro **status_s** je již vybrána.

   ![Vyberte filtr](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

8. V levém panelu, vyberte hodnota filtru, který chcete použít a vyberte **použít**.

   ![Vyberte hodnotu filtru, zvolte "Použít"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

9. Nyní se vraťte na dotaz, který vytváříte. Dotaz se aktualizuje se vybraný filtr a hodnotou. Předchozí výsledky jsou nyní příliš filtrovány.

   ![Vrátí do dotazu s filtrované výsledky.](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

10. Pokud chcete uložit dotazu pro budoucí použití, zvolte **Uložit**. Další informace [jak uložit dotazu](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Rozšíření jak a kde používáte diagnostických dat s jinými službami

Společně s Azure Log Analytics můžete rozšířit použití aplikace logiky diagnostických dat s jinými službami Azure, například: 

* [Archiv, který Azure Diagnostics protokolů v úložišti Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Datový proud protokolů Azure Diagnostics do služby Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Můžete pak monitorování pomocí telemetrie a analýzy z jiných služeb, jako například get v reálném čase [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) a [Power BI](../log-analytics/log-analytics-powerbi.md). Příklad:

* [Datový proud dat ze služby Event Hubs do služby Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analýza dat pomocí služby Stream Analytics a vytvořit řídicí panel analýzu v reálném čase v Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

V závislosti na možnosti, které chcete nastavit, ujistěte se, že jste první [vytvořit účet úložiště Azure](../storage/common/storage-create-storage-account.md) nebo [vytváření centra událostí Azure](../event-hubs/event-hubs-create.md). Vyberte požadované možnosti pro které chcete poslat diagnostická data:

![Odesílání dat do úložiště Azure účet nebo události rozbočovače](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Období uchovávání se projeví pouze v případě, že ji rozhodnete použít účet úložiště.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Nastavit výstrahy pro svou aplikaci logiky

Chcete-li monitorovat konkrétní metriky nebo překročil prahové hodnoty pro svou aplikaci logiky, nastavte [výstrahy v Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). Další informace o [metriky v Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Nastavit výstrahy bez [Azure Log Analytics](../log-analytics/log-analytics-overview.md), postupujte podle těchto kroků. Pro pokročilejší kritéria výstrahy a akce [nastavení analýzy protokolů](#azure-diagnostics) příliš.

1. V nabídce okno aplikace logiky v rámci **monitorování**, zvolte **diagnostiky** > **výstrah pravidla** > **přidat upozornění**jak je vidět tady:

   ![Přidání oznámení pro svou aplikaci logiky](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. Na **přidání pravidla výstrahy** okně vytvořit upozornění, jak je znázorněno:

   1. V části **prostředků**, vyberte svou aplikaci logiky, pokud není vybrána. 
   2. Zadejte název a popis pro upozornění.
   3. Vyberte **metrika** nebo událost, která chcete sledovat.
   4. Vyberte **podmínku**, zadejte **prahová hodnota** pro metriku, vyberte **období** pro monitorování tato metrika.
   5. Vyberte, jestli se mají odesílat e-mailu pro výstrahy. 
   6. Zadejte jiné e-mailové adresy pro odesílání upozornění. 
   Můžete také zadat adresu URL webhooku, kde chcete odeslat výstrahu.

   Například toto pravidlo, odešle výstrahu, pokud pět nebo další spustí selhání za hodinu:

   ![Vytvoření metriky pravidlo výstrahy](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Pokud chcete spustit aplikaci logiky z výstrahy, můžete zahrnout [aktivační událost požadavku](../connectors/connectors-native-reqres.md) v pracovním postupu, která vám umožňuje provádět úlohy, jako tyto příklady:
> 
> * [POST k Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Odeslat jako text](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Přidání zprávy do fronty](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Nastavení Azure Diagnostics událostí a podrobnosti

Každý diagnostických událostí obsahuje podrobnosti o aplikaci logiky a tuto událost, například stav, čas spuštění, koncový čas a tak dále. Chcete-li nastavit prostřednictvím kódu programu, monitorování, sledování a protokolování, můžete použít tyto podrobnosti s [REST API pro Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) a [REST API pro Azure Diagnostics](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

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

* `clientTrackingId`: Pokud není zadáno, Azure automaticky vygeneruje ID a korelaci událostí napříč spustit aplikace logiky, včetně všech vnořených pracovních postupů, se nazývají z aplikace logiky. Můžete ručně zadat toto ID z aktivační událost pomocí předání `x-ms-client-tracking-id` hlavička s vlastní hodnota ID v žádosti o aktivační události. Můžete použít aktivační událost požadavku, triggeru protokolu HTTP nebo aktivační události webhooku.

* `trackedProperties`: Chcete-li sledovat vstupů nebo výstupů v diagnostická data, můžete přidat sledovaných vlastnosti na akce v definici aplikace logiky JSON. Sledované vlastnosti můžete sledovat jenom jednu akci vstupy a výstupy, ale můžete použít `correlation` vlastnosti události ke korelaci napříč akce v spustit.

  Chcete-li sledovat jednu nebo více vlastností, přidejte `trackedProperties` části a vlastnosti, které chcete definici akce. Předpokládejme například, že chcete sledovat data jako "ID pořadí" v telemetrie:

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

* [Vytvoření šablony pro nasazení aplikace logiky a správa verzí](../logic-apps/logic-apps-create-deploy-template.md)
* [Scénáře B2B s Enterprise integračního balíčku](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Monitorování zpráv B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
