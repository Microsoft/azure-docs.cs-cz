---
title: Protokoly a metriky
titleSuffix: Azure Virtual WAN
description: Přečtěte si o protokolech a metrikách Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: 78165e9c14d4a83dbc20cbccd2f31dc8ac4c79ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440857"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Protokoly a metriky služby Azure Virtual WAN

Službu Azure Virtual WAN můžete monitorovat pomocí Azure Monitor. Virtuální síť WAN je síťová služba, která přináší dohromady mnoho funkcí v síti, zabezpečení a směrování, aby poskytovaly jediné provozní rozhraní. Brány VPN pro virtuální sítě WAN, brány ExpressRoute a Azure Firewall mají k dispozici protokolování a metriky prostřednictvím Azure Monitor. Informace o Azure Firewall najdete v tématu [protokoly Azure firewall a metriky](../firewall/logs-and-metrics.md).

Tento článek popisuje metriky a diagnostiku, které jsou k dispozici prostřednictvím portálu. Metriky jsou odlehčené a můžou podporovat scénáře téměř v reálném čase, které jsou užitečné při upozorňování a rychlé detekci problémů.

## <a name="metrics"></a>Metriky

Metriky v Azure Monitor jsou numerické hodnoty, které popisují určitý aspekt systému v určitou dobu. Metriky se shromažďují každou minutu a jsou užitečné pro upozorňování, protože je možné je často vzorkovat. Výstraha se dá rychle aktivovat pomocí relativně jednoduché logiky.

### <a name="site-to-site-vpn-gateways"></a>Brány VPN typu Site-to-site

Pro brány VPN typu Site-to-site jsou k dispozici následující metriky:

* **Šířka pásma brány** – průměrná šířka pásma sítě mezi lokalitami brány v bajtech za sekundu.
* **Šířka pásma tunelu** – průměrná šířka pásma tunelu v bajtech za sekundu.
* Výstupní **bajty tunelu** – odchozí bajty tunelu 
* **Tunelové výstupní pakety** – odchozí počet paketů tunelu. 
* Přetažení odchozích **paketů s neshodou tunelového propojení TS** – odchozí počet odchozích paketů z výběru přenosů nesouhlasí s výběrem 
* **Výstupní bajty tunelu** – Příchozí bajty tunelu. 
* Příchozí paket **tunelu** – počet příchozích paketů tunelu. 
* **Odpojování paketů** s nekompatibilními přenosy TS – počet příchozích paketů neodpovídajícím vyřazením provozu tunelu 

### <a name="point-to-site-vpn-gateways"></a>Brány VPN typu Point-to-site

Pro brány VPN typu Point-to-site jsou k dispozici následující metriky:

* **Šířka pásma P2S brány** – průměrná šířka pásma sítě typu Point-to-site brány v bajtech za sekundu.
* **Počet připojení P2S** – počet připojení typu Point-to-site brány

### <a name="azure-expressroute-gateways"></a>Brány Azure ExpressRoute

Pro brány Azure ExpressRoute jsou k dispozici následující metriky:

* **BitsInPerSecond** – počet příchozích přenosů za sekundu v Azure za sekundu
* **BitsOutPerSecond** – výstup Azure v bitech za sekundu

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Zobrazit metriky brány

Následující kroky vám pomůžou najít a zobrazit metriky:

1. Na portálu přejděte na virtuální rozbočovač, který má bránu.

2. Vyberte **VPN (site-to** -site) k vyhledání brány typu Site-to-site, **ExpressRoute** k vyhledání brány ExpressRoute nebo **uživatele VPN (Point** -to-site) k vyhledání brány Point-to-site. Na stránce můžete zobrazit informace o bráně. Zkopírujte tyto informace. Později ji budete používat k zobrazení diagnostiky pomocí Azure Monitor.

3. Vyberte **Metriky**.

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="Snímek obrazovky zobrazuje podokno site to site V P N s zobrazením Azure Monitor vybrané.":::

4. Na stránce **metriky** můžete zobrazit metriky, které vás zajímají.

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="Snímek obrazovky zobrazuje podokno site to site V P N s zobrazením Azure Monitor vybrané.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Diagnostické protokoly

### <a name="site-to-site-vpn-gateways"></a>Brány VPN typu Site-to-site

K dispozici jsou následující diagnostické služby pro brány VPN typu Site-to-site:

* **Diagnostické protokoly brány** – diagnostiku specifickou pro bránu, jako je stav, konfigurace, aktualizace služby, a další Diagnostika.
* **Protokoly diagnostiky tunelu** – jedná se o protokoly týkající se tunelu IPSec, jako jsou události připojení a odpojení pro tunelové propojení mezi lokalitami IPSec, vyjednané SAS, důvody odpojování a další diagnostiku.
* **Protokoly diagnostiky směrování** – jedná se o protokoly související s událostmi pro statické trasy, protokol BGP, aktualizace tras a další diagnostiku.
* **Diagnostické protokoly IKE** – Diagnostika specifická pro protokol IKE pro připojení IPsec.

### <a name="point-to-site-vpn-gateways"></a>Brány VPN typu Point-to-site

Pro brány VPN typu Point-to-site jsou k dispozici následující diagnostika:

* **Diagnostické protokoly brány** – diagnostiku specifickou pro bránu, jako je stav, konfigurace, aktualizace služby, a další Diagnostika.
* **Diagnostické protokoly IKE** – Diagnostika specifická pro protokol IKE pro připojení IPsec.
* **Diagnostické protokoly P2S** – jedná se o uživatelskou síť VPN (Point-to-site) P2S Configuration a události klienta. Patří mezi ně připojení a odpojení klienta, přidělování adres klienta VPN a další diagnostiku.

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Zobrazit diagnostické protokoly

Následující kroky vám pomůžou najít a zobrazit diagnostiku:

1. Na portálu přejděte na prostředek virtuální sítě WAN. V části **Přehled** na stránce virtuální síť WAN na portálu vyberte **základy** a rozbalte zobrazení a získejte informace o skupině prostředků. Zkopírujte informace o skupině prostředků.

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="Snímek obrazovky zobrazuje podokno site to site V P N s zobrazením Azure Monitor vybrané.":::

2. V části monitorování přejděte do skupiny prostředků. Vyberte **nastavení diagnostiky**a pak zadejte informace o zdroji. Toto jsou informace o prostředcích, které jste zkopírovali v kroku 2 v části [Zobrazit metriky brány](#metrics-steps) , dříve v tomto článku.

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="Snímek obrazovky zobrazuje podokno site to site V P N s zobrazením Azure Monitor vybrané.":::

3. Na stránce výsledky vyberte **+ Přidat nastavení diagnostiky**a pak vyberte možnost. Můžete se rozhodnout, že se má Log Analytics, streamovat do centra událostí nebo jednoduše archivovat do účtu úložiště.

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="Snímek obrazovky zobrazuje podokno site to site V P N s zobrazením Azure Monitor vybrané.":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Ukázkový dotaz Log Analytics

Protokoly se nacházejí v **pracovním prostoru Azure Log Analytics**. Dotaz můžete nastavit v Log Analytics. Následující příklad obsahuje dotaz pro získání diagnostiky tras typu Site-to-site.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

V případě potřeby nahraďte níže uvedené hodnoty za **= =**.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Protokoly aktivit

Položky **protokolu aktivit** jsou shromažďovány ve výchozím nastavení a lze je zobrazit v Azure Portal. K zobrazení všech operací odeslaných do vašeho předplatného Azure můžete použít protokoly aktivit Azure (dřív označované jako *operační protokoly* a *protokoly auditu*).

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak monitorovat protokoly Azure Firewall a metriky, najdete v tématu [kurz: monitorování protokolů Azure firewall](../firewall/tutorial-diagnostics.md).
* Další informace o metrikách v Azure Monitor najdete v tématu [metriky v Azure monitor](../azure-monitor/platform/data-platform-metrics.md).
