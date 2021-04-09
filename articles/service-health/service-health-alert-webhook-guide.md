---
title: Odesílání oznámení o Azure Service Health prostřednictvím webhooků
description: Odesílat přizpůsobená oznámení o událostech služby Service Health do stávajícího systému správy problémů.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: a70c4fcd01a1a95c598d980004ee60292a6cf24b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594614"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Použití Webhooku ke konfiguraci oznámení o stavu pro systémy správy problémů

V tomto článku se dozvíte, jak nakonfigurovat výstrahy Azure Service Health pro posílání dat prostřednictvím webhooků do stávajícího systému oznámení.

Můžete nakonfigurovat výstrahy Service Health, abyste na základě textové zprávy nebo e-mailu upozornili na incident služby Azure.

Je ale možné, že už máte k dispozici existující externí systém oznámení, který budete chtít použít. Tento článek popisuje nejdůležitější části datové části Webhooku. A popisuje, jak vytvořit vlastní výstrahy, které vás upozorní, když dojde k relevantním problémům se službou.

Pokud chcete použít přednakonfigurovanou integraci, přečtěte si:
* [Konfigurace upozornění pomocí ServiceNow](service-health-alert-webhook-servicenow.md)
* [Konfigurace upozornění pomocí PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Konfigurace upozornění pomocí OpsGenie](service-health-alert-webhook-opsgenie.md)

**Podívejte se na úvodní video:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Konfigurace vlastního oznámení pomocí Service Health datové části Webhooku
Chcete-li nastavit vlastní integraci webhooků, je nutné analyzovat datovou část JSON, která je odeslána prostřednictvím Service Health oznámení.

Podívejte [se na příklad](../azure-monitor/alerts/activity-log-alerts-webhook.md) `ServiceHealth` datové části Webhooku.

Můžete potvrdit, že se jedná o výstrahu o stavu služby, a to tak, že si prohlédněte `context.eventSource == "ServiceHealth"` . Následující vlastnosti jsou nejrelevantnější:
- **data. Context. activityLog. status**
- **data. Context. activityLog. Level**
- **data. Context. activityLog. subscriptionId**
- **data. Context. activityLog. Properties. title**
- **data. Context. activityLog. Properties. impactStartTime**
- **data. Context. activityLog. Properties. Communication**
- **data. Context. activityLog. Properties. impactedServices**
- **data. Context. activityLog. Properties. trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Vytvoření odkazu na řídicí panel Service Health pro incident
Můžete vytvořit přímý odkaz na Service Health řídicí panel na počítači nebo mobilním zařízení vygenerováním specializované adresy URL. Použijte *trackingId* a první tři a poslední tři číslice vašeho *SubscriptionId* v tomto formátu:

https <i></i> ://App.Azure.com/h/*&lt; trackingId &gt;* / *&lt; první tři a poslední tři číslice čísla SubscriptionId &gt;*

Pokud je vaše *SubscriptionId* například bba14129-e895-429b-8809-278e836ecdb3 a vaše *trackingId* je 0DET-URB, vaše Service Health adresa URL:

https <i></i> ://App.Azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>K detekci závažnosti problému použijte úroveň
Od nejnižší k nejvyšší závažnosti může být vlastnost **Level** v datové části *informativní*, *varovná*, *Chyba* nebo *kritická*.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Analyzujte ovlivněné služby a určete rozsah incidentu.
Výstrahy Service Health můžou informovat o problémech napříč různými oblastmi a službami. Chcete-li získat podrobné informace, je nutné analyzovat hodnotu `impactedServices` .

Obsah, který je uvnitř je řídicí řetězec formátu [JSON](https://json.org/) , který v případě neřídicího znaku obsahuje jiný objekt JSON, který lze pravidelně analyzovat. Například:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Z  je:

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
- "Výstrahy & metriky" v oblasti Austrálie – východ a Austrálie – jihovýchod.
- "App Service" v Austrálii – jihovýchod.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Otestujte integraci Webhooku přes požadavek HTTP POST.

Postupujte takto:

1. Vytvořte datovou část stavu služby, kterou chcete odeslat. Podívejte se na příklad datové části Webhooku Webhooku u [webhooků pro výstrahy protokolu aktivit Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Požadavek HTTP POST vytvoříte takto:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Měla by se zobrazit odpověď "2XX-úspěch".

1. Pokud chcete ověřit, že se integrace úspěšně nastavila, navštivte [PagerDuty](https://www.pagerduty.com/) .

## <a name="next-steps"></a>Další kroky
- Zkontrolujte [schéma Webhooku upozornění protokolu aktivit](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Přečtěte si o [oznámeních o stavu služby](./service-notifications.md).
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/alerts/action-groups.md).
