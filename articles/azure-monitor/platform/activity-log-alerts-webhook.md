---
title: Seznamte se se schématem webhooku používaným ve výstrahách protokolu aktivit
description: Informace o schématu JSON, který je zaúčtován do webhooku URL při aktivaci protokolu aktivit.
ms.topic: conceptual
ms.date: 03/31/2017
ms.subservice: alerts
ms.openlocfilehash: c076b8dcea350f9ddd66977e89ce99b81f377b17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669042"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooky pro výstrahy protokolu aktivit Azure
Jako součást definice skupiny akcí můžete nakonfigurovat koncové body webhooku tak, aby přijímabyly upozornění protokolu aktivit. Pomocí webhooků můžete tato oznámení směrovat do jiných systémů pro následné zpracování nebo vlastní akce. Tento článek ukazuje, jak vypadá datová část pro HTTP POST na webhooku.

Další informace o výstrahách protokolu aktivit najdete v tématu [jak vytvořit výstrahy protokolu aktivit Azure](activity-log-alerts.md).

Informace o skupinách akcí naleznete v tématu [jak vytvořit skupiny akcí](../../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Můžete také použít [společné schéma výstrah](https://aka.ms/commonAlertSchemaDocs), které poskytuje výhodu, že mají jednu rozšiřitelnou a jednotnou datovou část výstrahy ve všech službách výstrah v Azure Monitoru pro vaše integrace webhooku. [Seznamte se s definicemi schématu běžných výstrah.](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>Ověření webového háku
Webhook můžete volitelně použít autorizaci založenou na tokenech pro ověřování. Identifikátor URI webhooku je uložen s ID tokenu, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`například .

## <a name="payload-schema"></a>Schéma datové části
Datová část JSON obsažená v operaci POST se liší v závislosti na poli data.context.activityLog.eventSource datové části.

### <a name="common"></a>Společné

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Správa

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Zabezpečení

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Doporučení

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Konkrétní podrobnosti o schématu v výstrahách protokolu aktivit y oznámení o stavu služby naleznete v [tématu Oznámení o stavu služby](../../azure-monitor/platform/service-notifications.md). Dále se dozvíte, jak [nakonfigurovat oznámení webhooku stavu služby s existujícími řešeními správy problémů](../../service-health/service-health-alert-webhook-guide.md).

### <a name="resourcehealth"></a>ResourceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Název elementu | Popis |
| --- | --- |
| status |Používá se pro upozornění na metriky. Vždy nastavte "aktivováno" pro upozornění protokolu aktivit. |
| kontext |Kontext události. |
| název_prostředku_prostředku_prostředku_prostředku |Poskytovatel prostředků ovlivněného prostředku. |
| conditionType |Vždy "Událost". |
| jméno |Název pravidla výstrahy. |
| id |ID prostředku výstrahy. |
| description |Popis výstrahy nastavit při vytvoření výstrahy. |
| subscriptionId |ID předplatného Azure. |
| časové razítko |Čas, kdy byla událost vygenerována službou Azure, která zpracovala požadavek. |
| resourceId |ID zdroje ovlivněného prostředku. |
| resourceGroupName |Název skupiny prostředků pro ovlivněný prostředek. |
| properties |Sada `<Key, Value>` párů (tj. `Dictionary<String, String>`), která obsahuje podrobnosti o události. |
| event |Prvek, který obsahuje metadata o události. |
| autorizace |Vlastnosti řízení přístupu na základě rolí události. Tyto vlastnosti obvykle zahrnují akci, roli a obor. |
| category |Kategorie události. Mezi podporované hodnoty patří správa, výstraha, zabezpečení, stav služby a doporučení. |
| Volající |E-mailová adresa uživatele, který operaci provedl, deklarace hlavního uživatele nebo deklarace služby SPN na základě dostupnosti. Může být null pro určitá systémová volání. |
| correlationId |Obvykle identifikátor GUID ve formátu řetězce. Události s correlationId patří do stejné větší akce a obvykle sdílejí correlationId. |
| eventDescription |Statický textový popis události. |
| eventDataId |Jedinečný identifikátor události |
| eventSource |Název služby Azure nebo infrastruktury, která událost vygenerovala. |
| httpRequest |Požadavek obvykle zahrnuje clientRequestId, clientIpAddress a HTTP metodu (například PUT). |
| level |Jedna z následujících hodnot: Kritická, Chyba, Upozornění a Informační. |
| operationId |Obvykle identifikátor GUID sdílené mezi událostmi odpovídající jedné operaci. |
| operationName |Název operace. |
| properties |Vlastnosti události. |
| status |Řetězec. Stav operace. Mezi běžné hodnoty patří Spuštěno, Probíhá, Úspěšné, Neúspěšné, Aktivní a Vyřešeno. |
| subStatus |Obvykle obsahuje stavový kód HTTP odpovídající volání REST. Může také obsahovat další řetězce, které popisují dílčí stav. Mezi běžné hodnoty dílčího stavu patří OK (stavový kód HTTP: 200), Vytvořeno (stavový kód HTTP: 201), Přijat (stavový kód HTTP: 202), Žádný obsah (stavový kód HTTP: 204), Chybný požadavek (Stavový kód HTTP: 400), Nebyl nalezen (stavový kód HTTP: 404), Konflikt (Stavový kód HTTP: 409 ), Vnitřní chyba serveru (stavový kód HTTP: 500), Služba není k dispozici (stavový kód HTTP: 503) a časový výtok brány (stavový kód HTTP: 504). |

Podrobnosti o konkrétním schématu ve všech ostatních výstrahách protokolu aktivit najdete [v tématu Přehled protokolu aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md).

## <a name="next-steps"></a>Další kroky
* [Další informace o protokolu aktivit](../../azure-monitor/platform/platform-logs-overview.md).
* [Spouštění skriptů automatizace Azure (Runbook) ve výstrahách Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Pomocí aplikace logiky odeslat SMS přes Twilio z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Tento příklad je pro upozornění metriky, ale lze upravit pro práci s výstrahou protokolu aktivit.
* [Pomocí aplikace logiky odeslat zprávu Slack z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Tento příklad je pro upozornění metriky, ale lze upravit pro práci s výstrahou protokolu aktivit.
* [Pomocí aplikace logiky odeslat zprávu do fronty Azure z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Tento příklad je pro upozornění metriky, ale lze upravit pro práci s výstrahou protokolu aktivit.

