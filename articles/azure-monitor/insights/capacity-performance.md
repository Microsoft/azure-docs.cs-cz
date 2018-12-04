---
title: Kapacitu a výkon řešení v Azure Log Analytics | Dokumentace Microsoftu
description: Pomocí řešení kapacitu a výkon v Log Analytics vám objasní, kapacita serverech technologie Hyper-V.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: aa940e238601f06a53247c0da4d1a92f16b6f56c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840601"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-preview"></a>Plánování kapacity virtuálních počítačů Hyper-V s řešením kapacitu a výkon (Preview)

![Symbol kapacitu a výkon](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Kapacitu a výkon řešení je zastaralá.  Zákazníci, kteří už jste nainstalovali řešení můžete ho nadále používat, ale kapacitu a výkon nelze přidat do jakékoli nové pracovní prostory.

Můžete použít řešení kapacitu a výkon v Log Analytics vám objasní, kapacita serverech technologie Hyper-V. Řešení poskytuje přehled o prostředí Hyper-V tím, že zobrazuje celkové využití (využití procesoru, paměti a disku) hostitelů a virtuálních počítačů spuštěných na těchto hostitelích Hyper-V. Shromažďování metrik pro využití procesoru, paměti a disků napříč virtuálními počítači na nich spuštěné a všichni hostitelé.

Řešení:

-   Hostitelé s nejvyšším a nejnižším využití procesoru a paměti
-   Zobrazuje virtuální počítače s nejnižší a nejvyšší využití procesoru a paměti
-   Zobrazuje virtuální počítače s nejnižší a nejvyšší využití vstupně-výstupních operací a propustnosti
-   Zobrazuje, které virtuální počítače jsou spuštěné na hostitelích, které
-   Zobrazuje hlavní disky s vysokou propustností, vstupně-výstupních operací a latence ve sdílených svazků clusteru
- Umožňuje přizpůsobení a filtrovat podle skupin

> [!NOTE]
> Předchozí verze kapacitu a výkon řešení správy kapacity vyžaduje System Center Operations Manager a System Center Virtual Machine Manager. Toto řešení aktualizované nemá těchto závislostí.


## <a name="connected-sources"></a>Připojené zdroje

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podpora | Popis |
|---|---|---|
| [Agenti systému Windows](../../azure-monitor/platform/agent-windows.md) | Ano | Řešení shromažďuje informace o datech kapacitu a výkon z agentů Windows. |
| [Agenti systému Linux](../../log-analytics/log-analytics-quick-collect-linux-computer.md) | Ne    | Řešení neshromažďuje informace o datech kapacitu a výkon z přímí agenti systému Linux.|
| [Skupiny pro správu SCOM](../../log-analytics/log-analytics-om-agents.md) | Ano |Řešení shromažďuje data kapacity a výkonu z agentů v připojené skupině pro správu nástroje SCOM. Přímé připojení z agenta nástroje SCOM k Log Analytics se nevyžaduje.|
| [Účet služby Azure Storage](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Ne | Úložiště Azure neobsahuje data kapacitu a výkon.|

## <a name="prerequisites"></a>Požadavky

- V systému Windows Server 2012 nebo vyšší hostitele Hyper-V, ne virtuální počítače je nutné nainstalovat Windows nebo agenty nástroje Operations Manager.


## <a name="configuration"></a>Konfigurace

Proveďte následující krok a přidejte kapacitu a výkon řešení do pracovního prostoru.

- Přidat kapacitu a výkon řešení do pracovního prostoru Log Analytics pomocí postupu popsaného v [přidání řešení Log Analytics z Galerie řešení](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Sady Management Pack

Pokud vaši skupinu pro správu nástroje SCOM je připojený k pracovnímu prostoru Log Analytics, pak tyto sady management Pack se nainstalují v aplikaci SCOM při přidání tohoto řešení. Není potřeba žádná konfigurace ani údržba těchto sad Management Pack.

- Microsoft.IntelligencePacks.CapacityPerformance

Událost 1201 vypadá podobně jako:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Při aktualizaci řešení kapacitu a výkon, číslo verze se změní.

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../../log-analytics/log-analytics-om-agents.md).

## <a name="using-the-solution"></a>Použití řešení

Když přidáte kapacitu a výkon řešení do pracovního prostoru, kapacitu a výkon se přidá do řídicího panelu s přehledem. Tuto dlaždici se zobrazuje počet aktuálně aktivních hostitelů Hyper-V a vybrat počet aktivních virtuálních počítačů, které byly sledovány pro toto časové období.

![Dlaždice kapacitu a výkon](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Zkontrolujte využití

Kliknutím na dlaždici kapacitu a výkon otevřete řídicí panel kapacitu a výkon. Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec uvádí až deset položek odpovídajících kritériím tohoto sloupce pro zadaný obor a časový rozsah. Kliknutím na **Zobrazit vše** v dolní části sloupce nebo na záhlaví sloupce můžete spustit hledání v protokolu, které vrátí všechny záznamy.

- **Hostitelé**
    - **Využití procesoru hostitele** zobrazí grafické zobrazení trendu využití výkonu procesoru hostitelských počítačů a seznam hostitelů, v závislosti na vybraném časovém období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název hostitele a otevřete prohledávání protokolů a zobrazte podrobnosti čítače CPU pro hostované virtuální počítače.
    - **Hostování využití paměti v aplikaci** zobrazí grafické zobrazení trendu využití paměti v aplikaci hostitelských počítačích a seznam hostitelů, v závislosti na vybraném časovém období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název hostitele a otevřete prohledávání protokolů a zobrazte podrobnosti čítače paměti pro hostované virtuální počítače.
- **Virtual Machines**
    - **Využití procesoru virtuálního počítače** zobrazí grafické zobrazení trendu využití výkonu procesoru virtuálních počítačů a seznam virtuálních počítačů, na základě vybraného časového období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase pro hlavní 3 virtuální počítače. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název virtuálního počítače a otevřete prohledávání protokolů a zobrazte podrobnosti agregované čítače CPU pro virtuální počítač.
    - **Využití paměti virtuálního počítače** zobrazí grafické zobrazení trendu využití paměti virtuálních počítačů a seznam virtuálních počítačů, na základě vybraného časového období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase pro hlavní 3 virtuální počítače. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název virtuálního počítače a otevřete prohledávání protokolů a zobrazte podrobnosti čítače agregované paměti pro virtuální počítač.
    - **Celkový počet IOPS disku virtuálního počítače** zobrazí seznam virtuálních počítačů s vstupně-výstupních operací a grafické zobrazení trendu celkový počet vstupně-výstupních diskových pro virtuální počítače založené na vybrané časové období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase pro hlavní 3 virtuální počítače. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název virtuálního počítače a otevřete prohledávání protokolů a zobrazení agregované vstupně-výstupních diskových čítač podrobnosti pro virtuální počítač.
    - **Celková propustnost disku virtuálního počítače** zobrazuje grafické zobrazení trendu celková propustnost disku pro virtuální počítače a seznam virtuálních počítačů s propustností celkový počet disků, na základě vybraného časového období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase pro hlavní 3 virtuální počítače. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název virtuálního počítače a otevřete prohledávání protokolů a zobrazte podrobnosti čítače propustnost agregované celkový počet disků pro virtuální počítač.
- **Sdílené svazky clusteru**
    - **Celková propustnost** zobrazí součet obou čte a zapisuje na sdílený svazek clusteru.
    - **Celkový počet vstupně-výstupních operací** zobrazovat součet vstupně výstupní operace za sekundu na sdílený svazek clusteru.
    - **Celková latence** ukazuje celkovou latenci na sdílený svazek clusteru.
- **Hustota hostitele** nejvyšší dlaždice ukazuje celkový počet hostitelů a virtuálních počítačů, které jsou k dispozici pro řešení. Klepněte na horní dlaždice k zobrazení dalších podrobností v prohledávání protokolu. Obsahuje také seznam všechny hostitele a počet virtuálních počítačů, které jsou hostované. Klikněte na hostitele, chcete-li zobrazit podrobnosti výsledků virtuálního počítače v prohledávání protokolu.


![řídicí panel hostitele okno](./media/capacity-performance/dashboard-hosts.png)

![řídicí panel okno virtual machines](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Vyhodnocení výkonu

Produkční výpočetního prostředí liší výrazně z jedné organizace. Kromě toho může kapacitu a výkon úloh závisí na jak běží vaše virtuální počítače, a co byste zvážit normální. Konkrétní postupy, které vám pomůže měřit výkon by pravděpodobně nelze použít pro vaše prostředí. Takže více zobecnit doporučené pokyny je vhodnější pomoct. Microsoft zveřejňuje řadu doporučený články, které umožňují měřit výkon.

Souhrnně řečeno, řešení shromažďuje kapacitu a výkon data z nejrůznějších zdrojů, včetně čítačů výkonu. Použít kapacitu a výkon data, která se zobrazí v různých ploch v řešení a porovnat výsledky těm na [měření výkonu v technologii Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) článku. I když se článek byl publikován dřívějška, metriky, důležité informace a pokyny jsou stále platné. Tento článek obsahuje odkazy na další užitečné zdroje informací.


## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

Následující tabulka obsahuje ukázky hledání v protokolech pro kapacitu a výkon data shromážděná a počítá tímto řešením.


| Dotaz | Popis |
|:--- |:--- |
| Všechny konfigurace paměti hostitele | Perf &#124; kde ObjectName == "Kapacity a výkonu" a hodnota CounterName == "Hostitele přiřadit MB paměti" &#124; shrnout MB = avg(CounterValue) podle InstanceName |
| Všechny konfigurace paměti virtuálního počítače | Perf &#124; kde ObjectName == "Kapacity a výkonu" a hodnota CounterName == "Virtuální počítač přiřazen MB paměti" &#124; shrnout MB = avg(CounterValue) podle InstanceName |
| Rozpis celkový počet IOPS na disku ve všech virtuálních počítačích | Perf &#124; kde ObjectName == "Kapacity a výkonu" a (hodnota CounterName == "virtuálního pevného disku čtení/s, nebo hodnota CounterName =="Zápisy virtuálního pevného disku/s") &#124; summarize AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 1 hodina), CounterName, InstanceName |
| Rozpis celkové propustnosti disků ve všech virtuálních počítačích | Perf &#124; kde ObjectName == "Kapacity a výkonu" a (hodnota CounterName == "virtuálního pevného disku pro čtení MB/s, nebo hodnota CounterName =="Virtuálního pevného disku zápis MB/s") &#124; summarize AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 1 hodina), CounterName, InstanceName |
| Rozpis celkový počet IOPS na všech sdílených svazcích clusteru | Perf &#124; kde ObjectName == "Kapacity a výkonu" a (hodnota CounterName == "sdíleného svazku clusteru čtení/s, nebo hodnota CounterName =="Sdíleného svazku clusteru zápisů za sekundu") &#124; summarize AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 1 hodina), CounterName, InstanceName |
| Rozpis celkové propustnosti ve všech sdílených svazcích clusteru | Perf &#124; kde ObjectName == "Kapacity a výkonu" a (hodnota CounterName == "sdíleného svazku clusteru čtení/s, nebo hodnota CounterName =="Sdíleného svazku clusteru zápisů za sekundu") &#124; summarize AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 1 hodina), CounterName, InstanceName |
| Rozpis celkové latence ve všech sdílených svazcích clusteru | Perf &#124; kde ObjectName == "Kapacity a výkonu" a (hodnota CounterName == "Latence pro čtení sdíleného svazku clusteru" nebo hodnota CounterName == "Latence zápisu sdíleného svazku clusteru") &#124; summarize AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 1 hodina), CounterName, InstanceName |


## <a name="next-steps"></a>Další postup
* Použití [prohledávání protokolů v Log Analytics](../../azure-monitor/log-query/log-query-overview.md) k zobrazení podrobných dat o kapacitě a výkonu.
