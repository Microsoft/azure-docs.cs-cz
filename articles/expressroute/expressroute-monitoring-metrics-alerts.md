---
title: Sledování, metriky a upozorněními – Azure ExpressRoute | Dokumentace Microsoftu
description: Tato stránka obsahuje informace o monitorování ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: dbe03ef29bd28d465fa671abc915d63d4b038cb2
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154763"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorování, metriky a výstrahy služby ExpressRoute

Tento článek vám pomůže porozumět monitorování metrik a výstrah pomocí monitorování Azure ExpressRoute. Azure Monitor je na jednom místě pro všechny metriky, výstrahy, diagnostických protokolů napříč všemi Azure.
 
>[!NOTE]
>Pomocí **klasické metriky** se nedoporučuje.
>

## <a name="expressroute-metrics"></a>ExpressRoute metriky

Pokud chcete zobrazit **metriky**, přejděte na stránku *Azure monitor* a klikněte na *metriky*. Chcete-li zobrazit metriky **ExpressRoute** , souborového podle typu prostředku *ExpressRoute okruhy*. Pokud chcete zobrazit **Global REACH** metriky, filtrujte podle typu prostředku *ExpressRoute okruhy* a vyberte prostředek okruhu ExpressRoute, který má povolený Global REACH. Pokud chcete zobrazit **ExpressRoute přímé** metriky, filtrujte typ prostředku pomocí *portů ExpressRoute*. 

Po výběru metriky se použije výchozí agregace. Volitelně můžete použít rozdělení, které zobrazí metriku s různými dimenzemi.

### <a name="available-metrics"></a>Dostupné metriky
|**Metrika**|**Kategorie**|**Dimenze (y)**|**Funkce (y)**|
| --- | --- | --- | --- |
|Dostupnost protokolu ARP|Dostupnost|<ui><li>Partner (primární/sekundární ExpressRoute směrovač)</ui></li><ui><li> Typ partnerského vztahu (Private/Public/Microsoft)</ui></li>|ExpressRoute|
|Dostupnost protokolu BGP|Dostupnost|<ui><li> Partner (primární/sekundární ExpressRoute směrovač)</ui></li><ui><li> Typ partnerského vztahu</ui></li>|ExpressRoute|
|BitsInPerSecond|Přenos|<ui><li> Typ partnerského vztahu (ExpressRoute)</ui></li><ui><li>Odkaz (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Přenos| <ui><li>Typ partnerského vztahu (ExpressRoute)</ui></li><ui><li> Odkaz (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Přenos|<ui><li>Skey okruhu s partnerským vztahem (klíč služby)</ui></li>|Globální dosah|
|GlobalReachBitsOutPerSecond|Přenos|<ui><li>Skey okruhu s partnerským vztahem (klíč služby)</ui></li>|Globální dosah|
|AdminState|Fyzické připojení|Odkaz|ExpressRoute Direct|
|LineProtocol|Fyzické připojení|Odkaz|ExpressRoute Direct|
|RxLightLevel|Fyzické připojení|<ui><li>Propojit</ui></li><ui><li>Pásu</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fyzické připojení|<ui><li>Propojit</ui></li><ui><li>Pásu</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Použití *GlobalGlobalReachBitsInPerSecond* a *GlobalGlobalReachBitsOutPerSecond* se zobrazí jenom v případě, že se naváže aspoň jedno připojení Global REACH.
>

## <a name="circuits-metrics"></a>Metriky okruhů

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Služby a výstupní metriky v rámci všech partnerských vztahů

Metriky můžete zobrazit ve všech partnerských vztazích na daném okruhu ExpressRoute.

![Metriky okruh](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bity a výstupní metriky na partnerský vztah

Můžete zobrazit metriky pro privátní, veřejný a Microsoftu partnerský vztah v bitech za sekundu.

![Metriky na partnerský vztah](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Dostupnost protokolu BGP – rozdělit podle partnerského vztahu  

Můžete si prohlédnout dostupnost protokolu BGP v reálném čase napříč partnerskými vztahy a partnery (primárními a sekundárními ExpressRoute směrovači). Tento řídicí panel zobrazuje primární relaci protokolu BGP pro privátní partnerské vztahy a druhou relaci protokolu BGP pro privátní partnerský vztah. 

![Dostupnost protokolu BGP na partnerský uzel](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Dostupnost protokolu ARP – rozdělení podle partnerského vztahu  

Můžete si prohlédnout dostupnost [protokolu ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) v reálném čase napříč partnerskými vztahy a partnery (primárními a sekundárními ExpressRoute směrovači). Tento řídicí panel zobrazuje relaci protokolu ARP privátního partnerského vztahu v obou partnerských uzlech, ale dokončí pro partnerský vztah Microsoftu napříč partnerskými vztahy. Výchozí agregace (průměr) byla využívána v obou partnerských uzlech.  

![Dostupnost protokolu ARP na partnerský uzel](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

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

Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření a změny okruhu](expressroute-howto-circuit-arm.md)
  * [Vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
