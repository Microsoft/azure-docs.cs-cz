---
title: Azure Monitor pro sítě (Preview)
description: Rychlý přehled pro Azure Monitor pro síť, který poskytuje komplexní přehled o stavu a metriky pro všechny nasazené síťové prostředky bez jakékoli konfigurace.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654864"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor pro sítě (Preview)
Azure Monitor for Network poskytuje komplexní přehled o stavu a metriky pro všechny nasazené síťové prostředky bez jakékoli konfigurace. Pokročilá funkce vyhledávání pomáhá identifikovat závislosti prostředků a umožňuje scénáře, jako je identifikace prostředků, které hostují váš web, pouhým vyhledáním názvu hostovaného webu.

Stránka **Přehled** monitorování sítí Azure pro sítě poskytuje snadný způsob, jak vizualizovat inventář síťových prostředků spolu se stavem prostředků a výstrahami. Je rozdělena do čtyř klíčových funkčních oblastí:

- Vyhledávání a filtrování
- Stav zdrojů a metriky
- Výstrahy 
- Zobrazení závislostí

![Stránka s přehledem](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Vyhledávání a filtrování
Zobrazení stavu prostředků a výstrah lze přizpůsobit pomocí filtrů, jako je **Odběr**, **Skupina prostředků** a **Typ prostředku**. Vyhledávací pole poskytuje možnost prohledávat vlastnosti prostředků.

Vyhledávací pole lze použít k vyhledání zdrojů a přidružených zdrojů. Například veřejná IP adresa je přidružena k aplikační bráně. Při hledání názvu DNS veřejných IP adres se identifikuje veřejná IP adresa i přidružená aplikační brána.

![Search](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Stav a metrika zdrojů
Každá dlaždice představuje typ prostředku s počtem instancí nasazených ve všech předplatných vybraných spolu se stavem prostředku. V níže uvedeném příkladu je nasazeno 105 připojení ER a VPN, 103 je v pořádku a 2 nejsou k dispozici.

![Stav prostředků](media/network-insights-overview/resource-health.png)

Kliknutím na dvě připojení Nedostupné ER a VPN se spustí metrické zobrazení. 

![Zobrazení metriky](media/network-insights-overview/metric-view.png)

Můžete kliknout na každý prvek v zobrazení mřížky. Kliknutím na ikonu Stav přesměrujete na stav prostředků pro toto připojení. Kliknutím na Upozornění přesměrujete na stránku výstrah a metrik pro toto připojení. 

## <a name="alerts"></a>Výstrahy
Mřížka **výstrah** vpravo poskytuje zobrazení všech výstrah generovaných pro vybrané prostředky ve všech předplatných. Kliknutím na počet výstrah přejdete na stránku s podrobnými výstrahami.

## <a name="dependency-view"></a>Zobrazení závislostí
Zobrazení **závislostpomáhá** vizualizovat, jak je prostředek nakonfigurován. V současné době je zobrazení závislostí podporováno pouze pro aplikační bránu. Zobrazení závislostí lze přistupovat kliknutím na název prostředku aplikační brány ze zobrazení mřížky metriky.

![Zobrazení brány aplikace](media/network-insights-overview/application-gateway.png)

Zobrazení **závislostí** pro aplikační bránu poskytuje zjednodušené zobrazení toho, jak jsou ip adresy front-endu připojeny k naslouchacím líným a back-endovým fondům. Spojovací hrany jsou barevně odlišené a poskytují další podrobnosti založené na stavu back-endového fondu. Zobrazení také poskytuje podrobné zobrazení metriky application gateway a metriky pro všechny související back-endové fondy, jako jsou instance VMSS a VM.

![Zobrazení závislostí](media/network-insights-overview/dependency-view.png)

Graf závislostí umožňuje snadnou navigaci do nastavení konfigurace. Klikněte pravým tlačítkem myši na back-end ový fond pro přístup k dalším funkcím. Například pokud back-endový fond je virtuální modul, pak můžete přímo přistupovat k Přehledy virtuálního zařízení a sledování sítě řešení potíží k identifikaci problémů s připojením.

![Nabídka zobrazení závislostí](media/network-insights-overview/dependency-view-menu.png)

Panel hledání a filtrování v zobrazení závislostí poskytuje snadný způsob, jak prohledávat graf. Například hledání *AppGWTestRule* v příkladu níže zúží grafické zobrazení na všechny uzly připojené přes *AppGWTestRule*. 

![Příklad hledání](media/network-insights-overview/search-example.png)

Různé filtry poskytují pomoc zúžit na konkrétní cestu a stav. V rozevíracím souboru **Stav například** vyberte pouze *možnost Není* v pořádku, chcete-li zobrazit všechny hrany, kde je stav *Není v pořádku*.

Kliknutím na **podrobné zobrazení metriky** spustíte předkonfigurovaný sešit s podrobnými metrikami pro aplikační bránu, všechny prostředky back-endového fondu a IP adresy front-endu. 

## <a name="next-steps"></a>Další kroky 

- Další informace o monitorování sítě na webu Co je [Azure Network Watcher?](/azure/network-watcher/network-watcher-monitoring-overview).
