---
title: 'Azure ExpressRoute: Monitorování, metriky a výstrahy'
description: Tato stránka obsahuje informace o monitorování ExpressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436920"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorování, metriky a výstrahy služby ExpressRoute

Tento článek vám pomůže pochopit monitorování ExpressRoute, metriky a výstrahy pomocí Azure Monitoru. Azure Monitor je one stop shop pro všechny metriky, výstrahy, diagnostické protokoly ve všech Azure.
 
>[!NOTE]
>Použití **klasických metrik** se nedoporučuje.
>

## <a name="expressroute-metrics"></a>Metriky ExpressRoute

Chcete-li **zobrazit metriky**, přejděte na stránku *Azure Monitor* a klikněte na *metriky*. Chcete-li zobrazit metriky **ExpressRoute,** filtrujte podle okruhů Typu zdroje *ExpressRoute*. Chcete-li zobrazit metriky **globálního dosahu,** filtrujte podle okruhů Typu zdroje *ExpressRoute* a vyberte prostředek okruhu ExpressRoute, který má povolený globální dosah. Chcete-li zobrazit metriky **ExpressRoute Direct,** filtrujte typ prostředku podle *portů ExpressRoute*. 

Jakmile je vybrána metrika, použije se výchozí agregace. Volitelně můžete použít rozdělení, které zobrazí metriku s různými dimenzemi.

### <a name="available-metrics"></a>Dostupné metriky
|**Metrika**|**Kategorie**|**Rozměry**|**Funkce (funkce)**|
| --- | --- | --- | --- |
|Dostupnost ARP|Dostupnost|<ui><li>Peer (primární/sekundární směrovač ExpressRoute)</ui></li><ui><li> Typ partnerského vztahu (soukromý/veřejný/Microsoft)</ui></li>|ExpressRoute|
|Dostupnost protokolu BGP|Dostupnost|<ui><li> Peer (primární/sekundární směrovač ExpressRoute)</ui></li><ui><li> Typ partnerského vztahu</ui></li>|ExpressRoute|
|BitsInPerSecond|Provoz|<ui><li> Typ partnerského vztahu (ExpressRoute)</ui></li><ui><li>Odkaz (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Provoz| <ui><li>Typ partnerského vztahu (ExpressRoute)</ui></li><ui><li> Odkaz (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|Globální DosahBitsInPerSecond|Provoz|<ui><li>Kód ový obvod (servisní klíč)</ui></li>|Globální dosah|
|GlobalReachBitsOutPerSecond|Provoz|<ui><li>Kód ový obvod (servisní klíč)</ui></li>|Globální dosah|
|Stav správce|Fyzické připojení|Odkaz|ExpressRoute Direct|
|LineProtocol|Fyzické připojení|Odkaz|ExpressRoute Direct|
|Úroveň RxLight|Fyzické připojení|<ui><li>Odkaz</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fyzické připojení|<ui><li>Odkaz</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Pomocí *GlobalGlobalReachBitsInSecond* a *GlobalGlobalReachBitsOutPerSecond* bude viditelný pouze v případě, že je navázáno alespoň jedno připojení globálního dosahu.
>

## <a name="circuits-metrics"></a>Metriky obvodů

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bity dovnitř a ven – metriky ve všech partnerských partnerech

Můžete zobrazit metriky ve všech partnerských vztahu na daném okruhu ExpressRoute.

![metriky obvodu](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bity dovnitř a ven – metriky na partnerský vztah

Metriky pro soukromý, veřejný a partnerský vztah Microsoftu můžete zobrazit v bitech za sekundu.

![metriky na partnerský vztah](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Dostupnost protokolu BGP – rozdělení podle partnera  

Můžete zobrazit v blízkosti dostupnosti Protokolu BGP v reálném čase napříč partnerskými partnery a partnery (primární a sekundární směrovače ExpressRoute). Tento řídicí panel zobrazuje primární relaci Protokolu BGP pro soukromý partnerský vztah a druhou relaci Protokolu BGP pro soukromý partnerský vztah. 

![Dostupnost protokolu BGP na druhou partnerku](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Dostupnost ARP – rozdělení podle partnerského vztahu  

Můžete zobrazit v blízkosti dostupnosti [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) v reálném čase napříč partnerskými partnery a partnery (primární a sekundární směrovače ExpressRoute). Tento řídicí panel zobrazuje relaci ARP soukromého partnerského vztahu mezi oběma partnery, ale dokončený pro partnerský vztah Microsoftu napříč partnerskými partnery. Výchozí agregace (Průměr) byla využita v obou partnerech.  

![Dostupnost ARP na druhou partnerku](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Přímé metriky ExpressRoute

### <a name="admin-state---split-by-link"></a>Stav správce – rozdělení podle odkazu
Můžete zobrazit stav správce pro každý odkaz dvojice portů ExpressRoute Direct.

![er stav přímého správce](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bity za sekundu – rozdělení podle odkazu
Bity za sekundu můžete zobrazit na obou odkazech dvojice portů ExpressRoute Direct. 

![er přímé bity za sekundu](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bity za sekundu - rozdělit podle odkazu
Můžete také zobrazit bity za sekundu napříč oběma odkazy dvojice portů ExpressRoute Direct. 

![er přímé bity za sekundu](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Line Protocol - Rozdělení odkazem
Protokol linky můžete zobrazit napříč jednotlivými odkazy dvojice portů ExpressRoute Direct.

![er protokol přímé linky](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx Light Level - Rozdělení odkazem
Můžete zobrazit úroveň světla Rx (úroveň světla, kterou **přijímá**port ExpressRoute Direct) pro každý port. Zdravé hladiny světla Rx obecně spadají do rozmezí -10 až 0 dBm

![er přímka Rx Úroveň světla](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx Light Level - Rozdělit odkazem
Můžete zobrazit úroveň světla Tx (úroveň světla, kterou port ExpressRoute Direct **vysílá)** pro každý port. Zdravé úrovně světla Tx obecně spadají do rozmezí -10 až 0 dBm

![er přímka Rx Úroveň světla](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Připojení brány ExpressRoute v bitech/sekundách

![připojení brány](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Výstrahy pro připojení brány ExpressRoute

1. Chcete-li konfigurovat výstrahy, přejděte na **Azure Monitor**a klikněte na **položku Výstrahy**.

   ![výstrahy](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Klikněte na **+Vybrat cíl** a vyberte prostředek připojení brány ExpressRoute.

   ![Cíl]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Definujte podrobnosti výstrahy.

   ![skupina akcí](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Definujte a přidejte skupinu akcí.

   ![přidat skupinu akcí](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Výstrahy založené na jednotlivých partnerských partnerech

 ![Co](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurace výstrah pro protokoly aktivit na obvodech

V **kritériích výstrah**můžete vybrat **protokol aktivit** pro typ signálu a vybrat signál.

  ![Další](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Další kroky

Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření a změny okruhu](expressroute-howto-circuit-arm.md)
  * [Vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
