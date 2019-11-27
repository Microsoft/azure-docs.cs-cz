---
title: Schéma událostí protokolu aktivit Azure
description: Popisuje schéma událostí pro každou kategorii v protokolu aktivit Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 1/16/2019
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: d196cf4024513d891182f3b916bd8412a2f81d14
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74305494"
---
# <a name="azure-activity-log-event-schema"></a>Schéma událostí protokolu aktivit Azure
**Protokol aktivit Azure** je protokol, který poskytuje přehled o všech událostech na úrovni předplatného, ke kterým došlo v Azure. Tento článek popisuje schéma událostí podle kategorie dat. Schéma dat se liší v závislosti na tom, jestli čtete data na portálu, PowerShellu, CLI nebo přímo prostřednictvím REST API a [streamování dat do úložiště nebo Event Hubs pomocí profilu protokolu](activity-log-export.md). Následující příklady znázorňují schéma tak, jak je dostupné prostřednictvím portálu, PowerShellu, CLI a REST API. Mapování těchto vlastností na [schéma diagnostických protokolů Azure](diagnostic-logs-schema.md) je uvedené na konci článku.

## <a name="administrative"></a>Správa
Tato kategorie obsahuje záznam všech operací vytvoření, aktualizace, odstranění a akce provedené prostřednictvím Správce prostředků. Příklady typů událostí zobrazených v této kategorii zahrnují "vytvořit virtuální počítač" a "odstranit skupinu zabezpečení sítě" každou akci prováděnou uživatelem nebo aplikací pomocí Správce prostředků je modelem operace pro konkrétní typ prostředku. Pokud je typ operace zápis, odstranění nebo akce, záznamy o zahájení i úspěchu nebo selhání této operace se zaznamenávají do administrativní kategorie. Administrativní kategorie zahrnuje také všechny změny řízení přístupu na základě role v rámci předplatného.

### <a name="sample-event"></a>Událost vzorku
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Popisy vlastností
| Název elementu | Popis |
| --- | --- |
| authorization |Objekt BLOB vlastností RBAC události Obvykle zahrnuje vlastnosti "Action", "role" a "Scope". |
| volající |E-mailová adresa uživatele, který provedl operaci, deklaraci hlavního názvu uživatele (UPN) nebo deklaraci identity SPN na základě dostupnosti. |
| barev |Jedna z následujících hodnot: "admin", "Operation" |
| claims |Token JWT používaný službou Active Directory k ověření uživatele nebo aplikace k provedení této operace v Správce prostředků. |
| correlationId |Obvykle identifikátor GUID ve formátu řetězce. Události, které sdílejí ID korelace, patří ke stejné akci Uber. |
| description |Statický text popis události |
| eventDataId |Jedinečný identifikátor události |
| eventName | Popisný název události správy |
| category | Vždy "administrativní" |
| httpRequest |Objekt blob, který popisuje požadavek HTTP. Obvykle zahrnuje "ID žádosti klienta", "clientIpAddress" a "Method" (metoda HTTP. Například PUT). |
| úroveň |Úroveň události Jedna z následujících hodnot: "kritická", "Error", "Warning" a "informativní" |
| resourceGroupName |Název skupiny prostředků pro ovlivněný prostředek. |
| resourceProviderName |Název poskytovatele prostředků pro ovlivněný prostředek |
| resourceType | Typ prostředku, který byl ovlivněn událostí správy. |
| resourceId |ID prostředku ovlivněného prostředku |
| operationId |Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName |Název operace |
| properties |Sada dvojic `<Key, Value>` (to znamená slovníku) popisující podrobnosti události. |
| status |Řetězec popisující stav operace. Mezi běžné hodnoty patří: spuštěno, probíhá, úspěšné, neúspěšné, aktivní, vyřešené. |
| subStatus |Obvykle stavový kód HTTP odpovídajícího volání REST, ale může také zahrnovat další řetězce popisující dílčí stav, jako jsou tyto běžné hodnoty: OK (kód stavu HTTP: 200), Vytvořeno (kód stavu HTTP: 201), přijatý (Stavový kód HTTP: 202) bez obsahu (stav HTTP Kód: 204), chybný požadavek (kód stavu HTTP: 400), Nenalezeno (kód stavu HTTP: 404), konflikt (kód stavu HTTP: 409), interní chyba serveru (kód stavu http: 500), nedostupná služba (Stavový kód HTTP: 503), časový limit brány (kód stavu HTTP: 504). |
| eventTimestamp |Časové razítko, kdy se událost vygenerovala službou Azure, zpracování žádosti odpovídající události |
| submissionTimestamp |Časové razítko, kdy se událost stala k dispozici pro dotazování |
| subscriptionId |ID předplatného Azure. |

## <a name="service-health"></a>Stav služeb
Tato kategorie obsahuje záznam všech incidentů služby Health Service, ke kterým došlo v Azure. Příkladem typu události, kterou byste viděli v této kategorii, je "SQL Azure v Východní USA dochází k výpadkům." Události stavu služby přicházejí v pěti různých variantách: je vyžadována akce, pomocná obnovení, incident, údržba, informace nebo zabezpečení a zobrazí se pouze v případě, že máte prostředek v předplatném, který by měl být ovlivněn událostí.

### <a name="sample-event"></a>Událost vzorku
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Dokumentaci k hodnotám ve vlastnostech najdete v článku o [oznámeních o stavu služby](./../../azure-monitor/platform/service-notifications.md) .

## <a name="resource-health"></a>Stav prostředků
Tato kategorie obsahuje záznam o všech událostech stavu prostředku, ke kterým došlo u vašich prostředků Azure. Příkladem typu události, kterou byste viděli v této kategorii, je stav "stav virtuálního počítače se změnil na nedostupný". Události stavu prostředků mohou představovat jeden ze čtyř stavů: k dispozici, nedostupné, degradované a neznámé. Události stavu prostředků je navíc možné kategorizovat jako iniciované platformou nebo uživatelem iniciované.

### <a name="sample-event"></a>Událost vzorku

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Popisy vlastností
| Název elementu | Popis |
| --- | --- |
| barev | Vždy "admin, operace" |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický textový popis události výstrahy. |
| eventDataId |Jedinečný identifikátor události výstrahy. |
| category | Always "ResourceHealth" |
| eventTimestamp |Časové razítko, kdy se událost vygenerovala službou Azure, zpracování žádosti odpovídající události |
| úroveň |Úroveň události Jedna z následujících hodnot: "kritická", "Error", "Warning", "informativní" a "Verbose" |
| operationId |Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName |Název operace |
| resourceGroupName |Název skupiny prostředků, která obsahuje prostředek. |
| resourceProviderName |Vždy "Microsoft. Resourcehealth/healthevent/Action". |
| resourceType | Typ prostředku, na který měl vliv událost Resource Health. |
| resourceId | Název ID prostředku pro ovlivněný prostředek. |
| status |Řetězec popisující stav události stavu. Hodnoty mohou být: aktivní, Vyřešeno, InProgress, aktualizováno. |
| subStatus | Pro výstrahy obvykle je null. |
| submissionTimestamp |Časové razítko, kdy se událost stala k dispozici pro dotazování |
| subscriptionId |ID předplatného Azure. |
| properties |Sada dvojic `<Key, Value>` (to znamená slovníku) popisující podrobnosti události.|
| Properties. title | Uživatelsky přívětivý řetězec, který popisuje stav prostředku. |
| Properties. Details | Uživatelsky přívětivý řetězec, který popisuje další podrobnosti o události. |
| properties.currentHealthStatus | Aktuální stav prostředku. Jedna z následujících hodnot: "dostupný", "nedostupné", "Degraded" a "unknown". |
| properties.previousHealthStatus | Předchozí stav prostředku. Jedna z následujících hodnot: "dostupný", "nedostupné", "Degraded" a "unknown". |
| properties.type | Popis typu události stavu prostředku. |
| properties.cause | Popis příčiny události stavu prostředku. Buď "UserInitiated" a "PlatformInitiated". |


## <a name="alert"></a>Výstrahy
Tato kategorie obsahuje záznam všech aktivací výstrah Azure. Příkladem typu události, kterou byste viděli v této kategorii, je "procesor% v myVM byl za posledních 5 minut vyšší než 80." V různých systémech Azure je koncept upozornění – můžete definovat pravidlo pro určité řazení a přijímat oznámení, když podmínky splňují toto pravidlo. Pokaždé, když se v této kategorii protokolu aktivit doplní podporovaný typ výstrahy Azure, nebo jsou splněné podmínky, zobrazí se také záznam o aktivaci.

### <a name="sample-event"></a>Událost vzorku

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Popisy vlastností
| Název elementu | Popis |
| --- | --- |
| volající | Vždy Microsoft. Insights/alertRules |
| barev | Vždy "admin, operace" |
| claims | Objekt BLOB JSON s identifikátorem SPN (hlavní název služby) nebo typem prostředku modulu výstrah. |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický textový popis události výstrahy. |
| eventDataId |Jedinečný identifikátor události výstrahy. |
| category | Vždy "Výstraha" |
| úroveň |Úroveň události Jedna z následujících hodnot: "kritická", "Error", "Warning" a "informativní" |
| resourceGroupName |Název skupiny prostředků pro ovlivněný prostředek, pokud se jedná o výstrahu metriky. U jiných typů výstrah je to název skupiny prostředků, která obsahuje samotný výstrahu. |
| resourceProviderName |Název poskytovatele prostředků pro ovlivněný prostředek, pokud se jedná o výstrahu metriky. U jiných typů výstrah je to název poskytovatele prostředků samotné výstrahy. |
| resourceId | Název ID prostředku pro ovlivněný prostředek, pokud se jedná o výstrahu metriky. U jiných typů výstrah je to ID prostředku samotného prostředku výstrahy. |
| operationId |Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName |Název operace |
| properties |Sada dvojic `<Key, Value>` (to znamená slovníku) popisující podrobnosti události. |
| status |Řetězec popisující stav operace. Mezi běžné hodnoty patří: spuštěno, probíhá, úspěšné, neúspěšné, aktivní, vyřešené. |
| subStatus | Pro výstrahy obvykle je null. |
| eventTimestamp |Časové razítko, kdy se událost vygenerovala službou Azure, zpracování žádosti odpovídající události |
| submissionTimestamp |Časové razítko, kdy se událost stala k dispozici pro dotazování |
| subscriptionId |ID předplatného Azure. |

### <a name="properties-field-per-alert-type"></a>Pole vlastností na typ výstrahy
Pole Properties (vlastnosti) bude obsahovat různé hodnoty v závislosti na zdroji události výstrahy. Dvěma běžnými zprostředkovateli událostí výstrah jsou výstrahy protokolu aktivit a upozornění na metriky.

#### <a name="properties-for-activity-log-alerts"></a>Vlastnosti pro výstrahy protokolu aktivit
| Název elementu | Popis |
| --- | --- |
| properties.subscriptionId | ID předplatného z události protokolu aktivit, které způsobilo aktivaci tohoto pravidla upozornění protokolu aktivit. |
| properties.eventDataId | ID dat události z události protokolu aktivit, která způsobila aktivaci tohoto pravidla upozornění protokolu aktivit. |
| properties.resourceGroup | Skupina prostředků z události protokolu aktivit, která způsobila aktivaci tohoto pravidla upozornění protokolu aktivit. |
| properties.resourceId | ID prostředku z události protokolu aktivit, která způsobila aktivaci tohoto pravidla upozornění protokolu aktivit. |
| properties.eventTimestamp | Časové razítko události protokolu aktivit, která způsobila aktivaci tohoto pravidla upozornění protokolu aktivit. |
| properties.operationName | Název operace z události protokolu aktivit, která způsobila aktivaci tohoto pravidla upozornění protokolu aktivit. |
| vlastnosti. status | Stav z události protokolu aktivit, která způsobila aktivaci tohoto pravidla upozornění protokolu aktivit.|

#### <a name="properties-for-metric-alerts"></a>Vlastnosti pro výstrahy metriky
| Název elementu | Popis |
| --- | --- |
| vlastnosti. RuleUri | ID prostředku pro samotné pravidlo výstrahy metriky. |
| vlastnosti. RuleName | Název pravidla výstrahy metriky. |
| vlastnosti. RuleDescription | Popis pravidla upozornění metriky (jak je definováno v pravidle výstrahy). |
| vlastnosti. Mezí | Prahová hodnota použitá při vyhodnocování pravidla upozornění na metriku. |
| vlastnosti. WindowSizeInMinutes | Velikost okna použitá při vyhodnocování pravidla upozornění na metriku. |
| vlastnosti. Agregovat | Typ agregace definovaný v pravidle výstrahy metriky. |
| vlastnosti. Podnikatel | Podmíněný operátor použitý při vyhodnocování pravidla upozornění na metriky. |
| properties.MetricName | Název metriky metriky, která se používá při vyhodnocování pravidla upozornění na metriky. |
| vlastnosti. MetricUnit | Jednotka metriky pro metriku použitou při vyhodnocování pravidla upozornění na metriku. |

## <a name="autoscale"></a>Automatické škálování
Tato kategorie obsahuje záznam všech událostí souvisejících s provozem modulu automatického škálování na základě všech nastavení automatického škálování, které jste definovali v předplatném. Příkladem typu události, kterou byste viděli v této kategorii, je akce automatického škálování horizontálního škálování se nezdařila. Pomocí automatického škálování můžete automatické horizontální navýšení kapacity nebo škálování podle počtu instancí v podporovaném typu prostředku na základě denního nebo zatíženého data (metrického) pomocí nastavení automatického škálování. V případě splnění podmínek pro horizontální navýšení nebo snížení kapacity se v této kategorii budou zaznamenávat události začátek a úspěch nebo neúspěšné.

### <a name="sample-event"></a>Událost vzorku
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Popisy vlastností
| Název elementu | Popis |
| --- | --- |
| volající | Vždy Microsoft. Insights/autoscaleSettings |
| barev | Vždy "admin, operace" |
| claims | Objekt BLOB JSON s identifikátorem SPN (hlavní název služby) nebo typem prostředku modulu automatického škálování. |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický textový popis události automatického škálování. |
| eventDataId |Jedinečný identifikátor události automatického škálování |
| úroveň |Úroveň události Jedna z následujících hodnot: "kritická", "Error", "Warning" a "informativní" |
| resourceGroupName |Název skupiny prostředků pro nastavení automatického škálování. |
| resourceProviderName |Název poskytovatele prostředků pro nastavení automatického škálování. |
| resourceId |ID prostředku pro nastavení automatického škálování |
| operationId |Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName |Název operace |
| properties |Sada dvojic `<Key, Value>` (to znamená slovníku) popisující podrobnosti události. |
| vlastnosti. Název | Podrobný popis toho, co modul automatického škálování prováděl. |
| vlastnosti. ResourceName | ID prostředku ovlivněného prostředku (prostředek, na kterém se akce škálování prováděla) |
| properties.OldInstancesCount | Počet instancí před uplatněním akce automatického škálování. |
| properties.NewInstancesCount | Počet instancí po uplatnění akce automatického škálování. |
| properties.LastScaleActionTime | Časové razítko, kdy došlo k akci automatického škálování. |
| status |Řetězec popisující stav operace. Mezi běžné hodnoty patří: spuštěno, probíhá, úspěšné, neúspěšné, aktivní, vyřešené. |
| subStatus | Pro automatické škálování obvykle je null. |
| eventTimestamp |Časové razítko, kdy se událost vygenerovala službou Azure, zpracování žádosti odpovídající události |
| submissionTimestamp |Časové razítko, kdy se událost stala k dispozici pro dotazování |
| subscriptionId |ID předplatného Azure. |

## <a name="security"></a>Zabezpečení
Tato kategorie obsahuje záznam výstrahy vygenerované Azure Security Center. Příkladem typu události, kterou byste viděli v této kategorii, je "podezřelý soubor s dvojitou příponou" spuštěný.

### <a name="sample-event"></a>Událost vzorku
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Popisy vlastností
| Název elementu | Popis |
| --- | --- |
| barev | Vždy "operace" |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický textový popis události zabezpečení |
| eventDataId |Jedinečný identifikátor události zabezpečení |
| eventName |Popisný název události zabezpečení |
| category | Vždy "zabezpečení" |
| id |Jedinečný identifikátor prostředku události zabezpečení |
| úroveň |Úroveň události Jedna z následujících hodnot: "kritická", "Error", "Warning" nebo "informativní" |
| resourceGroupName |Název skupiny prostředků pro daný prostředek. |
| resourceProviderName |Název poskytovatele prostředků pro Azure Security Center. Vždy "Microsoft. Security". |
| resourceType |Typ prostředku, který vygeneroval událost zabezpečení, například "Microsoft. Security/Locations/Alerts" |
| resourceId |ID prostředku výstrahy zabezpečení |
| operationId |Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName |Název operace |
| properties |Sada dvojic `<Key, Value>` (to znamená slovníku) popisující podrobnosti události. Tyto vlastnosti se budou lišit v závislosti na typu výstrahy zabezpečení. Na [této stránce](../../security-center/security-center-alerts-overview.md) najdete popis typů výstrah, které pocházejí z Security Center. |
| vlastnosti. Závažnost |Úroveň závažnosti. Možné hodnoty jsou "vysoká", "střední" nebo "nízká". |
| status |Řetězec popisující stav operace. Mezi běžné hodnoty patří: spuštěno, probíhá, úspěšné, neúspěšné, aktivní, vyřešené. |
| subStatus | Pro události zabezpečení obvykle je null. |
| eventTimestamp |Časové razítko, kdy se událost vygenerovala službou Azure, zpracování žádosti odpovídající události |
| submissionTimestamp |Časové razítko, kdy se událost stala k dispozici pro dotazování |
| subscriptionId |ID předplatného Azure. |

## <a name="recommendation"></a>Doporučení
Tato kategorie obsahuje záznam všech nových doporučení, která jsou vygenerována pro vaše služby. Příkladem doporučení je použití skupin dostupnosti pro lepší odolnost proti chybám. Existují čtyři typy událostí doporučení, které je možné vygenerovat: vysoká dostupnost, výkon, zabezpečení a optimalizace nákladů. 

### <a name="sample-event"></a>Událost vzorku
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Popisy vlastností
| Název elementu | Popis |
| --- | --- |
| barev | Vždy "operace" |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický text popis události doporučení |
| eventDataId | Jedinečný identifikátor události doporučení |
| category | Vždy "doporučení" |
| id |Jedinečný identifikátor prostředku události doporučení |
| úroveň |Úroveň události Jedna z následujících hodnot: "kritická", "Error", "Warning" nebo "informativní" |
| operationName |Název operace  Vždy "Microsoft. Advisor/generateRecommendations/Action"|
| resourceGroupName |Název skupiny prostředků pro daný prostředek. |
| resourceProviderName |Název poskytovatele prostředků pro prostředek, pro který toto doporučení platí, jako je například MICROSOFT. COMPUTE |
| resourceType |Název typu prostředku pro prostředek, pro který se toto doporučení vztahuje, například "MICROSOFT. COMPUTE/VirtualMachines" |
| resourceId |ID prostředku prostředku, na který se doporučení vztahuje |
| status | Vždy "aktivní" |
| submissionTimestamp |Časové razítko, kdy se událost stala k dispozici pro dotazování |
| subscriptionId |ID předplatného Azure. |
| properties |Sada dvojic `<Key, Value>` (to znamená slovníku) popisující podrobnosti doporučení.|
| properties.recommendationSchemaVersion| Verze schématu vlastností doporučení publikovaných v záznamu protokolu aktivit |
| Properties. recommendationCategory | Kategorie doporučení Možné hodnoty jsou vysoká dostupnost, výkon, zabezpečení a náklady. |
| properties.recommendationImpact| Dopad doporučení. Možné hodnoty jsou "vysoké", "střední", "nízká". |
| Properties. recommendationRisk| Riziko doporučení. Možné hodnoty jsou "Error", "Warning", "none" |

## <a name="policy"></a>Zásada

Tato kategorie obsahuje záznamy všech operací akcí prováděných pomocí [Azure Policy](../../governance/policy/overview.md). Příklady typů událostí zobrazených v této kategorii zahrnují _audit_ a _Odepřít_. Každá akce prováděná zásadami je modelovaná jako operace na prostředku.

### <a name="sample-policy-event"></a>Událost vzorové zásady

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Popis vlastností události zásad

| Název elementu | Popis |
| --- | --- |
| authorization | Pole vlastností RBAC události Pro nové prostředky se jedná o akci a rozsah žádosti, která aktivovala vyhodnocení. U existujících prostředků se jedná o akci "Microsoft. Resources/checkPolicyCompliance/Read". |
| volající | Pro nové prostředky se jedná o identitu, která iniciovala nasazení. U stávajících prostředků se jedná o identifikátor GUID Microsoft Azure Insights RP. |
| barev | Události zásad používají pouze kanál "operace". |
| claims | Token JWT používaný službou Active Directory k ověření uživatele nebo aplikace k provedení této operace v Správce prostředků. |
| correlationId | Obvykle identifikátor GUID ve formátu řetězce. Události, které sdílejí ID korelace, patří ke stejné akci Uber. |
| description | Toto pole je prázdné pro události zásad. |
| eventDataId | Jedinečný identifikátor události |
| eventName | Buď "BeginRequest", nebo "EndRequest". "BeginRequest" se používá pro opožděné vyhodnocení auditIfNotExists a deployIfNotExists a když deployIfNotExists efekt spustí nasazení šablony. Všechny ostatní operace vrátí "EndRequest". |
| category | Deklaruje událost protokolu aktivit jako patřící k zásadě. |
| eventTimestamp | Časové razítko, kdy se událost vygenerovala službou Azure, zpracování žádosti odpovídající události |
| id | Jedinečný identifikátor události u konkrétního prostředku |
| úroveň | Úroveň události Audit používá upozornění a zamítnutí používá chybu. Chyba auditIfNotExists nebo deployIfNotExists může vygenerovat upozornění nebo chybu v závislosti na závažnosti. Všechny ostatní události zásad používají "informativní". |
| operationId | Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName | Název operace a přímo koreluje s účinkem zásad. |
| resourceGroupName | Název skupiny prostředků pro vyhodnocený prostředek. |
| resourceProviderName | Název poskytovatele prostředků pro vyhodnocený prostředek. |
| resourceType | Pro nové prostředky se jedná o typ, který se vyhodnocuje. U existujících prostředků vrátí "Microsoft. Resources/checkPolicyCompliance". |
| resourceId | ID prostředku vyhodnoceného prostředku |
| status | Řetězec popisující stav výsledku vyhodnocení zásad. Většina vyhodnocení zásad vrátí "úspěch", ale efekt zamítnutí vrátí "Failed". Chyby v auditIfNotExists nebo deployIfNotExists také vrací "Failed". |
| subStatus | Pole je prázdné pro události zásad. |
| submissionTimestamp | Časové razítko, kdy se událost stala k dispozici pro dotazování |
| subscriptionId | ID předplatného Azure. |
| properties.isComplianceCheck | Při nasazení nového prostředku vrátí hodnotu false nebo se aktualizují vlastnosti Správce prostředků existujícího prostředku. Všechny ostatní [triggery vyhodnocení](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) mají za následek "pravda". |
| properties.resourceLocation | Oblast Azure prostředku, který se vyhodnocuje |
| vlastnosti. nadřazených prvků | Čárkami oddělený seznam nadřazených skupin pro správu seřazených z přímých nadřazených na nejvzdálenější. |
| vlastnosti. policies | Obsahuje podrobnosti o definici zásady, přiřazení, vlivu a parametrech, které jsou výsledkem tohoto vyhodnocení zásad. |
| relatedEvents | Toto pole je prázdné pro události zásad. |

## <a name="mapping-to-diagnostic-logs-schema"></a>Mapování na schéma diagnostických protokolů

Když streamuje protokol aktivit Azure do účtu úložiště nebo Event Hubs oboru názvů, data se řídí [schématem diagnostických protokolů Azure](./diagnostic-logs-schema.md). Tady je mapování vlastností z schématu výše na schéma diagnostických protokolů:

| Vlastnost schématu diagnostických protokolů | Vlastnost schématu REST API protokolu aktivit | Poznámky: |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | subscriptionId, ResourceType, resourceGroupName jsou všechny odvozeny od resourceId. |
| operationName | operationName.value |  |
| category | Součást názvu operace | Užitečných typu operace – "zapsat"/"odstranit"/"akci" |
| resultType | stav. hodnota | |
| resultSignature | dílčí stav. hodnota | |
| resultDescription | description |  |
| durationMs | neuvedeno | Vždycky 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | deklarace identity a vlastnosti autorizace |  |
| Úroveň | Úroveň |  |
| location | neuvedeno | Umístění, kde byla událost zpracována. *Toto není umístění prostředku, ale místo, kde byla událost zpracována. Tato vlastnost bude v budoucí aktualizaci odebrána.* |
| Vlastnosti | Properties. eventProperties |  |
| properties.eventCategory | category | Pokud nejsou k dispozici vlastnosti. eventCategory, kategorie je "administrativní". |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| Properties. eventProperties | properties |  |


## <a name="next-steps"></a>Další kroky
* [Další informace o protokolu aktivit](activity-logs-overview.md)
* [Export protokolu aktivit do Azure Storage nebo Event Hubs](activity-log-export.md)

