---
title: Azure ExpressRoute Insights s využitím služby Network Insights
description: Seznamte se s Azure ExpressRoute Insights pomocí služby Network Insights.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/23/2021
ms.author: duau
ms.openlocfilehash: 7033ea6a1ba6d85f9aa15e14bb9577b2439c59a8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050453"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Azure ExpressRoute Insights s využitím služby Network Insights

V tomto článku se dozvíte, jak vám síťové přehledy můžou pomáhat s zobrazením metrik a konfigurací ExpressRoute na jednom místě. Prostřednictvím Network Insights můžete zobrazit topologické mapy a řídicí panely stavu obsahující důležité informace ExpressRoute, aniž byste museli dokončit žádné další nastavení.

:::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="Snímek obrazovky s cílovou stránkou ExpressRoute monitoru" lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>Vizualizace funkčních závislostí

Chcete-li zobrazit toto řešení, přejděte na stránku *Azure monitor* , vyberte položku *sítě* a potom vyberte kartu *okruhy ExpressRoute* . Potom pro okruh, který chcete zobrazit, vyberte tlačítko topologie.

Zobrazení funkční závislosti poskytuje jasný přehled o nastavení ExpressRoute a nabízí přehled o relacích mezi různými součástmi ExpressRoute (partnerskými vztahy, připojeními, branami).

:::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="Snímek obrazovky s zobrazením topologie pro Network Insights" lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

Pokud chcete zobrazit informace o konfiguraci, najeďte myší na libovolnou komponentu na mapě topologie. Například najeďte myší na komponentu partnerských vztahů ExpressRoute a zobrazte podrobnosti, jako je například šířka pásma okruhu a povolení Global Reach.

:::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="Snímek obrazovky s ukazateli myši nad prostředky zobrazení topologie" lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>Zobrazení podrobného a předem načteného řídicího panelu metrik

Jakmile provedete kontrolu topologie nastavení ExpressRoute pomocí zobrazení funkční závislost, vyberte **Zobrazit detailní metriky** a přejděte do podrobného zobrazení metriky, abyste pochopili výkon okruhu. Toto zobrazení nabízí uspořádaný seznam propojených prostředků a bohatý řídicí panel důležitých ExpressRoutech metrik.

Část **propojené prostředky** obsahuje seznam připojených bran ExpressRoute a nakonfigurovaných partnerských vztahů, které můžete vybrat, chcete-li přejít na odpovídající stránku prostředku.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="Snímek obrazovky propojeného prostředku na stránce monitorování":::


Oddíl **metriky ExpressRoute** obsahuje grafy důležitých metrik okruhů v rámci kategorií **dostupnosti**, **propustnosti**, **poklesu paketů** a **metrik brány**.

### <a name="availability"></a>Dostupnost

Karta *dostupnost* sleduje dostupnost protokolu ARP a protokolu BGP, vykreslí data pro okruh jako celé a samostatné připojení (primární a sekundární). 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="Snímek obrazovky s grafy metrik dostupnosti" lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>Propustnost

Podobně karta *propustnost* zobrazuje celkovou propustnost příchozího a odchozího provozu pro okruh v bitech za sekundu. Můžete také zobrazit propustnost pro jednotlivá připojení a jednotlivé typy nakonfigurovaných partnerských vztahů.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="Snímek obrazovky s grafy metrik propustnosti." lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>Zahazování paketů

Karta zahození *paketů* vykreslí vyřazené bity za sekundu pro příchozí a odchozí přenos přes okruh. Tato karta nabízí snadný způsob, jak monitorovat problémy s výkonem, které se mohou vyskytnout, pokud často potřebujete nebo překročíte šířku pásma okruhu.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="Snímek obrazovky se zrušenými grafy paketů." lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>Metriky brány

Nakonec karta metriky brány naplní grafy klíčových metrik pro vybranou bránu ExpressRoute (z oddílu propojené prostředky). Tuto kartu použijte, když potřebujete monitorovat připojení ke konkrétním virtuálním sítím.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="Snímek obrazovky s propustností brány a metrikami procesoru." lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>Další kroky

Nakonfigurujte připojení ExpressRoute.
  
* Další informace o [Azure ExpressRoute](expressroute-introduction.md), [Network Insights](../azure-monitor/insights/network-insights-overview.md)a [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)
* [Vytvoření a změny okruhu](expressroute-howto-circuit-arm.md)
* [Vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-arm.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
* [Přizpůsobení metrik](expressroute-monitoring-metrics-alerts.md) a vytvoření [monitorování připojení](../network-watcher/connection-monitor-overview.md)
