---
title: 'Azure ExpressRoute: monitorování, metriky a výstrahy'
description: Seznamte se s Azure ExpressRoute monitoring, metrikami a výstrahami pomocí Azure Monitor, což je jedna zastávka pro všechny metriky, výstrahy a diagnostické protokoly v Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2020
ms.author: duau
ms.openlocfilehash: 7a5da35da35b2f447256bc742681ccd7a7d403da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99091555"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorování, metriky a výstrahy služby ExpressRoute

Tento článek vám pomůže pochopit monitorování, metriky a výstrahy ExpressRoute pomocí Azure Monitor. Azure Monitor je jedno zastavení pro všechny metriky, výstrahy a diagnostické protokoly napříč všemi Azure.
 
>[!NOTE]
>Použití **klasických metrik** se nedoporučuje.
>

## <a name="expressroute-metrics"></a>ExpressRoute metriky

Pokud chcete zobrazit **metriky**, přejděte na stránku *Azure monitor* a klikněte na *metriky*. Pokud chcete zobrazit metriky **ExpressRoute** , filtrujte podle typu prostředku *ExpressRoute okruhy*. Pokud chcete zobrazit **Global REACH** metriky, filtrujte podle typu prostředku *ExpressRoute okruhy* a vyberte prostředek okruhu ExpressRoute, který má povolený Global REACH. Pokud chcete zobrazit **ExpressRoute přímé** metriky, filtrujte typ prostředku pomocí *portů ExpressRoute*. 

Po výběru metriky se použije výchozí agregace. Volitelně můžete použít rozdělení, které zobrazí metriku s různými dimenzemi.

### <a name="available-metrics"></a>Dostupné metriky

|**Metrika**|**Kategorie**|**Dimenze (y)**|**Funkce (y)**|
| --- | --- | --- | --- |
|Dostupnost protokolu ARP|Dostupnost|<ui><li>Partner (primární/sekundární ExpressRoute směrovač)</ui></li><ui><li> Typ partnerského vztahu (Private/Public/Microsoft)</ui></li>|ExpressRoute|
|Dostupnost protokolu BGP|Dostupnost|<ui><li> Partner (primární/sekundární ExpressRoute směrovač)</ui></li><ui><li> Typ partnerského vztahu</ui></li>|ExpressRoute|
|BitsInPerSecond|Provoz|<ui><li> Typ partnerského vztahu (ExpressRoute)</ui></li><ui><li>Odkaz (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>Připojení brány ExpressRoute</ui></li>|
|BitsOutPerSecond|Provoz| <ui><li>Typ partnerského vztahu (ExpressRoute)</ui></li><ui><li> Odkaz (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>Připojení brány ExpressRoute</ui></li>|
|Využití procesoru|Výkon| <ui><li>Instance</ui></li>|ExpressRoute Virtual Network bránu|
|Pakety za sekundu|Výkon| <ui><li>Instance</ui></li>|ExpressRoute Virtual Network bránu|
|Počet tras inzerovaných pro partnerský uzel |Dostupnost| <ui><li>Instance</ui></li>|ExpressRoute Virtual Network bránu|
|Počet tras získaných z partnerského vztahu |Dostupnost| <ui><li>Instance</ui></li>|ExpressRoute Virtual Network bránu|
|Četnost změn tras |Dostupnost| <ui><li>Instance</ui></li>|ExpressRoute Virtual Network bránu|
|Počet virtuálních počítačů v Virtual Network |Dostupnost| – |ExpressRoute Virtual Network bránu|
|GlobalReachBitsInPerSecond|Provoz|<ui><li>Skey okruhu s partnerským vztahem (klíč služby)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Provoz|<ui><li>Skey okruhu s partnerským vztahem (klíč služby)</ui></li>|Global Reach|
|AdminState|Fyzické připojení|Odkaz|ExpressRoute Direct|
|LineProtocol|Fyzické připojení|Odkaz|ExpressRoute Direct|
|RxLightLevel|Fyzické připojení|<ui><li>Odkaz</ui></li><ui><li>Pásu</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fyzické připojení|<ui><li>Odkaz</ui></li><ui><li>Pásu</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Použití *GlobalGlobalReachBitsInPerSecond* a *GlobalGlobalReachBitsOutPerSecond* se zobrazí jenom v případě, že se naváže aspoň jedno připojení Global REACH.
>

## <a name="circuits-metrics"></a>Metriky okruhů

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Služby a výstupní metriky v rámci všech partnerských vztahů

Metriky můžete zobrazit ve všech partnerských vztazích na daném okruhu ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="metriky okruhu":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bity a výstupní metriky na partnerský vztah

V bitech za sekundu si můžete zobrazit metriky pro privátní, veřejné a partnerské vztahy Microsoftu.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="metriky na partnerský vztah":::

### <a name="bgp-availability---split-by-peer"></a>Dostupnost protokolu BGP – rozdělit podle partnerského vztahu  

Můžete si prohlédnout dostupnost protokolu BGP v reálném čase napříč partnerskými vztahy a partnery (primárními a sekundárními ExpressRoute směrovači). Tento řídicí panel zobrazuje primární relaci protokolu BGP pro privátní partnerské vztahy a druhou relaci protokolu BGP pro privátní partnerský vztah. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Dostupnost protokolu BGP na partnerský uzel":::

### <a name="arp-availability---split-by-peering"></a>Dostupnost protokolu ARP – rozdělení podle partnerského vztahu  

Můžete si prohlédnout dostupnost [protokolu ARP](./expressroute-troubleshooting-arp-resource-manager.md) v reálném čase napříč partnerskými vztahy a partnery (primárními a sekundárními ExpressRoute směrovači). Tento řídicí panel zobrazuje relaci protokolu ARP privátního partnerského vztahu v obou partnerských uzlech, ale dokončí pro partnerský vztah Microsoftu napříč partnerskými vztahy. Výchozí agregace (průměr) byla využívána v obou partnerských uzlech.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Dostupnost protokolu ARP na partnerský uzel":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute přímé metriky

### <a name="admin-state---split-by-link"></a>Stav Správce – rozdělit podle odkazu

Můžete zobrazit stav správce pro každý odkaz dvojice portů ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Stav Správce ER Direct":::

### <a name="bits-in-per-second---split-by-link"></a>Bity za sekundu – rozdělit podle propojení

V obou propojeních dvojice portů ExpressRoute můžete zobrazit bity za sekundu.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="Přímých bitů ER za sekundu":::

### <a name="bits-out-per-second---split-by-link"></a>Bity za sekundu – rozdělit podle propojení

Můžete také zobrazit bity za sekundu v obou odkazech dvojice portů ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER – přímé bity za sekundu":::

### <a name="line-protocol---split-by-link"></a>Protokol řádku – rozdělit podle odkazu

Protokol linky můžete zobrazit přes každý odkaz dvojice portů ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Přímý spojnicový protokol ER":::

### <a name="rx-light-level---split-by-link"></a>Úroveň Light pro příjem – rozdělení podle propojení

Můžete zobrazit úroveň indikátoru příjmu (úroveň světla, kterou port pro přímý přenos ExpressRoute **přijímá**) pro každý port. V pořádku jsou úrovně nízké úrovně příjmu obvykle v rozsahu od-10 do 0 dBm.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Úroveň nízké linky pro příjem na ER":::

### <a name="tx-light-level---split-by-link"></a>Úroveň Light tx – rozdělení podle propojení

Úroveň síla pro odesílání můžete zobrazit (úroveň světla, kterou port ExpressRoute Direct **odesílá) pro** každý port. Slabá úroveň nesprávného zpomalení v pořádku spadá do rozsahu od-10 do 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="Světlá úroveň TX přímého řádku ER":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute Virtual Network metriky brány

### <a name="cpu-utilization---split-instance"></a>Využití CPU – dělená instance

Můžete zobrazit využití CPU instancí brány.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Rozdělení procesoru":::

### <a name="packets-per-second---split-by-instance"></a>Počet paketů za sekundu – rozdělení podle instance

Můžete zobrazit pakety za sekundu, které procházejí bránou.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Počet paketů za sekundu – rozdělení":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Počet tras inzerovaných pro rozdělení peer-Split podle instance

Můžete zobrazit počet tras inzerovaných pro okruh ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Počet tras inzerovaných pro partnerský uzel":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Počet tras získaných z instance peer-Split podle instance

Můžete si prohlédnout počet tras přijatých z okruhu ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Počet tras získaných z partnerského vztahu":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Frekvence změny – rozdělení podle instance

Můžete zobrazit frekvenci změny trasy v bráně.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Četnost změněných tras":::

### <a name="number-of-vms-in-the-virtual-network"></a>Počet virtuálních počítačů v Virtual Network

Můžete zobrazit počet virtuálních počítačů ve virtuální síti.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Počet virtuálních počítačů ve virtuální síti":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Připojení brány ExpressRoute v bitech za sekundu

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="připojení brány":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Výstrahy pro připojení brány ExpressRoute

1. Chcete-li nakonfigurovat výstrahy, přejděte na **Azure monitor** a pak vyberte **výstrahy**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="výstrahy":::
2. Klikněte na **+ vybrat cíl** a vyberte prostředek připojení brány ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="cílové":::
3. Zadejte podrobnosti výstrahy.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="Skupina akcí":::
4. Definujte a přidejte skupinu akcí.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="Přidat skupinu akcí":::

## <a name="alerts-based-on-each-peering"></a>Výstrahy na základě jednotlivých partnerských vztahů

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="Každý partnerský vztah":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurace výstrah pro protokoly aktivit na okruhech

V části **kritéria výstrahy** můžete pro typ signálu vybrat **protokol aktivity** a vybrat signál.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="protokoly aktivit":::

## <a name="additional-metrics-in-log-analytics"></a>Další metriky v Log Analytics

Metriky ExpressRoute můžete zobrazit také tak, že přejdete do svého prostředku okruhu ExpressRoute a vyberete kartu *protokoly* . Pro všechny metriky, které dotazují, bude výstup obsahovat následující sloupce.

|**Sloupec**|**Typ**|**Popis**|
| --- | --- | --- |
|TimeGrain|řetězec|PT1M (hodnoty metriky se posunou každou minutu)|
|Počet|real|Obvykle se rovná 2 (každý MSEE po každou minutu nahraje jednu hodnotu metriky)|
|Minimum|real|Minimum dvou hodnot metrik nabízených dvěma směrovači msee|
|Maximum|real|Maximální počet dvou hodnot metrik nabízených dvěma směrovači mseey|
|Průměr|real|Rovná se (minimálně + maximum)/2|
|Celkem|real|Součet dvou hodnot metriky z obou směrovači msee (hlavní hodnota, která se má zaměřit na dotazování metriky)|
  
## <a name="next-steps"></a>Další kroky

Nakonfigurujte připojení ExpressRoute.
  
* [Vytvoření a změny okruhu](expressroute-howto-circuit-arm.md)
* [Vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-arm.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
