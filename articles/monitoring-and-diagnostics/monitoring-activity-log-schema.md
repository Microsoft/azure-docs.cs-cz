---
title: Azure schématu aktivity protokolu události
description: Pochopení událostí schéma pro data do protokolu činnosti
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 4/12/2018
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: f6f6c59195fdc79959a1964c1f2770c3b6a68b22
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264547"
---
# <a name="azure-activity-log-event-schema"></a>Azure schématu aktivity protokolu události
**Protokol činnosti Azure** protokolu, který poskytuje přehled o všech událostí na úrovni předplatného, k nimž došlo v Azure. Tento článek popisuje schématu události podle kategorie dat.

## <a name="administrative"></a>Správa
Tato kategorie obsahuje záznam všech vytvořit, operace aktualizace, odstranění a akce provést pomocí Správce prostředků. Typy událostí, zobrazí se v této kategorii příklady "vytvořit virtuální počítač" a "odstranit skupinu zabezpečení sítě" každou akci provedenou uživatele nebo aplikace pomocí Resource Manager je modelovaná jako operace na konkrétní typ prostředku. Pokud je typ operaci zápisu, Delete nebo akce, záznamy start a úspěch nebo selhání této operace se zaznamenávají do administrativní kategorie. Administrativní kategorie také zahrnuje všechny změny na řízení přístupu na základě rolí v předplatném.

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
| Autorizace |Objekt BLOB RBAC vlastností události. Obvykle zahrnuje vlastnosti "action", "role" a "obor". |
| volající |E-mailovou adresu uživatele, který se má provést operace, deklarace hlavní název uživatele nebo název SPN deklarace identity na základě dostupnosti. |
| kanály |Jeden z následujících hodnot: "Admin", "Operace" |
| Deklarace identity |Token JWT, používá služba Active Directory k ověření uživatele nebo aplikace k provedení této operace v správce prostředků. |
| correlationId |Obvykle GUID ve formátu řetězce. Události, které sdílejí correlationId patřit do stejné uber akce. |
| description |Statický text popisu události. |
| eventDataId |Jedinečný identifikátor události. |
| httpRequest |Objekt BLOB popisující požadavek Http. Obvykle zahrnuje "clientRequestId", "clientIpAddress" a "metodu" (metoda HTTP. For example, PUT). |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritická", "Chyba", "Upozornění", "Informační" a "Podrobné" |
| resourceGroupName |Název skupiny prostředků pro prostředek dopad. |
| resourceProviderName |Název zprostředkovatele prostředků pro prostředek dopad |
| resourceId |Id prostředku ovlivněné prostředku. |
| operationId |Identifikátor GUID sdílen události, které odpovídají jedné operace. |
| operationName |Název operace |
| properties |Sada `<Key, Value>` páry (tedy slovník) popisující podrobnosti o události. |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: spuštění v průběhu, bylo úspěšné, neúspěšné, aktivní, vyřešeno. |
| Podřízený stav |Obvykle stavový kód HTTP REST odpovídající volání, ale můžou taky patřit jiných řetězců popisující podřízeného stavu, jako jsou tyto hodnoty běžné: OK (stavový kód HTTP: 200), které byly vytvořeny (stavový kód HTTP: 201), platné (stavový kód HTTP: 202), ne obsahu (stavový kód HTTP: 204), chybný požadavek (stavový kód HTTP: 400), nebyl nalezen (stavový kód HTTP: 404), konflikt (kód stavu HTTP : 409), vnitřní chybu serveru (kód stavu HTTP: 500), služba není k dispozici (kód stavu HTTP: 503), vypršel časový limit brány (kód stavu HTTP: 504). |
| eventTimestamp |Časové razítko při zpracování požadavku odpovídající události služby Azure vygenerovalo událost. |
| submissionTimestamp |Časové razítko, když jsou události dostupné pro zadávání dotazů. |
| subscriptionId |ID předplatného Azure. |

## <a name="service-health"></a>Stav služby
Tato kategorie obsahuje záznam všechny služby stavu incidentů, kterým došlo v Azure. Je například typ události, které se zobrazí se v této kategorii "SQL Azure v oblasti Východ USA dochází k výpadku." Události stavu služby existují ve pět variantách: je vyžadována akce, jíž obnovení, Incident, údržby, informace nebo zabezpečení a objeví jenom tehdy, pokud máte prostředku v odběru, který bude mít vliv události.

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
Odkazovat [oznámení o stavu služby](./monitoring-service-notifications.md) článku pro dokumentaci o hodnotách ve vlastnostech.

## <a name="alert"></a>Výstrahy
Tato kategorie obsahuje záznam všech aktivací Azure výstrah. Je například typ události, které se zobrazí se v této kategorii "% využití procesoru na Můjvp je už více než 80 za posledních 5 minut." Výstrahy koncept mají různé systémy Azure – můžete definovat pravidla nějaká a přijímat oznámení v případě podmínky odpovídají daného pravidla. Pokaždé, když podporovaných Azure typu výstrahy, aktivuje,' nebo ke splnění podmínek pro generování oznámení, záznam aktivace také vložena do této kategorie protokolu činnosti.

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
| Deklarace identity | Objekt blob JSON s typem hlavního názvu služby (hlavní název služby), nebo prostředek výstrahy stroje. |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický text popis výstrahy události. |
| eventDataId |Jedinečný identifikátor výstrahy události. |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritická", "Chyba", "Upozornění", "Informační" a "Podrobné" |
| resourceGroupName |Název skupiny prostředků pro ovlivněné prostředek, pokud se jedná o metriky výstrahu. Pro ostatní typy výstrah to je název skupiny prostředků, který obsahuje výstrahy samotné. |
| resourceProviderName |Název zprostředkovatele prostředků pro ovlivněné prostředek, pokud se jedná o metriky výstrahu. Pro ostatní typy výstrah to je název poskytovatele prostředků pro výstrahu sám sebe. |
| resourceId | Název ID prostředku pro ovlivněné prostředek, pokud se jedná o metriky výstrahu. Pro ostatní typy výstrah to je ID prostředku výstrahy prostředku sám sebe. |
| operationId |Identifikátor GUID sdílen události, které odpovídají jedné operace. |
| operationName |Název operace |
| properties |Sada `<Key, Value>` páry (tedy slovník) popisující podrobnosti o události. |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: spuštění v průběhu, bylo úspěšné, neúspěšné, aktivní, vyřešeno. |
| Podřízený stav | Obvykle se hodnota null pro výstrahy. |
| eventTimestamp |Časové razítko při zpracování požadavku odpovídající události služby Azure vygenerovalo událost. |
| submissionTimestamp |Časové razítko, když jsou události dostupné pro zadávání dotazů. |
| subscriptionId |ID předplatného Azure. |

### <a name="properties-field-per-alert-type"></a>Vlastnosti pole na každý typ výstrahy
Vlastnosti pole bude obsahovat různé hodnoty v závislosti na zdroj výstrahy událostí. Dva poskytovatelé běžné výstrah událostí jsou výstrahy protokol aktivit a metriky výstrahy.

#### <a name="properties-for-activity-log-alerts"></a>Vlastnosti výstrah protokol aktivit
| Název elementu | Popis |
| --- | --- |
| properties.subscriptionId | ID předplatného z protokolu událostí aktivity, který chybu způsobil aktivity protokolu pravidlo výstrahy aktivovat. |
| properties.eventDataId | ID data události z protokolu událostí aktivity, který chybu způsobil aktivity protokolu pravidlo výstrahy aktivovat. |
| properties.resourceGroup | Skupina prostředků z protokolu událostí aktivity, který chybu způsobil aktivity protokolu pravidlo výstrahy aktivovat. |
| properties.resourceId | ID prostředku z protokolu událostí aktivity, který chybu způsobil aktivity protokolu pravidlo výstrahy aktivovat. |
| properties.eventTimestamp | Časové razítko události protokolu události aktivity, který chybu způsobil aktivity protokolu pravidlo výstrahy aktivovat. |
| properties.operationName | Název operace z protokolu událostí aktivity, který chybu způsobil aktivity protokolu pravidlo výstrahy aktivovat. |
| Properties.status | Stav z protokolu událostí aktivity, který chybu způsobil aktivity protokolu pravidlo výstrahy aktivovat.|

#### <a name="properties-for-metric-alerts"></a>Vlastnosti metriky výstrah
| Název elementu | Popis |
| --- | --- |
| Vlastnosti. RuleUri | ID prostředku metriky pravidlo výstrahy sám sebe. |
| Vlastnosti. RuleName | Název metriky pravidlo výstrahy. |
| Vlastnosti. RuleDescription | Popis metriky pravidlo výstrahy (jak je definována v pravidlu upozornění). |
| Vlastnosti. Prahová hodnota | Prahová hodnota použít při vyhodnocení metriky pravidlo výstrahy. |
| Vlastnosti. WindowSizeInMinutes | Velikost okna použít při vyhodnocení metriky pravidlo výstrahy. |
| Vlastnosti. Agregace | Typ agregace definované v metriky pravidlo výstrahy. |
| Vlastnosti. Operátor | Podmíněný operátor použít při vyhodnocení metriky pravidlo výstrahy. |
| properties.MetricName | Název metriky metriky použít při vyhodnocení metriky pravidlo výstrahy. |
| Vlastnosti. MetricUnit | Metriky jednotky pro metriku použít při vyhodnocení metriky pravidlo výstrahy. |

## <a name="autoscale"></a>Automatické škálování
Tato kategorie obsahuje záznam všechny události související s operací škálování stroje podle nastavení automatického škálování, které jste definovali ve vašem předplatném. Je například typ události, které se zobrazí se v této kategorii "Škálování rozšiřování škálování využívajících akce se nezdařila." Použití automatického škálování, můžete automaticky škálovat nebo škálovat počet instancí v typu prostředku podporované na základě času, den nebo zatížení (metriky) dat, na které se používá nastavení automatického škálování. Pokud jsou podmínky splněny, aby škálování nahoru nebo dolů, spuštění a úspěšné nebo neúspěšné události budou popsané v této kategorii.

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
| volající | Vždy Microsoft.Insights/autoscaleSettings |
| kanály | Vždy "Admin, operace" |
| Deklarace identity | Objekt blob JSON s typem hlavního názvu služby (hlavní název služby), nebo prostředek stroje škálování. |
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický text popis události škálování. |
| eventDataId |Jedinečný identifikátor události škálování. |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritická", "Chyba", "Upozornění", "Informační" a "Podrobné" |
| resourceGroupName |Název skupiny prostředků pro nastavení automatického škálování. |
| resourceProviderName |Název zprostředkovatele prostředků pro nastavení automatického škálování. |
| resourceId |Id prostředku nastavení automatického škálování. |
| operationId |Identifikátor GUID sdílen události, které odpovídají jedné operace. |
| operationName |Název operace |
| properties |Sada `<Key, Value>` páry (tedy slovník) popisující podrobnosti o události. |
| Vlastnosti. Popis | Podrobný popis činnosti modul škálování. |
| Vlastnosti. ResourceName | ID prostředku ovlivněné prostředku (prostředků, na kterém byla provedena akce škálování) |
| Vlastnosti. OldInstancesCount | Počet instancí předtím, než platnost trvala daná akce škálování. |
| properties.NewInstancesCount | Počet instancí po vliv trvala daná akce škálování. |
| properties.LastScaleActionTime | Časové razítko při akci škálování došlo k chybě. |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: spuštění v průběhu, bylo úspěšné, neúspěšné, aktivní, vyřešeno. |
| Podřízený stav | Obvykle se hodnota null pro škálování. |
| eventTimestamp |Časové razítko při zpracování požadavku odpovídající události služby Azure vygenerovalo událost. |
| submissionTimestamp |Časové razítko, když jsou události dostupné pro zadávání dotazů. |
| subscriptionId |ID předplatného Azure. |

## <a name="security"></a>Zabezpečení
Tato kategorie obsahuje záznam všech výstrah generovaných Azure Security Center. Je například typ události, které se zobrazí se v této kategorii "soubor podezřelé dvojité rozšíření spustit."

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
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický text popis události zabezpečení. |
| eventDataId |Jedinečný identifikátor události zabezpečení. |
| eventName |Popisný název události zabezpečení. |
| id |Jedinečný identifikátor události zabezpečení. |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritická", "Chyba", "Upozornění", "Informační" nebo "Podrobné" |
| resourceGroupName |Název skupiny prostředků pro prostředek. |
| resourceProviderName |Název zprostředkovatele prostředků pro Azure Security Center. Vždy "Microsoft.Security". |
| Typ prostředku |Typ prostředku, který vygeneroval událostí zabezpečení, jako je například "Microsoft.Security/locations/alerts" |
| resourceId |Id prostředku výstrahy zabezpečení. |
| operationId |Identifikátor GUID sdílen události, které odpovídají jedné operace. |
| operationName |Název operace |
| properties |Sada `<Key, Value>` páry (tedy slovník) popisující podrobnosti o události. Tyto vlastnosti budou lišit v závislosti na typu výstrahy zabezpečení. V tématu [tuto stránku](../security-center/security-center-alerts-type.md) popis typy výstrah, které pocházejí z Security Center. |
| Vlastnosti. Závažnost |Úroveň závažnosti. Možné hodnoty jsou "Vysoká", "Střední" nebo "Nízká". |
| status |Řetězec popisující stav operace. Některé běžné hodnoty jsou: spuštění v průběhu, bylo úspěšné, neúspěšné, aktivní, vyřešeno. |
| Podřízený stav | Obvykle se hodnota null pro události zabezpečení. |
| eventTimestamp |Časové razítko při zpracování požadavku odpovídající události služby Azure vygenerovalo událost. |
| submissionTimestamp |Časové razítko, když jsou události dostupné pro zadávání dotazů. |
| subscriptionId |ID předplatného Azure. |

## <a name="recommendation"></a>Doporučení
Tato kategorie obsahuje záznam všech nová doporučení, které jsou generovány pro vaše služby. Příkladem doporučení by "použijte sady dostupnosti pro lepší odolnost proti chybám." Existují 4 typy událostí doporučení, které lze generovat: vysokou dostupnost, výkon, zabezpečení a optimalizaci nákladů. 

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
| correlationId | Identifikátor GUID ve formátu řetězce. |
| description |Statický text popis události doporučení |
| eventDataId | Jedinečný identifikátor události doporučení. |
| category | Vždy "doporučení" |
| id |Jedinečný identifikátor události doporučení. |
| úroveň |Úroveň události. Jeden z následujících hodnot: "Kritická", "Chyba", "Upozornění", "Informační" nebo "Podrobné" |
| operationName |Název operace  Vždy "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Název skupiny prostředků pro prostředek. |
| resourceProviderName |Název zprostředkovatele prostředků pro prostředek, který toto doporučení se vztahují na, jako je například "MICROSOFT.COMPUTE" |
| Typ prostředku |Název typu prostředku pro prostředek, který toto doporučení se vztahují na, jako je například "MICROSOFT.COMPUTE/virtualmachines" |
| resourceId |Id prostředku prostředku, který doporučení platí pro |
| status | Vždy "aktivní" |
| submissionTimestamp |Časové razítko, když jsou události dostupné pro zadávání dotazů. |
| subscriptionId |ID předplatného Azure. |
| properties |Sada `<Key, Value>` páry (tedy slovník) popisující podrobnosti o doporučení.|
| properties.recommendationSchemaVersion| Verze schématu z vlastností doporučení publikované v položce protokolu aktivit |
| properties.recommendationCategory | Kategorie doporučení. Možné hodnoty jsou "Vysoké dostupnosti", "Výkon", "Zabezpečení" a "Náklady" |
| properties.recommendationImpact| Dopad doporučení. Možné hodnoty jsou "Vysoká", "Střední", "Nízká" |
| properties.recommendationRisk| Riziko doporučení. Možné hodnoty jsou "Chyba", "Upozornění", "Žádný" |



## <a name="next-steps"></a>Další postup
* [Další informace o protokolu činnosti (dříve protokoly auditu)](monitoring-overview-activity-logs.md)
* [Datový proud protokolu Azure činnosti do centra událostí](monitoring-stream-activity-logs-event-hubs.md)
