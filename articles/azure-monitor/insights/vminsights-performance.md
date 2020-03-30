---
title: Jak grafovat výkon pomocí Azure Monitoru pro virtuální počítače
description: Výkon je funkce Azure Monitor pro virtuální počítače, která automaticky zjišťuje součásti aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. Tento článek obsahuje podrobnosti o tom, jak ji používat v různých scénářích.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: a50ba39777e6a9d3d609e584c0c7d872f2a65f35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283714"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Jak grafovat výkon pomocí Azure Monitoru pro virtuální počítače

Azure Monitor pro virtuální počítače obsahuje sadu grafů výkonu, které cílí na několik klíčových ukazatelů výkonu (KU), které vám pomohou určit, jak dobře si virtuální počítač vede. Grafy zobrazují využití zdrojů za určité časové období, takže můžete identifikovat kritická místa, anomálie nebo přepnout na perspektivu se seznamem jednotlivých strojů a zobrazit využití prostředků na základě vybrané metriky. Zatímco existuje mnoho prvků, které je třeba zvážit při práci s výkonem, Azure Monitor pro virtuální počítače monitoruje klíčové ukazatele výkonu operačního systému související s procesorem, pamětí, síťovým adaptérem a využitím disku. Výkon doplňuje funkci monitorování stavu a pomáhá odhalit problémy, které indikují možné selhání systémové součásti, podporují ladění a optimalizaci pro dosažení efektivity nebo podporují plánování kapacity.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspektiva více virtuálních počítačů z Azure Monitoru

Z Azure Monitor funkce výkon poskytuje zobrazení všech monitorovaných virtuálních počítačích nasazených napříč pracovními skupinami ve vašich předplatných nebo ve vašem prostředí. Chcete-li získat přístup z Azure Monitoru, proveďte následující kroky. 

1. Na webu Azure Portal vyberte **Monitor**. 
2. V části **Řešení** zvolte **Virtuální počítače.**
3. Vyberte kartu **Výkon.**

![Zobrazení seznamu Nejlepších n seznamů výsledků virtuálních virtuálních měn](media/vminsights-performance/vminsights-performance-aggview-01.png)

Pokud máte na kartě **Grafy nejvyšší n** více než jeden pracovní prostor Analýzy protokolů, zvolte pracovní prostor povolený s řešením z voliče **pracovního prostoru** v horní části stránky. Volič **skupiny** vrátí odběry, skupiny prostředků, [skupiny počítačů](../platform/computer-groups.md)a škálovací sady virtuálních počítačů související s vybraným pracovním prostorem, které můžete použít k dalšímu filtrování výsledků zobrazených v grafech na této stránce a na ostatních stránkách. Váš výběr se vztahuje pouze na funkci Výkon a nepřenáší se na zdraví nebo mapu.  

Ve výchozím nastavení grafy zobrazují posledních 24 hodin. Pomocí voliče **TimeRange** můžete dotazovat na historické časové rozsahy až 30 dní a ukázat, jak vypadal výkon v minulosti.

Pět grafů využití kapacity zobrazených na stránce jsou:

* Využití procesoru % - zobrazuje pět nejlepších strojů s nejvyšším průměrným využitím procesoru 
* Dostupná paměť - zobrazuje pět nejlepších počítačů s nejnižším průměrným množstvím dostupné paměti 
* Využité místo logického disku % - zobrazuje pět nejlepších počítačů s nejvyšším průměrným využitým místem na disku na všech svazcích disku 
* Míra odeslaných bajtů - zobrazuje pět nejlepších počítačů s nejvyšším průměrem odeslaných bajtů 
* Míra příjmu bajtů - zobrazuje pět nejlepších strojů s nejvyšším průměrem přijatých bajtů 

Kliknutím na ikonu špendlíku v pravém horním rohu některého z pěti grafů připnete vybraný graf na poslední řídicí panel Azure, který jste si naposledy prohlíželi.  Na řídicím panelu můžete změnit velikost a umístění grafu. Výběrgrafu z řídicího panelu vás přesměruje na Azure Monitor pro virtuální počítače a načte správný obor a zobrazení.  

Kliknutím na ikonu umístěnou vlevo od ikony špendlíku na některém z pěti grafů se otevře zobrazení **Seznamu N** .  Tady vidíte využití prostředků pro tuto metriku výkonu podle jednotlivých virtuálních počítačů v zobrazení seznamu a který počítač je nejvyšší trendy.  

![Zobrazení seznamu N v horní části pro vybranou metriku výkonu](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Když kliknete na virtuální počítač, podokno **Vlastnosti** se rozbalí vpravo, aby se zobrazily vlastnosti vybrané položky, jako jsou systémové informace hlášené operačním systémem, vlastnosti virtuálního počítače Azure atd. Kliknutím na jednu z možností v části **Rychlé odkazy** se přesměrujete na tuto funkci přímo z vybraného virtuálního počítače.  

![Podokno Vlastnosti virtuálního počítače](./media/vminsights-performance/vminsights-properties-pane-01.png)

Přepnutím na kartu **Agregované grafy** zobrazíte metriky výkonu filtrované podle průměrných nebo percentilů.  

![Přehledy virtuálních virtuálních společností Agregované zobrazení výkonu](./media/vminsights-performance/vminsights-performance-aggview-02.png)

K dispozici jsou následující grafy využití kapacity:

* % využití procesoru – výchozí hodnoty zobrazující průměr a horní 95. 
* Dostupná paměť – výchozí hodnoty zobrazující průměr, pětá a desetinná percentil 
* Využité místo logického disku % – výchozí hodnoty zobrazující průměr a 95. 
* Míra odeslání bajtů – výchozí hodnoty zobrazující průměrné odeslané bajty 
* Míra příjmu bajtů – výchozí hodnoty zobrazující průměrné přijaté bajty

Rozlišovací schopnost grafů v časovém rozsahu můžete také změnit výběrem **možností Avg**, **Min**, **Max**, **50th**, **90th**a **95th** v selektoru percentilu.

Chcete-li zobrazit využití prostředků jednotlivými virtuálními počítači v zobrazení seznamu a zjistit, který počítač je trendy s nejvyšším využitím, vyberte kartu **Seznam N na nejvyšší úrovni.**  Stránka **Seznam N je** zobrazena mezi 20 nejlepšími počítači seřazených podle nejvyužitějších percentilů pro metrické využití *procesoru %*.  Můžete vidět více strojů výběrem **Load More**a výsledky se rozbalí a zobrazí se 500 nejlepších strojů. 

>[!NOTE]
>V seznamu nelze zobrazit více než 500 počítačů najednou.  
>

![Příklad stránky seznamu N](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Chcete-li filtrovat výsledky na konkrétním virtuálním počítači v seznamu, zadejte jeho název počítače do textového pole **Hledat podle názvu.**  

Pokud byste raději viděli využití z jiné metriky výkonu, v rozevíracím seznamu **Metrika** vyberte možnost **Dostupná paměť**, **Využito logické místo**na disku % , Počet **přijatých síťových bajtů nebo**bajty nebo počet **síťových bajtů** a aktualizace seznamu, aby se zobrazilo využití vymezené pro tuto metriku.  

Výběrem virtuálního počítače ze seznamu se otevře panel **Vlastnosti** na pravé straně stránky a odtud můžete vybrat **podrobnosti výkonu**.  Otevře se stránka **Podrobnosti virtuálního počítače** a je vymezena na tento virtuální počítač, podobné prostředí při přístupu k výkonu přehledů virtuálních zařízení přímo z virtuálního počítače Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Zobrazení výkonu přímo z virtuálního počítače Azure

Chcete-li získat přístup přímo z virtuálního počítače, proveďte následující kroky.

1. Na webu Azure Portal vyberte **Virtuální počítače**. 
2. V seznamu zvolte virtuální hod a v části **Monitorování** zvolte **Přehledy**.  
3. Vyberte kartu **Výkon.** 

Tato stránka obsahuje nejen grafy využití výkonu, ale také tabulku zobrazující každý zjištěný logický disk, jeho kapacitu, využití a celkový průměr podle jednotlivých měr.  

K dispozici jsou následující grafy využití kapacity:

* % využití procesoru – výchozí hodnoty zobrazující průměr a horní 95. 
* Dostupná paměť – výchozí hodnoty zobrazující průměr, pětá a desetinná percentil 
* Využité místo logického disku % – výchozí hodnoty zobrazující průměr a 95. 
* Logické diskové iOPS – výchozí hodnoty zobrazující průměr a 95.
* Logický disk MB/s - výchozí hodnoty zobrazující průměr a 95.
* Maximální počet použitých logických disků % – výchozí hodnoty zobrazující průměr a 95.
* Míra odeslání bajtů – výchozí hodnoty zobrazující průměrné odeslané bajty 
* Míra příjmu bajtů – výchozí hodnoty zobrazující průměrné přijaté bajty

Kliknutím na ikonu špendlíku v pravém horním rohu některého z grafů připnete vybraný graf k poslednímu řídicímu panelu Azure, který jste si prohlíželi. Na řídicím panelu můžete změnit velikost a umístění grafu. Výběr grafu z řídicího panelu přesměruje na Azure Monitor pro virtuální počítače a načte zobrazení podrobností výkonu pro virtuální počítač.  

![Přehledy virtuálních společností Výkon přímo ze zobrazení virtuálních můek](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Zobrazení výkonu přímo ze škálovací sady virtuálních strojů Azure

Chcete-li získat přístup přímo z škálovací sady virtuálních strojů Azure, proveďte následující kroky.

1. Na webu Azure Portal vyberte **škálovací sady virtuálních strojů**.
2. V seznamu zvolte virtuální hod a v části **Monitorování** zvolte **Přehledy,** chcete-li zobrazit kartu **Výkon.**

Tato stránka načte zobrazení výkonu Azure Monitor, s vymezenou na vybrané škálovací sady. To vám umožní zobrazit nejvyšší počet instancí N v sadě měřítek v sadě sledovaných metrik, zobrazit agregovaný výkon v celé škálovací sadě a zobrazit trendy pro vybrané metriky napříč jednotlivými instancemi n škálovací sadou. Výběr instance ze zobrazení seznamu umožňuje načíst mapu nebo přejít do podrobného zobrazení výkonu pro tuto instanci.

Kliknutím na ikonu špendlíku v pravém horním rohu některého z grafů připnete vybraný graf k poslednímu řídicímu panelu Azure, který jste si prohlíželi. Na řídicím panelu můžete změnit velikost a umístění grafu. Výběr grafu z řídicího panelu přesměruje na Azure Monitor pro virtuální počítače a načte zobrazení podrobností výkonu pro virtuální počítač.  

![Přehledy virtuálních počítačů Výkon přímo ze zobrazení škálovací sady virtuálních strojů](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Můžete také přistupovat k podrobnému zobrazení výkonu pro konkrétní instanci z zobrazení instance pro škálovací sadu. Přejděte do **části Instance** v části **Nastavení** a pak zvolte **Přehledy**.



## <a name="next-steps"></a>Další kroky

- Zjistěte, jak používat [sešity,](vminsights-workbooks.md) které jsou součástí Azure Monitoru pro virtuální počítače, k další analýze výkonu a síťových metrik.  

- Informace o zjištěných závislostech aplikací najdete [v tématu Zobrazení mapy Azure Monitor for VM .](vminsights-maps.md)
