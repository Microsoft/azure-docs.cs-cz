---
title: Azure Monitor pro sítě
description: Přehled Azure Monitor pro sítě, který poskytuje komplexní přehled o stavu a metrikách pro všechny nasazené síťové prostředky bez jakékoli konfigurace.
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: db94a376112b3d715da9095acb05c0b4fd4ea02f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104605796"
---
# <a name="azure-monitor-for-networks"></a>Azure Monitor pro sítě

Azure Monitor pro sítě poskytuje komplexní přehled o [stavu](../../service-health/resource-health-checks-resource-types.md) a [metrikách](../essentials/metrics-supported.md) pro všechny nasazené síťové prostředky, a to bez nutnosti konfigurace. Poskytuje taky přístup k funkcím monitorování sítě, jako je [monitorování připojení](../../network-watcher/connection-monitor-overview.md), [protokolování toku pro skupiny zabezpečení sítě (skupin zabezpečení sítě)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md), a [Analýza provozu](../../network-watcher/traffic-analytics.md). A poskytuje další funkce [diagnostiky](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) sítě.

Azure Monitor pro sítě je členěné kolem těchto klíčových komponent monitorování:
- [Stav sítě a metriky](#networkhealth)
- [Připojení](#connectivity)
- [Provoz](#traffic)
- [Diagnostická sada](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Stav sítě a metriky

Stránka **přehled** Azure monitor pro sítě poskytuje snadný způsob vizualizace inventáře síťových prostředků spolu se stavem prostředků a výstrahami. Je rozdělená do čtyř klíčových funkčních oblastí: hledání a filtrování, stav prostředku a metriky, výstrahy a zobrazení závislostí.

[![Snímek obrazovky znázorňující stránku Přehled](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png#lightbox)

### <a name="search-and-filtering"></a>Hledání a filtrování
Zobrazení stavu prostředku a výstrah můžete přizpůsobit pomocí filtrů, jako je **předplatné**, **Skupina prostředků** a **typ**.

Pomocí vyhledávacího pole můžete vyhledat prostředky a jejich přidružené prostředky. Například veřejná IP adresa je přidružená k aplikační bráně. Hledání názvu DNS veřejné IP adresy vrátí veřejnou IP adresu i přidruženou Aplikační bránu:

[![Snímek obrazovky zobrazující Azure Monitor pro výsledky hledání sítí](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>Stav prostředku a metriky
V následujícím příkladu každá dlaždice představuje typ prostředku. Tato dlaždice zobrazuje počet instancí tohoto typu prostředku nasazených ve všech vybraných předplatných. Zobrazuje taky stav prostředku. V tomto příkladu jsou nasazená připojení 105 ER a VPN. 103 jsou v pořádku a 2 nejsou k dispozici.

![Snímek obrazovky, který zobrazuje stav prostředku a metriky v Azure Monitor pro sítě.](media/network-insights-overview/resource-health.png)

Pokud vyberete nedostupná připojení ER a VPN, zobrazí se zobrazení metriky: 

![Snímek obrazovky zobrazující zobrazení metriky v Azure Monitor pro sítě.](media/network-insights-overview/metric-view.png)

V zobrazení mřížky můžete vybrat libovolnou položku. Výběrem ikony ve sloupci **stav** získáte stav prostředků pro toto připojení. Výběrem hodnoty ve sloupci **Výstraha** přejdete na stránku výstrahy a metriky pro dané připojení. 

### <a name="alerts"></a>Výstrahy
Pole **výstrahy** na pravé straně stránky poskytuje zobrazení všech výstrah vygenerovaných pro vybrané prostředky ve všech předplatných. Vyberte počet výstrah, které chcete přejít na stránku podrobné výstrahy.

### <a name="dependency-view"></a>Zobrazení závislostí
Zobrazení závislostí pomáhá vizualizovat způsob konfigurace prostředku. Zobrazení závislostí je aktuálně k dispozici pro Azure Application Gateway, Azure Virtual WAN a Azure Load Balancer. Například pro Application Gateway můžete získat přístup k zobrazení závislostí tak, že vyberete název prostředku Application Gateway v zobrazení mřížky metrik. Totéž můžete udělat pro virtuální síť WAN a Load Balancer.

![Sreenshot, která zobrazuje Application Gateway zobrazení v Azure Monitor pro sítě.](media/network-insights-overview/application-gateway.png)

Zobrazení závislostí pro Application Gateway poskytuje zjednodušený přehled o tom, jak jsou IP adresy front-endu připojené ke posluchačům, pravidlům a back-endovému fondu. Propojovací linky jsou barevně kódované a poskytují další podrobnosti na základě stavu fondu back-endu. Toto zobrazení také nabízí podrobné zobrazení Application Gateway metrik a metrik pro všechny související back-end fondy, jako je třeba sada škálování virtuálních počítačů a instance virtuálních počítačů.

[![Snímek obrazovky, který zobrazuje zobrazení závislostí v Azure Monitor pro sítě.](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

Graf závislosti poskytuje snadnou navigaci na nastavení konfigurace. Pokud chcete získat přístup k dalším informacím, klikněte pravým tlačítkem na back-end fond. Pokud je například back-end fond virtuálním počítačem, můžete k řešení potíží s připojením získat přímý přístup k virtuálním počítačům v oblasti cloud Insights a Azure Network Watcher:

![Snímek obrazovky, který zobrazuje nabídku zobrazení závislosti v Azure Monitor pro sítě.](media/network-insights-overview/dependency-view-menu.png)

Panel hledání a filtrování v zobrazení závislostí poskytuje snadný způsob, jak hledat v grafu. Pokud například v předchozím příkladu vyhledáte **AppGWTestRule** , zobrazení se bude škálovat na všechny uzly připojené přes AppGWTestRule:

![Snímek obrazovky, který zobrazuje příklad hledání v Azure Monitor pro sítě.](media/network-insights-overview/search-example.png)

Různé filtry vám pomůžou škálovat směrem dolů ke konkrétní cestě a stavu. Například vyberte v seznamu **stav** stavu pouze není v **pořádku** , aby se zobrazily všechny hrany, pro které stav není v pořádku.

Vyberte **Zobrazit podrobné metriky** a otevřete tak předkonfigurovaný sešit, který poskytuje podrobné metriky pro aplikační bránu, všechny prostředky fondu back-endu a IP adresy front-endu. 

## <a name="connectivity"></a><a name="connectivity"></a>Komunikační

Karta **připojení** nabízí snadný způsob, jak vizualizovat všechny testy nakonfigurované přes [monitorování připojení](../../network-watcher/connection-monitor-overview.md) a monitor připojení (Classic) pro vybranou sadu předplatných.

![Snímek obrazovky, který zobrazuje kartu připojení v Azure Monitor pro sítě.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Testy jsou seskupeny podle dlaždic **zdroje** a **cíle** a zobrazují stav dostupnosti pro každý test. Dostupná nastavení poskytují snadný přístup ke konfiguracím pro vaše kritéria dostupnosti, a to na základě neúspěšných kontrol (%) a RTT (MS). Po nastavení hodnot se stav jednotlivých testů aktualizuje na základě kritérií výběru.

[![Snímek obrazovky, který zobrazuje testy připojení v Azure Monitor pro sítě.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

Můžete vybrat libovolnou dlaždici zdroje nebo cíle a otevřít tak zobrazení metriky:

[![Snímek obrazovky, který zobrazuje metriky připojení v Azure Monitor pro sítě.](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


V zobrazení mřížky můžete vybrat libovolnou položku. Vyberte ikonu ve sloupci **dosažitelnost** a přejdete na stránku portálu monitorování připojení a Prohlédněte si topologii směrování po směrování a možnosti připojení, které mají vliv na zjištěné problémy. Výběrem hodnoty ve sloupci **Výstraha** přejdete na výstrahy. V případě, že chcete přejít na stránku metriky pro vybrané monitorování připojení, vyberte grafy ve sloupcích **zkontrolovat neúspěšné procento** a **Doba odezvy (MS)** .

Okno **výstrahy** na pravé straně stránky nabízí zobrazení všech výstrah vygenerovaných pro testy připojení nakonfigurované ve všech předplatných. Vyberte počet výstrah, které chcete přejít na stránku podrobné výstrahy.

## <a name="traffic"></a><a name="traffic"></a>Provoz
Karta **přenos** poskytuje přístup ke všem skupin zabezpečení sítě nakonfigurovaným pro [protokoly toku NSG](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) a [Analýza provozu](../../network-watcher/traffic-analytics.md) pro vybranou sadu předplatných seskupené podle umístění. Funkce vyhledávání na této kartě vám umožní identifikovat skupin zabezpečení sítě nakonfigurovanou pro vyhledaná IP adresu. Ve svém prostředí můžete hledat libovolnou IP adresu. Místní zobrazení v dlaždicích zobrazí všechny skupin zabezpečení sítě spolu s protokoly toků NSG a stav konfigurace Analýza provozu.

[![Snímek obrazovky, který zobrazuje kartu přenos v Azure Monitor pro sítě.](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

Pokud vyberete dlaždici jakékoli oblasti, zobrazí se zobrazení mřížky. Mřížka poskytuje protokoly toku NSG a Analýza provozu v zobrazení, které je snadné číst a konfigurovat:  

[![Snímek obrazovky zobrazující zobrazení oblasti provozu v Azure Monitor pro sítě.](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

V zobrazení mřížky můžete vybrat libovolnou položku. Výběrem ikony ve sloupci **stav konfigurace Flowlog** upravte protokol toku NSG a konfiguraci Analýza provozu. Výběrem hodnoty ve sloupci **Výstraha** přejdete k výstrahám přenosu nakonfigurovaným pro vybrané NSG. Podobně můžete přejít na zobrazení Analýza provozu tím, že vyberete **pracovní prostor Analýza provozu**.  

Pole **výstrahy** na pravé straně stránky poskytuje zobrazení všech analýza provozuch výstrah na základě pracovního prostoru ve všech předplatných. Vyberte počet výstrah, které chcete přejít na stránku podrobné výstrahy.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Diagnostická sada
Diagnostická sada poskytuje přístup ke všem diagnostickým funkcím dostupným pro řešení potíží se sítí. Tento rozevírací seznam můžete použít k přístupu k funkcím, jako je [zachytávání paketů](../../network-watcher/network-watcher-packet-capture-overview.md), [řešení potíží s VPN](../../network-watcher/network-watcher-troubleshoot-overview.md), [odstraňování potíží s připojením](../../network-watcher/network-watcher-connectivity-overview.md), k [dalšímu směrování](../../network-watcher/network-watcher-next-hop-overview.md)a [ověření toku protokolu IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md):

![Snímek obrazovky zobrazující kartu diagnostická sada](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="onboarded-resources"></a>Připojené prostředky 

Připojené prostředky mají předdefinované sešity a zobrazení závislostí. Aktuálně připojené prostředky jsou virtuální sítě WAN, Application Gateway, Load Balancer a ExpressRoute.

## <a name="troubleshooting"></a>Řešení potíží 
Obecné pokyny k odstraňování potíží najdete v článku věnovaném [řešení potíží](troubleshoot-workbooks.md)s vyhrazenými sešity na základě článků.
Tato část vám pomůže diagnostikovat a řešit některé běžné problémy, se kterými se můžete setkat při použití Azure Monitor pro sítě. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>Návody vyřešit problémy s výkonem nebo chyby?

Další informace o řešení potíží souvisejících se sítí, které identifikujete pomocí Azure Monitor pro sítě, najdete v dokumentaci k řešení potíží pro prostředek, který nefunguje. 

Tady jsou některé odkazy na články týkající se řešení potíží pro často používané služby. Další články o řešení potíží s těmito službami najdete v dalších článcích v části věnované řešení potíží v obsahu služby.
* [Azure Virtual Network](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Azure Application Gateway](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Azure Load Balancer](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>Proč se mi nezobrazují prostředky pro všechna předplatná, která jsem vybral?

Azure Monitor pro sítě může zobrazit prostředky pouze pro pět předplatných. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-azure-monitor-for-networks"></a>Návody udělat změny nebo přidat vizualizace do Azure Monitor pro sítě?

Chcete-li provést změny, vyberte upravit **režim úprav** a upravte sešit. Změny pak můžete uložit jako nový sešit, který je svázán s určeným předplatným a skupinou prostředků.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>Jaké jsou časové intervaly po připnutí libovolné části sešitů?

Azure Monitor pro sítě používá interval **automatického** intervalu, takže časový interval je založený na vybraném časovém rozsahu.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>Jaký je časový rozsah, kdy je připnuté část sešitu?

Časový rozsah závisí na nastavení řídicího panelu.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-azure-monitor-for-networks"></a>Co když chci zobrazit další data nebo vytvořit vlastní vizualizace? Jak mohu provádět změny Azure Monitor pro sítě?

Sešit, který vidíte v jakémkoli bočním panelu nebo podrobné zobrazení metriky, můžete upravit pomocí režimu úprav. Změny pak můžete uložit jako nový sešit.

## <a name="next-steps"></a>Další kroky

- Další informace o monitorování sítě: [co je Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md)
- Seznamte se s scénáři, které jsou navržené tak, aby podporovaly, vytváření sestav a přizpůsobení existujících sestav a další: [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../visualize/workbooks-overview.md) .
