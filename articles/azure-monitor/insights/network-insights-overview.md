---
title: Azure Monitor pro sítě (Preview)
description: Rychlý přehled Azure Monitor pro síť, který poskytuje komplexní přehled o stavu a metrikách pro všechny nasazené síťové prostředky bez jakékoli konfigurace.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: 5f076f477c36f96d1807ce7071720225a6df8e03
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803802"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor pro sítě (Preview)
Azure Monitor pro síť poskytuje komplexní přehled o [stavu](https://docs.microsoft.com/azure/service-health/resource-health-checks-resource-types) a [metrikách](../platform/metrics-supported.md) pro všechny nasazené síťové prostředky bez jakékoli konfigurace.  Poskytuje taky přístup ke všem funkcím monitorování sítě, jako je [monitorování připojení](../../network-watcher/connection-monitor-preview.md), [protokolování toků pro skupiny zabezpečení sítě (skupin zabezpečení sítě)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md), [Analýza provozu](../../network-watcher/traffic-analytics.md)a další funkce [diagnostiky](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) sítě.

Azure Monitor pro sítě je členěná na následující klíčové komponenty monitorování:
- [Stav sítě a metriky](#networkhealth)
- [Připojení](#connectivity)
- [Provoz](#traffic)
- [Diagnostická sada](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Stav sítě a metriky

Stránka **přehled** Azure monitor pro sítě nabízí snadnou představu o inventáři síťových prostředků spolu se stavem prostředků a výstrahami. Je rozdělená na čtyři klíčové oblasti a funkce hledání a filtrování, Resource Health a metriky, výstrahy. Zobrazení závislostí a

![Stránka Přehled](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>Hledání a filtrování
Zobrazení stavu prostředku a upozornění je možné přizpůsobit pomocí filtrů, jako je **předplatné**, **Skupina prostředků** a **typ prostředku**. Vyhledávací pole poskytuje schopnost vyhledávat prostřednictvím vlastností prostředku.

Vyhledávací pole lze použít k vyhledání prostředků a přidružených prostředků. Například veřejná IP adresa je přidružená k Application Gateway. Při hledání názvu DNS veřejných IP adres se určí jak veřejná, tak přidružená Application Gateway.

![Azure Monitor pro sítě – hledání](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>Resource Health a metrika
Každá dlaždice představuje typ prostředku s počtem instancí nasazených ve všech předplatných vybraných společně se stavem prostředku. V následujícím příkladu jsou nasazená připojení 45 ER a VPN, 44 jsou v pořádku a 1 nedostupné.

![Azure Monitor pro sítě – stav prostředků](media/network-insights-overview/resource-health.png)

Kliknutím na nedostupná připojení ER a VPN se spustí zobrazení metriky. 

![Azure Monitor pro sítě – zobrazení metriky](media/network-insights-overview/metric-view.png)

V zobrazení mřížky můžete kliknout na jednotlivé prvky. Kliknutím na ikonu stav můžete přesměrovat na stav prostředků pro toto připojení. Klikněte na výstrahy a přesměrujte na stránku výstrahy a metriky pro toto připojení. 

### <a name="alerts"></a>Výstrahy
Mřížka **výstrahy** na pravé straně poskytuje zobrazení všech výstrah vygenerovaných pro vybrané prostředky ve všech předplatných. Klikněte na tlačítko počty výstrah a přejděte na stránku podrobné výstrahy.

### <a name="dependency-view"></a>Zobrazení závislostí
Zobrazení **závislostí** pomáhá vizualizovat způsob konfigurace prostředku. Současné zobrazení závislosti je nyní podporováno pro Application Gateway, virtuální síť WAN a Load Balancer. V případě Application Gateway můžete například zobrazit zobrazení závislostí kliknutím na název prostředku Application Gateway v zobrazení mřížky metrik. To platí i pro virtuální sítě WAN a Load Balancer.

![Azure Monitor pro sítě – zobrazení Application Gateway](media/network-insights-overview/application-gateway.png)

Zobrazení **závislostí** pro Application Gateway poskytuje zjednodušený přehled o tom, jak jsou IP adresy front-endu připojené ke posluchačům, pravidlům a back-endovému fondu. Připojené okraje jsou barevně kódované a poskytují další podrobnosti na základě stavu fondu back-endu. Toto zobrazení také nabízí podrobné zobrazení Application Gateway metrik a metrik pro všechny související fondy back-end, jako jsou například sady škálování virtuálních počítačů a instance virtuálních počítačů.

![Azure Monitor pro sítě – zobrazení závislostí](media/network-insights-overview/dependency-view.png)

Graf závislosti umožňuje snadnou navigaci na nastavení konfigurace. Kliknutím pravým tlačítkem na back-end fond získáte přístup k dalším funkcím. Pokud je například back-end fond virtuálním počítačem, můžete k řešení potíží s připojením získat přímý přístup k VIRTUÁLNÍm počítačům s Network Watcher a problémy s připojením.

![Azure Monitor pro sítě – nabídka zobrazení závislostí](media/network-insights-overview/dependency-view-menu.png)

Panel hledání a filtrování v zobrazení závislostí nabízí snadnou možnost vyhledávání v grafu. Například hledání *AppGWTestRule* v následujícím příkladu omezí grafické zobrazení na všechny uzly připojené prostřednictvím *AppGWTestRule*.

![Azure Monitor pro sítě – příklad hledání](media/network-insights-overview/search-example.png)

Různé filtry poskytují lepší informace o konkrétní cestě a stavu. Například vyberte v rozevíracím seznamu **stav** pouze není v *pořádku* , aby se zobrazily všechny hrany, ve kterých stav není v *pořádku*.

Kliknutím na **podrobné zobrazení metriky** spustíte předem nakonfigurovaný sešit s podrobnými metrikami pro Application Gateway, všechny prostředky back-end fondu a IP adresy front-endu. 

## <a name="connectivity"></a><a name="connectivity"></a>Připojení

Karta **připojení** nabízí možnost snadného zobrazení všech testů nakonfigurovaných pomocí monitorování připojení a [monitorování připojení (Preview)](../../network-watcher/connection-monitor-preview.md) pro vybranou sadu předplatných.

![Karta připojení v Azure Monitor pro sítě](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Testy jsou seskupeny podle dlaždic zdroje a cíle a zobrazují stav dostupnosti pro každý test. Dostupná nastavení poskytují snadný přístup ke konfiguraci kritérií dostupnosti na základě neúspěšných kontrol (%) a RTT (MS). Po nastavení hodnot pro každý test se aktualizuje stav na základě kritérií výběru.

![Testy připojení v Azure Monitor pro sítě](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

Kliknutím na dlaždici zdroj nebo cíl se spustí zobrazení metriky.

![Metriky připojení v Azure Monitor pro sítě](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


V zobrazení mřížky můžete kliknout na jednotlivé prvky. Kliknutím na ikonu **dostupnosti** můžete přesměrovat na stránku portálu **Sledování připojení** , aby se zobrazila chyba směrování podle topologie směrování směrování a zjištěné problémy s dopadem na konektivitu. Kliknutím na **výstrahy** přesměrujte na výstrahy a **Zkontrolujte neúspěšné procento/dobu odezvy** na stránku metriky pro vybrané monitorování připojení.

V mřížce **výstrahy**   na pravé straně najdete zobrazení všech výstrah vygenerovaných pro testy připojení nakonfigurované ve všech předplatných. Klikněte na tlačítko počty výstrah a přejděte na stránku podrobné výstrahy.

## <a name="traffic"></a><a name="traffic"></a>Provoz
Karta přenos poskytuje přístup ke všem skupin zabezpečení sítě nakonfigurovaným pro [protokoly toku NSG](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) a [Analýza provozu](../../network-watcher/traffic-analytics.md) pro vybranou sadu předplatných a seskupená podle umístění. Funkce hledání, která je k dispozici na této kartě, umožňuje identifikovat skupin zabezpečení sítě nakonfigurovanou pro vyhledaná IP adresu. Ve svém prostředí můžete hledat libovolnou IP adresu a v místním zobrazení se zobrazí všechny skupin zabezpečení sítě spolu s protokoly toků NSG a stavem konfigurace analýzy provozu.

![Zobrazení přenosů v Azure Monitor pro sítě](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

Kliknutím na dlaždici jakékoli oblasti se spustí zobrazení mřížky, které vám umožní snadno zobrazit a nakonfigurovat protokoly toku NSG a Analýza provozu.  

![Zobrazení oblasti provozu v Azure Monitor pro sítě](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

V zobrazení mřížky můžete kliknout na jednotlivé prvky. Kliknutím na stav konfigurace upravíte protokol toku NSG a konfiguraci Analýza provozu. Kliknutím na výstrahy přesměrujete na výstrahy provozu nakonfigurované pro vybrané NSG. Podobně můžete přejít na zobrazení Analýza provozu kliknutím na pracovní prostor.  

Mřížka **výstrahy**   na pravé straně poskytuje zobrazení všech výstrah na základě Analýza provozu v pracovním prostoru ve všech předplatných. Klikněte na tlačítko počty výstrah a přejděte na stránku podrobné výstrahy.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Diagnostická sada
Diagnostická sada poskytuje přístup ke všem diagnostickým funkcím dostupným pro řešení potíží se sítí. V tomto rozevíracím seznamu získáte přístup k funkcím, jako je například [zachytávání paketů](../../network-watcher/network-watcher-packet-capture-overview.md), [řešení potíží s VPN](../../network-watcher/network-watcher-troubleshoot-overview.md), [odstraňování potíží s připojením](../../network-watcher/network-watcher-connectivity-overview.md), ověření [dalšího segmentu směrování](../../network-watcher/network-watcher-next-hop-overview.md) a [protokolu IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md).

![Karta diagnostická sada](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Řešení potíží 

Obecné pokyny k odstraňování potíží najdete v článku věnovaném [řešení problémů](troubleshoot-workbooks.md)na základě vyhrazeného sešitu.

Tato část vám pomůže s diagnostikou a řešením potíží s některými běžnými problémy, se kterými se můžete setkat při používání Azure Monitor pro sítě. K vyhledání informací týkajících se konkrétního problému použijte níže uvedený seznam.

### <a name="resolving-performance-issues-or-failures"></a>Řešení potíží s výkonem nebo selhání

Chcete-li pomoct řešit problémy související se sítí, které identifikujete pomocí Azure Monitor pro sítě, přečtěte si dokumentaci k řešení potíží s nefunkčním prostředkem. Problémy – odkazy pro vysoce používané služby jsou uvedené níže.
* Virtual Network (VNET)
* Application Gateway
* VPN Gateway
* ExpressRoute 
* Load Balancer 

### <a name="why-dont-i-see-the-resources-from-all-the-subscriptions-i-have-selected"></a>Proč se nezobrazují prostředky ze všech vybraných předplatných

Network Insights může zobrazit jenom prostředky z 5 předplatných. 

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-network-insights-how-do-i-do-so"></a>Chci udělat změny nebo přidat další vizualizace do Network Insights, jak to mám udělat

Chcete-li provést změny, vyberte úpravou "režim úprav" a upravte sešit. potom můžete svou práci uložit jako nový sešit, který je svázán s určeným předplatným a skupinou prostředků.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Jaký je časový interval, po kterém se připnout k libovolné části sešitů

Používáme časový interval "automatického", proto závisí na tom, jaký časový rozsah je vybraný.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Jaký je časový rozsah, kdy je připnuté část sešitu

Časový rozsah bude záviset na nastavení řídicího panelu.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-network-insights"></a>Co když chci zobrazit další data nebo vytvořit vlastní vizualizace? Jak udělat změny v síťové službě Insights

Sešit, který vidíte v jakémkoli postranním panelu a podrobné zobrazení metriky, můžete upravit pomocí režimu úprav a pak uložit práci jako nový sešit, který bude mít všechny nové změny.


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o monitorování sítě v [tom, co je Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md).
- Seznamte se s scénáři, které jsou navržené tak, aby podporovaly, vytváření nových a přizpůsobení existujících sestav a další informace najdete v tématu [vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../platform/workbooks-overview.md).
