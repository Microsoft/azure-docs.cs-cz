---
title: Pochopení schématu webhooku v upozornění protokolu aktivit
description: Další informace o schématu JSON, který se pošle na adresu URL webhooku po upozornění protokolu aktivit aktivuje.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 147e67efa901c834afef8f0da2acf6f5b523f6ad
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254192"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooky pro výstrahy protokolu aktivit Azure
Jako součást definice skupiny akcí můžete nakonfigurovat webhooku koncových bodů pro příjem oznámení výstrah protokolu aktivit. Pomocí webhooků můžete směrovat tato oznámení s dalšími systémy pro následné zpracování nebo vlastní akce. Tento článek popisuje, jak vypadá datová část požadavku HTTP POST do webhooku.

Další informace o upozornění protokolu aktivit najdete v tématu Jak [vytvoření upozornění protokolu aktivit Azure](monitoring-activity-log-alerts.md).

Informace o skupinách akcí najdete v tématu Jak [vytvoření skupiny akcí](monitoring-action-groups.md).

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
                    "cause": "PlatformInitiated",
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

Podrobnosti o konkrétní schématu na upozornění protokolu aktivit oznámení stavu služby, najdete v části [služby oznámení o stavu](monitoring-service-notifications.md). Také se dozvíte, jak [konfigurace webhooku oznámení o stavu služby se stávajícími řešeními správy problému](../service-health/service-health-alert-webhook-guide.md).

Podrobnosti konkrétní schématu na všechny ostatní upozornění protokolu aktivit najdete v tématu [přehled protokolu aktivit Azure](monitoring-overview-activity-logs.md).

| Název elementu | Popis |
| --- | --- |
| status |Používá se pro upozornění na metriku. Vždy nastaven na "aktivované" pro upozornění protokolu aktivit. |
| Kontext |Kontext události. |
| Název resourceProviderName |Poskytovatel prostředků ovlivněných prostředků. |
| conditionType |Vždy "událost". |
| jméno |Název pravidla výstrahy. |
| id |ID prostředku výstrahy. |
| description |Popis výstrahy nastavit, pokud je výstraha vytvořena. |
| subscriptionId |ID předplatného Azure. |
| časové razítko |Čas, kdy byla událost vygenerována podle služeb Azure, který požadavek zpracoval. |
| resourceId |ID prostředku ovlivněných prostředků. |
| resourceGroupName |Název skupiny prostředků pro ovlivněných prostředků. |
| properties |Sada `<Key, Value>` páry (to znamená `Dictionary<String, String>`), který obsahuje podrobnosti o události. |
| událost |Element, který obsahuje metadata o události. |
| Autorizace |Řízení přístupu na základě Role vlastnosti události. Tyto vlastnosti se obvykle zahrnují akce, role a obor. |
| category |Kategorie události. Mezi podporované hodnoty patří pro správu, oznámení, zabezpečení, ServiceHealth a doporučení. |
| volající |E-mailová adresa uživatele, který provedl operaci, deklarace nebo hlavní název služby deklarace identity na základě dostupnosti. Nesmí být null u některých systémových volání. |
| correlationId |Obvykle GUID ve formátu řetězce. Události s ID korelace patřit do stejné akce větší a obvykle sdílet ID korelace. |
| eventDescription |Statický text popis události. |
| eventDataId |Jedinečný identifikátor pro událost. |
| EventSource |Název služby Azure nebo infrastruktury, které vygenerovalo událost. |
| httpRequest |Požadavek obvykle zahrnuje ID žádosti klienta, clientIpAddress a metodou HTTP (například UMÍSTIT). |
| úroveň |Jeden z následujících hodnot: kritické, chyba, upozornění a na informativní sdělení. |
| operationId |Obvykle GUID sdílen události odpovídá jedné operace. |
| operationName |Název operace |
| properties |Vlastnosti události. |
| status |řetězec. Stav operace. Mezi běžné hodnoty patří spuštěno, v průběhu, úspěšné, neúspěšné, aktivní a vyřešeno. |
| Podřízený stav |Obvykle zahrnuje stavový kód HTTP odpovídající volání REST. Může taky obsahovat další řetězce, které popisují substatus. Běžné hodnoty substatus zahrnují OK (stavový kód HTTP: 200), které byly vytvořeny (stavový kód HTTP: 201), platné (stavový kód HTTP: 202), ne obsahu (stavový kód HTTP: 204), chybný požadavek (stavový kód HTTP: 400), nebyl nalezen (stavový kód HTTP: 404), konflikt (stavový kód HTTP: 409 ), Se interní chyba serveru (kód stavu HTTP: 500), služba není k dispozici (kód stavu HTTP: 503) a časový limit brány (kód stavu HTTP: 504). |

## <a name="next-steps"></a>Další postup
* [Další informace o protokolu aktivit](monitoring-overview-activity-logs.md).
* [Spouštění skriptů Azure automation (Runbooky) na upozornění Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Pomocí aplikace logiky můžete poslat zprávu SMS přes Twilio z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Tento příklad je určený pro upozornění na metriky, ale může být upraveno pro práci s upozornění protokolu aktivit.
* [Pomocí aplikace logiky můžete poslat zprávu Slack z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Tento příklad je určený pro upozornění na metriky, ale může být upraveno pro práci s upozornění protokolu aktivit.
* [Použijte aplikace logiky pro odeslání zprávy do fronty služby Azure z Azure výstrahy](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Tento příklad je určený pro upozornění na metriky, ale může být upraveno pro práci s upozornění protokolu aktivit.
