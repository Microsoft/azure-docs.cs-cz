---
title: 'Azure ExpressRoute: monitorování, metriky a výstrahy'
description: Tato stránka obsahuje informace o monitorování ExpressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436920"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorování, metriky a výstrahy služby ExpressRoute

Tento článek vám pomůže porozumět monitorování metrik a výstrah pomocí monitorování Azure ExpressRoute. Azure Monitor je na jednom místě pro všechny metriky, výstrahy, diagnostických protokolů napříč všemi Azure.
 
>[!NOTE]
>Pomocí **klasické metriky** se nedoporučuje.
>

## <a name="expressroute-metrics"></a>ExpressRoute metriky

Pokud chcete zobrazit **metriky**, přejděte na stránku *Azure monitor* a klikněte na *metriky*. Pokud chcete zobrazit metriky **ExpressRoute** , filtrujte podle typu prostředku *ExpressRoute okruhy*. Pokud chcete zobrazit **Global REACH** metriky, filtrujte podle typu prostředku *ExpressRoute okruhy* a vyberte prostředek okruhu ExpressRoute, který má povolený Global REACH. Pokud chcete zobrazit **ExpressRoute přímé** metriky, filtrujte typ prostředku pomocí *portů ExpressRoute*. 

Po výběru metriky se použije výchozí agregace. Volitelně můžete použít rozdělení, které zobrazí metriku s různými dimenzemi.

### <a name="available-metrics"></a>Dostupné metriky
|**Metrika**|**Kategorie**|**Dimenze (y)**|**Funkce (y)**|
| --- | --- | --- | --- |
|Dostupnost protokolu ARP|Dostupnost|<ui><li>Partner (primární/sekundární ExpressRoute směrovač)</ui></li><ui><li> Typ partnerského vztahu (Private/Public/Microsoft)</ui></li>|ExpressRoute|
|Dostupnost protokolu BGP|Dostupnost|<ui><li> Partner (primární/sekundární ExpressRoute směrovač)</ui></li><ui><li> Typ partnerského vztahu</ui></li>|ExpressRoute|
|BitsInPerSecond|Provoz|<ui><li> Typ partnerského vztahu (ExpressRoute)</ui></li><ui><li>Odkaz (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Provoz| <ui><li>Typ partnerského vztahu (ExpressRoute)</ui></li><ui><li> Odkaz (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Provoz|<ui><li>Partnerský okruh skey (klíč služby)</ui></li>|Globální dosah|
|GlobalReachBitsOutPerSecond|Provoz|<ui><li>Partnerský okruh skey (klíč služby)</ui></li>|Globální dosah|
|AdminState|Fyzické připojení|Odkaz|ExpressRoute Direct|
|LineProtocol|Fyzické připojení|Odkaz|ExpressRoute Direct|
|RxLightLevel|Fyzické připojení|<ui><li></ui> propojení</li><ui><li></ui> dráhy</li>|ExpressRoute Direct|
|TxLightLevel|Fyzické připojení|<ui><li></ui> propojení</li><ui><li></ui> dráhy</li>|ExpressRoute Direct|
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

## <a name="expressroute-direct-metrics"></a>ExpressRoute přímé metriky

### <a name="admin-state---split-by-link"></a>Stav Správce – rozdělit podle odkazu
Můžete zobrazit stav správce pro každý odkaz dvojice portů ExpressRoute Direct.

![Stav Správce ER Direct](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bity za sekundu – rozdělit podle propojení
V obou propojeních dvojice portů ExpressRoute můžete zobrazit bity za sekundu. 

![přímých bitů ER za sekundu](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bity za sekundu – rozdělit podle propojení
Můžete také zobrazit bity za sekundu v obou odkazech dvojice portů ExpressRoute Direct. 

![ER – přímé bity za sekundu](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Protokol řádku – rozdělit podle odkazu
Protokol linky můžete zobrazit přes každý odkaz dvojice portů ExpressRoute Direct.

![přímý spojnicový protokol ER](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Úroveň Light pro příjem – rozdělení podle propojení
Můžete zobrazit úroveň indikátoru příjmu (úroveň světla, kterou port pro přímý přenos ExpressRoute **přijímá**) pro každý port. V pořádku jsou úrovně nízké úrovně příjmu obvykle v rozsahu od-10 do 0 dBm.

![Úroveň nízké linky pro příjem na er](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Úroveň Light tx – rozdělení podle propojení
Úroveň síla pro odesílání můžete zobrazit (úroveň světla, kterou port ExpressRoute Direct **odesílá) pro**každý port. Slabá úroveň nesprávného zpomalení v pořádku spadá do rozsahu od-10 do 0 dBm

![Úroveň nízké linky pro příjem na er](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Připojení brány ExpressRoute v bitů/sekundách

![připojení brány](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Upozornění na připojení ExpressRoute gateway

1. Pokud chcete nakonfigurovat výstrahy, přejděte na **Azure Monitor**, pak klikněte na tlačítko **výstrahy**.

   ![výstrahy](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Klikněte na tlačítko **+ vyberte cíl** a vyberte prostředek brány připojení ExpressRoute.

   ![target]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Definujte podrobnosti o upozornění.

   ![Skupina akcí](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Definovat a přidat skupinu akcí.

   ![Přidat skupinu akcí](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Výstrahy založené na každý partnerský vztah

 ![Co](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurace upozornění protokolů aktivit pro okruhy

V **kritérií výstrah**, můžete vybrat **protokolu aktivit** pro typ signálu a vyberte signál.

  ![jiné](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Další kroky

Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření a změny okruhu](expressroute-howto-circuit-arm.md)
  * [Vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
