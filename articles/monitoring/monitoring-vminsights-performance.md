---
title: Jak graf výkonu pomocí Azure monitoru pro virtuální počítače | Dokumentace Microsoftu
description: Výkon je funkce služby Azure Monitor pro virtuální počítače, který automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi službami. Tento článek obsahuje podrobnosti o tom, jak použít v nejrůznějších scénářích.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: magoedte
ms.openlocfilehash: a63a9d22638231aa076cc4ced9030a378d0c36e4
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429483"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Jak graf výkonu pomocí Azure monitoru pro virtuální počítače
Azure Monitor pro virtuální počítače obsahuje sadu grafy výkonu, které se zaměřují provádění několika klíčových ukazatelů výkonu (KPI), které vám pomohou určit, jak dobře virtuálního počítače. Grafy zobrazit využití prostředků za časové období, abyste mohli identifikovat kritické body, anomálie, nebo přepněte do pohledu výpis každý počítač, chcete-li zobrazit využití prostředků na základě metriky vybrané. I když existují mnoho prvků vzít v úvahu při práci s výkonem, monitorování Azure pro virtuální počítače se zaměřuje na operačním systému tak, jak se prostřednictvím procesor, paměť, síťové adaptéry a disky. Výkon doplňuje funkci monitorování stavu a pomáhá vystavit problémy, které označují selhání součásti systému je to možné, podpora ladění a optimalizace pro dosažení efektivity nebo podporují plánování kapacity.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspektiva více virtuálních počítačů ze služby Azure Monitor
Ze služby Azure Monitor funkce výkon poskytuje zobrazení více virtuálních počítačů nasazených napříč skupinami prostředků ve vašem předplatném nebo v prostředí všech monitorovaných virtuálních počítačů.  Pro přístup ze služby Azure Monitor, proveďte následující kroky. 

1. Na webu Azure Portal, vyberte **monitorování**. 
2. Zvolte **virtuálních počítačů (preview)** v **řešení** oddílu.
3. Vyberte **výkonu** kartu.

![Zobrazení výkonu nejlepších N insights virtuálního počítače](./media/monitoring-vminsights-performance/vminsights-performance-aggview-01.png)

Na **Top N grafy** kartu, pokud máte více než jeden pracovní prostor Log Analytics vyberte pracovní prostor povolený s řešením z **pracovní prostor** selektoru v horní části stránky. **Skupiny** výběr předplatného, skupiny prostředků, vrátí [skupiny počítačů](../log-analytics/log-analytics-computer-groups.md)a škálovací sady virtuálních počítačů z počítače související s vybraný pracovní prostor, který vám umožní dál filtrovat výsledky se zobrazí v grafech na této stránce a na stránkách. Výběr pouze se vztahuje na funkce výkonu a ne přenesou do stavu nebo mapy.  

Ve výchozím nastavení zobrazit grafy posledních 24 hodin. Použití **TimeRange** selektor, můžete zadat dotaz na historické časových rozsahů ukazují, jak výkon hledá v minulosti až 30 dnů.   

Grafy využití kapacity pět zobrazený na stránce jsou:

* Procento využití procesoru – zobrazuje hlavní pět počítačů s nejvyšší průměrné využití procesoru 
* Dostupná paměť – zobrazuje hlavní pět počítačů s nejnižší průměrné množství dostupné paměti 
* Logický Disk používá prostor % – zobrazuje hlavní pět počítačů s nejvyšší průměrný místa použít % přes všechny svazky na disku 
* Frekvence odeslaných bajtů – ukazuje prvních pěti počítačů s nejvyšší průměrný počet odeslaných bajtů 
* Rychlost přijímání bajtů - ukazuje prvních pěti počítačů s nejvyšší průměrný počet odeslaných bajtů 

Otevře se kliknutím na pravém horním rohu jedné z pěti grafy **N nejlepších** zobrazení.  Tady vidíte využití prostředků pro tuto metriku výkonu podle konkrétního virtuálního počítače v zobrazení seznamu a počítač, který je nejvyšší sledování trendů.  

![Horní N seznam metriky výkonu vybrané](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

Když kliknete na virtuálním počítači, **vlastnosti** rozbalený na pravé straně zobrazíte vlastnosti položky vybrané, jako je například hlášených operačního systému, vlastnosti virtuálního počítače Azure a další informace o systému. Kliknutím na jednu z možností v části **rychlé odkazy** části vás přesměrují na tuto funkci přímo ze zvoleného virtuálního počítače.  

![Podokno vlastností virtuálního počítače](./media/monitoring-vminsights-performance/vminsights-properties-pane-01.png)

Přepněte **agregovat grafy** kartu k zobrazení metriky výkonu Filtroval průměr nebo percentily opatření.  

![Zobrazení výkonu agregační insights virtuálního počítače](./media/monitoring-vminsights-performance/vminsights-performance-aggview-02.png)

Jsou k dispozici následující grafy využití kapacity:

* Procento využití procesoru – výchozí zobrazující průměrnou a horní 95. percentil 
* Dostupná paměť – výchozí hodnoty zobrazující průměrnou, prvních 5 a 10. percentilu 
* Logický Disk používá prostor % – výchozí zobrazující průměrnou a 95. percentilu 
* Bajtů odeslaných frekvence – výchozí hodnoty zobrazuje průměrný počet odeslaných bajtů 
* Rychlost přijímání bajtů – výchozí zobrazení průměrný počet bajtů přijatých

Členitost grafy v časovém rozsahu můžete také změnit tak, že vyberete **Avg**, **Min**, **maximální**, **50**,  **90. percentil**, a **95** v modulu pro výběr percentil.   

Chcete-li zobrazit využití prostředků podle konkrétního virtuálního počítače v zobrazení seznamu a zjistit počítač, který je s nejvyšším využitím trendů, vyberte **N nejlepších** kartu.  **N nejlepších** stránka zobrazuje hlavní 20 počítače seřazené podle nejčastěji využívaných podle 95. percentil metriky *% využití CPU*.  Zobrazí se další počítače tak, že vyberete **zatížení více**, a výsledky po rozbalení zobrazují nahoře 500 počítačů. 

>[!NOTE]
>V seznamu nelze zobrazit více než 500 počítačů najednou.  
>

![Hlavní příklad stránky seznamu N](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

K filtrování výsledků na konkrétní virtuální počítač v seznamu, zadejte jeho název počítače **hledat podle názvu** textového pole.  

Pokud by místo toho zobrazit využití z jiné výkonové metriky z **metrika** rozevíracího seznamu vyberte **dostupnou paměť**, **používá logické místa na disku %**,  **Přijaté bajty/s sítě**, nebo **sítě odeslaných bajtů/s** a aktualizuje seznamu zobrazíte využití obor pro tuto metriku.  

Výběr virtuálního počítače ze seznamu se otevře **vlastnosti** panelu na pravé straně stránky a pak můžete vybrat **podrobností výkonu**.  **Podrobnosti virtuálních počítačů** otevře se stránka a působí na tento virtuální počítač, podobně jako v prostředí při přístupu k Insights výkon virtuálního počítače přímo z virtuálního počítače Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Výkon zobrazení přímo z virtuálního počítače Azure
Pokud chcete získat přístup přímo z virtuálního počítače, postupujte takto.

1. Na webu Azure Portal, vyberte **virtuálních počítačů**. 
2. V seznamu vyberte virtuální počítač a **monitorování** zvolte **Insights (preview)**.  
3. Vyberte **výkonu** kartu. 

Tato stránka nejen zahrnuje grafy využití výkonu, ale také tabulka zobrazující pro každý logický disk zjištění své kapacity, využití a celkový průměrný podle jednotlivých míry.  

Jsou k dispozici následující grafy využití kapacity:

* Procento využití procesoru – výchozí zobrazující průměrnou a horní 95. percentil 
* Dostupná paměť – výchozí hodnoty zobrazující průměrnou, prvních 5 a 10. percentilu 
* Logický Disk používá prostor % – výchozí zobrazující průměrnou a 95. percentilu 
* Logický Disk vstupně-výstupních operací – výchozí hodnoty zobrazující průměrnou a 95. percentilu
* Logický Disk MB/s – výchozí hodnoty zobrazující průměrnou a 95. percentilu
* Maximální počet logický Disk používá % – výchozí hodnoty zobrazující průměrnou a 95. percentilu
* Bajtů odeslaných frekvence – výchozí hodnoty zobrazuje průměrný počet odeslaných bajtů 
* Rychlost přijímání bajtů – výchozí zobrazení průměrný počet bajtů přijatých

![Virtuální počítač insights přímo výkonu z virtuálního počítače zobrazení](./media/monitoring-vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="alerting-and-alert-management"></a>Správa výstrah a upozornění 
Metriky výkonu, které jsou povolené v rámci služby Azure Monitor pro virtuální počítače neobsahují předem nakonfigurovaná pravidla upozornění. I když existují [výstrahy týkající se stavu](monitoring-vminsights-health.md#alerting-and-alert-management) odpovídající problémy s výkonem na virtuálním počítači Azure, jako je například vysoké využití procesoru, nedostatek paměti k dispozici, diskové vstupně-výstupních operací, málo místa na disku atd., zjistí tato upozornění na stav se použijí jenom u všech virtuálních počítačů připojení ke stejné pracovní prostor Log Analytics pro monitorování Azure pro virtuální počítače povolena. 

Může však pouze shromažďujeme a ukládáme podmnožinu metriky výkonu, které budete potřebovat v pracovním prostoru Log Analytics. Pokud vaše strategie monitorování vyžaduje analýzy nebo výstrahy, které zahrnují další metriky výkonu aby bylo možné vyhodnotit efektivně kapacitu nebo stav virtuálního počítače, nebo pokud potřebujete možnost zadat vlastní výstrahy kritéria nebo logiky, můžete si konfigurace [shromažďování těchto čítačů výkonu](../log-analytics/log-analytics-data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json) v Log Analytics a definovat [upozornění protokolů](../monitoring-and-diagnostics/alert-log.md?toc=/azure/azure-monitor/toc.json). Log Analytics umožňuje provádět komplexní analýzy s jinými datovými typy a poskytovat delší dobu uchování pro podporu analýzy trendů metrik na druhé straně, jsou jednoduché a schopný zajistit podporu téměř v reálném čase scénáře. Jsou shromážděné [agenta diagnostiky Azure](../virtual-machines/windows/monitor.md) a uložené v úložišti Azure Monitor metriky, umožňují vytvářet výstrahy s nižší latencí a s nižšími náklady.

Projděte si přehled [shromažďování metrik a protokolů pomocí Azure monitoru](monitoring-data-collection.md?toc=/azure/azure-monitor/toc.json) abyste ještě lépe pochopili základní rozdíly a další důležité informace před konfigurací kolekce tyto další metriky a pravidla upozornění.  

## <a name="next-steps"></a>Další postup
Zjistěte, jak použít funkci stavu, najdete v článku [zobrazení monitorování Azure pro virtuální počítače stavu](monitoring-vminsights-health.md), nebo chcete-li zobrazit závislosti zjištěných aplikací, najdete v článku [zobrazení monitorování Azure pro virtuální počítače mapu](monitoring-vminsights-maps.md). 