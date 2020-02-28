---
title: Capacity and Performance řešení v Azure Monitor | Microsoft Docs
description: Pomocí řešení Capacity and Performance v části monitorování vám pomůže pochopit kapacitu serverů Hyper-V.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 75c65cf9f76e711a3aeed764de8b92ed619bad2f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666939"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Plánování kapacity virtuálních počítačů Hyper-V pomocí řešení Capacity and Performance (zastaralé)

![Symbol kapacitu a výkon](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Kapacitu a výkon řešení je zastaralá.  Zákazníci, kteří už jste nainstalovali řešení můžete ho nadále používat, ale kapacitu a výkon nelze přidat do jakékoli nové pracovní prostory.

Pomocí řešení Capacity and Performance v části monitorování můžete lépe pochopit kapacitu serverů Hyper-V. Řešení poskytuje přehled o prostředí Hyper-V tím, že zobrazuje celkové využití (využití procesoru, paměti a disku) hostitelů a virtuálních počítačů spuštěných na těchto hostitelích Hyper-V. Shromažďování metrik pro využití procesoru, paměti a disků napříč virtuálními počítači na nich spuštěné a všichni hostitelé.

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
| [Agenti systému Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Ne    | Řešení neshromažďuje informace o datech kapacitu a výkon z přímí agenti systému Linux.|
| [Skupina pro správu SCOM](../../azure-monitor/platform/om-agents.md) | Ano |Řešení shromažďuje data kapacity a výkonu z agentů v připojené skupině pro správu nástroje SCOM. Přímé připojení z agenta nástroje SCOM k Log Analytics se nevyžaduje.|
| [Účet služby Azure Storage](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Ne | Úložiště Azure neobsahuje data kapacitu a výkon.|

## <a name="prerequisites"></a>Požadavky

- V systému Windows Server 2012 nebo vyšší hostitele Hyper-V, ne virtuální počítače je nutné nainstalovat Windows nebo agenty nástroje Operations Manager.


## <a name="configuration"></a>Konfigurace

Proveďte následující krok a přidejte kapacitu a výkon řešení do pracovního prostoru.

- Přidejte Capacity and Performance řešení do svého pracovního prostoru Log Analytics pomocí postupu popsaného v tématu [přidání Log Analytics řešení ze galerie řešení](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Sady Management Pack

Pokud vaši skupinu pro správu nástroje SCOM je připojený k pracovnímu prostoru Log Analytics, pak tyto sady management Pack se nainstalují v aplikaci SCOM při přidání tohoto řešení. Není potřeba žádná konfigurace ani údržba těchto sad Management Pack.

- Microsoft.IntelligencePacks.CapacityPerformance

Událost 1201 vypadá podobně jako:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Při aktualizaci řešení kapacitu a výkon, číslo verze se změní.

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="using-the-solution"></a>Použití řešení

Když přidáte kapacitu a výkon řešení do pracovního prostoru, kapacitu a výkon se přidá do řídicího panelu s přehledem. Tuto dlaždici se zobrazuje počet aktuálně aktivních hostitelů Hyper-V a vybrat počet aktivních virtuálních počítačů, které byly sledovány pro toto časové období.

![Dlaždice kapacitu a výkon](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Zkontrolujte využití

Kliknutím na dlaždici kapacitu a výkon otevřete řídicí panel kapacitu a výkon. Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec uvádí až deset položek odpovídajících kritériím tohoto sloupce pro zadaný obor a časový rozsah. Kliknutím na **Zobrazit vše** v dolní části sloupce nebo na záhlaví sloupce můžete spustit hledání v protokolu, které vrátí všechny záznamy.

- **Dvou**
    - **Využití CPU hostitele** Zobrazuje grafický trend využití CPU hostitelských počítačů a seznam hostitelů na základě vybraného časového období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název hostitele a otevřete prohledávání protokolů a zobrazte podrobnosti čítače CPU pro hostované virtuální počítače.
    - **Využití paměti hostitele** Zobrazuje grafický trend využití paměti hostitelských počítačů a seznamu hostitelů na základě vybraného časového období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název hostitele a otevřete prohledávání protokolů a zobrazte podrobnosti čítače paměti pro hostované virtuální počítače.
- **Virtual Machines**
    - **Využití CPU virtuálních počítačů** Zobrazuje grafický trend využití procesoru u virtuálních počítačů a seznam virtuálních počítačů na základě vybraného časového období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase pro hlavní 3 virtuální počítače. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název virtuálního počítače a otevřete prohledávání protokolů a zobrazte podrobnosti agregované čítače CPU pro virtuální počítač.
    - **Využití paměti virtuálního počítače** Zobrazuje grafický trend využití paměti pro virtuální počítače a seznam virtuálních počítačů na základě vybraného časového období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase pro hlavní 3 virtuální počítače. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název virtuálního počítače a otevřete prohledávání protokolů a zobrazte podrobnosti čítače agregované paměti pro virtuální počítač.
    - **Celkový počet** vstupně-výstupních operací virtuálního počítače Zobrazuje grafický trend celkového počtu vstupně-výstupních operací disku pro virtuální počítače a seznam virtuálních počítačů s jednotlivými vstupně-výstupních operací na základě vybraného časového období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase pro hlavní 3 virtuální počítače. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název virtuálního počítače a otevřete prohledávání protokolů a zobrazení agregované vstupně-výstupních diskových čítač podrobnosti pro virtuální počítač.
    - **Celková propustnost disku virtuálního počítače** Zobrazuje grafický trend celkové propustnosti disku pro virtuální počítače a seznam virtuálních počítačů s celkovou propustností disku pro každý z nich, na základě vybraného časového období. Najeďte myší spojnicový graf, chcete-li zobrazit podrobnosti pro konkrétní bod v čase pro hlavní 3 virtuální počítače. Klepněte na graf, chcete-li zobrazit více podrobností v prohledávání protokolu. Klikněte na libovolný název virtuálního počítače a otevřete prohledávání protokolů a zobrazte podrobnosti čítače propustnost agregované celkový počet disků pro virtuální počítač.
- **Sdílené svazky v clusteru**
    - **Celková propustnost** Zobrazuje součet operací čtení a zápisu na sdílených svazcích clusteru.
    - **Celkový počet IOPS** Zobrazuje součet vstupně-výstupních operací za sekundu na sdílených svazcích clusteru.
    - **Celková latence** Zobrazuje celkovou latenci na sdílených svazcích clusteru.
- **Hustota hostitele** V horní dlaždici se zobrazuje celkový počet hostitelů a virtuálních počítačů, které jsou k dispozici pro řešení. Klepněte na horní dlaždice k zobrazení dalších podrobností v prohledávání protokolu. Obsahuje také seznam všechny hostitele a počet virtuálních počítačů, které jsou hostované. Klikněte na hostitele, chcete-li zobrazit podrobnosti výsledků virtuálního počítače v prohledávání protokolu.


![řídicí panel hostitele okno](./media/capacity-performance/dashboard-hosts.png)

![řídicí panel okno virtual machines](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Vyhodnocení výkonu

Produkční výpočetního prostředí liší výrazně z jedné organizace. Kromě toho může kapacitu a výkon úloh závisí na jak běží vaše virtuální počítače, a co byste zvážit normální. Konkrétní postupy, které vám pomůže měřit výkon by pravděpodobně nelze použít pro vaše prostředí. Takže více zobecnit doporučené pokyny je vhodnější pomoct. Microsoft zveřejňuje řadu doporučený články, které umožňují měřit výkon.

Souhrnně řečeno, řešení shromažďuje kapacitu a výkon data z nejrůznějších zdrojů, včetně čítačů výkonu. Využijte tato data o kapacitě a výkonu, která se zobrazují na různých površích v řešení, a porovnejte je s výsledky při [měření výkonu na článku technologie Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) . I když se článek byl publikován dřívějška, metriky, důležité informace a pokyny jsou stále platné. Tento článek obsahuje odkazy na další užitečné zdroje informací.


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


## <a name="next-steps"></a>Další kroky
* Pomocí [prohledávání protokolů v Log Analytics](../../azure-monitor/log-query/log-query-overview.md) můžete zobrazit podrobná data Capacity and Performance.
