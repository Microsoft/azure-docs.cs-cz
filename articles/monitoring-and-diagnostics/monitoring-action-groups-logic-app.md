---
title: Spouštění složitých akcí s výstrahami monitorování Azure a skupiny akce
description: Zjistěte, jak vytvořit aplikaci logiky akce procesu Azure monitorování výstrah.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: eafb2bcf0175190748c9dd020051cbebfcaee1fd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263880"
---
# <a name="create-a-logic-app-action"></a>Vytvoření aplikace logiky akce
## <a name="overview"></a>Přehled ##
Aktivuje výstrahu Azure monitorování, zavolá [akce skupiny](monitoring-action-groups.md). Akce skupiny umožňují aktivovat jeden nebo více akcí a upozornit uživatele, kteří výstrahy i jeho řešení.

Tento článek ukazuje, jak nastavit a aktivovat aplikaci logiky k vytvoření konverzace v Teams společnosti Microsoft, když se aktivuje výstraha.

Obecný postup je:

-   Vytvoření aplikace logiky pro odpovídající typ výstrahy

-   Import schématu pro odpovídající typ výstrahy do aplikace logiky

-   Zadejte chování aplikace logiky

-   Zkopírujte koncový bod HTTP aplikace logiky do skupiny akce Azure

Proces je podobný, pokud chcete aplikaci logiky provést jinou akci.

## <a name="create-an-activity-log-alert--administrative"></a>Vytvořit výstrahu protokolu aktivit – správy

1.  Klikněte na tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2.  Vyhledejte a vyberte **Aplikace logiky**. Klikněte na tlačítko **Vytvořit**.

3.  Pojmenujte svou aplikaci logiky, vyberte skupinu prostředků atd.

    ![Dialogové okno aplikace logiky vytvořit](media/monitoring-action-groups/create-logic-app-dialog.png "vytvořit logiku aplikace dialogové okno")

4.  Klikněte na tlačítko Vytvořit k vytvoření aplikace logiky. Po vytvoření aplikace logiky se zobrazí místní okno. Klikněte na tlačítko Spustit prostředků otevřete návrháře Logic Apps.

5.  Vyberte aktivační událost **obdrží žádost HTTP při**.

    ![Triggery pro aplikace logiky](media/monitoring-action-groups/logic-app-triggers.png "triggery pro aplikace logiky")

6.  Vyberte **upravit** Chcete-li změnit aktivační událost požadavku HTTP

    ![Tvar aktivační událost požadavku HTTP](media/monitoring-action-groups/http-request-trigger-shape.png "tvar aktivační událost požadavku HTTP")

7.  Vyberte **K vygenerování schématu použijte ukázkovou datovou část**.

    ![Ukázka použití – tlačítko datové části](media/monitoring-action-groups/use-sample-payload-button.png "použití ukázkové datové části – tlačítko")

8.  Zkopírujte a vložte následující ukázka schématu do dialogových oken.

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

9. Návrhář aplikace logiky zobrazí poznámka upozorněním, že požadavek odeslaný do aplikace logiky musíte nastavit hlavičku Content-Type na application/json. Pokračujte a zavřete toto dialogové okno. Výstraha Azure monitorování bude provádět správně.

    ![Hlavička Content-Type](media/monitoring-action-groups/content-type-header.png "hlavičku Content-Type")

10. Vyberte **+ nový krok** a potom zvolte **přidat akci**.

    ![Přidat akci](media/monitoring-action-groups/add-action.png "akce pro přidání")

11. Vyhledejte a vyberte konektor Teams společnosti Microsoft. Vyberte **Teams společnosti Microsoft – zpráva Post** akce.

    ![Akce Microsoft Teams](media/monitoring-action-groups/microsoft-teams-actions.png "akce Teams společnosti Microsoft")

12. Nakonfigurujte akci, která Teams společnosti Microsoft. Návrhář aplikace logiky zobrazí výzvu k ověření účtu Office 365. Vyberte **Id týmu** a **Id kanálu** k odeslání zprávy do.

13. Konfigurace **zpráva** používající kombinaci statického textu a odkazy na \<pole\> v dynamický obsah. Vyjmout a vložte následující text do pole zpráva.

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Poté vyhledejte a nahraďte \<pole\> s dynamického obsahu značky se stejným názvem.

    **[POZNÁMKA!]**  Existují dvě dynamické pole s názvem **stav**. Do zprávy můžete přidáte i pole stavu. Použijte jednu v kontejneru objektů activityLog a dalších odstranit. Pokud umístěte ukazatel myši nad **stav** pole se zobrazí odkaz na plně kvalifikovaný pole, jak je znázorněno na snímku obrazovky

    ![Týmy akce – zpráva Post](media/monitoring-action-groups/teams-action-post-message.png "týmy akce – zpráva Post")

14. Uložit aplikaci logiky kliknutím na tlačítko Uložit v horní části návrháře

15. Klikněte na tvar požadavek HTTP, který má způsobit, že rozšíření. Zkopírujte adresu URL protokolu HTTP POST.

    ![ADRESA URL PROTOKOLU HTTP POST](media/monitoring-action-groups/http-post-url.png "ADRESY URL PROTOKOLU HTTP POST")

16. Otevřete existující skupině Akce a přidejte akci, která má odkazovat na aplikaci logiky. Pokud nemáte existující skupinu pro akce, přečtěte si téma <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups> k jeho vytvoření. Nezapomeňte si uložte změny.

    ![Skupiny aktualizací akce](media/monitoring-action-groups/update-action-group.png "akce skupiny aktualizací")

Při příštím výstrahu vyvolá skupině Akce, se nazývá aplikace logiky.

## <a name="create-a-service-health-alert"></a>Vytvořit výstrahu pro stav služby

Položky stavu služby jsou součástí protokolu aktivit, takže proces je podobný s následujícími změnami

1.  Kroky 1 až 7 jsou stejné.
2.  Použijte následující schéma ukázka pro triggeru protokolu HTTP v kroku 8.

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
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

3.  Kroky 9 10 jsou stejné.
4.  Z krok 11 v pomocí procesu níže.
5.  Klikněte na **+ nový krok** tlačítko a zvolte **přidat podmínku**. Nastavte následující podmínky zajistit, že aplikace logiky pouze provede, když se vstupní data odpovídá tyto hodnoty
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - verze == 0.1.1

    !["Služba stavu datové části podmínky"](media/monitoring-action-groups/service-health-payload-condition.png "služby stavu datové části podmínky")

6. V **v případě hodnoty true** podmínky, přidejte akci Teams společnosti Microsoft pomocí kroků 11 až 13 z předchozího příkladu.

7. Zadejte zprávu pomocí kombinace HTML a [dynamický obsah]. Zkopírujte a vložte obsah níže do pole zpráva. Nahraďte [incidentType], [trackingID], [název] a [komunikace] pole s dynamického obsahu značky se stejným názvem

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    !["Služba stavu true podmínku post akce"](media/monitoring-action-groups/service-health-true-condition-post-action.png "akce post true podmínka stavu služby")

8. Pro **-li pravda** podmínky zadejte užitečné zprávu

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    !["Stav služby false podmínku post action"](media/monitoring-action-groups/service-health-false-condition-post-action.png "akce post false stav služby stavu")

9.  Postupujte podle kroků 15 – 16 předchozí příklad uložte svou aplikaci logiky a vaše akce skupiny aktualizací

## <a name="metric-alert"></a>Upozornění na metriku

1.  Kroky 1 až 7 jsou stejné jako v prvním příkladu
2.  Použijte následující schéma ukázka pro triggeru protokolu HTTP v kroku 8.

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
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
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

3.  Kroky 9 10 jsou stejné.
4.  Z krok 11 v pomocí procesu níže.
5.  Klikněte na **+ nový krok** tlačítko a zvolte **přidat podmínku**. Nastavte následující podmínky zajistit, že aplikace logiky pouze provede, když se vstupní data odpovídá tyto hodnoty

    - schemaId == AzureMonitorMetricAlert
    - verze == 2.0

    !["Metriky výstrahy datové části podmínky"](media/monitoring-action-groups/metric-alert-payload-condition.png "metriky výstrahy datové části podmínky")

6.  V **v případě hodnoty true** podmínku přidáme **pro každou** tvar a akce Teams společnosti Microsoft a definovat zprávy pomocí kombinace HTML a [dynamický obsah]

    !["Action post metriky podmínka true upozornění"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "metriky podmínka upozornění true post akce")

7.  V **-li pravda** utvářejí, definujte akce Teams společnosti Microsoft, která komunikuje, výstrahy metrika nemá odpovídající aplikaci logiky očekávání a zahrnují datové části JSON. Všimněte si, jak jsme dynamický obsah triggerBody ve výrazu json() odkazovat.

    !["Action post metriky podmínka upozornění false"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "metriky podmínka upozornění false post akce")

8.  Postupujte podle kroků 15 – 16 v prvním příkladu uložte svou aplikaci logiky a vaše akce skupiny aktualizací

## <a name="next-steps"></a>Další postup ##
* Získat [přehled výstrah aktivity protokolu](monitoring-overview-alerts.md)a zjistěte, jak dostávat výstrahy.  
* Zjistěte, jak [Konfigurace upozornění pokaždé, když je odeslána oznámení o stavu služby](monitoring-activity-log-alerts-on-service-notifications.md).
* Další informace o [skupiny akcí](monitoring-action-groups.md)