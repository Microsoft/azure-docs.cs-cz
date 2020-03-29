---
title: Odesílání oznámení o stavu služby Azure prostřednictvím webhooků
description: Odešlete přizpůsobená oznámení o událostech stavu služby do stávajícího systému správy problémů.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062860"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Konfigurace oznámení o stavu pro systémy správy problémů pomocí webového háku

Tento článek ukazuje, jak nakonfigurovat výstrahy stavu služby Azure pro odesílání dat prostřednictvím webhooků do stávajícího oznamovacího systému.

Můžete nakonfigurovat výstrahy stavu služby, aby vás upozornili textovou zprávou nebo e-mailem, když se vás incident služby Azure týká.

Ale můžete již mít existující externí oznamovací systém na místě, které chcete použít. Tento článek identifikuje nejdůležitější části datové části webhooku. A popisuje, jak vytvořit vlastní výstrahy, které vás upozorní, když dojde k příslušným problémům se službami.

Pokud chcete použít předkonfigurovanou integraci, přečtěte si:
* [Konfigurace výstrah pomocí služby ServiceNow](service-health-alert-webhook-servicenow.md)
* [Konfigurace výstrah pomocí služby PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Konfigurace výstrah pomocí OpsGenie](service-health-alert-webhook-opsgenie.md)

**Podívejte se na úvodní video:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Konfigurace vlastního oznámení pomocí datové části webhooku service health
Chcete-li nastavit vlastní integraci webhooku, musíte analyzovat datovou část JSON, která je odeslána prostřednictvím oznámení o stavu služby.

Podívejte se [na příklad](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` datové části webhooku.

Můžete potvrdit, že se jedná o výstrahu stavu služby při pohledu na `context.eventSource == "ServiceHealth"`. Nejdůležitější jsou následující vlastnosti:
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Vytvoření odkazu na řídicí panel Stav služby pro incident
Pomocí specializované adresy URL můžete vytvořit přímý odkaz na řídicí panel Stavu služby na počítači nebo mobilním zařízení. Použijte *trackingId* a první tři a poslední tři číslice *vašeho subscriptionId* v tomto formátu:

<i></i>https://app.azure.com/h/*&lt;trackingId&gt;*/*&lt;první tři a poslední tři&gt; číslice subscriptionId*

Například pokud vaše *subscriptionId* je bba14129-e895-429b-8809-278e836ecdb3 a *vaše trackingId* je 0DET-URB, vaše adresa URL stavu služby je:

https<i></i>://app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Pomocí úrovně zjistíte závažnost problému
Od nejnižší po nejvyšší závažnost může být **vlastností level** v datové části *informační*, *upozornění*, *chyba*nebo *kritická*.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Analyzovat ovlivněné služby k určení rozsahu incidentu
Výstrahy stavu služby vás mohou informovat o problémech ve více oblastech a službách. Chcete-li získat úplné podrobnosti, je `impactedServices`třeba analyzovat hodnotu .

Obsah, který je uvnitř je uvozený řetězec [JSON,](https://json.org/) který při neuvozenou, obsahuje jiný objekt JSON, který lze pravidelně analyzovat. Například:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

se stává:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Tento příklad ukazuje problémy pro:
- "Upozornění & metriky" v Austrálii východ a Austrálie jihovýchod.
- "App Service" v Jihovýchodní Austrálie.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Testování integrace webhooku prostřednictvím požadavku HTTP POST

Postupujte následovně:

1. Vytvořte datovou část stavu služby, kterou chcete odeslat. Podívejte se na příklad stavu služby webhookužitečné datové části na [Webhooks pro výstrahy protokolu aktivit Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Vytvořte požadavek HTTP POST následujícím způsobem:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Měli byste obdržet odpověď "2XX - Successful".

1. Přejděte na [StránkrDuty](https://www.pagerduty.com/) a potvrďte, že vaše integrace byla úspěšně nastavena.

## <a name="next-steps"></a>Další kroky
- Zkontrolujte [webhookové schéma výstrahy protokolu aktivit](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Další informace o [oznámeních o stavu služby](../azure-monitor/platform/service-notifications.md).
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/platform/action-groups.md).