---
title: Pochopení schématu webhooku v upozornění protokolu aktivit
description: Další informace o schématu JSON, který se pošle na adresu URL webhooku po upozornění protokolu aktivit aktivuje.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: johnkem
ms.subservice: alerts
ms.openlocfilehash: c91c1badaa4b1bc055859d700857cfd4d062babd
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491501"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooky pro výstrahy protokolu aktivit Azure
Jako součást definice skupiny akcí můžete nakonfigurovat webhooku koncových bodů pro příjem oznámení výstrah protokolu aktivit. Pomocí webhooků můžete směrovat tato oznámení s dalšími systémy pro následné zpracování nebo vlastní akce. Tento článek popisuje, jak vypadá datová část požadavku HTTP POST do webhooku.

Další informace o upozornění protokolu aktivit najdete v tématu Jak [vytvoření upozornění protokolu aktivit Azure](activity-log-alerts.md).

Informace o skupinách akcí najdete v tématu Jak [vytvoření skupiny akcí](../../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Můžete také použít [společné schéma produktu výstrah](https://aka.ms/commonAlertSchemaDocs), která nabízí výhodu v podobě jediného rozšiřitelné a sjednocené výstrah datová část mezi všechny výstrahy služby ve službě Azure Monitor pro vaše integrace webhooku. [Další informace o běžných výstrah schématu definice.](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>Ověření se webhook.
Webhook můžete volitelně použít ověřování založené na tokenech pro ověřování. Webhook identifikátoru URI je uložen s ID tokenu, například `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Datová část schématu
Datová část JSON, které jsou obsaženy v operaci POST liší v závislosti na poli data.context.activityLog.eventSource datové části.

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

Podrobnosti o konkrétní schématu na upozornění protokolu aktivit oznámení stavu služby, najdete v části [služby oznámení o stavu](../../azure-monitor/platform/service-notifications.md). Také se dozvíte, jak [konfigurace webhooku oznámení o stavu služby se stávajícími řešeními správy problému](../../service-health/service-health-alert-webhook-guide.md).

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
| status |Používá se pro upozornění na metriku. Vždy nastaven na "aktivované" pro upozornění protokolu aktivit. |
| context |Kontext události. |
| resourceProviderName |Poskytovatel prostředků ovlivněných prostředků. |
| conditionType |Vždy "událost". |
| name |Název pravidla výstrahy. |
| id |ID prostředku výstrahy. |
| description |Popis výstrahy nastavit, pokud je výstraha vytvořena. |
| subscriptionId |ID předplatného Azure. |
| timestamp |Čas, kdy byla událost vygenerována podle služeb Azure, který požadavek zpracoval. |
| resourceId |ID prostředku ovlivněných prostředků. |
| resourceGroupName |Název skupiny prostředků pro ovlivněných prostředků. |
| properties |Sada `<Key, Value>` páry (to znamená `Dictionary<String, String>`), který obsahuje podrobnosti o události. |
| událost |Element, který obsahuje metadata o události. |
| authorization |Řízení přístupu na základě Role vlastnosti události. Tyto vlastnosti se obvykle zahrnují akce, role a obor. |
| category |Kategorie události. Mezi podporované hodnoty patří pro správu, oznámení, zabezpečení, ServiceHealth a doporučení. |
| Volající |E-mailová adresa uživatele, který provedl operaci, deklarace nebo hlavní název služby deklarace identity na základě dostupnosti. Nesmí být null u některých systémových volání. |
| correlationId |Obvykle GUID ve formátu řetězce. Události s ID korelace patřit do stejné akce větší a obvykle sdílet ID korelace. |
| eventDescription |Statický text popis události. |
| eventDataId |Jedinečný identifikátor pro událost. |
| eventSource |Název služby Azure nebo infrastruktury, které vygenerovalo událost. |
| httpRequest |Požadavek obvykle zahrnuje ID žádosti klienta, clientIpAddress a metodou HTTP (například UMÍSTIT). |
| level |Jeden z následujících hodnot: Kritické, chyba, upozornění a informativní. |
| operationId |Obvykle GUID sdílen události odpovídá jedné operace. |
| operationName |Název operace |
| properties |Vlastnosti události. |
| status |řetězec. Stav operace. Mezi běžné hodnoty patří spuštěno, v průběhu, úspěšné, neúspěšné, aktivní a vyřešeno. |
| Podřízený stav |Obvykle zahrnuje stavový kód HTTP odpovídající volání REST. Může taky obsahovat další řetězce, které popisují substatus. Běžné hodnoty substatus zahrnují OK (stavový kód HTTP: 200), vytvořit (kód stavu HTTP: 201), přijato (kód stavu HTTP: 202), žádný obsah (kód stavu HTTP: 204), chybná žádost (kód stavu HTTP: 400), nebyl nalezen (kód stavu HTTP: 404), konflikt (kód stavu HTTP: 409), se interní chyba serveru (kód stavu HTTP: 500), služba není k dispozici (kód stavu HTTP: 503) a časový limit brány (kód stavu HTTP: 504). |

Podrobnosti konkrétní schématu na všechny ostatní upozornění protokolu aktivit najdete v tématu [přehled protokolu aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md).

## <a name="next-steps"></a>Další postup
* [Další informace o protokolu aktivit](../../azure-monitor/platform/activity-logs-overview.md).
* [Spouštění skriptů Azure automation (Runbooky) na upozornění Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Pomocí aplikace logiky můžete poslat zprávu SMS přes Twilio z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Tento příklad je určený pro upozornění na metriky, ale může být upraveno pro práci s upozornění protokolu aktivit.
* [Pomocí aplikace logiky můžete poslat zprávu Slack z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Tento příklad je určený pro upozornění na metriky, ale může být upraveno pro práci s upozornění protokolu aktivit.
* [Použijte aplikace logiky pro odeslání zprávy do fronty služby Azure z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Tento příklad je určený pro upozornění na metriky, ale může být upraveno pro práci s upozornění protokolu aktivit.

