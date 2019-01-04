---
title: Konfigurace oznámení o stavu pro existující systémy pro správu problémů pomocí webhooku | Dokumentace Microsoftu
description: Získáte přizpůsobená oznámení o události služby service health na existující systému pro správu problémů.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/27/2018
ms.author: shtabriz
ms.openlocfilehash: 740adefbb8bfa53e96b5ed0e1c7181abf22f1490
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580264"
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Konfigurace oznámení o stavu pro existující systémy pro správu problémů pomocí webhooku

Tento článek ukazuje postup při konfiguraci upozornění služba health k odesílání dat prostřednictvím Webhooků do stávajícího systému oznámení.

Upozornění na stav služby v současné době můžete nakonfigurovat tak, aby při Incident služby Azure setkáte, dostanete textovou zprávu nebo e-mailem.
Ale již můžete mít existující externí oznámení systému v místě, ke kterému chcete použít.
Tento dokument ukazuje nejdůležitější části datová část webhooku a jak můžete vytvářet vlastní výstrahy chcete dostávat oznámení, kdy vás ovlivňují problémy se službou.

Pokud chcete použít předkonfigurované integraci, naleznete v tématu Postupy:
* [Konfigurace upozornění pomocí ServiceNow](service-health-alert-webhook-servicenow.md)
* [Konfigurace upozornění pomocí PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Konfigurace výstrah s použitím OpsGenie](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>Konfigurace vlastní oznámení pomocí datová část webhooku stavu služby
Pokud chcete nastavit vlastní integraci vlastních webhooků, potřebujete analyzovat datovou část JSON, který se odesílá při oznámení o stavu služby.

Podívejte se [tady s ukázkovým](../azure-monitor/platform/activity-log-alerts-webhook.md) co `ServiceHealth` datová část webhooku bude vypadat takto.

Toto je upozornění na stav služby zobrazením, můžete zjistit `context.eventSource == "ServiceHealth"`. Tady jsou vlastnosti, které jsou nejrelevantnější pro zpracování příjmu:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-the-service-health-dashboard-for-an-incident"></a>Vytváří se přímý odkaz na řídicí panel stavu služeb pro incident
Můžete vytvořit přímý odkaz na řídicí panel Service Health na ploše nebo mobilní vygenerováním specializované adresy URL. Použití `trackingId`, a také na první a poslední tři číslice vašeho `subscriptionId`, k vytvoření:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Například pokud vaše `subscriptionId` je `bba14129-e895-429b-8809-278e836ecdb3` a `trackingId` je `0DET-URB`, pak je vaše adresa URL služby stavu:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>Použití úroveň ke zjištění závažnosti problému
Od nejnižší závažnost na nejvyšší závažnost `level` vlastnost v datové části může být libovolná z `Informational`, `Warning`, `Error`, a `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Analýza kódu ovlivněné služby pochopit plný rozsah incidentu
Upozornění na stav služby může informovat o problémech ve více oblastech a služeb. Pokud chcete získat všechny podrobnosti, potřebujete analyzovat hodnotu `impactedServices`.
Obsah uvnitř je [JSON uvozeny řídicími znaky](http://json.org/) řetězec, když znaků bez řídících, obsahuje jiný objekt JSON, který může být analyzován pravidelně.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

změní:

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

Ukazuje to, že jsou problémy s "Výstrahy a metriky" na Austrálie – východ a jihovýchodní, stejně jako problémy s "App Service" v Austrálie – jihovýchod.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování vaší integraci webhooků prostřednictvím požadavku HTTP POST
1. Vytvořte datová část služby stavu, který chcete odeslat. Můžete najít příkladu služby health datová část webhooku v [upozornění protokolů Webhooky Azure aktivity](../azure-monitor/platform/activity-log-alerts-webhook.md).

2. Vytvoření požadavku HTTP POST následujícím způsobem:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Měli byste obdržet `2XX - Successful` odpovědi.

4. Přejděte na [PagerDuty](https://www.pagerduty.com/) potvrďte, že vaše integrace byl nastaven úspěšně.

## <a name="next-steps"></a>Další postup
- Zkontrolujte [schéma webhooku v upozornění protokolu aktivit](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Další informace o [služby oznámení o stavu](../azure-monitor/platform/service-notifications.md).
- Další informace o [skupiny akcí](../azure-monitor/platform/action-groups.md).