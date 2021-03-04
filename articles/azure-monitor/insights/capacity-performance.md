---
title: Capacity and Performance řešení v Azure Monitor | Microsoft Docs
description: Pomocí řešení Capacity and Performance v části monitorování vám pomůže pochopit kapacitu serverů Hyper-V.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: aa08b9f5db5e6371bcd20b473a0c755db3199e7c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704289"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Plánování kapacity virtuálních počítačů Hyper-V pomocí řešení Capacity and Performance (zastaralé)

![Symbol Capacity and Performance](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Řešení Capacity and Performance se už nepoužívá.  Zákazníci, kteří už toto řešení nainstalovali, ho můžou dál používat, ale Capacity and Performance nemůžou být přidané do žádných nových pracovních prostorů.

Pomocí řešení Capacity and Performance v části monitorování můžete lépe pochopit kapacitu serverů Hyper-V. Řešení nabízí přehledy o prostředí Hyper-V, protože vám ukáže celkové využití (CPU, paměť a disk) hostitelů a virtuálních počítačů spuštěných na těchto hostitelích Hyper-V. Metriky se shromažďují pro procesor, paměť a disky napříč všemi vašimi hostiteli a virtuálními počítači, které jsou v nich spuštěné.

Řešení:

-   Zobrazuje hostitele s nejvyšším a nejnižším využitím procesoru a paměti.
-   Zobrazuje virtuální počítače s nejvyšším a nejnižším využitím procesoru a paměti.
-   Zobrazuje virtuální počítače s nejvyšší a nejnižší hodnotou IOPS a využití propustnosti.
-   Zobrazuje, které virtuální počítače jsou spuštěné na kterých hostitelích.
-   Zobrazuje horní disky s vysokou propustností, IOPS a latencí ve sdílených svazcích clusteru.
- Umožňuje přizpůsobit a filtrovat na základě skupin.

> [!NOTE]
> Předchozí verze Capacity and Performance řešení s názvem správa kapacity vyžadovala System Center Operations Manager i System Center Virtual Machine Manager. Toto aktualizované řešení nemá tyto závislosti.


## <a name="connected-sources"></a>Připojené zdroje

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podpora | Popis |
|---|---|---|
| [Agenti systému Windows](../agents/agent-windows.md) | Ano | Řešení shromažďuje informace o kapacitě a datech o výkonu od agentů systému Windows. |
| [Agenti systému Linux](../vm/quick-collect-linux-computer.md) | Ne    | Řešení neshromažďuje informace o kapacitě a datech výkonu z přímých agentů systému Linux.|
| [Skupina pro správu SCOM](../agents/om-agents.md) | Ano |Řešení shromažďuje data o kapacitě a výkonu od agentů v připojené skupině pro správu systému SCOM. Přímé připojení od agenta SCOM k Log Analytics není vyžadováno.|
| [Účet úložiště Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Ne | Služba Azure Storage neobsahuje data o kapacitě a výkonu.|

## <a name="prerequisites"></a>Požadavky

- Agenti Windows nebo Operations Manager musí být nainstalovaná na Windows Serveru 2012 nebo vyšších hostitelích Hyper-V, ne na virtuálních počítačích.


## <a name="configuration"></a>Konfigurace

Chcete-li přidat řešení Capacity and Performance do pracovního prostoru, proveďte následující krok.

- Přidejte Capacity and Performance řešení do svého pracovního prostoru Log Analytics pomocí postupu popsaného v tématu [přidání Log Analytics řešení ze galerie řešení](./solutions.md).

## <a name="management-packs"></a>Sady Management Pack

Pokud je skupina pro správu SCOM připojená k vašemu pracovnímu prostoru Log Analytics, při přidání tohoto řešení se do SCOM nainstalují následující sady Management Pack. Není potřeba žádná konfigurace ani údržba těchto sad Management Pack.

- Microsoft. IntelligencePacks. CapacityPerformance

Událost 1201 se podobá:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Když se aktualizuje řešení Capacity and Performance, číslo verze se změní.

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../agents/om-agents.md).

## <a name="using-the-solution"></a>Použití řešení

Když do svého pracovního prostoru přidáte řešení Capacity and Performance, Capacity and Performance se přidá na řídicí panel přehled. Tato dlaždice zobrazuje počet aktuálně aktivních hostitelů Hyper-V a počet aktivních virtuálních počítačů, které byly monitorovány pro vybrané časové období.

![Dlaždice Capacity and Performance](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Zkontrolovat využití

Kliknutím na dlaždici Capacity and Performance otevřete řídicí panel Capacity and Performance. Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec uvádí až deset položek odpovídajících kritériím tohoto sloupce pro zadaný obor a časový rozsah. Kliknutím na **Zobrazit vše** v dolní části sloupce nebo na záhlaví sloupce můžete spustit hledání v protokolu, které vrátí všechny záznamy.

- **Hostitelé**
    - **Využití CPU hostitele** Zobrazuje grafický trend využití CPU hostitelských počítačů a seznam hostitelů na základě vybraného časového období. Když najedete myší na spojnicový graf, zobrazí se podrobnosti určitého bodu v čase. Kliknutím na graf zobrazíte další podrobnosti v hledání v protokolu. Kliknutím na libovolný název hostitele otevřete prohledávání protokolů a zobrazte podrobnosti o čítačích procesoru pro hostované virtuální počítače.
    - **Využití paměti hostitele** Zobrazuje grafický trend využití paměti hostitelských počítačů a seznamu hostitelů na základě vybraného časového období. Když najedete myší na spojnicový graf, zobrazí se podrobnosti určitého bodu v čase. Kliknutím na graf zobrazíte další podrobnosti v hledání v protokolu. Kliknutím na libovolný název hostitele otevřete prohledávání protokolů a zobrazte podrobnosti o čítačích paměti pro hostované virtuální počítače.
- **Virtual Machines**
    - **Využití CPU virtuálních počítačů** Zobrazuje grafický trend využití procesoru u virtuálních počítačů a seznam virtuálních počítačů na základě vybraného časového období. Když najedete myší na spojnicový graf, zobrazí se podrobnosti o konkrétním bodu v čase pro první 3 virtuální počítače. Kliknutím na graf zobrazíte další podrobnosti v hledání v protokolu. Kliknutím na libovolný název virtuálního počítače otevřete prohledávání protokolů a zobrazte podrobnosti agregovaného čítače CPU pro virtuální počítač.
    - **Využití paměti virtuálního počítače** Zobrazuje grafický trend využití paměti pro virtuální počítače a seznam virtuálních počítačů na základě vybraného časového období. Když najedete myší na spojnicový graf, zobrazí se podrobnosti o konkrétním bodu v čase pro první 3 virtuální počítače. Kliknutím na graf zobrazíte další podrobnosti v hledání v protokolu. Kliknutím na libovolný název virtuálního počítače otevřete prohledávání protokolů a zobrazte podrobnosti o čítači agregované paměti pro virtuální počítač.
    - **Celkový počet** vstupně-výstupních operací virtuálního počítače Zobrazuje grafický trend celkového počtu vstupně-výstupních operací disku pro virtuální počítače a seznam virtuálních počítačů s jednotlivými vstupně-výstupních operací na základě vybraného časového období. Když najedete myší na spojnicový graf, zobrazí se podrobnosti o konkrétním bodu v čase pro první 3 virtuální počítače. Kliknutím na graf zobrazíte další podrobnosti v hledání v protokolu. Kliknutím na libovolný název virtuálního počítače otevřete prohledávání protokolů a zobrazte podrobnosti o čítači agregovaného disku IOPS pro virtuální počítač.
    - **Celková propustnost disku virtuálního počítače** Zobrazuje grafický trend celkové propustnosti disku pro virtuální počítače a seznam virtuálních počítačů s celkovou propustností disku pro každý z nich, na základě vybraného časového období. Když najedete myší na spojnicový graf, zobrazí se podrobnosti o konkrétním bodu v čase pro první 3 virtuální počítače. Kliknutím na graf zobrazíte další podrobnosti v hledání v protokolu. Kliknutím na libovolný název virtuálního počítače otevřete prohledávání protokolů a zobrazte agregované celkové informace o čítačích propustnosti disku pro virtuální počítač.
- **Sdílené svazky v clusteru**
    - **Celková propustnost** Zobrazuje součet operací čtení a zápisu na sdílených svazcích clusteru.
    - **Celkový počet IOPS** Zobrazuje součet vstupně-výstupních operací za sekundu na sdílených svazcích clusteru.
    - **Celková latence** Zobrazuje celkovou latenci na sdílených svazcích clusteru.
- **Hustota hostitele** V horní dlaždici se zobrazuje celkový počet hostitelů a virtuálních počítačů, které jsou k dispozici pro řešení. Kliknutím na horní dlaždici zobrazíte další podrobnosti v hledání v protokolu. Uvádí také seznam všech hostitelů a počet hostovaných virtuálních počítačů. Kliknutím na hostitele přejdete k podrobnostem o výsledcích prohledávání protokolu.


![okno hostitelů řídicího panelu](./media/capacity-performance/dashboard-hosts.png)

![okno virtuální počítače řídicího panelu](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Vyhodnocení výkonu

Produkční výpočetní prostředí se značně liší od jedné organizace k druhé. Také úlohy kapacity a výkonu můžou záviset na tom, jak vaše virtuální počítače běží a co považujete za normální. Konkrétní postupy, které vám pomůžou změřit výkon, se pravděpodobně nevztahují na vaše prostředí. Z tohoto důvodu jsou lépe vhodné Doporučené doprovodné materiály, které vám pomůžou. Microsoft zveřejňuje celou řadu doporučených článků s pokyny, které vám pomůžou změřit výkon.

V rámci sumarizace řešení shromažďuje data o kapacitě a výkonu z nejrůznějších zdrojů, včetně čítačů výkonu. Využijte tato data o kapacitě a výkonu, která se zobrazují na různých površích v řešení, a porovnejte je s výsledky při [měření výkonu na článku technologie Hyper-V](https://www.microsoft.com/en-us/download/details.aspx?id=56495) . I když byl článek dříve publikován, metriky, požadavky a pokyny jsou stále platné. Článek obsahuje odkazy na další užitečné prostředky.


## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

V následující tabulce najdete ukázková hledání v protokolech pro data o kapacitě a výkonu shromážděná a vypočtená tímto řešením.


| Dotaz | Popis |
|:--- |:--- |
| Všechny konfigurace paměti hostitele | &#124; výkonu, kde ObjectName = = "Capacity and Performance" a CounterName = = "hostovaná paměť MB" &#124; sumarizaci MB = AVG (CounterValue) podle InstanceName |
| Všechny konfigurace paměti virtuálního počítače | &#124; výkonu, kde ObjectName = = "Capacity and Performance" a CounterName = = "virtuální počítač přiřadil MB" &#124; sumarizaci MB = AVG (CounterValue) pomocí InstanceName |
| Rozpis celkového počtu vstupně-výstupních operací napříč všemi virtuálními počítači | &#124; výkonu, kde ObjectName = = "Capacity and Performance" a (CounterName = = "čtení VHD/s" nebo CounterName = = "zápisy VHD/s") &#124; Shrnutí AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 1H), CounterName, InstanceName |
| Rozpis celkové propustnosti disků napříč všemi virtuálními počítači | Výkon &#124;, kde ObjectName = = "Capacity and Performance" a (CounterName = = "Read VHD MB/s" nebo CounterName = = "VHD Write MB/s") &#124; Shrnutí AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 1H), CounterName, InstanceName |
| Rozpis celkového počtu vstupně-výstupních operací napříč všemi CSV | &#124; výkonu, kde ObjectName = = "Capacity and Performance" a (CounterName = = "^ čtení/s" nebo CounterName = = "zápisy sdílených svazků clusteru/s") &#124; sumarizace AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 1H), CounterName, InstanceName |
| Rozpis celkové propustnosti napříč všemi CSV | &#124; výkonu, kde ObjectName = = "Capacity and Performance" a (CounterName = = "^ čtení/s" nebo CounterName = = "zápisy sdílených svazků clusteru/s") &#124; sumarizace AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 1H), CounterName, InstanceName |
| Rozpis celkové latence ve všech CSV | Výkon &#124;, kde ObjectName = = "Capacity and Performance" a (CounterName = = "latence čtení sdíleného svazku clusteru" nebo CounterName = = "latence zápisu CSV") &#124; Shrnutí AggregatedValue = AVG (CounterValue) by bin (TimeGenerated, 1H), CounterName, InstanceName |


## <a name="next-steps"></a>Další kroky
* Pomocí [prohledávání protokolů v Log Analytics](../logs/log-query-overview.md) můžete zobrazit podrobná data Capacity and Performance.

