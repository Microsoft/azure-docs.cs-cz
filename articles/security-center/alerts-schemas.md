---
title: Schémata pro výstrahy Azure Security Center
description: Tento článek popisuje různá schémata používaná centrem zabezpečení Azure pro výstrahy zabezpečení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062947"
---
# <a name="security-alerts-schemas"></a>Schémata výstrah zabezpečení

Uživatelé standardní vrstvy Azure Security Center obdrží výstrahy zabezpečení, když Security Center detekuje hrozby pro jejich prostředky.

Tyto výstrahy zabezpečení můžete zobrazit na stránkách **Ochrany před hrozbami** centra zabezpečení Azure nebo prostřednictvím externích nástrojů, jako jsou:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) – Siem nativní pro cloud společnosti Microsoft. Sentinel Konektor získá výstrahy z Azure Security Center a odešle je do [pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) pro Azure Sentinel.
- Siem y třetích stran – pomocí nástrojů [pro průběžný export](continuous-export.md) Centra zabezpečení odesílejte data do [centra událostí Azure](https://docs.microsoft.com/azure/event-hubs/). Pak integrujte data centra událostí s siem třetí strany.
- [Rozhraní REST API](https://docs.microsoft.com/rest/api/securitycenter/) – pokud používáte rozhraní REST API pro přístup k výstrahám, podívejte se na [dokumentaci k rozhraní API online upozornění](https://docs.microsoft.com/rest/api/securitycenter/alerts).

Pokud používáte jakékoli programové metody využívat výstrahy, budete potřebovat správné schéma najít pole, která jsou pro vás relevantní. Také pokud exportujete do centra událostí nebo se pokoušíte aktivovat automatizaci pracovního postupu s obecnými konektory HTTP, použijte schémata ke správnému analýzu objektů JSON.

>[!IMPORTANT]
> Schéma se mírně liší pro každý z těchto scénářů, takže nezapomeňte vybrat příslušnou kartu níže.


## <a name="the-schemas"></a>Schémata 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automatizace pracovních postupů a nepřetržitý export do centra událostí](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Ukázka json pro výstrahy odeslané do logic aplikací, Centra událostí a siem třetích stran

Níže najdete schéma událostí výstrah předaných:

- Instance aplikace Azure Logic App, které byly nakonfigurované v automatizaci pracovního postupu Centra zabezpečení
- Azure Event Hub pomocí funkce průběžného exportu Centra zabezpečení

Další informace o funkci automatizace pracovního postupu naleznete v [tématu Automatizace odpovědí na výstrahy a doporučení](workflow-automation.md).
Další informace o průběžném exportu naleznete v [tématu Výstrahy a doporučení pro export](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Pracovní prostory Azure Sentinel a Log Analytics](#tab/schema-sentinel)

Sentinel Konektor získá výstrahy z Azure Security Center a odešle je do pracovního prostoru Analýzy protokolů pro Azure Sentinel. 

Chcete-li vytvořit případ Sentinel nebo incident pomocí výstrah Centra zabezpečení, budete potřebovat schéma pro níže uvedené výstrahy. 

Další informace o Azure [Sentinelu najdete v dokumentaci](https://docs.microsoft.com/azure/sentinel/).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Protokol aktivit Azure](#tab/schema-activitylog)

Azure Security Center audity generované výstrahy zabezpečení jako události v protokolu aktivit Azure.

Události výstrah zabezpečení můžete zobrazit v protokolu aktivit tak, že vyhledáte událost Aktivovat výstrahu, jak je znázorněno:

[![Hledání události Aktivovat výstrahu v protokolu aktivit](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Ukázka JSON pro výstrahy odeslané do protokolu aktivit Azure

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>Datový model schématu

|Pole|Popis|
|----|----|
|**Kanály**|Konstanta, "Operace"|
|**correlationId**|ID výstrahy Centra zabezpečení Azure|
|**Popis**|Popis výstrahy|
|**eventDataId**|Viz correlationId|
|**Eventname**|Dílčí pole value a localizedValue obsahují zobrazovaný název výstrahy.|
|**Kategorie**|Dílčí pole value a localizedValue jsou konstantní - "Zabezpečení"|
|**eventTimestamp**|Časové razítko UTC pro vygenerování výstrahy|
|**Id**|ID úplného upozornění|
|**Úrovni**|Konstanta, "Informační"|
|**operationId**|Viz correlationId|
|**název_operace**|Pole hodnoty je konstantní - "Microsoft.Security/locations/alerts/activate/action" a lokalizovaná hodnota bude "Aktivovat výstrahu" (může být potenciálně lokalizována par uživatelského národního prostředí)|
|**resourceGroupName**|Bude obsahovat název skupiny prostředků.|
|**název_prostředku_prostředku_prostředku_prostředku**|Dílčí pole value a localizedValue jsou konstantní - Microsoft.Security|
|**resourceType**|Dílčí pole value a localizedValue jsou konstantní - "Microsoft.Security/locations/alerts"|
|**Resourceid**|Plně kvalifikované ID prostředků Azure|
|**status**|Dílčí pole value a localizedValue jsou konstantní - "Aktivní"|
|**subStatus**|Dílčí pole value a localizedValue jsou prázdná.|
|**submissionTimestamp**|Časové razítko UTC odeslání události do protokolu aktivit|
|**subscriptionId**|ID předplatného ohroženého prostředku|
|**Vlastnosti**|JSON pytel dalších vlastností týkajících se výstrahy. Ty se mohou změnit z jedné výstrahy na druhou, ale ve všech výstrahách se zobrazí následující pole:<br>- závažnost: Závažnost útoku<br>- compromisedEntity: Název ohroženého prostředku<br>- nápravná opatření: Řada nápravných opatření, která mají být přijata<br>- záměr: Záměr smrtícího řetězce výstrahy. Možné záměry jsou popsány v [tabulce Záměry.](alerts-reference.md#intentions)|
|**souvisejícíudálosti**|Konstanta - prázdné pole|
|||





### <a name="ms-graph-api"></a>[Rozhraní API ms grafu](#tab/schema-graphapi)

Microsoft Graph je brána k datům a inteligenci v Microsoftu 365. Poskytuje jednotný model programovatelnosti, který můžete použít pro přístup k obrovskémnožství dat v Office 365, Windows 10 a Enterprise Mobility + Security. Využijte velké množství dat v Microsoft Graphu k vytváření aplikací pro organizace a spotřebitele, kteří komunikují s miliony uživatelů.

Schéma a reprezentace JSON pro výstrahy zabezpečení odeslané do grafu MS jsou k dispozici v [dokumentaci k aplikaci Microsoft Graph](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0).

---


## <a name="next-steps"></a>Další kroky

Tento článek popisuje schémata, která nástroje Azure Security Center pro ochranu před hrozbami používají při odesílání informací o výstrahách zabezpečení.

Další informace o způsobech přístupu k výstrahám zabezpečení mimo Centrum zabezpečení naleznete na následujících stránkách:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) – SIEM nativní pro cloud společnosti Microsoft
- [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) – plně spravovaná služba microsoftu pro přijímaní dat v reálném čase
- [Funkce nepřetržitého exportu](continuous-export.md) centra zabezpečení
- [Pracovní prostory Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) – Azure Monitor ukládá data protokolu do pracovního prostoru Log Analytics, kontejneru, který obsahuje data a informace o konfiguraci
