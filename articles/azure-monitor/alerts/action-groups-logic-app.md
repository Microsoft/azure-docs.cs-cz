---
title: Aktivace složitých akcí s výstrahami Azure Monitor
description: Naučte se vytvořit akci aplikace logiky, která bude zpracovávat výstrahy Azure Monitor.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: f1e81dca6926ae9f57e428eb1cef761c588a78b6
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029841"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Postup aktivace složitých akcí s výstrahami Azure Monitor

V tomto článku se dozvíte, jak nastavit a aktivovat aplikaci logiky pro vytvoření konverzace v Microsoft teams, když se aktivuje výstraha.

## <a name="overview"></a>Přehled

Když se aktivuje výstraha Azure Monitor, zavolá [skupinu akcí](./action-groups.md). Skupiny akcí umožňují aktivovat jednu nebo více akcí, aby bylo možné upozornit ostatní na výstrahu a zároveň ji opravit.

Obecný proces:

-   Vytvořte aplikaci logiky pro příslušný typ výstrahy.

-   Importujte ukázkovou datovou část pro příslušný typ výstrahy do aplikace logiky.

-   Definujte chování aplikace logiky.

-   Zkopírujte koncový bod HTTP aplikace logiky do skupiny akcí Azure.

Postup je podobný, pokud chcete, aby aplikace logiky prováděla jinou akci.

## <a name="create-an-activity-log-alert-administrative"></a>Vytvoření upozornění protokolu aktivit: Správa

1. [Vytvořte aplikaci logiky](~/articles/logic-apps/quickstart-create-first-logic-app-workflow.md).

1.  Vyberte aktivační událost: **když se přijme požadavek HTTP**.

1. V dialogu pro, **kdy se přijme požadavek HTTP**, vyberte **použít ukázkovou datovou část k vygenerování schématu**.

    ![Snímek obrazovky, který zobrazuje dialog v případě, že je vybraná možnost použít ukázkovou datovou část v případě, že se vygeneruje možnost použít ukázku schématu ](~/articles/app-service/media/tutorial-send-email/generate-schema-with-payload.png)

1.  Zkopírujte a vložte následující datovou část ukázkového souboru do dialogového okna:

    ```json
        {
            "schemaId&quot;: &quot;Microsoft.Insights/activityLogs&quot;,
            &quot;data&quot;: {
                &quot;status&quot;: &quot;Activated&quot;,
                &quot;context&quot;: {
                &quot;activityLog&quot;: {
                    &quot;authorization&quot;: {
                    &quot;action&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;scope&quot;: &quot;/subscriptions/…&quot;
                    },
                    &quot;channels&quot;: &quot;Operation&quot;,
                    &quot;claims&quot;: &quot;…&quot;,
                    &quot;caller&quot;: &quot;logicappdemo@contoso.com&quot;,
                    &quot;correlationId&quot;: &quot;91ad2bac-1afa-4932-a2ce-2f8efd6765a3&quot;,
                    &quot;description&quot;: &quot;&quot;,
                    &quot;eventSource&quot;: &quot;Administrative&quot;,
                    &quot;eventTimestamp&quot;: &quot;2018-04-03T22:33:11.762469+00:00&quot;,
                    &quot;eventDataId&quot;: &quot;ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0&quot;,
                    &quot;level&quot;: &quot;Informational&quot;,
                    &quot;operationName&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;operationId&quot;: &quot;61f59fc8-1442-4c74-9f5f-937392a9723c&quot;,
                    &quot;resourceId&quot;: &quot;/subscriptions/…&quot;,
                    &quot;resourceGroupName&quot;: &quot;LOGICAPP-DEMO&quot;,
                    &quot;resourceProviderName&quot;: &quot;microsoft.insights&quot;,
                    &quot;status&quot;: &quot;Succeeded&quot;,
                    &quot;subStatus&quot;: &quot;&quot;,
                    &quot;subscriptionId&quot;: &quot;…&quot;,
                    &quot;submissionTimestamp&quot;: &quot;2018-04-03T22:33:36.1068742+00:00&quot;,
                    &quot;resourceType&quot;: &quot;microsoft.insights/activityLogAlerts&quot;
                }
                },
                &quot;properties&quot;: {}
            }
        }
    ```

1. **Návrhář Logic Apps** zobrazí automaticky otevírané okno s upozorněním, že požadavek odeslaný do aplikace logiky musí nastavit hlavičku **Content-Type** na **Application/JSON**. Zavřete automaticky otevírané okno. Výstraha Azure Monitor nastaví hlavičku.

    ![Nastavení záhlaví Content-Type](media/action-groups-logic-app/content-type-header.png &quot;Nastavení záhlaví Content-Type")

1. Vyberte **+** **Nový krok** a pak zvolte **přidat akci**.

    ![Přidání akce](media/action-groups-logic-app/add-action.png "Přidání akce")

1. Vyhledejte a vyberte konektor Microsoft Teams. Vyberte akci **Microsoft Teams – poslat zprávu** .

    ![Akce týmů Microsoftu](media/action-groups-logic-app/microsoft-teams-actions.png "Akce týmů Microsoftu")

1. Nakonfigurujte akci Microsoft Teams. **Návrhář Logic Apps** vás vyzve, abyste ověřili svůj pracovní nebo školní účet. Vyberte **ID týmu** a **ID kanálu** , do kterého se má zpráva poslat.

13. Nakonfigurujte zprávu pomocí kombinace statického textu a odkazů na \<fields\> dynamický obsah. Zkopírujte následující text a vložte ho do pole **zpráva** :

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Pak vyhledejte a nahraďte \<fields\> značkami dynamického obsahu se stejným názvem.

    > [!NOTE]
    > Existují dvě dynamická pole s názvem **status**. Přidejte obě tato pole do zprávy. Použijte pole, které je v kontejneru vlastností **activityLog** , a odstraňte druhé pole. Pokud chcete zobrazit úplný odkaz na pole, jak je znázorněno na následujícím snímku obrazovky, umístěte ukazatel myši na pole **stav** .

    ![Akce Microsoft Teams: odeslání zprávy](media/action-groups-logic-app/teams-action-post-message.png "Akce Microsoft Teams: odeslání zprávy")

1. V horní části **návrháře Logic Apps** vyberte **Uložit** a uložte svoji aplikaci logiky.

1. Otevřete existující skupinu akcí a přidejte akci, která se odkazuje na aplikaci logiky. Pokud nemáte existující skupinu akcí, přečtěte si téma [Vytvoření a Správa skupin akcí v Azure Portal](./action-groups.md) k jeho vytvoření. Nezapomeňte uložit změny.

    ![Aktualizace skupiny akcí](media/action-groups-logic-app/update-action-group.png "Aktualizace skupiny akcí")

Když výstraha příště vyvolá vaši skupinu akcí, vaše aplikace logiky se zavolá.

## <a name="create-a-service-health-alert"></a>Vytvoření upozornění na stav služby

Položky Azure Service Health jsou součástí protokolu aktivit. Proces vytvoření výstrahy je podobný jako při [vytváření upozornění protokolu aktivit](#create-an-activity-log-alert-administrative), ale s několika změnami:

- Kroky 1 až 3 jsou stejné.
- V kroku 4 použijte následující vzorovou datovou část pro aktivační událost požadavku HTTP:

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
                        "impactedServices": "[{\"ImpactedRegions\"}]",
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

-  Kroky 5 a 6 jsou stejné.
-  V krocích 7 až 11 použijte následující postup:

   1. Vyberte **+** **Nový krok** a pak zvolte **Přidat podmínku**. Nastavte následující podmínky, aby se aplikace logiky vykonává pouze v případě, že vstupní data odpovídají hodnotám uvedeným níže.  Při zadávání hodnoty verze do textového pole vložte uvozovky kolem ("0.1.1"), abyste se ujistili, že se vyhodnotí jako řetězec, nikoli číselný typ.  Systém nezobrazuje uvozovky, pokud se vrátíte na stránku, ale podkladový kód stále udržuje typ řetězce.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Service Health podmínka datové části"](media/action-groups-logic-app/service-health-payload-condition.png "Podmínka datové části Service Health")

   1. V případě podmínky v **hodnotě true** postupujte podle pokynů v krocích 11 až 13 v tématu [Vytvoření upozornění protokolu aktivit](#create-an-activity-log-alert-administrative) a přidejte akci Microsoft Teams.

   1. Definujte zprávu pomocí kombinace HTML a dynamického obsahu. Zkopírujte a vložte následující obsah do pole **zpráva** . Nahraďte `[incidentType]` `[trackingID]` pole,, `[title]` a `[communication]` pomocí značek dynamického obsahu se stejným názvem:

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

       !["Service Health pravdivá podmínka akce post"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Akce po Service Health pravdivé podmínky")

   1. V **případě podmínky false** zadejte užitečnou zprávu:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Service Health podmínka po akci post false"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Akce post podmínky Service Health false")

- Krok 15 je stejný. Postupujte podle pokynů a uložte svoji aplikaci logiky a aktualizujte svou skupinu akcí.

## <a name="create-a-metric-alert"></a>Vytvoření upozornění na metriku

Proces vytvoření upozornění na metriku se podobá [Vytvoření upozornění protokolu aktivit](#create-an-activity-log-alert-administrative), ale s několika změnami:

- Kroky 1 až 3 jsou stejné.
- V kroku 4 použijte následující vzorovou datovou část pro aktivační událost požadavku HTTP:

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

- Kroky 5 a 6 jsou stejné.
- V krocích 7 až 11 použijte následující postup:

  1. Vyberte **+** **Nový krok** a pak zvolte **Přidat podmínku**. Nastavte následující podmínky, aby se aplikace logiky vykonává pouze v případě, že vstupní data odpovídají následujícím hodnotám. Při zadávání hodnoty verze do textového pole vložte uvozovky kolem ("2,0"), abyste se ujistili, že se vyhodnotí jako řetězec, nikoli číselný typ.  Systém nezobrazuje uvozovky, pokud se vrátíte na stránku, ale podkladový kód stále udržuje typ řetězce. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Podmínka datové části výstrahy metriky"](media/action-groups-logic-app/metric-alert-payload-condition.png "Podmínka datové části výstrahy metriky")

  1. V podmínce **při hodnotě true** přidejte **pro každou** smyčku a akci Microsoft Teams. Definujte zprávu pomocí kombinace HTML a dynamického obsahu.

      !["Akce po odeslání podmínky upozornění metriky výstrahy"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Akce po odeslání podmínky upozornění metriky výstrahy")

  1. V **případě podmínky false** definujte akci Microsoft teams, aby komunikovala, že výstraha metriky neodpovídá očekáváním aplikace logiky. Zahrňte datovou část JSON. Všimněte si, jak odkazovat na `triggerBody` dynamický obsah ve `json()` výrazu.

      !["Akce post nepravdivé podmínky upozornění na metriku"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Akce post nepravdivé podmínky upozornění metriky")

- Krok 15 je stejný. Postupujte podle pokynů a uložte svoji aplikaci logiky a aktualizujte svou skupinu akcí.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Volání jiných aplikací kromě Microsoft Teams
Logic Apps má řadu různých konektorů, které umožňují aktivovat akce v široké škále aplikací a databází. Časová rezerva, SQL Server, Oracle, Salesforce, jsou jenom příklady. Další informace o konektorech najdete v tématu [konektory Logic](../../connectors/apis-list.md)Apps.  

## <a name="next-steps"></a>Další kroky
* Získejte [Přehled výstrah protokolu aktivit Azure](./alerts-overview.md) a Naučte se přijímat výstrahy.  
* Naučte se [konfigurovat výstrahy, když se publikuje oznámení Azure Service Health](../../service-health/alerts-activity-log-service-notifications-portal.md).
* Přečtěte si další informace o [skupinách akcí](./action-groups.md).
