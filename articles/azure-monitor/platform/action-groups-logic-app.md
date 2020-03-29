---
title: Jak spustit složité akce pomocí výstrah Azure Monitor
description: Zjistěte, jak vytvořit akci aplikace logiky pro zpracování výstrah Azure Monitor.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 07/18/2018
ms.subservice: alerts
ms.openlocfilehash: 655a3acc44a1418778b37fbef85e5df75d042317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206232"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Jak spustit složité akce pomocí výstrah Azure Monitor

Tento článek ukazuje, jak nastavit a aktivovat aplikaci logiky k vytvoření konverzace v Microsoft Teams, když se aktivuje výstraha.

## <a name="overview"></a>Přehled

Když se aktivuje výstraha Azure Monitor, zavolá [skupinu akcí](../../azure-monitor/platform/action-groups.md). Skupiny akcí umožňují spustit jednu nebo více akcí, které upozorní ostatní na výstrahu a také ji najdou nápravou.

Obecný proces je:

-   Vytvořte aplikaci logiky pro příslušný typ výstrahy.

-   Importukázkové datové části pro příslušný typ výstrahy do aplikace logiky.

-   Definujte chování aplikace logiky.

-   Zkopírujte koncový bod HTTP aplikace logiky do skupiny akcí Azure.

Proces je podobný, pokud chcete, aby aplikace logiky provést jinou akci.

## <a name="create-an-activity-log-alert-administrative"></a>Vytvoření výstrahy protokolu aktivit: Správce

1.  Na webu Azure Portal vyberte **Vytvořit prostředek** v levém horním rohu.

2.  Vyhledejte a vyberte **aplikaci Logika**a vyberte **možnost Vytvořit**.

3.  Dejte aplikaci logiky **název**, zvolte **skupinu prostředků**a tak dále.

    ![Vytvoření aplikace logiky](media/action-groups-logic-app/create-logic-app-dialog.png "Vytvoření aplikace logiky")

4.  Chcete-li vytvořit aplikaci logiky, vyberte **vytvořit.** Automaticky otevíraná zpráva označuje, že aplikace logiky je vytvořen. Vyberte **Spustit prostředek** a otevřete **Návrhář etomových aplikací**.

5.  Vyberte aktivační událost: **Při přijetí požadavku HTTP**.

    ![Triggery aplikace logiky](media/action-groups-logic-app/logic-app-triggers.png "Triggery aplikace logiky")

6.  Výběrem **možnosti Upravit** změníte aktivační událost požadavku HTTP.

    ![Aktivační události požadavku HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Aktivační události požadavku HTTP")

7.  Vyberte **K vygenerování schématu použijte ukázkovou datovou část**.

    ![Použití ukázkové datové části](media/action-groups-logic-app/use-sample-payload-button.png "Použití ukázkové datové části")

8.  Zkopírujte a vložte do dialogového okna následující ukázkovou datovou část:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. **Návrhář aplikace logiky** zobrazí vyskakovací okno, které vám připomene, že požadavek odeslaný do aplikace logiky musí nastavit **hlavičku Content-Type** na **aplikaci/json**. Zavřete vyskakovací okno. Výstraha Azure Monitor nastaví záhlaví.

    ![Nastavení záhlaví Typu obsahu](media/action-groups-logic-app/content-type-header.png "Nastavení záhlaví Typu obsahu")

10. Vyberte **+** **Nový krok** a pak zvolte Přidat **akci**.

    ![Přidání akce](media/action-groups-logic-app/add-action.png "Přidání akce")

11. Vyhledejte a vyberte konektor Microsoft Teams. Zvolte akci **Microsoft Teams – Post message** .

    ![Akce Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "Akce Microsoft Teams")

12. Konfigurace akce Microsoft Teams. **Návrhář aplikací logiky** vás požádá o ověření vašeho účtu Office 365. Zvolte **ID týmu** a **ID kanálu,** na které chcete zprávu odeslat.

13. Nakonfigurujte zprávu pomocí kombinace statického \<textu\> a odkazů na pole v dynamickém obsahu. Zkopírujte a vložte do pole **Zpráva** následující text:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Poté vyhledejte a \<\> nahraďte pole značkami dynamického obsahu se stejným názvem.

    > [!NOTE]
    > Existují dvě dynamická pole s názvem **stav**. Přidejte obě tato pole do zprávy. Použijte pole, které je v tašce **vlastností activityLog,** a odstraňte druhé pole. Najeďte kurzorem na **stavové** pole, abyste viděli plně kvalifikovaný odkaz na pole, jak je znázorněno na následujícím snímku obrazovky:

    ![Akce Microsoft Teams: Odeslání zprávy](media/action-groups-logic-app/teams-action-post-message.png "Akce Microsoft Teams: Odeslání zprávy")

14. V horní části **Návrháře logických aplikací**vyberte **Uložit,** chcete-li uložit aplikaci logiky.

15. Otevřete existující skupinu akcí a přidejte akci, která bude odkazovat na aplikaci logiky. Pokud nemáte existující skupinu akcí, najdete [v tématu Vytvoření a správa skupin akcí na webu Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) a vytvořte je. Nezapomeňte uložit změny.

    ![Aktualizace skupiny akcí](media/action-groups-logic-app/update-action-group.png "Aktualizace skupiny akcí")

Při příštím volání výstrahy skupiny akcí se volá aplikace logiky.

## <a name="create-a-service-health-alert"></a>Vytvoření výstrahy stavu služby

Položky stavu služby Azure jsou součástí protokolu aktivit. Proces vytvoření výstrahy je podobný [vytvoření výstrahy protokolu aktivit](#create-an-activity-log-alert-administrative), ale s několika změnami:

- Kroky 1 až 7 jsou stejné.
- Pro krok 8 použijte následující ukázkovou datovou část pro aktivační událost požadavku HTTP:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Kroky 9 a 10 jsou stejné.
-  Pro kroky 11 až 14 použijte následující postup:

   1. Vyberte **+** **Nový krok** a pak zvolte Přidat **podmínku**. Nastavte následující podmínky, aby se aplikace logiky spustila pouze v případě, že vstupní data odpovídají níže uvedeným hodnotám.  Při zadávání hodnoty verze do textového pole kolem něj vložte uvozovky ("0.1.1"), abyste se ujistili, že je vyhodnocena jako řetězec a nikoli číselný typ.  Systém nezobrazí uvozovky, pokud se vrátíte na stránku, ale základní kód stále udržuje typ řetězce.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Stav datové části stavu služby"](media/action-groups-logic-app/service-health-payload-condition.png "Stav datové části stavu služby")

   1. V případě **skutečného** stavu postupujte podle pokynů v krocích 11 až 13 v [části Vytvoření výstrahy protokolu aktivit](#create-an-activity-log-alert-administrative) a přidejte akci Microsoft Teams.

   1. Definujte zprávu pomocí kombinace HTML a dynamického obsahu. Zkopírujte a vložte následující obsah do pole **Zpráva.** `[incidentType]`Nahraďte `[trackingID]` `[title]`pole `[communication]` , , a značkami dynamického obsahu se stejným názvem:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Služba zdraví skutečný stav po akci"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Servisní stav skutečný stav po akci")

   1. Pro **pokud false** podmínku, zadejte užitečnou zprávu:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Služba zdraví falešný stav post akce"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Služba Zdraví false stavu po akci")

- Krok 15 je stejný. Podle pokynů uložte aplikaci logiky a aktualizujte skupinu akcí.

## <a name="create-a-metric-alert"></a>Vytvoření upozornění na metriku

Proces vytvoření upozornění metriky je podobný [vytvoření výstrahy protokolu aktivit](#create-an-activity-log-alert-administrative), ale s několika změnami:

- Kroky 1 až 7 jsou stejné.
- Pro krok 8 použijte následující ukázkovou datovou část pro aktivační událost požadavku HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- Kroky 9 a 10 jsou stejné.
- Pro kroky 11 až 14 použijte následující postup:

  1. Vyberte **+** **Nový krok** a pak zvolte Přidat **podmínku**. Nastavte následující podmínky, aby se aplikace logiky spustila pouze v případě, že vstupní data odpovídají těmto hodnotám níže. Při zadávání hodnoty verze do textového pole kolem něj vložte uvozovky ("2.0"), abyste se ujistili, že je vyhodnocena jako řetězec a nikoli číselný typ.  Systém nezobrazí uvozovky, pokud se vrátíte na stránku, ale základní kód stále udržuje typ řetězce. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Podmínka užitečného zatížení metriky výstrahy"](media/action-groups-logic-app/metric-alert-payload-condition.png "Podmínka užitečného zatížení upozornění na metriku")

  1. V případě **true** podmínky přidejte **pro každou** smyčku a akce Microsoft Teams. Definujte zprávu pomocí kombinace HTML a dynamického obsahu.

      !["Metrika upozornění true podmínka post akce"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Akce příspěvku upozornění na metriku pravdivý stav")

  1. V **případě false** stavu definujte akci Microsoft Teams sdělit, že upozornění metriky neodpovídá očekávání aplikace logiky. Zahrňte datovou část JSON. Všimněte si, `triggerBody` jak odkazovat na dynamický obsah ve výrazu. `json()`

      !["Metrika upozornění false podmínka post akce"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Akce upozornění na upozornění na nepravdivé podmínky metriky")

- Krok 15 je stejný. Podle pokynů uložte aplikaci logiky a aktualizujte skupinu akcí.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Volání dalších aplikací kromě Microsoft Teams
Logic Apps má řadu různých konektorů, které umožňují spouštět akce v široké škále aplikací a databází. Slack, SQL Server, Oracle, Salesforce, jsou jen některé příklady. Další informace o konektory naleznete [v tématu konektory aplikace logiky](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Další kroky
* Získejte [přehled výstrah protokolu aktivit Azure](../../azure-monitor/platform/alerts-overview.md) a zjistěte, jak přijímat výstrahy.  
* Přečtěte si, jak [nakonfigurovat výstrahy při zaúčtování oznámení o stavu služby Azure](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
* Přečtěte si další informace o [skupinách akcí](../../azure-monitor/platform/action-groups.md).

