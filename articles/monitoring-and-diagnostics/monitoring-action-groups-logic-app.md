---
title: Jak aktivovat složité akce s Azure Monitor výstrah a skupiny akcí
description: Zjistěte, jak vytvořit akce aplikace logiky ke zpracování Azure Monitor výstrah.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 51d47b87f898aa65fe4ee76c312240a50d45231d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049183"
---
# <a name="create-a-logic-app-action"></a>Vytvoření akce aplikace logiky

Tento článek ukazuje, jak nastavit a spustit aplikaci logiky vytvořte konverzaci v Microsoft Teams, když se aktivuje upozornění.

## <a name="overview"></a>Přehled
Azure Monitor upozornění se aktivuje, zavolá [skupiny akcí](monitoring-action-groups.md). Skupiny akcí umožní aktivovat jeden nebo více akcí a informovat ostatní o určité výstraze a také jeho řešení.

Obecný postup je:

-   Vytvoření aplikace logiky pro příslušný typ výstrahy.

-   Importujte schématu pro příslušný typ výstrahy do aplikace logiky.

-   Definují chování aplikace logiky.

-   Zkopírujte koncovému bodu HTTP z aplikace logiky do skupiny akcí Azure.

Proces je podobný, pokud chcete aplikaci logiky provádět různé akce.

## <a name="create-an-activity-log-alert-administrative"></a>Vytvoření upozornění protokolu aktivit: pro správu

1.  Na webu Azure Portal, vyberte **vytvořit prostředek** v levém horním rohu.

2.  Vyhledejte a vyberte **aplikace logiky**a pak vyberte **vytvořit**.

3.  Dejte aplikaci logiky **název**, zvolte **skupiny prostředků**, a tak dále.

    ![Vytvoření aplikace logiky](media/monitoring-action-groups/create-logic-app-dialog.png "vytvoření aplikace logiky")

4.  Vyberte **vytvořit** k vytvoření aplikace logiky. Místní zpráva označuje, že je vytvořená aplikace logiky. Vyberte **spuštění prostředků** otevřít **návrhář pro Logic Apps**.

5.  Vyberte trigger: **přijetí požadavku HTTP když**.

    ![Triggery aplikace logiky](media/monitoring-action-groups/logic-app-triggers.png "triggery aplikace logiky")

6.  Vyberte **upravit** změnit aktivační událost požadavek HTTP.

    ![Aktivační události HTTP požadavku](media/monitoring-action-groups/http-request-trigger-shape.png "triggerů požadavek HTTP")

7.  Vyberte **K vygenerování schématu použijte ukázkovou datovou část**.

    ![Použít ukázkovou datovou část](media/monitoring-action-groups/use-sample-payload-button.png "použít ukázkovou datovou část")

8.  Zkopírujte a vložte následující schéma ukázka do dialogového okna:

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

9. **Návrhář aplikace logiky** zobrazí automaticky otevírané okno s připomínkou, že musíte nastavit požadavku odeslaného do aplikace logiky **Content-Type** záhlaví **application/json**. Automaticky otevírané okno zavřete. Azure Monitor alert nastaví záhlaví.

    ![Nastavit hlavičku Content-Type](media/monitoring-action-groups/content-type-header.png "nastavit hlavičku Content-Type")

10. Vyberte **+** **nový krok** a klikněte na tlačítko **přidat akci**.

    ![Přidání akce](media/monitoring-action-groups/add-action.png "přidat akci")

11. Vyhledejte a vyberte konektor Microsoft Teams. Zvolte **Microsoft Teams – publikovat zprávu** akce.

    ![Akce Microsoft Teams](media/monitoring-action-groups/microsoft-teams-actions.png "akce Microsoft Teams")

12. Nakonfigurujte akci, která Microsoft Teams. **Návrhář pro Logic Apps** vás vyzve k ověření vašeho účtu Office 365. Zvolte **ID týmu** a **ID kanálu** odeslat zprávu do.

13. Konfigurace s použitím kombinace statický text a odkazy na zprávu \<pole\> v seznamu dynamického obsahu. Zkopírujte a vložte následující text do **zpráva** pole:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Potom hledání a nahrazování \<pole\> dynamické obsahu značky se stejným názvem.

    > [!NOTE]
    > Existují dvě dynamické pole, které jsou pojmenovány **stav**. Přidejte oba z těchto polí do zprávy. Použijte pole, které je v **activityLog** kontejner objektů a dat a odstranění jiné pole. Ukazatele myši **stav** pole zobrazíte úplný pole odkazu, jak je znázorněno na následujícím snímku obrazovky:

    ![Akce Microsoft Teams: odeslání zprávy](media/monitoring-action-groups/teams-action-post-message.png "akce Microsoft Teams: odeslání zprávy")

14. V horní části **návrhář pro Logic Apps**vyberte **Uložit** uložte aplikaci logiky.

15. Otevřete váš stávající skupiny akcí a přidejte akci, která odkazují na aplikaci logiky. Pokud nemáte stávající skupiny akcí, přečtěte si téma [vytvořit a spravovat skupiny akcí na webu Azure Portal](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups) k jejímu vytvoření. Nezapomeňte si uložit změny.

    ![Aktualizovat skupinu akcí](media/monitoring-action-groups/update-action-group.png "aktualizovat skupinu akcí")

Při příštím výstrahu volá vaši skupinu akcí se nazývá svou aplikaci logiky.

## <a name="create-a-service-health-alert"></a>Vytvoření upozornění na stav služby

Azure Service Health položky jsou součástí protokolu aktivit. Je podobný procesu pro vytváření upozornění [vytvoření upozornění protokolu aktivit](#create-an-activity-log-alert-administrative), ale s drobnými změnami:

- Kroky 1 až 7 jsou stejné.
- V kroku 8 použijte následující ukázkový schématu pro trigger požadavku HTTP:

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
-  K provedení kroků 11 až 14 použijte následující postup:

   1. Vyberte **+** **nový krok** a klikněte na tlačítko **přidat podmínku**. Nastavte následující podmínky, aby aplikace logiky se provádí pouze v případě, že se vstupní data shodují s níže uvedené hodnoty.  Při zadávání hodnoty verze do textového pole, umístěte kolem něj ("0.1.1") uvozovky, abyste měli jistotu, že je vyhodnocen jako řetězec a není číselného typu.  Systém se nezobrazují-li se vrátit na stránku, ale základní kód stále udržuje typ řetězce uvozovky.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Service Health datové části podmínku"](media/monitoring-action-groups/service-health-payload-condition.png "podmínka datové části Service Health")

   1. V **při hodnotě true** podmínky, postupujte podle pokynů v kroků 11 až 13 [vytvoření upozornění protokolu aktivit](#create-an-activity-log-alert-administrative) přidáte akci Microsoft Teams.

   1. Definujte zprávy pomocí kombinace HTML a dynamický obsah. Zkopírujte a vložte následující obsah **zpráva** pole. Nahradit `[incidentType]`, `[trackingID]`, `[title]`, a `[communication]` pole s dynamického obsahu značky se stejným názvem:

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

       !["Service Health splněné podmínky příspěvek action"](media/monitoring-action-groups/service-health-true-condition-post-action.png "akci po splněna podmínka stavu služby")

   1. Pro **případě hodnoty false** podmínky, poskytují užitečné zpráva:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Service Health nepravdivé příspěvek action"](media/monitoring-action-groups/service-health-false-condition-post-action.png "akci po nepravdivé Service Health")

- Krok 15 je stejný. Postupujte podle pokynů pro svou aplikaci logiky a aktualizaci vaší skupiny akcí.

## <a name="create-a-metric-alert"></a>Vytvoření upozornění na metriku

Je podobný procesu pro vytváření upozornění na metriku [vytvoření upozornění protokolu aktivit](#create-an-activity-log-alert-administrative), ale s drobnými změnami:

- Kroky 1 až 7 jsou stejné.
- V kroku 8 použijte následující ukázkový schématu pro trigger požadavku HTTP:

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
- K provedení kroků 11 až 14 použijte následující postup:

   1. Vyberte **+** **nový krok** a klikněte na tlačítko **přidat podmínku**. Nastavte následující podmínky, aby aplikace logiky se provádí pouze v případě, že se vstupní data odpovídá tyto hodnoty níže. Pokud zadáte hodnotu verze do textového pole, umístěte kolem něj ("2.0") uvozovky k zajišťuje, že se vyhodnotí jako řetězec a není číselného typu.  Systém se nezobrazují-li se vrátit na stránku, ale základní kód stále udržuje typ řetězce uvozovky. 
       - `schemaId == AzureMonitorMetricAlert`
       - `version == "2.0"`
       
       !["Podmínka metriky upozornění datovou část"](media/monitoring-action-groups/metric-alert-payload-condition.png "podmínka metriky upozornění datové části")

   1. V **při hodnotě true** podmínky, přidejte **pro každou** smyčky a akce Microsoft Teams. Definujte zprávy pomocí kombinace HTML a dynamický obsah.

       !["Akci po metriky true vyskytl výstražný stav"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "akci po upozornění true podmínku metriky")

   1. V **případě hodnoty false** podmínky, definujte akce Microsoft Teams pro komunikaci, že upozornění metriky neodpovídá požadavkům aplikace logiky. Zahrnout datovou část JSON. Všimněte si, jak odkazovat `triggerBody` dynamický obsah `json()` výrazu.

       !["Akci po metriky false vyskytl výstražný stav"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "akci po upozornění false podmínku metriky")

- Krok 15 je stejný. Postupujte podle pokynů pro svou aplikaci logiky a aktualizaci vaší skupiny akcí.

## <a name="next-steps"></a>Další postup
* Získat [přehled upozornění protokolu aktivit Azure](monitoring-overview-alerts.md) a zjistěte, jak dostávat upozornění.  
* Zjistěte, jak [nakonfigurovat výstrahy, když se pošle oznámení o Azure Service Health](monitoring-activity-log-alerts-on-service-notifications.md).
* Další informace o [skupiny akcí](monitoring-action-groups.md).
