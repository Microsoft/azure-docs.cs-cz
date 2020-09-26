---
title: Jak vynést výkon grafu pomocí Azure Monitor pro virtuální počítače
description: Výkon je funkce Azure Monitor pro virtuální počítače, která automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. Tento článek poskytuje podrobné informace o tom, jak ho používat v různých scénářích.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/31/2020
ms.openlocfilehash: f9578fadfbe057b723af63e338bf8bda63cf6f21
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330906"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Jak vynést výkon grafu pomocí Azure Monitor pro virtuální počítače

Azure Monitor pro virtuální počítače obsahuje sadu grafů výkonu, které cílí na několik klíčových ukazatelů výkonu (KPI), které vám pomůžou určit, jak dobře je virtuální počítač prováděn. Grafy ukazují využití prostředků v časovém intervalu, takže můžete identifikovat kritická místa, anomálie nebo přepnout na jednotlivé počítače a zobrazit tak využití prostředků na základě vybrané metriky. I když existuje mnoho prvků, které je potřeba vzít v úvahu při práci s výkonem, Azure Monitor pro virtuální počítače sleduje klíčové ukazatele výkonu operačního systému související s procesorem, pamětí, síťovým adaptérem a využitím disku. Výkon doplňuje funkci monitorování stavu a pomáhá vystavovat problémy, které naznačují možné selhání součásti systému, podporují optimalizaci a optimalizaci, aby dosáhly efektivity, nebo podporovala plánování kapacity.  

## <a name="limitations"></a>Omezení
Níže jsou uvedena omezení pro shromažďování výkonu pomocí Azure Monitor pro virtuální počítače.

- **Dostupná paměť** není dostupná pro virtuální počítače se systémem Red Hat Linux (RHEL) 6. Tato metrika se počítá z **MemAvailable** , která byla představena v [jádru verze 3,14](http://www.man7.org/linux/man-pages/man1/free.1.html).
- Metriky jsou k dispozici pouze pro datové disky na virtuálních počítačích se systémem Linux pomocí systému souborů XFS nebo řady EXT systému souborů (EXT2, EXT3, EXT4).

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspektiva pro více virtuálních počítačů z Azure Monitor

V Azure Monitor funkce Performance poskytuje zobrazení všech monitorovaných virtuálních počítačů nasazených v rámci pracovních skupin ve vašich předplatných nebo ve vašem prostředí. Chcete-li získat přístup z Azure Monitor, proveďte následující kroky. 

1. V Azure Portal vyberte **monitorovat**. 
2. V části **řešení** vyberte **Virtual Machines** .
3. Vyberte kartu **výkon** .

![Zobrazení seznamu v horní části výkonu virtuálního počítače Insights](media/vminsights-performance/vminsights-performance-aggview-01.png)

Pokud máte více než jeden Log Analytics pracovní prostor, na kartě **horních N grafů** vyberte pracovní prostor povolený pomocí řešení v selektoru **pracovního prostoru** v horní části stránky. Selektor **skupin** bude vracet předplatná, skupiny prostředků, [skupiny počítačů](../platform/computer-groups.md)a sady škálování virtuálních počítačů, které souvisejí s vybraným pracovním prostorem, který můžete použít k dalšímu filtrování výsledků prezentovaných v grafech na této stránce a na dalších stránkách. Váš výběr se vztahuje pouze na funkci výkonu a neprovádí se nad stavem ani mapováním.  

Ve výchozím nastavení jsou grafy zobrazeny v posledních 24 hodinách. Pomocí selektoru **TimeRange** se můžete dotazovat na historické časové rozsahy až na 30 dní, abyste viděli, jak v minulosti vypadal výkon.

Pět grafů využití kapacity se zobrazuje na stránce:

* Využití CPU% – zobrazí pět nejlepších počítačů s nejvyšším průměrem využití procesoru. 
* Dostupná paměť – zobrazuje pět nejlepších počítačů s nejnižším průměrným množstvím dostupné paměti. 
* Využité místo na logickém disku% – zobrazí pět nejlepších počítačů s největším průměrem místa na disku% na všech svazcích disku. 
* Počet odeslaných bajtů – zobrazuje horních pět počítačů s nejvyšším průměrem odeslaných bajtů. 
* Počet přijatých bajtů – zobrazuje pět nejlepších počítačů s nejvyšším průměrem přijatých bajtů. 

Kliknutím na ikonu připnutí v pravém horním rohu kteréhokoli z pěti grafů se vybraný graf připnout na poslední prohlížený řídicí panel Azure.  Z řídicího panelu můžete změnit velikost grafu nebo změnit jeho umístění. Výběr grafu z řídicího panelu vás přesměruje na Azure Monitor pro virtuální počítače a načte správný rozsah a zobrazení.  

Kliknutím na ikonu umístěnou nalevo od ikony připnutí v některém z pěti grafů se otevře **seznam horních N seznamů** .  Tady vidíte využití prostředků pro tuto metriku výkonu podle jednotlivých virtuálních počítačů v zobrazení seznamu a tento počítač má nejvyšší trend.  

![Zobrazení seznamu hlavních N pro vybranou metriku výkonu](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Po kliknutí na virtuální počítač se podokno **vlastností** rozbalí na pravé straně, aby se zobrazily vlastnosti vybrané položky, například systémové informace hlášené operačním systémem, vlastnosti virtuálního počítače Azure atd. Kliknutím na jednu z možností v části **Rychlé odkazy** vás přesměruje na tuto součást přímo z vybraného virtuálního počítače.  

![Podokno vlastností virtuálního počítače](./media/vminsights-performance/vminsights-properties-pane-01.png)

Přepněte na kartu **agregované grafy** pro zobrazení metrik výkonu filtrovaných měření podle průměrných nebo percentilů.  

![Agregované zobrazení výkonu pro virtuální počítač Insights](./media/vminsights-performance/vminsights-performance-aggview-02.png)

K dispozici jsou následující grafy využití kapacity:

* Využití CPU% – výchozí hodnota zobrazuje průměr a horní 95. percentil. 
* Dostupná paměť – výchozí hodnoty zobrazují průměr, hlavní 5. a desátý percentil. 
* Využité místo na logickém disku:% – výchozí hodnota zobrazuje průměr a 95. percentil. 
* Míra odeslaných bajtů – výchozí hodnota zobrazuje průměrné odeslané bajty 
* Frekvence přijímání bajtů – výchozí hodnota zobrazuje průměrné přijaté bajty

Můžete také změnit členitost grafů v časovém intervalu tím, že v selektoru percentilu vyberete **AVG**, **min**, **Max**, **50**, **devadesát**a **95.** .

Pokud chcete zobrazit využití prostředků jednotlivými virtuálními počítači v zobrazení seznamu a zjistit, který počítač má trend s nejvyšším využitím, vyberte kartu **horních N seznamů** .  Stránka s **horním seznamem N** zobrazuje prvních 20 počítačů seřazených podle 95. percentilu pro *využití procesoru*metriky%.  Další počítače si můžete zobrazit tak, že vyberete **načíst další**a výsledky se rozbalí a zobrazí se 500 počítačů. 

>[!NOTE]
>Seznam nemůže zobrazit více než 500 počítačů najednou.  
>

![Příklad hlavní stránky seznamu N](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Chcete-li filtrovat výsledky na konkrétním virtuálním počítači v seznamu, zadejte název jeho počítače do textového pole **Hledat podle názvu** .  

Pokud místo toho chcete zobrazit využití z jiné metriky výkonu, v rozevíracím seznamu **metrika** vyberte **dostupná paměť**, **využité místo na logickém disku:%**, **přijatá síť bajtů/s**nebo **Počet odeslaných bajtů sítě** .  

Když vyberete virtuální počítač ze seznamu, otevře se panel **vlastnosti** na pravé straně stránky a odsud můžete vybrat **Podrobnosti o výkonu**.  Otevře se stránka s **podrobnostmi o virtuálním počítači** , která má tento virtuální počítač v oboru, podobně jako v tomto prostředí při přístupu k výkonu virtuálního počítače přímo z virtuálního počítače Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Zobrazení výkonu přímo z virtuálního počítače Azure

Chcete-li získat přístup přímo z virtuálního počítače, proveďte následující kroky.

1. V Azure Portal vyberte **Virtual Machines**. 
2. V seznamu vyberte virtuální počítač a v části **monitorování** vyberte **přehledy**.  
3. Vyberte kartu **výkon** . 

Tato stránka nejen zahrnuje grafy využití výkonu, ale také tabulku ukazující pro každý zjištěný logický disk, jeho kapacitu, využití a celkový průměr podle jednotlivých měr.  

K dispozici jsou následující grafy využití kapacity:

* Využití CPU% – výchozí hodnota zobrazuje průměr a horní 95. percentil. 
* Dostupná paměť – výchozí hodnoty zobrazují průměr, hlavní 5. a desátý percentil. 
* Využité místo na logickém disku:% – výchozí hodnota zobrazuje průměr a 95. percentil. 
* Logický disk IOPS – výchozí hodnota zobrazuje průměr a 95. percentil.
* Logický disk MB/s – výchozí hodnota zobrazuje průměr a 95. percentil.
* Počet využitých logických disků:% – výchozí hodnota zobrazuje průměr a 95. percentil.
* Míra odeslaných bajtů – výchozí hodnota zobrazuje průměrné odeslané bajty 
* Frekvence přijímání bajtů – výchozí hodnota zobrazuje průměrné přijaté bajty

Kliknutím na ikonu připnutí v pravém horním rohu libovolného grafu si můžete vybraný graf připnout na poslední prohlížený řídicí panel Azure. Z řídicího panelu můžete změnit velikost grafu nebo změnit jeho umístění. Výběr grafu z řídicího panelu vás přesměruje na Azure Monitor pro virtuální počítače a načte podrobné zobrazení výkonu pro virtuální počítač.  

![Výkon virtuálního počítače přímo ze zobrazení virtuálního počítače](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Zobrazení výkonu přímo ze sady škálování virtuálních počítačů Azure

Pokud chcete získat přístup přímo ze sady škálování virtuálních počítačů Azure, proveďte následující kroky.

1. V Azure Portal vyberte **Virtual Machine Scale Sets**.
2. V seznamu vyberte virtuální počítač a v části **monitorování** klikněte na **přehledy** . zobrazí se karta **výkon** .

Tato stránka načte Azure Monitor zobrazení výkonu, která je vymezená na vybranou sadu škálování. Díky tomu můžete v sadě škálování v rámci sady monitorovaných metrik zobrazit agregované instance N, zobrazit agregovaný výkon v rámci sady škálování a zobrazit trendy pro vybrané metriky v rámci jednotlivých instancí N sady škálování. Výběrem instance ze zobrazení seznamu můžete načíst mapu, nebo přejít do podrobného zobrazení výkonu této instance.

Kliknutím na ikonu připnutí v pravém horním rohu libovolného grafu si můžete vybraný graf připnout na poslední prohlížený řídicí panel Azure. Z řídicího panelu můžete změnit velikost grafu nebo změnit jeho umístění. Výběr grafu z řídicího panelu vás přesměruje na Azure Monitor pro virtuální počítače a načte podrobné zobrazení výkonu pro virtuální počítač.  

![Výkon virtuálního počítače přímo ze zobrazení sady škálování virtuálních počítačů](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>K podrobnému zobrazení výkonu pro určitou instanci můžete získat přístup z zobrazení instance pro sadu škálování. V části **Nastavení** přejděte na **instance** a pak zvolte **přehledy**.



## <a name="next-steps"></a>Další kroky

- Naučte se používat [sešity](vminsights-workbooks.md) , které jsou součástí Azure monitor pro virtuální počítače k dalšímu analýze výkonu a metrik sítě.  

- Další informace o zjištěných závislostech aplikace najdete v tématu [zobrazení mapy Azure monitor pro virtuální počítače](vminsights-maps.md).
