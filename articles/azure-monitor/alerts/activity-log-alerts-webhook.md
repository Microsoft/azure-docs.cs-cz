---
title: Pochopení schématu Webhooku používaného v upozorněních protokolu aktivit
description: Přečtěte si o schématu JSON, které se pošle na adresu URL Webhooku, když se aktivuje výstraha protokolu aktivit.
ms.topic: conceptual
ms.date: 03/31/2017
ms.subservice: alerts
ms.openlocfilehash: bdac71655ca0c517624f8aa033ee5bc7fcc9d12a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609699"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooky pro výstrahy protokolu aktivit Azure
V rámci definice skupiny akcí můžete nakonfigurovat koncové body Webhooku tak, aby přijímaly oznámení o výstrahách protokolu aktivit. Pomocí webhooků můžete tato oznámení směrovat do jiných systémů pro následné zpracování nebo vlastní akce. V tomto článku se dozvíte, jak se datová část příspěvku HTTP na Webhook líbí.

Další informace o výstrahách protokolů aktivit najdete v tématu [vytvoření výstrah protokolu aktivit Azure](../platform/activity-log-alerts.md).

Informace o skupinách akcí najdete v tématu [Vytvoření skupin akcí](../platform/action-groups.md).

> [!NOTE]
> Můžete také použít [běžné schéma výstrah](./alerts-common-schema.md), které poskytuje pro integraci Webhooku jedinou rozšiřitelnou a jednotnou datovou část napříč všemi službami výstrahy v Azure monitor. [Přečtěte si o běžných definicích schémat výstrah.](./alerts-common-schema-definitions.md)


## <a name="authenticate-the-webhook"></a>Ověření Webhooku
Webhook může volitelně použít autorizaci založenou na tokenech pro ověřování. Identifikátor URI Webhooku se uloží s ID tokenu, například `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue` .

## <a name="payload-schema"></a>Schéma datové části
Datová část JSON obsažená v operaci POST se liší v závislosti na poli data datové části Context. activityLog. eventSource.

> [!NOTE]
> V současné době je popis, který je součástí události protokolu aktivit, zkopírován do vlastnosti **"Popis výstrahy"** .
>
> Aby bylo možné zarovnávat datovou část protokolu aktivit s jinými typy výstrah od 1. dubna 2021 vlastnost aktivované výstrahy **Description (popis)** místo toho bude obsahovat popis pravidla výstrahy.
>
> Při přípravě této změny jsme vytvořili novou vlastnost **Popis události protokolu aktivit** do výstrahy aktivované protokolem aktivit. Tato nová vlastnost bude vyplněna vlastností **Description (popis)** , která je již k dispozici pro použití. To znamená, že nové pole **"Popis události protokolu aktivit"** bude obsahovat popis, který je součástí události protokolu aktivit.
>
> Zkontrolujte pravidla výstrah, pravidla akcí, Webhooky, aplikaci logiky nebo jakékoli jiné konfigurace, kde můžete použít vlastnost **Description** z výstrahy aktivované a nahradit ji **názvem "Popis události protokolu aktivit"** .
>
> Pokud je vaše podmínka (v pravidlech akcí, webhookech, Logic Apps nebo jakýchkoli jiných konfiguracích) aktuálně založená na vlastnosti **Description** pro výstrahy protokolu aktivit, může být potřeba ji upravit tak, aby se místo toho změnila na základě vlastnosti **"Popis události protokolu aktivit"** .
>
> Aby bylo možné vyplnit novou vlastnost **Description** , můžete do definice pravidla výstrahy přidat popis.
> ![Výstrahy protokolu aktivit aktivovány](media/activity-log-alerts-webhook/activity-log-alert-fired.png)

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

### <a name="administrative"></a>Správcovské

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

Podrobnosti o konkrétním schématu pro výstrahy protokolu aktivit oznámení o stavu služby najdete v tématu [Upozornění na stav služby](../../service-health/service-notifications.md). Kromě toho se dozvíte, jak [nakonfigurovat oznámení Webhooku služby Health Service pomocí stávajících řešení pro správu problémů](../../service-health/service-health-alert-webhook-guide.md).

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
| status |Používá se pro výstrahy metriky. Pro výstrahy protokolu aktivit vždycky nastavte na aktivované. |
| kontext |Kontext události |
| resourceProviderName |Poskytovatel prostředků ovlivněného prostředku. |
| conditionType |Vždy "Event" |
| name |Název pravidla výstrahy. |
| id |ID prostředku výstrahy |
| description |Popis výstrahy nastavený při vytvoření výstrahy |
| subscriptionId |ID předplatného Azure. |
| časové razítko |Čas, kdy byla událost vygenerována službou Azure, která zpracovala požadavek. |
| resourceId |ID prostředku ovlivněného prostředku |
| resourceGroupName |Název skupiny prostředků pro ovlivněný prostředek. |
| properties |Sada `<Key, Value>` párů (to znamená `Dictionary<String, String>` ), která obsahuje podrobnosti o události. |
| event |Prvek, který obsahuje metadata o události. |
| autorizace |Vlastnosti řízení přístupu na základě role v Azure Tyto vlastnosti obvykle zahrnují akci, roli a obor. |
| category |Kategorie události Mezi podporované hodnoty patří administrativní, výstraha, zabezpečení, ServiceHealth a doporučení. |
| volající |E-mailová adresa uživatele, který provedl operaci, deklaraci hlavního názvu uživatele (UPN) nebo deklaraci identity SPN na základě dostupnosti. Pro určitá systémová volání může mít hodnotu null. |
| correlationId |Obvykle identifikátor GUID ve formátu řetězce. Události s ID korelace patří stejné větší akci a obvykle sdílejí ID korelace. |
| eventDescription |Statický text popis události |
| eventDataId |Jedinečný identifikátor události |
| eventSource |Název služby nebo infrastruktury Azure, která událost vygenerovala. |
| httpRequest |Požadavek obvykle zahrnuje metodu ID žádosti klienta, clientIpAddress a HTTP (například PUT). |
| úroveň |Jedna z následujících hodnot: kritická, chyba, upozornění a informativní. |
| operationId |Identifikátor GUID se obvykle sdílí mezi událostmi, které odpovídají jedné operaci. |
| operationName |Název operace |
| properties |Vlastnosti události |
| status |Řetězec. Stav operace. Mezi běžné hodnoty patří počáteční, probíhající, úspěšná, neúspěšná, aktivní a vyřešená. |
| subStatus |Obvykle zahrnuje stavový kód HTTP odpovídajícího volání REST. Může také obsahovat další řetězce, které popisují dílčí stav. Mezi běžné hodnoty substavu patří OK (kód stavu HTTP: 200), Vytvořeno (kód stavu HTTP: 201), přijato (kód stavu HTTP: 202), žádný obsah (kód stavu HTTP: 204), chybný požadavek (kód stavu HTTP: 400), nenalezen (kód stavu HTTP: 404), konflikt (Stavový kód HTTP: 409), interní chyba serveru (kód stavu HTTP: 500), nedostupná služba (Stavový kód http: 503) a časový limit brány (kód stavu http : 504). |

Konkrétní podrobnosti o schématu pro všechny ostatní výstrahy protokolu aktivit najdete v tématu [Přehled protokolu aktivit Azure](../platform/platform-logs-overview.md).

## <a name="next-steps"></a>Další kroky
* [Přečtěte si další informace o protokolu aktivit](../platform/platform-logs-overview.md).
* [Spouštění skriptů Azure Automation (runbooky) na výstrahách Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Pomocí aplikace logiky můžete poslat SMS přes Twilio z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Tento příklad je pro výstrahy metrik, ale dá se upravit tak, aby fungoval s upozorněním protokolu aktivit.
* [Pomocí aplikace logiky odešlete zprávu o časové rezervě z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Tento příklad je pro výstrahy metrik, ale dá se upravit tak, aby fungoval s upozorněním protokolu aktivit.
* [Použijte aplikaci logiky k odeslání zprávy do fronty Azure z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Tento příklad je pro výstrahy metrik, ale dá se upravit tak, aby fungoval s upozorněním protokolu aktivit.