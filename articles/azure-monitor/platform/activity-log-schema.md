---
title: Schéma událostí protokolu aktivit Azure
description: Popisuje schéma událostí pro každou kategorii v protokolu aktivit Azure.
author: bwren
services: azure-monitor
ms.topic: reference
ms.date: 12/04/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c2f171c79423e0cfe8b57c05b8248679f9ada9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472737"
---
# <a name="azure-activity-log-event-schema"></a>Schéma událostí protokolu aktivit Azure
[Protokol aktivit Azure](platform-logs-overview.md) poskytuje přehled o všech událostech na úrovni předplatného, ke kterým došlo v Azure. Tento článek popisuje schéma událostí pro každou kategorii. 

Níže uvedené příklady ukazují schéma při přístupu k protokolu aktivit z portálu, Prostředí PowerShell, CLI a ROZHRANÍ REST API. Schéma se liší při [streamování protokolu aktivit do úložiště nebo centra událostí](resource-logs-stream-event-hubs.md). Mapování vlastností na [schéma protokolů prostředků](diagnostic-logs-schema.md) je k dispozici na konci článku.

## <a name="administrative"></a>Správa
Tato kategorie obsahuje záznam všech operací vytvoření, aktualizace, odstranění a akce prováděných prostřednictvím Správce prostředků. Příklady typů událostí, které se zobrazí v této kategorii, zahrnují "vytvořit virtuální počítač" a "odstranit skupinu zabezpečení sítě" Každá akce provedená uživatelem nebo aplikací pomocí Správce prostředků je modelována jako operace pro určitý typ prostředku. Pokud typ operace je Zápis, Odstranit nebo Akce, záznamy zahájení a úspěch u této operace jsou zaznamenány v kategorii Správa. Kategorie Správa také zahrnuje všechny změny řízení přístupu na základě rolí v předplatném.

### <a name="sample-event"></a>Ukázková událost
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
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.microsoft.com/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
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
| Název prvku | Popis |
| --- | --- |
| autorizace |Objekt blob RBAC vlastnosti události. Obvykle zahrnuje vlastnosti "akce", "role" a "obor". |
| Volající |E-mailová adresa uživatele, který operaci provedl, deklarace hlavního uživatele nebo deklarace služby SPN na základě dostupnosti. |
| Kanály |Jedna z následujících hodnot: "Admin", "Operace" |
| Nároky |Token JWT používaný službou Active Directory k ověření uživatele nebo aplikace k provedení této operace ve Správci prostředků. |
| correlationId |Obvykle identifikátor GUID ve formátu řetězce. Události, které sdílejí correlationId patří do stejné akce uber. |
| description |Statický textový popis události. |
| eventDataId |Jedinečný identifikátor události |
| Eventname | Popisný název události správce. |
| category | Vždy "Administrativní" |
| httpRequest |Objekt blob popisující požadavek http. Obvykle zahrnuje "clientRequestId", "clientIpAddress" a "metoda" (metoda HTTP. Například PUT). |
| level |Úroveň události. Jedna z následujících hodnot: "Kritická", "Chyba", "Upozornění" a "Informační" |
| resourceGroupName |Název skupiny prostředků pro ovlivněný prostředek. |
| název_prostředku_prostředku_prostředku_prostředku |Název poskytovatele prostředků pro ovlivněný prostředek |
| resourceType | Typ prostředku, který byl ovlivněn událostí správce. |
| resourceId |ID zdroje ovlivněného prostředku. |
| operationId |Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName |Název operace. |
| properties |Sada `<Key, Value>` párů (tj. slovník) popisující podrobnosti události. |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: Spuštěno, Probíhá, Úspěšné, Neúspěšné, Aktivní, Vyřešeno. |
| subStatus |Stavový kód HTTP odpovídajícího volání REST, ale může obsahovat i další řetězce popisující dílčí stav, například tyto společné hodnoty: OK (stavový kód HTTP: 200), Vytvořeno (Stavový kód HTTP: 201), Přijato (Stavový kód HTTP: 202), Žádný obsah (stav HTTP Kód: 204), Chybný požadavek (Stavový kód HTTP: 400), Nebyl nalezen (stavový kód HTTP: 404), Konflikt (Stavový kód HTTP: 409), Vnitřní chyba serveru (Stavový kód HTTP: 500), Služba není k dispozici (stavový kód HTTP: 503), Časový výtok brány (stavový kód HTTP: 504). |
| eventTimestamp |Časové razítko, kdy byla událost generována službou Azure, která zpracovává požadavek odpovídající události. |
| submissionTimestamp |Časové razítko, kdy událost byla k dispozici pro dotazování. |
| subscriptionId |ID předplatného Azure. |

## <a name="service-health"></a>Stav služeb
Tato kategorie obsahuje záznam všech incidentů stavu služby, ke kterým došlo v Azure. Příkladem typu události, která se zobrazí v této kategorii, je "SQL Azure ve východní části USA dochází k prostojům." Události stavu služby přicházejí v pěti variantách: Požadovaná akce, Asistované obnovení, Incident, Údržba, Informace nebo Zabezpečení a zobrazí se pouze v případě, že máte v předplatném prostředek, který by byl událostí ovlivněn.

### <a name="sample-event"></a>Ukázková událost
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
Naleznete v článku [oznámení o stavu služby](./../../azure-monitor/platform/service-notifications.md) pro dokumentaci o hodnotách ve vlastnostech.

## <a name="resource-health"></a>Stav prostředků
Tato kategorie obsahuje záznam všech událostí stavu prostředků, ke kterým došlo k prostředkům Azure. Příkladem typu události, která se zobrazí v této kategorii, je "Stav virtuálního počítače byl změněn na nedostupný". Události stavu prostředků může představovat jeden ze čtyř stavů: K dispozici, Není k dispozici, Degradován a Neznámý. Události stavu prostředků lze navíc kategorizovat jako spuštěné platformy nebo inicializovány uživatelem.

### <a name="sample-event"></a>Ukázková událost

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
| Název prvku | Popis |
| --- | --- |
| Kanály | Vždy "Admin, Operace" |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický textový popis události výstrahy. |
| eventDataId |Jedinečný identifikátor události výstrahy |
| category | Vždy "ResourceHealth" |
| eventTimestamp |Časové razítko, kdy byla událost generována službou Azure, která zpracovává požadavek odpovídající události. |
| level |Úroveň události. Jedna z následujících hodnot: "Kritická", "Chyba", "Upozornění", "Informační" a "Podrobné" |
| operationId |Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName |Název operace. |
| resourceGroupName |Název skupiny prostředků, která obsahuje prostředek. |
| název_prostředku_prostředku_prostředku_prostředku |Vždy "Microsoft.Resourcehealth/healthevent/action". |
| resourceType | Typ prostředku, který byl ovlivněn událostí stavu prostředku. |
| resourceId | Název ID prostředku pro ovlivněný prostředek. |
| status |Řetězec popisující stav události stavu. Hodnoty mohou být: Aktivní, Vyřešeno, Probíhá, Aktualizováno. |
| subStatus | Obvykle null pro výstrahy. |
| submissionTimestamp |Časové razítko, kdy událost byla k dispozici pro dotazování. |
| subscriptionId |ID předplatného Azure. |
| properties |Sada `<Key, Value>` párů (tj. slovník) popisující podrobnosti události.|
| vlastnosti.název | Uživatelsky přívětivý řetězec, který popisuje stav prostředku. |
| properties.details | Uživatelsky přívětivý řetězec, který popisuje další podrobnosti o události. |
| properties.currentHealthStatus | Aktuální stav prostředku. Jedna z následujících hodnot: "K dispozici", "Nedostupné", "Degradované" a "Neznámé". |
| properties.previousHealthStatus | Předchozí stav prostředku. Jedna z následujících hodnot: "K dispozici", "Nedostupné", "Degradované" a "Neznámé". |
| properties.type | Popis typu události stavu prostředku. |
| properties.cause | Popis příčiny události stavu prostředku. Buď "UserInitiated" a "PlatformInitiated". |


## <a name="alert"></a>Výstrahy
Tato kategorie obsahuje záznam všech aktivací výstrah Azure. Příklad typu události, kterou byste viděli v této kategorii je "CPU % na myVM bylo více než 80 za posledních 5 minut." Různé systémy Azure mají koncept výstrahy – můžete definovat pravidlo určitého druhu a přijímat oznámení, pokud podmínky odpovídají tomuto pravidlu. Pokaždé, když se podporovaný typ výstrahy Azure aktivuje nebo jsou splněny podmínky pro generování oznámení, je do této kategorie protokolu aktivit také posunut záznam aktivace.

### <a name="sample-event"></a>Ukázková událost

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
| Název prvku | Popis |
| --- | --- |
| Volající | Vždy Microsoft.Insights/alertRules |
| Kanály | Vždy "Admin, Operace" |
| Nároky | Objekt blob JSON s hlavním názvem spn (název hlavního názvu služby) nebo typ prostředku modulu výstrah. |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický textový popis události výstrahy. |
| eventDataId |Jedinečný identifikátor události výstrahy |
| category | Vždy "Výstraha" |
| level |Úroveň události. Jedna z následujících hodnot: "Kritická", "Chyba", "Upozornění" a "Informační" |
| resourceGroupName |Název skupiny prostředků pro ovlivněný prostředek, pokud se jedná o upozornění metriky. Pro ostatní typy výstrah je název skupiny prostředků, která obsahuje samotnou výstrahu. |
| název_prostředku_prostředku_prostředku_prostředku |Název poskytovatele prostředků pro ovlivněný prostředek, pokud se jedná o upozornění metriky. Pro ostatní typy výstrah je název zprostředkovatele prostředků pro samotnou výstrahu. |
| resourceId | Název ID prostředku pro ovlivněný prostředek, pokud se jedná o upozornění metriky. Pro ostatní typy výstrah je ID prostředku samotného prostředku výstrahy. |
| operationId |Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName |Název operace. |
| properties |Sada `<Key, Value>` párů (tj. slovník) popisující podrobnosti události. |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: Spuštěno, Probíhá, Úspěšné, Neúspěšné, Aktivní, Vyřešeno. |
| subStatus | Obvykle null pro výstrahy. |
| eventTimestamp |Časové razítko, kdy byla událost generována službou Azure, která zpracovává požadavek odpovídající události. |
| submissionTimestamp |Časové razítko, kdy událost byla k dispozici pro dotazování. |
| subscriptionId |ID předplatného Azure. |

### <a name="properties-field-per-alert-type"></a>Pole Vlastnosti podle typu výstrahy
Pole vlastností bude obsahovat různé hodnoty v závislosti na zdroji události výstrahy. Dvěma běžnými poskytovateli událostí výstrah jsou výstrahy protokolu aktivit a upozornění na metriky.

#### <a name="properties-for-activity-log-alerts"></a>Vlastnosti výstrah protokolu aktivit
| Název prvku | Popis |
| --- | --- |
| properties.subscriptionId | ID předplatného z události protokolu aktivit, která způsobila aktivaci tohoto pravidla výstrahy protokolu aktivit. |
| properties.eventDataId | ID dat události z události protokolu aktivit, které způsobilo aktivaci tohoto pravidla výstrahy protokolu aktivit. |
| properties.resourceGroup | Skupina prostředků z události protokolu aktivit, která způsobila aktivaci tohoto pravidla výstrahy protokolu aktivit. |
| vlastnosti.resourceId | ID prostředku z události protokolu aktivit, které způsobilo aktivaci tohoto pravidla výstrahy protokolu aktivit. |
| properties.eventČasové razítko | Časové razítko události protokolu aktivit, které způsobilo aktivaci tohoto pravidla výstrahy protokolu aktivit. |
| properties.operationName | Název operace z události protokolu aktivit, která způsobila aktivaci tohoto pravidla výstrahy protokolu aktivit. |
| vlastnosti.stav | Stav z události protokolu aktivit, která způsobila aktivaci tohoto pravidla výstrahy protokolu aktivit.|

#### <a name="properties-for-metric-alerts"></a>Vlastnosti pro upozornění na metriky
| Název prvku | Popis |
| --- | --- |
| Vlastnosti. Ruleuri | ID prostředku samotného pravidla upozornění metriky. |
| Vlastnosti. Název_pravidla | Název pravidla upozornění metriky. |
| Vlastnosti. Popis pravidla | Popis pravidla upozornění metriky (jak je definováno v pravidle výstrahy). |
| Vlastnosti. Práh | Prahová hodnota použitá při hodnocení pravidla upozornění metriky. |
| Vlastnosti. WindowSizeInMinutes | Velikost okna použitá při hodnocení pravidla upozornění metriky. |
| Vlastnosti. Agregace | Typ agregace definovaný v pravidle upozornění metriky. |
| Vlastnosti. Operátor | Podmíněný operátor použitý při hodnocení pravidla upozornění metriky. |
| Vlastnosti. Název_metricu | Název metriky použité při hodnocení pravidla upozornění metriky. |
| Vlastnosti. Metricunit | Jednotka metriky pro metriku použitou při hodnocení pravidla upozornění metriky. |

## <a name="autoscale"></a>Automatické škálování
Tato kategorie obsahuje záznam všech událostí souvisejících s provozem modulu automatického škálování na základě všech nastavení automatického škálování, které jste definovali v předplatném. Příkladem typu události, která by se v této kategorii zoátla, je "Akce automatického škálování navertíksek se nezdařila". Pomocí automatického škálování můžete automaticky vertikálně navýšit kapacitu nebo škálovat v počtu instancí v podporovaném typu prostředku na základě denní doby nebo načíst (metrické) údaje pomocí nastavení automatického škálování. Pokud jsou splněny podmínky pro škálování nahoru nebo dolů, budou v této kategorii zaznamenány události zahájení a úspěšné nebo neúspěšné události.

### <a name="sample-event"></a>Ukázková událost
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
| Název prvku | Popis |
| --- | --- |
| Volající | Vždy Microsoft.Insights/autoscaleSettings |
| Kanály | Vždy "Admin, Operace" |
| Nároky | Objekt blob JSON s hlavním názvem spn (název hlavního serveru služby) nebo typ prostředku modulu automatického škálování. |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický textový popis události automatického škálování. |
| eventDataId |Jedinečný identifikátor události automatického škálování |
| level |Úroveň události. Jedna z následujících hodnot: "Kritická", "Chyba", "Upozornění" a "Informační" |
| resourceGroupName |Název skupiny prostředků pro nastavení automatického škálování. |
| název_prostředku_prostředku_prostředku_prostředku |Název zprostředkovatele prostředků pro nastavení automatického škálování. |
| resourceId |ID prostředku nastavení automatického škálování. |
| operationId |Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName |Název operace. |
| properties |Sada `<Key, Value>` párů (tj. slovník) popisující podrobnosti události. |
| Vlastnosti. Popis | Podrobný popis toho, co dělal motor s automatickým škálou. |
| Vlastnosti. Název_zdroje | ID prostředku ovlivněného prostředku (prostředek, na kterém byla provedena akce škálování) |
| Vlastnosti. OldInstancesCount | Počet instancí před akce automatického škálování se projevil. |
| Vlastnosti. NewInstancesCount | Počet instancí po akce automatického škálování se projevil. |
| Vlastnosti. LastScaleActionTime | Časové razítko, kdy došlo k akci automatického škálování. |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: Spuštěno, Probíhá, Úspěšné, Neúspěšné, Aktivní, Vyřešeno. |
| subStatus | Obvykle null pro automatické škálování. |
| eventTimestamp |Časové razítko, kdy byla událost generována službou Azure, která zpracovává požadavek odpovídající události. |
| submissionTimestamp |Časové razítko, kdy událost byla k dispozici pro dotazování. |
| subscriptionId |ID předplatného Azure. |

## <a name="security"></a>Zabezpečení
Tato kategorie obsahuje záznam všechny výstrahy generované Azure Security Center. Příkladem typu události, která se zobrazí v této kategorii, je "Podezřelý soubor dvojité přípony byl spuštěn".

### <a name="sample-event"></a>Ukázková událost
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
| Název prvku | Popis |
| --- | --- |
| Kanály | Vždy "Operace" |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický textový popis události zabezpečení. |
| eventDataId |Jedinečný identifikátor události zabezpečení |
| Eventname |Popisný název události zabezpečení. |
| category | Vždy "Bezpečnost" |
| ID |Jedinečný identifikátor prostředku události zabezpečení |
| level |Úroveň události. Jedna z následujících hodnot: "Kritická", "Chyba", "Upozornění" nebo "Informační" |
| resourceGroupName |Název skupiny prostředků pro prostředek. |
| název_prostředku_prostředku_prostředku_prostředku |Název poskytovatele prostředků pro Azure Security Center. Vždy "Microsoft.Security". |
| resourceType |Typ prostředku, který vygeneroval událost zabezpečení, například Microsoft.Security/locations/alerts |
| resourceId |ID prostředku výstrahy zabezpečení. |
| operationId |Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName |Název operace. |
| properties |Sada `<Key, Value>` párů (tj. slovník) popisující podrobnosti události. Tyto vlastnosti se budou lišit v závislosti na typu výstrahy zabezpečení. Na [této stránce](../../security-center/security-center-alerts-overview.md) naleznete popis typů výstrah, které pocházejí z Centra zabezpečení. |
| Vlastnosti. Závažnosti |Úroveň závažnosti. Možné hodnoty jsou "Vysoká", "Střední" nebo "Nízká". |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: Spuštěno, Probíhá, Úspěšné, Neúspěšné, Aktivní, Vyřešeno. |
| subStatus | Obvykle null pro události zabezpečení. |
| eventTimestamp |Časové razítko, kdy byla událost generována službou Azure, která zpracovává požadavek odpovídající události. |
| submissionTimestamp |Časové razítko, kdy událost byla k dispozici pro dotazování. |
| subscriptionId |ID předplatného Azure. |

## <a name="recommendation"></a>Doporučení
Tato kategorie obsahuje záznam všech nových doporučení, které jsou generovány pro vaše služby. Příkladem doporučení může být "Použít sady dostupnosti pro lepší odolnost proti chybám." Existují čtyři typy událostí doporučení, které lze generovat: vysoká dostupnost, výkon, zabezpečení a optimalizace nákladů. 

### <a name="sample-event"></a>Ukázková událost
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
| Název prvku | Popis |
| --- | --- |
| Kanály | Vždy "Operace" |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický textový popis události doporučení |
| eventDataId | Jedinečný identifikátor události doporučení |
| category | Vždy "Doporučení" |
| ID |Jedinečný identifikátor prostředku události doporučení |
| level |Úroveň události. Jedna z následujících hodnot: "Kritická", "Chyba", "Upozornění" nebo "Informační" |
| operationName |Název operace.  Vždy "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Název skupiny prostředků pro prostředek. |
| název_prostředku_prostředku_prostředku_prostředku |Název zprostředkovatele prostředků pro prostředek, který se vztahuje na toto doporučení, například "MICROSOFT.COMPUTE" |
| resourceType |Název typu prostředku pro prostředek, který se vztahuje na toto doporučení, například "MICROSOFT.COMPUTE/virtualmachines" |
| resourceId |ID prostředku, na který se doporučení vztahuje |
| status | Vždy "Aktivní" |
| submissionTimestamp |Časové razítko, kdy událost byla k dispozici pro dotazování. |
| subscriptionId |ID předplatného Azure. |
| properties |Sada `<Key, Value>` párů (to znamená Dictionary) popisující podrobnosti doporučení.|
| properties.recommendationSchemaVersion| Verze schématu vlastností doporučení publikovaných v položce Protokolu aktivit |
| properties.recommendationCategory | Kategorie doporučení. Možné hodnoty jsou "Vysoká dostupnost", "Výkon", "Zabezpečení" a "Náklady" |
| properties.recommendationImpact| Dopad doporučení. Možné hodnoty jsou "Vysoká", "Střední", "Nízká" |
| properties.recommendationRisk| Riziko doporučení. Možné hodnoty jsou "Chyba", "Upozornění", "Žádné" |

## <a name="policy"></a>Zásada

Tato kategorie obsahuje záznamy o všech operacích akce efektu prováděných zásadami [Azure](../../governance/policy/overview.md). Příklady typů událostí, které se zobrazí v této kategorii, zahrnují _audit_ a _odepřít_. Každá akce přijatá zásadami je modelována jako operace s prostředkem.

### <a name="sample-policy-event"></a>Ukázková událost zásad

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

### <a name="policy-event-property-descriptions"></a>Popisy vlastností události zásad

| Název prvku | Popis |
| --- | --- |
| autorizace | Pole RBAC vlastnosti události. Pro nové prostředky se jedná o akci a rozsah požadavku, který spustil vyhodnocení. Pro existující prostředky akce je "Microsoft.Resources/checkPolicyCompliance/read". |
| Volající | Pro nové prostředky identity, která iniciovala nasazení. U stávajících prostředků identifikátor GUID rp Microsoft Azure Policy Insights rp. |
| Kanály | Události zásad používají pouze kanál Operace. |
| Nároky | Token JWT používaný službou Active Directory k ověření uživatele nebo aplikace k provedení této operace ve Správci prostředků. |
| correlationId | Obvykle identifikátor GUID ve formátu řetězce. Události, které sdílejí correlationId patří do stejné akce uber. |
| description | Toto pole je pro události zásad prázdné. |
| eventDataId | Jedinečný identifikátor události |
| Eventname | "BeginRequest" nebo "EndRequest". "BeginRequest" se používá pro opožděné auditIfNotExists a deployIfNotExists hodnocení a při deployIfNotExists efekt spustí nasazení šablony. Všechny ostatní operace vrátí "EndRequest". |
| category | Deklaruje událost protokolu aktivit jako patřící do "Zásady". |
| eventTimestamp | Časové razítko, kdy byla událost generována službou Azure, která zpracovává požadavek odpovídající události. |
| ID | Jedinečný identifikátor události v konkrétním zdroji |
| level | Úroveň události. Audit používá "Upozornění" a Deny používá "Chyba". AuditIfNotExists nebo deployIfNotExists chyba může generovat "Upozornění" nebo "Chyba" v závislosti na závažnosti. Všechny ostatní akce zásad používají "Informační". |
| operationId | Identifikátor GUID sdílený mezi událostmi, které odpovídají jedné operaci. |
| operationName | Název operace a přímo koreluje s policy efekt. |
| resourceGroupName | Název skupiny prostředků pro vyhodnocovnou prostředek. |
| název_prostředku_prostředku_prostředku_prostředku | Název poskytovatele prostředků pro vyhodnocovaných prostředků. |
| resourceType | Pro nové prostředky je typ, který je vyhodnocován. Pro existující prostředky vrátí "Microsoft.Resources/checkPolicyCompliance". |
| resourceId | ID prostředku hodnoceného prostředku. |
| status | Řetězec popisující stav výsledku vyhodnocení zásad. Většina vyhodnocení zásad vrátí příkaz "Succeeded", ale efekt Odepřít vrátí hodnotu "Failed". Chyby v auditIfNotExists nebo deployIfNotExists také vrátí "Failed". |
| subStatus | Pole je prázdné pro události zásad. |
| submissionTimestamp | Časové razítko, kdy událost byla k dispozici pro dotazování. |
| subscriptionId | ID předplatného Azure. |
| vlastnosti.isComplianceCheck | Vrátí hodnotu "False", když je nasazen nový prostředek nebo jsou aktualizovány vlastnosti Správce prostředků existujícího prostředku. Všechny ostatní [aktivační události hodnocení](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) za následek "True". |
| properties.resourceLocation | Oblast Azure prostředku, který se vyhodnocuje. |
| properties.ancestors | Seznam nadřazených skupin pro správu oddělených čárkami seřazený od přímého rodiče k nejvzdálenějším prarodičům. |
| vlastnosti.zásady | Obsahuje podrobnosti o definici zásady, přiřazení, efekt u a parametry, které toto hodnocení zásad je výsledkem. |
| souvisejícíudálosti | Toto pole je pro události zásad prázdné. |


## <a name="schema-from-storage-account-and-event-hubs"></a>Schéma z účtu úložiště a centra událostí
Při streamování protokolu aktivit Azure do účtu úložiště nebo centra událostí data následují [schéma protokolu prostředků](diagnostic-logs-schema.md). Níže uvedená tabulka obsahuje mapování vlastností ze schématu výše do schématu protokolů prostředků.

> [!IMPORTANT]
> Formát dat protokolu aktivit zapsaných do účtu úložiště se změnil na Řádky JSON v listopadu 1st, 2018. Podrobnosti o této změně formátu najdete [v tématu Příprava změny formátu na protokoly prostředků Azure Monitoru archivované do účtu úložiště.](diagnostic-logs-append-blobs.md)


| Vlastnost schématu protokolů prostředků | Vlastnost schématu rozhraní REST protokolu aktivit | Poznámky |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | subscriptionId, resourceType, resourceGroupName jsou všechny odvozeny z resourceId. |
| operationName | operationName.value |  |
| category | Název části operace | Breakout typu operace - "Zápis"/"Odstranit"/"Akce" |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | description |  |
| durationMs | Není dostupné. | Vždy 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | deklarace identity a autorizační vlastnosti |  |
| Úroveň | Úroveň |  |
| location | Není dostupné. | Místo, kde byla událost zpracována. *Toto není umístění prostředku, ale místo, kde byla událost zpracována. Tato vlastnost bude odebrána v budoucí aktualizaci.* |
| Vlastnosti | properties.eventProperties |  |
| properties.eventCategory | category | Pokud vlastnosti.eventCategory není k dispozici, kategorie je "Administrative" |
| properties.eventName | Eventname |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |

Následuje příklad události pomocí tohoto schématu..

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>Další kroky
* [Další informace o protokolu aktivit](platform-logs-overview.md)
* [Vytvoření diagnostického nastavení pro odesílání protokolu aktivit do pracovního prostoru Log Analytics, úložiště Azure nebo centra událostí](diagnostic-settings.md)

