---
title: Pochopení schéma webhooku použít ve výstrahách protokolu aktivit
description: Další informace o schématu formátu JSON, který je odeslána do URL webhooku se nenačetla, když se aktivuje výstrahu protokolu aktivit.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 3935da72cb747a642ee1f360dc5318fc2d34e763
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263227"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooky Azure aktivity protokolu výstrahy
Jako součást definice skupiny akce můžete nakonfigurovat webhooku koncových bodů pro příjem oznámení o výstrahách protokolu aktivit. Pomocí webhooků je možné směrovat tato oznámení s dalšími systémy pro následné zpracování nebo vlastní akce. Tento článek ukazuje, jak vypadá pro HTTP POST na webhook, jehož datové části.

Další informace o protokolu upozornění, najdete v tématu Jak [vytvořit Azure aktivitu protokolu výstrahy](monitoring-activity-log-alerts.md).

Informace o skupinách akce najdete v tématu Jak [vytvořit skupiny akce](monitoring-action-groups.md).

## <a name="authenticate-the-webhook"></a>Ověření webhooku
Webhooku můžete volitelně použít ověření na základě tokenu pro ověřování. Identifikátor URI je uložit s tokenu ID, například webhooku `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Datová část schématu
Datová část JSON, které jsou obsažené v operaci POST liší v závislosti na pole data.context.activityLog.eventSource datové části.

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

Podrobnosti konkrétní schématu služby stavu oznámení aktivity protokolu výstrah najdete v tématu [oznámení o stavu služby](monitoring-service-notifications.md). Kromě toho další postup [webhooku oznámení o stavu služby nakonfigurovat vaše stávající řešení pro správu problém](../service-health/service-health-alert-webhook-guide.md).

Podrobnosti konkrétní schématu na všechny ostatní výstrahy protokolu aktivit najdete v tématu [přehled protokolu Azure činnosti](monitoring-overview-activity-logs.md).

| Název elementu | Popis |
| --- | --- |
| status |Používá pro výstrahy, metriky. Vždy nastaven na "aktivovaný" pro aktivitu protokolu výstrahy. |
| Kontext |Kontext události. |
| resourceProviderName |Zprostředkovatel prostředků ovlivněné prostředku. |
| conditionType |Vždy "událost". |
| jméno |Název pravidla výstrahy. |
| id |ID prostředku výstrahy. |
| description |Popis výstrahy nastavit při vytváření výstrahu. |
| subscriptionId |ID předplatného Azure. |
| časové razítko |Čas, kdy byla generována událost pomocí služby Azure, který požadavek zpracoval. |
| resourceId |ID prostředku ovlivněné prostředku. |
| resourceGroupName |Název skupiny prostředků pro prostředek dopad. |
| properties |Sada `<Key, Value>` páry (tedy `Dictionary<String, String>`) obsahující podrobnosti o události. |
| událost |Element, který obsahuje metadata o události. |
| Autorizace |Řízení přístupu na základě Role vlastnosti události. Tyto vlastnosti obvykle obsahovat akci, role a obor. |
| category |Kategorie události. Podporované hodnoty jsou správy, výstrahy, zabezpečení, ServiceHealth a doporučení. |
| volající |E-mailovou adresu uživatele, který provádí operace, deklarace hlavní název uživatele nebo název SPN deklarace identity na základě dostupnosti. Může mít hodnotu null pro určité systémová volání. |
| correlationId |Obvykle GUID ve formátu řetězce. Události se correlationId patřit do stejné větší akce a obvykle sdílet correlationId. |
| eventDescription |Statický text popis události. |
| eventDataId |Jedinečný identifikátor pro událost. |
| EventSource |Název služby Azure nebo infrastruktury, které vygenerovalo událost. |
| httpRequest |Požadavek obvykle obsahuje clientRequestId, clientIpAddress a metodou HTTP (například přidat). |
| úroveň |Jeden z následujících hodnot: kritická, chyba, upozornění a informativní. |
| operationId |Obvykle GUID sdílen události odpovídající jedné operace. |
| operationName |Název operace |
| properties |Vlastnosti události. |
| status |Řetězec. Stav operace. Běžné hodnoty zahrnují Začínáme, probíhá, bylo úspěšné, neúspěšné, aktivní a vyřešeno. |
| Podřízený stav |Obvykle zahrnuje stavový kód HTTP odpovídající volání REST. Může také obsahovat další řetězce, které popisují podřízeného stavu. Běžné substatus hodnoty zahrnují OK (stavový kód HTTP: 200), které byly vytvořeny (stavový kód HTTP: 201), platné (stavový kód HTTP: 202), ne obsahu (stavový kód HTTP: 204), chybný požadavek (stavový kód HTTP: 400), nebyl nalezen (stavový kód HTTP: 404), konflikt (stavový kód HTTP: 409 ), Vnitřní chybu serveru (kód stavu HTTP: 500), služba není k dispozici (kód stavu HTTP: 503) a vypršel časový limit brány (kód stavu HTTP: 504). |

## <a name="next-steps"></a>Další postup
* [Další informace o protokolu činnosti](monitoring-overview-activity-logs.md).
* [Spustit skripty služby Azure automation (Runbooky) na Azure výstrahy](http://go.microsoft.com/fwlink/?LinkId=627081).
* [Pomocí aplikace logiky odeslat zprávu SMS prostřednictvím Twilio z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). V tomto příkladu je metriky výstrahy, ale je možné ji upravit pro práci s výstrahu protokolu aktivit.
* [Použití aplikace logiky odeslat zprávu Slack z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). V tomto příkladu je metriky výstrahy, ale je možné ji upravit pro práci s výstrahu protokolu aktivit.
* [Použití aplikace logiky k odeslání zprávy do fronty Azure z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). V tomto příkladu je metriky výstrahy, ale je možné ji upravit pro práci s výstrahu protokolu aktivit.
