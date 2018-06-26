---
title: Postup spuštění komplexní akcí s Azure monitorování výstrah a skupinami akce
description: Zjistěte, jak vytvořit aplikaci akce logiku ke zpracování Azure monitorování výstrah.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 14e562234152d2f1f2f2d2b57b34cd5724d3dd14
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753089"
---
# <a name="create-a-logic-app-action"></a>Vytvoří akci aplikace logiky

Tento článek ukazuje, jak nastavit a aktivovat aplikaci logiky k vytvoření konverzace v Teams společnosti Microsoft, když se aktivuje výstraha.

## <a name="overview"></a>Přehled
Aktivuje výstrahu Azure monitorování, zavolá [akce skupiny](monitoring-action-groups.md). Akce skupiny umožňují aktivovat jeden nebo více akcí informovat ostatní o výstrahu a také jeho řešení.

Obecný postup je:

-   Vytvoření aplikace logiky pro odpovídající typ výstrahy.

-   Schéma pro typ příslušné výstrahy naimportujte do aplikace logiky.

-   Definování chování logiky aplikace.

-   Zkopírujte koncový bod HTTP logiku aplikace do skupiny služby Azure akce.

Proces je podobný, pokud chcete aplikaci logiky provést jinou akci.

## <a name="create-an-activity-log-alert-administrative"></a>Vytvořit výstrahu protokolu aktivit: pro správu

1.  Na portálu Azure vyberte **vytvořit prostředek** v levém horním rohu.

2.  Vyhledejte a vyberte **aplikace logiky**, pak vyberte **vytvořit**.

3.  Zadejte svou aplikaci logiky **název**, vyberte **skupiny prostředků**a tak dále.

    ![Vytvoření aplikace logiky](media/monitoring-action-groups/create-logic-app-dialog.png "vytvoření aplikace logiky")

4.  Vyberte **vytvořit** k vytvoření aplikace logiky. Zobrazí se zpráva označuje, jestli je vytvořená aplikace logiky. Vyberte **spusťte prostředků** otevřete **logiku aplikace Návrhář**.

5.  Vyberte aktivační události: **obdrží žádost HTTP při**.

    ![Triggery pro aplikace logiky](media/monitoring-action-groups/logic-app-triggers.png "triggery pro aplikace logiky")

6.  Vyberte **upravit** Chcete-li změnit aktivační událost požadavku HTTP.

    ![Aktivační události požadavku HTTP](media/monitoring-action-groups/http-request-trigger-shape.png "aktivační události požadavku HTTP")

7.  Vyberte **K vygenerování schématu použijte ukázkovou datovou část**.

    ![Použít datovou část ukázka](media/monitoring-action-groups/use-sample-payload-button.png "použít datovou část vzorku")

8.  Zkopírujte a vložte následující ukázka schématu do dialogových oken:

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

9. **Návrhář aplikace na základě logiky** zobrazí místní okno s upozorněním, že požadavek odeslaný do aplikace logiky, musíte nastavit **Content-Type** hlavičky k **application/json**. Zavřete okno automaticky otevírané okno. Výstraha Azure sledování nastaví hlavičku.

    ![Nastavit hlavičku Content-Type](media/monitoring-action-groups/content-type-header.png "nastavit hlavičku Content-Type")

10. Vyberte **+** **nový krok** a potom zvolte **přidat akci**.

    ![Přidat akci](media/monitoring-action-groups/add-action.png "přidat akci")

11. Vyhledejte a vyberte konektor Teams společnosti Microsoft. Vyberte **Teams společnosti Microsoft – zpráva Post** akce.

    ![Akce Microsoft Teams](media/monitoring-action-groups/microsoft-teams-actions.png "akce Teams společnosti Microsoft")

12. Nakonfigurujte akci, která Teams společnosti Microsoft. **Logiku aplikace Návrhář** žádostí o ověření účtu služeb Office 365. Vyberte **Id týmu** a **Id kanálu** k odeslání zprávy do.

13. Konfigurovat zprávy pomocí kombinaci statického textu a odkazy na \<pole\> v dynamický obsah. Zkopírujte a vložte následující text do **zpráva** pole:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Poté vyhledejte a nahraďte \<pole\> s dynamického obsahu značky se stejným názvem.

    > [!NOTE]
    > Existují dvě dynamická pole, které byly pojmenovány **stav**. Do zprávy můžete přidáte i z těchto polí. Do pole, která je v **activityLog** kontejneru objektů a dat a odstranění jiné pole. Ukazatele myši **stav** pole zobrazíte pole plně kvalifikovaný odkaz, jak je znázorněno na následujícím snímku obrazovky:

    ![Akce Teams společnosti Microsoft: příspěvek ve](media/monitoring-action-groups/teams-action-post-message.png "akce Teams společnosti Microsoft: odešlete zprávu")

14. V horní části **logiku aplikace Návrhář**, vyberte **Uložit** uložte svou aplikaci logiky.

15. Otevřete existující skupině Akce a přidejte akci, která má odkazovat na aplikaci logiky. Pokud nemáte existující skupinu pro akce, přečtěte si téma [vytvořit a spravovat skupiny akce na portálu Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups) k jeho vytvoření. Nezapomeňte si uložte změny.

    ![Aktualizace skupiny akce](media/monitoring-action-groups/update-action-group.png "akce skupiny aktualizací")

Při příštím výstrahu vyvolá skupině Akce, se nazývá aplikace logiky.

## <a name="create-a-service-health-alert"></a>Vytvořit výstrahu služby stavu

Azure položky stavu služby jsou součástí protokolu aktivit. Proces vytvoření výstrahy je podobná [vytváření výstrahu protokolu aktivit](#create-an-activity-log-alert-administrative), ale s několik změn:

- Kroky 1 až 7 jsou stejné.
- Krok 8 použijte následující ukázka schématu pro aktivační událost požadavku HTTP:

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

-  Kroky 9 a 10 jsou stejné.
-  Kroků 11 až 14 použijte následující postup:

   1. Vyberte **+** **nový krok** a potom zvolte **přidat podmínku**. Nastavte následující podmínky zajistit, že aplikace logiky spustí, pouze pokud vstupní data odpovídá tyto hodnoty:
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == 0.1.1`

      !["Stav služby datové části podmínky"](media/monitoring-action-groups/service-health-payload-condition.png "podmínky datové části stav služby")

   1. V **v případě hodnoty true** podmínky, postupujte podle kroků 11 až 13 v v [vytvořit výstrahu protokolu aktivit](#create-an-activity-log-alert-administrative) přidat akci Teams společnosti Microsoft.

   1. Zadejte zprávu pomocí kombinace HTML a dynamický obsah. Zkopírujte a vložte následující obsah do **zpráva** pole. Nahraďte `[incidentType]`, `[trackingID]`, `[title]`, a `[communication]` pole s dynamického obsahu značky se stejným názvem:

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

       !["Stav služby true podmínku post action"](media/monitoring-action-groups/service-health-true-condition-post-action.png "akce post true stav služby stavu")

   1. Pro **-li pravda** podmínky, zadejte užitečné zprávu:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Stav služby false podmínku post action"](media/monitoring-action-groups/service-health-false-condition-post-action.png "akce post false stav služby stavu")

- Krok 15 je stejný. Postupujte podle pokynů a uložte svou aplikaci logiky a aktualizovat vaše akce skupinu.

## <a name="create-a-metric-alert"></a>Vytvořit výstrahu pro metriky

Proces vytvoření metriky výstraha je podobná [vytváření výstrahu protokolu aktivit](#create-an-activity-log-alert-administrative), ale s několik změn:

- Kroky 1 až 7 jsou stejné.
- Krok 8 použijte následující ukázka schématu pro aktivační událost požadavku HTTP:

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

- Kroky 9 a 10 jsou stejné.
- Kroků 11 až 14 použijte následující postup:

   1. Vyberte **+** **nový krok** a potom zvolte **přidat podmínku**. Nastavte následující podmínky zajistit, že aplikace logiky spustí, pouze pokud vstupní data odpovídá tyto hodnoty:
       - `schemaId == AzureMonitorMetricAlert`
       - `version == 2.0`

       !["Metriky výstrahy datové části podmínky"](media/monitoring-action-groups/metric-alert-payload-condition.png "metriky výstrahy datové části podmínky")

   1. V **v případě hodnoty true** podmínky, přidejte **pro každou** smyčky a akce Teams společnosti Microsoft. Zadejte zprávu pomocí kombinace HTML a dynamický obsah.

       !["Action post metriky podmínka true upozornění"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "metriky podmínka upozornění true post akce")

   1. V **-li pravda** podmínky, definujte akce Teams společnosti Microsoft pro komunikaci, aby metriky výstrahy neodpovídá očekávání aplikaci logiky. Zahrnout datové části JSON. Všimněte si, jak odkazovat `triggerBody` dynamický obsah v `json()` výraz.

       !["Action post metriky podmínka upozornění false"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "metriky podmínka upozornění false post akce")

- Krok 15 je stejný. Postupujte podle pokynů a uložte svou aplikaci logiky a aktualizovat vaše akce skupinu.

## <a name="next-steps"></a>Další postup
* Získat [přehled výstrah protokolu aktivita Azure](monitoring-overview-alerts.md) a zjistěte, jak dostávat výstrahy.  
* Zjistěte, jak [nakonfigurovat výstrahy, když je odeslána oznámení o stavu služby Azure](monitoring-activity-log-alerts-on-service-notifications.md).
* Další informace o [skupiny akcí](monitoring-action-groups.md).