---
title: Monitorování Azure ExpressRoute, metriky a upozornění | Dokumentace Microsoftu
description: Tato stránka obsahuje informace o monitorování ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: fc8abee93983ce4ea06d0b433eb35ed22e0f61b4
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47218067"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorování, metriky a výstrahy služby ExpressRoute

 Tento článek vám pomůže pochopit monitorování ExpressRoute, metriky a výstrahy. Azure Monitor je na jednom místě pro všechny metriky, výstrahy, diagnostických protokolů napříč všemi Azure.

## <a name="circuit-metrics"></a>Metriky okruh

Přejít na **metriky**, klikněte na stránce ExpressRoute pro okruh, kterou chcete monitorovat. V části **monitorování** můžete zobrazit **metriky**.

![metriky okruh](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>Metriky na partnerský vztah

Můžete zobrazit metriky pro privátní, veřejný a Microsoftu partnerský vztah v bitech za sekundu.

![metriky na partnerský vztah](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Připojení brány ExpressRoute v bitů/sekundách

![připojení brány](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>Upozornění na připojení ExpressRoute gateway

1. Pokud chcete nakonfigurovat výstrahy, přejděte na **Azure Monitor**, pak klikněte na tlačítko **výstrahy**.

  ![výstrahy](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Klikněte na tlačítko **+ vyberte cíl** a vyberte prostředek brány připojení ExpressRoute.

  ![cíl]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Definujte podrobnosti o upozornění.

  ![Skupina akcí](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)


4. Definovat a přidat skupinu akcí.

  ![Přidat skupinu akcí](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Výstrahy založené na každý partnerský vztah

 ![Co](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurace upozornění protokolů aktivit pro okruhy

V **kritérií výstrah**, můžete vybrat **protokolu aktivit** pro typ signálu a vyberte signál.

  ![jiné](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)

## <a name="next-steps"></a>Další postup
* Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření a změny okruhu](expressroute-howto-circuit-arm.md)
  * [Vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
