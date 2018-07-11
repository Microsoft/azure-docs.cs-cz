---
title: Azure schéma událostí protokolu aktivit
description: Pochopení schématu události pro data do protokolu aktivit
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 4/12/2018
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: 123ae27310d70812918f3c81ac3b9a71959a6c2c
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917223"
---
# <a name="azure-activity-log-event-schema"></a>Azure schéma událostí protokolu aktivit
**Protokolu aktivit Azure** je protokol, který nabízí pohled na všechny události na úrovni předplatného, ke kterým došlo v Azure. Tento článek popisuje schéma událostí podle jednotlivých kategorií data. Schéma dat se liší v závislosti na tom, při čtení dat na portálu, Powershellu, rozhraní příkazového řádku, nebo přímo přes rozhraní REST API a [streamovaná data do úložiště nebo Event Hubs pomocí profilu protokolu](./monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). Následující příklady ukazují schématu jako k dispozici prostřednictvím portálu, Powershellu, rozhraní příkazového řádku a rozhraní REST API. Mapování těchto vlastností [Azure diagnostické protokoly schématu](./monitoring-diagnostic-logs-schema.md) je k dispozici na konci tohoto článku.

## <a name="administrative"></a>Správa
Tato kategorie obsahuje záznam všech vytvoření, aktualizace, odstranění a akce operace provést prostřednictvím Resource Manageru. Typy událostí, zobrazí se v této kategorii příklady "vytvořit virtuální počítač" a "odstranit skupinu zabezpečení sítě" každé akce, které uživatele nebo aplikace pomocí Resource Manageru je modelovaná jako operace na konkrétní typ prostředku. Pokud je typ operace zápisu, Delete nebo akce, záznamy o zahájení a úspěchu nebo selhání této operace se zaznamenávají do administrativní kategorie. Administrativní kategorie také zahrnuje všechny změny na řízení přístupu na základě rolí v rámci předplatného.

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
| Autorizace |Objekt BLOB RBAC vlastností události. Obvykle obsahuje vlastnosti "action", "role" a "rozsah". |
| volající |E-mailová adresa uživatele, který provedl operaci, deklarace nebo hlavní název služby deklarace identity na základě dostupnosti. |
| kanály |Jeden z následujících hodnot: "Admin", "Operace" |
| deklarace identity |Token JWT, používá služba Active Directory k ověření uživatele nebo aplikaci k provedení této operace v Resource Manageru. |
| correlationId |Obvykle GUID ve formátu řetězce. Události, které sdílejí ID korelace patřit do stejné akce uber. |
| description |Statický text popisu události. |
| eventDataId |Jedinečný identifikátor události. |
| httpRequest |Objekt BLOB popisující požadavku Http. Obvykle obsahuje "ID žádosti klienta", "clientIpAddress" a "method" (metoda protokolu HTTP. For example, Vložit). |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritický", "Chyba", "Upozornění", "Informační" a "Verbose" |
| resourceGroupName |Název skupiny prostředků pro ovlivněných prostředků. |
| Název resourceProviderName |Název poskytovatele prostředků pro ovlivněný prostředek |
| resourceId |ID prostředku ovlivněných prostředků. |
| operationId |Identifikátor GUID sdílen události, které odpovídají jedné operace. |
| operationName |Název operace |
| properties |Sada `<Key, Value>` páry (tj. slovník) popisující podrobnosti o události. |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: spustit v průběhu, úspěšné, neúspěšné, aktivní, vyřešeno. |
| Podřízený stav |Obvykle volání REST odpovídající stavový kód HTTP, ale může také obsahovat jiných řetězců popisující substatus, jako jsou tyto běžné hodnoty: OK (stavový kód HTTP: 200), které byly vytvořeny (stavový kód HTTP: 201), platné (stavový kód HTTP: 202), žádný obsah (stav protokolu HTTP Kód: 204), chybná žádost (kód stavu HTTP: 400) se nenašla (kód stavu HTTP: 404), konflikt (kód stavu HTTP: 409), se interní chyba serveru (kód stavu HTTP: 500), služba není k dispozici (kód stavu HTTP: 503), vypršel časový limit brány (kód stavu HTTP: 504). |
| eventTimestamp |Časové razítko události vygenerované službou Azure zpracování požadavku odpovídající události. |
| submissionTimestamp |Časové razítko, kdy je k dispozici pro dotazování na události. |
| subscriptionId |ID předplatného Azure. |

## <a name="service-health"></a>Stav služby
Tato kategorie obsahuje záznam všechny incidenty health service, ke kterým došlo v Azure. Je například typ události, kterou byste viděli v této kategorii "SQL Azure v oblasti východní USA má výpadek." Události služby Service health se dělí na pět typy: požaduje se akce, obnovení s asistencí, Incident, údržby, informace nebo zabezpečení a zobrazí pouze v případě, že máte prostředek v rámci předplatného, který bude mít vliv na událost.

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
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Odkazovat [služby oznámení o stavu](./monitoring-service-notifications.md) článku pro dokumentaci o hodnotách ve vlastnostech.

## <a name="alert"></a>Výstrahy
Tato kategorie obsahuje záznam všech aktivací upozornění v Azure. Je například typ události, kterou byste viděli v této kategorii "% využití procesoru na myVM je už více než 80 posledních 5 minut." Výstrahy koncept mají různé systémy pro Azure – můžete definovat pravidla s nějakým a když podmínky odpovídají tímto pravidlem, dostanete oznámení. Pokaždé, když typ podporovaných Azure výstrahy "aktivuje," nebo ke generování oznámení o splnění podmínek, záznamy o aktivaci se nasdílejí také do této kategorie protokolu aktivit.

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
| volající | Vždy Microsoft.Insights/alertRules |
| kanály | Vždy "Admin, operace" |
| deklarace identity | Možné hodnoty jsou "Vysoká", "Střední" nebo "Nízká". |
| correlationId | Obvykle s hodnotou null pro události zabezpečení. |
| description |Tato kategorie obsahuje záznam žádná nová doporučení, které jsou generovány pro vaše služby. |
| eventDataId |Příkladem doporučení může být "používání skupin dostupnosti pro lepší odolnost proti chybám." |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritický", "Chyba", "Upozornění", "Informační" a "Verbose" |
| resourceGroupName |Existují 4 typy událostí doporučení, které mohou být generovány: vysokou dostupnost, výkon, zabezpečení a optimalizaci nákladů. Statický text popisu události doporučení |
| Název resourceProviderName |Jedinečný identifikátor události doporučení. Vždy "doporučení" |
| resourceId | Jedinečný identifikátor události doporučení. Vždy Microsoft.Advisor/generateRecommendations/action"" |
| operationId |Identifikátor GUID sdílen události, které odpovídají jedné operace. |
| operationName |Název operace |
| properties |Sada `<Key, Value>` páry (tj. slovník) popisující podrobnosti o události. |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: spustit v průběhu, úspěšné, neúspěšné, aktivní, vyřešeno. |
| Podřízený stav | Název poskytovatele prostředků pro prostředek, který toto doporučení vztahuje, například "MICROSOFT.COMPUTE" |
| eventTimestamp |Časové razítko události vygenerované službou Azure zpracování požadavku odpovídající události. |
| submissionTimestamp |Časové razítko, kdy je k dispozici pro dotazování na události. |
| subscriptionId |ID předplatného Azure. |

### <a name="properties-field-per-alert-type"></a>Název typu prostředku pro prostředek, který toto doporučení vztahuje, například "MICROSOFT.COMPUTE/virtualmachines"
ID prostředku prostředků, které platí doporučení pro Vždy "aktivní"

#### <a name="properties-for-activity-log-alerts"></a>Sada  páry (tj. slovník) popisující podrobnosti o doporučení.
| Název elementu | Popis |
| --- | --- |
| properties.subscriptionId | properties.recommendationSchemaVersion |
| properties.eventDataId | Publikovaná verze schématu doporučení vlastnosti v položce protokolu aktivit |
| properties.resourceGroup | properties.recommendationCategory |
| Kategorie doporučení. | Možné hodnoty jsou "Vysoké dostupnosti", "Výkonu", "Zabezpečení" a "Cost" |
| properties.eventTimestamp | properties.recommendationImpact |
| properties.operationName | Dopad doporučení. |
| Možné hodnoty jsou "Vysoká", "Střední", "Nízká" | properties.recommendationRisk|

#### <a name="properties-for-metric-alerts"></a>Riziko doporučení.
| Název elementu | Popis |
| --- | --- |
| Možné hodnoty jsou "Chyba", "Upozornění", "None" | Mapování schématu diagnostické protokoly |
| Při streamování protokolu aktivit Azure do účtu úložiště nebo oboru názvů Event Hubs, následuje data Azure diagnostické protokoly schématu. | Tady je mapování vlastností ve schématu nad schématu diagnostických protokolů: |
| Vlastnost schématu diagnostické protokoly | Vlastnost schématu rozhraní REST API pro protokol aktivit |
| ID předplatného, resourceType, název skupiny prostředků jsou odvozeny z ID prostředku. | operationName.value |
| Součást název operace | Užitečných typ operace - "Zápisu" / "odstranit" / "Action" |
| status.Value | substatus.Value |
| Vždy 0 | httpRequest.clientIpAddress |
| properties.MetricName | deklarace identity a autorizace vlastnosti |
| Umístění, kde událost byla zpracována. | Toto není umístění prostředku, ale místo toho pokud událost byla zpracována. |

## <a name="autoscale"></a>Automatické škálování
Tato vlastnost bude v budoucí aktualizaci odebrána. properties.eventProperties properties.eventCategory Pokud properties.eventCategory není k dispozici, je kategorie "Správy"

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
| volající | properties.eventName |
| kanály | Vždy "Admin, operace" |
| deklarace identity | properties.operationId |
| correlationId | Obvykle s hodnotou null pro události zabezpečení. |
| description |Další informace o protokolu aktivit (dříve protokoly auditu) |
| eventDataId |Stream protokolů aktivit Azure do služby Event Hubs |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritický", "Chyba", "Upozornění", "Informační" a "Verbose" |
| resourceGroupName |Název skupiny prostředků pro nastavení automatického škálování. |
| Název resourceProviderName |Název poskytovatele prostředků pro nastavení automatického škálování. |
| resourceId |ID prostředku nastavení automatického škálování. |
| operationId |Identifikátor GUID sdílen události, které odpovídají jedné operace. |
| operationName |Název operace |
| properties |Sada `<Key, Value>` páry (tj. slovník) popisující podrobnosti o události. |
| Vlastnosti. Popis | Podrobný popis činnosti modul automatického škálování. |
| Vlastnosti. ResourceName | ID prostředku ovlivněný prostředek (prostředku, na kterém byla provedena akce škálování) |
| Vlastnosti. OldInstancesCount | Počet instancí před akcí automatického škálování, které vstoupily v platnost. |
| properties.NewInstancesCount | Počet instancí po akci automatické škálování, které vstoupily v platnost. |
| properties.LastScaleActionTime | Časové razítko kdy došlo k akce automatického škálování. |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: spustit v průběhu, úspěšné, neúspěšné, aktivní, vyřešeno. |
| Podřízený stav | Obvykle s hodnotou null pro automatické škálování. |
| eventTimestamp |Časové razítko události vygenerované službou Azure zpracování požadavku odpovídající události. |
| submissionTimestamp |Časové razítko, kdy je k dispozici pro dotazování na události. |
| subscriptionId |ID předplatného Azure. |

## <a name="security"></a>Zabezpečení
Tato kategorie obsahuje záznam všech výstrah generovaných v Azure Security Center. Je například typ události, kterou byste viděli v této kategorii "podezřelou dvojitou příponou souboru provést."

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
| kanály | Vždy "operace" |
| correlationId | Obvykle s hodnotou null pro události zabezpečení. |
| description |Statický text popisu události zabezpečení. |
| eventDataId |Jedinečný identifikátor události zabezpečení. |
| eventName |Popisný název události zabezpečení. |
| id |Jedinečný identifikátor události zabezpečení. |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritický", "Chyba", "Upozornění", "Informační" nebo "Verbose" |
| resourceGroupName |Název skupiny prostředků pro prostředek. |
| Název resourceProviderName |Název poskytovatele prostředků pro Azure Security Center. Vždy "Microsoft.Security". |
| Typ prostředku |Typ prostředku, které vygenerovalo událost zabezpečení, jako je například "Microsoft.Security/locations/alerts" |
| resourceId |ID prostředku dané výstraze zabezpečení. |
| operationId |Identifikátor GUID sdílen události, které odpovídají jedné operace. |
| operationName |Název operace |
| properties |Sada `<Key, Value>` páry (tj. slovník) popisující podrobnosti o události. Tyto vlastnosti budou lišit v závislosti na typu výstrahy zabezpečení. Zobrazit [na této stránce](../security-center/security-center-alerts-type.md) popis typů výstrah, které pocházejí ze služby Security Center. |
| Vlastnosti. Závažnost |Úroveň závažnosti. Možné hodnoty jsou "Vysoká", "Střední" nebo "Nízká". |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: spustit v průběhu, úspěšné, neúspěšné, aktivní, vyřešeno. |
| Podřízený stav | Obvykle s hodnotou null pro události zabezpečení. |
| eventTimestamp |Časové razítko události vygenerované službou Azure zpracování požadavku odpovídající události. |
| submissionTimestamp |Časové razítko, kdy je k dispozici pro dotazování na události. |
| subscriptionId |ID předplatného Azure. |

## <a name="recommendation"></a>Doporučení
Tato kategorie obsahuje záznam žádná nová doporučení, které jsou generovány pro vaše služby. Příkladem doporučení může být "používání skupin dostupnosti pro lepší odolnost proti chybám." Existují 4 typy událostí doporučení, které mohou být generovány: vysokou dostupnost, výkon, zabezpečení a optimalizaci nákladů. 

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
| kanály | Vždy "operace" |
| correlationId | Obvykle s hodnotou null pro události zabezpečení. |
| description |Statický text popisu události doporučení |
| eventDataId | Jedinečný identifikátor události doporučení. |
| category | Vždy "doporučení" |
| id |Jedinečný identifikátor události doporučení. |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritický", "Chyba", "Upozornění", "Informační" nebo "Verbose" |
| operationName |Název operace  Vždy Microsoft.Advisor/generateRecommendations/action""|
| resourceGroupName |Název skupiny prostředků pro prostředek. |
| Název resourceProviderName |Název poskytovatele prostředků pro prostředek, který toto doporučení vztahuje, například "MICROSOFT.COMPUTE" |
| Typ prostředku |Název typu prostředku pro prostředek, který toto doporučení vztahuje, například "MICROSOFT.COMPUTE/virtualmachines" |
| resourceId |ID prostředku prostředků, které platí doporučení pro |
| status | Vždy "aktivní" |
| submissionTimestamp |Časové razítko, kdy je k dispozici pro dotazování na události. |
| subscriptionId |ID předplatného Azure. |
| properties |Sada `<Key, Value>` páry (tj. slovník) popisující podrobnosti o doporučení.|
| properties.recommendationSchemaVersion| Publikovaná verze schématu doporučení vlastnosti v položce protokolu aktivit |
| properties.recommendationCategory | Kategorie doporučení. Možné hodnoty jsou "Vysoké dostupnosti", "Výkonu", "Zabezpečení" a "Cost" |
| properties.recommendationImpact| Dopad doporučení. Možné hodnoty jsou "Vysoká", "Střední", "Nízká" |
| properties.recommendationRisk| Riziko doporučení. Možné hodnoty jsou "Chyba", "Upozornění", "None" |

## <a name="mapping-to-diagnostic-logs-schema"></a>Mapování schématu diagnostické protokoly

Při streamování protokolu aktivit Azure do účtu úložiště nebo oboru názvů Event Hubs, následuje data [Azure diagnostické protokoly schématu](./monitoring-diagnostic-logs-schema.md). Tady je mapování vlastností ve schématu nad schématu diagnostických protokolů:

| Vlastnost schématu diagnostické protokoly | Vlastnost schématu rozhraní REST API pro protokol aktivit | Poznámky |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | ID předplatného, resourceType, název skupiny prostředků jsou odvozeny z ID prostředku. |
| operationName | operationName.value |  |
| category | Součást název operace | Užitečných typ operace - "Zápisu" / "odstranit" / "Action" |
| resultType | status.Value | |
| resultSignature | substatus.Value | |
| resultDescription | description |  |
| durationMs | neuvedeno | Vždy 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | deklarace identity a autorizace vlastnosti |  |
| Úroveň | Úroveň |  |
| location | neuvedeno | Umístění, kde událost byla zpracována. *Toto není umístění prostředku, ale místo toho pokud událost byla zpracována. Tato vlastnost bude v budoucí aktualizaci odebrána.* |
| Vlastnosti | properties.eventProperties |  |
| properties.eventCategory | category | Pokud properties.eventCategory není k dispozici, je kategorie "Správy" |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |


## <a name="next-steps"></a>Další postup
* [Další informace o protokolu aktivit (dříve protokoly auditu)](monitoring-overview-activity-logs.md)
* [Stream protokolů aktivit Azure do služby Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
