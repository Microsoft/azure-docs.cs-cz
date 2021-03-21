---
title: Schémata pro výstrahy Azure Security Center
description: Tento článek popisuje různá schémata používaná nástrojem Azure Security Center pro výstrahy zabezpečení.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 55f8d37d435aa8adeb4d97246ce7b2c7811140be
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557994"
---
# <a name="security-alerts-schemas"></a>Schémata výstrah zabezpečení

Pokud je ve vašem předplatném Azure Defender povolený, obdržíte výstrahy zabezpečení, když Security Center detekuje hrozby jejich prostředků.

Tyto výstrahy zabezpečení můžete zobrazit na stránkách **ochrany před internetovými útoky** v Azure Security Center nebo prostřednictvím externích nástrojů, jako jsou:

- [Azure Sentinel](../sentinel/index.yml) – cloudová nativní Siem Microsoftu Konektor Sentinel získá výstrahy od Azure Security Center a pošle je do [pracovního prostoru Log Analytics](../azure-monitor/logs/quick-create-workspace.md) pro Sentinel Azure.
- Systémů Siem třetích stran – odesílá data do [Azure Event Hubs](../event-hubs/index.yml). Potom Integrujte data centra událostí s SIEM třetí strany. Další informace o [výstrahách služby streamování pro Siem, společnosti nebo řešení správy služeb](export-to-siem.md)v oddělení IT.
- [REST API](/rest/api/securitycenter/) – pokud používáte REST API k přístupu k výstrahám, přečtěte si [dokumentaci k rozhraní API pro online výstrahy](/rest/api/securitycenter/alerts).

Pokud používáte nějaké programové metody pro využívání výstrah, budete potřebovat správné schéma, abyste našli pole, která jsou pro vás důležitá. Pokud exportujete do centra událostí nebo se pokoušíte aktivovat automatizaci pracovního postupu pomocí obecných konektorů HTTP, použijte schémata k správné analýze objektů JSON.

>[!IMPORTANT]
> Schéma se u každého z těchto scénářů mírně liší, proto se ujistěte, že jste vybrali příslušnou kartu.


## <a name="the-schemas"></a>Schémata 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automatizace pracovního postupu a průběžný export do centra událostí](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Ukázkový JSON pro výstrahy odeslané do Logic Apps, centra událostí a systémů Siem třetích stran

Níže najdete schéma událostí výstrah předaných do:

- Instance aplikace logiky Azure, které byly nakonfigurovány v automatizaci pracovního postupu Security Center
- Azure Event hub s využitím funkce průběžného exportu Security Center

Další informace o funkci automatizace pracovních postupů najdete v tématu [automatizace odpovědí na Security Center triggery](workflow-automation.md).

Další informace o průběžném exportu najdete v tématu [průběžné exportování dat Security Center](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Pracovní prostory Azure Sentinel a Log Analytics](#tab/schema-sentinel)

Konektor Sentinel získá výstrahy od Azure Security Center a pošle je do pracovního prostoru Log Analytics pro Sentinel Azure. 

K vytvoření případu nebo incidentu Sentinel pomocí výstrah Security Center budete potřebovat schéma pro tyto výstrahy zobrazené níže. 

Další informace o Sentinel Azure najdete v [dokumentaci](../sentinel/index.yml).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Protokol aktivit Azure](#tab/schema-activitylog)

Azure Security Center audity vygenerovaly výstrahy zabezpečení jako události v protokolu aktivit Azure.

Události výstrahy zabezpečení v protokolu aktivit můžete zobrazit tak, že vyhledáte událost aktivovat výstrahu, jak je znázorněno níže:

[![Hledání protokolu aktivit pro událost aktivace výstrahy](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Ukázkový JSON pro výstrahy odeslané do protokolu aktivit Azure

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

|Pole|Description|
|----|----|
|**barev**|Konstanta, "operace"|
|**ID**|ID výstrahy Azure Security Center|
|**název**|Popis výstrahy|
|**eventDataId**|Zobrazit ID korelace|
|**eventName**|Podpole Value a localizedValue obsahují zobrazované jméno výstrahy.|
|**kategorií**|Podpole Value a localizedValue jsou konstanta-"Security".|
|**eventTimestamp**|Časové razítko UTC pro vygenerování výstrahy|
|**id**|Plně kvalifikované ID výstrahy|
|**úroveň**|Konstanta, "informativní"|
|**operationId**|Zobrazit ID korelace|
|**operationName**|Pole hodnota je konstanta-"Microsoft. Security/Locations/Alerts/Activate/Action", a lokalizovaná hodnota bude "aktivovat výstrahu" (může být potenciálně lokalizována do národního prostředí uživatele).|
|**resourceGroupName**|Bude obsahovat název skupiny prostředků.|
|**resourceProviderName**|Dílčí pole Value a localizedValue jsou konstanta-"Microsoft. Security".|
|**resourceType**|Podpole hodnoty a localizedValue jsou konstanta – "Microsoft. Security/Locations/Alerts"|
|**Prostředku**|Plně kvalifikované ID prostředku Azure|
|**stav**|Podpole hodnoty a localizedValue jsou konstanta – "aktivní".|
|**subStatus**|Podpole hodnoty a localizedValue jsou prázdná.|
|**submissionTimestamp**|Časové razítko UTC odeslání události do protokolu aktivit|
|**subscriptionId**|ID předplatného napadeného prostředku|
|**vlastnosti**|Kontejner JSON dalších vlastností, které se týkají výstrahy. Ty se můžou změnit z jedné výstrahy na druhou, ale v následujících polích se zobrazí všechny výstrahy:<br>-závažnost: závažnost útoku<br>-compromisedEntity: název napadeného prostředku<br>-remediationSteps: pole nápravných kroků, které mají být provedeny<br>-záměr: záměr s dezaktivačním řetězem výstrahy. Možné záměry jsou zdokumentovány v [tabulce záměry](alerts-reference.md#intentions)|
|**relatedEvents**|Konstantní prázdné pole|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

Microsoft Graph je brána k datům a inteligentním Microsoft 365. Poskytuje jednotný model programovatelnosti, který můžete použít pro přístup k obrovskému množství dat v Microsoft 365, Windows 10 a Enterprise Mobility + Security. Využijte spoustu dat v Microsoft Graph k sestavování aplikací pro organizace a zákazníky, kteří komunikují s miliony uživatelů.

Schéma a reprezentace JSON pro výstrahy zabezpečení odeslané do služby MS Graph jsou k dispozici v [dokumentaci Microsoft Graph](/graph/api/resources/alert).

---


## <a name="next-steps"></a>Další kroky

Tento článek popisuje schémata, která Azure Security Center nástroje ochrany před hrozbami, které se používají při odesílání informací o výstrahách zabezpečení.

Další informace o způsobech přístupu k výstrahám zabezpečení z vnějších Security Center najdete na následujících stránkách:

- [Azure Sentinel](../sentinel/index.yml) – cloudová nativní Siem Microsoftu
- [Azure Event Hubs](../event-hubs/index.yml) – plně spravovaná služba pro přijímání dat v reálném čase od Microsoftu
- [Průběžný export Security Center dat](continuous-export.md)
- [Log Analytics pracovní prostory](../azure-monitor/logs/quick-create-workspace.md) – Azure monitor ukládá data protokolu v pracovním prostoru Log Analytics, kontejneru, který obsahuje informace o datech a konfiguraci.