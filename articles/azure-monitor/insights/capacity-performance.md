---
title: Řešení kapacity a výkonu ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Pomocí řešení Kapacita a výkon v monitoru pomůžete pochopit kapacitu serverů Hyper-V.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 75c65cf9f76e711a3aeed764de8b92ed619bad2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666939"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Plánování kapacity virtuálního počítače Hyper-V pomocí řešení Kapacita a výkon (zastaralé)

![Symbol kapacity a výkonu](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Řešení Kapacita a výkon byla zastaralá.  Zákazníci, kteří již nainstalovali řešení, jej mohou nadále používat, ale kapacita a výkon nelze přidat do žádného nového pracovního prostoru.

Řešení Kapacita a výkon můžete použít v monitoru, které vám pomůže pochopit kapacitu serverů Hyper-V. Řešení poskytuje přehled o prostředí Hyper-V tím, že vám ukáže celkové využití (procesor, paměť a disk) hostitelů a virtuálních počítačů spuštěných na těchto hostitelích Hyper-V. Metriky se shromažďují pro procesor, paměť a disky napříč všemi hostiteli a virtuálními počítači, které na nich běží.

Řešení:

-   Zobrazuje hostitele s nejvyšším a nejnižším využitím procesoru a paměti.
-   Zobrazuje virtuální počítače s nejvyšším a nejnižším využitím procesoru a paměti.
-   Zobrazuje virtuální virtuální hody s nejvyšším a nejnižším vypáčem a využitím propustností.
-   Zobrazuje, které virtuální počítačky běží na hostitelích
-   Zobrazuje horní disky s vysokou propustností, vipic a latencí ve sdílených svazcích clusteru.
- Umožňuje přizpůsobit a filtrovat na základě skupin

> [!NOTE]
> Předchozí verze řešení Kapacita a výkon s názvem Správa kapacity vyžadovala jak správce operací system centra, tak správce virtuálních strojů system center. Toto aktualizované řešení nemá tyto závislosti.


## <a name="connected-sources"></a>Připojené zdroje

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podpora | Popis |
|---|---|---|
| [Agenti systému Windows](../../azure-monitor/platform/agent-windows.md) | Ano | Řešení shromažďuje informace o kapacitě a výkonu od agentů systému Windows. |
| [Agenti systému Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Ne    | Řešení neshromažďuje informace o kapacitě a výkonu od přímých agentů Linuxu.|
| [Skupina pro správu SCOM](../../azure-monitor/platform/om-agents.md) | Ano |Řešení shromažďuje údaje o kapacitě a výkonu od agentů v připojené skupině pro správu SCOM. Přímé připojení od agenta SCOM k log analytics není vyžadováno.|
| [Účet služby Azure Storage](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Ne | Úložiště Azure nezahrnuje data o kapacitě a výkonu.|

## <a name="prerequisites"></a>Požadavky

- Agenti systému Windows nebo nástroje Operations Manager musí být nainstalováni na hostitelích Hyper-V systému Windows Server 2012 nebo vyšších, nikoli ve virtuálních počítačích.


## <a name="configuration"></a>Konfigurace

Provedením následujícího kroku přidejte řešení Kapacita a výkon do pracovního prostoru.

- Přidejte řešení Kapacita a výkon do pracovního prostoru Log Analytics pomocí procesu popsaného v [řešenípřidat log analytics z Galerie řešení](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Sady Management Pack

Pokud je vaše skupina pro správu SCOM připojena k pracovnímu prostoru Log Analytics, budou následující sady Management Pack nainstalovány v scom při přidání tohoto řešení. Není potřeba žádná konfigurace ani údržba těchto sad Management Pack.

- Microsoft.IntelligencePacks.CapacityPerformance

Událost 1201 se podobá:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Při aktualizaci řešení Kapacita a výkon se změní číslo verze.

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="using-the-solution"></a>Použití řešení

Když přidáte řešení Kapacita a výkon do pracovního prostoru, kapacita a výkon se přidá do řídicího panelu Přehled. Tato dlaždice zobrazuje počet aktuálně aktivních hostitelů Technologie Hyper-V a počet aktivních virtuálních počítačů, které byly monitorovány pro vybrané časové období.

![Dlaždice Kapacita a výkon](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Využití recenze

Kliknutím na dlaždici Kapacita a výkon otevřete řídicí panel Kapacita a výkon. Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec uvádí až deset položek odpovídajících kritériím tohoto sloupce pro zadaný obor a časový rozsah. Kliknutím na **Zobrazit vše** v dolní části sloupce nebo na záhlaví sloupce můžete spustit hledání v protokolu, které vrátí všechny záznamy.

- **Hostitelů**
    - **Využití procesoru hostitele** Zobrazuje grafický trend využití procesoru hostitelských počítačů a seznam hostitelů na základě zvoleného časového období. Najeďte přes spojnicový graf a zobrazte podrobnosti pro konkrétní bod v čase. Kliknutím na graf zobrazíte další podrobnosti ve vyhledávání v protokolu. Kliknutím na libovolný název hostitele otevřete hledání protokolu a zobrazte podrobnosti čítače procesoru pro hostované virtuální počítače.
    - **Využití paměti hostitele** Zobrazuje grafický trend využití paměti hostitelských počítačů a seznam hostitelů na základě zvoleného časového období. Najeďte přes spojnicový graf a zobrazte podrobnosti pro konkrétní bod v čase. Kliknutím na graf zobrazíte další podrobnosti ve vyhledávání v protokolu. Kliknutím na libovolný název hostitele otevřete hledání protokolu a zobrazte podrobnosti čítače paměti pro hostované virtuální počítače.
- **Virtuální počítače**
    - **Využití procesoru virtuálního počítače** Zobrazuje grafický trend využití procesoru virtuálních počítačů a seznam virtuálních počítačů na základě zvoleného časového období. Najeďte přes spojnicový graf a zobrazte podrobnosti o konkrétním bodu v čase pro horní 3 virtuální hod. Kliknutím na graf zobrazíte další podrobnosti ve vyhledávání v protokolu. Kliknutím na libovolný název virtuálního počítače otevřete hledání protokolu a zobrazte agregované podrobnosti čítače procesoru pro virtuální počítač.
    - **Využití paměti virtuálního zařízení** Zobrazuje grafický trend využití paměti virtuálních počítačů a seznam virtuálních počítačů na základě zvoleného časového období. Najeďte přes spojnicový graf a zobrazte podrobnosti o konkrétním bodu v čase pro horní 3 virtuální hod. Kliknutím na graf zobrazíte další podrobnosti ve vyhledávání v protokolu. Kliknutím na libovolný název virtuálního počítači otevřete hledání protokolu a zobrazte podrobnosti čítače agregované paměti pro virtuální počítač.
    - **VOP** Celkový počet virtuálních počítačů disku Zobrazuje grafický trend celkového vodvicích bodů disku pro virtuální počítače a seznam virtuálních počítačů s vodicími prvky pro každý z nich na základě vybraného časového období. Najeďte přes spojnicový graf a zobrazte podrobnosti o konkrétním bodu v čase pro horní 3 virtuální hod. Kliknutím na graf zobrazíte další podrobnosti ve vyhledávání v protokolu. Kliknutím na libovolný název virtuálního počítače otevřete hledání protokolu a zobrazte podrobnosti čítače agregovaných diskových viopů pro virtuální počítač.
    - **Propustnost disku virtuálního počítače** Zobrazuje grafický trend celkové propustnostdisku disku pro virtuální počítače a seznam virtuálních počítačů s celkovou propustností disku pro každý z nich na základě vybraného časového období. Najeďte přes spojnicový graf a zobrazte podrobnosti o konkrétním bodu v čase pro horní 3 virtuální hod. Kliknutím na graf zobrazíte další podrobnosti ve vyhledávání v protokolu. Kliknutím na libovolný název virtuálního počítače otevřete hledání protokolu a zobrazte podrobnosti o celkové propustnosti disku pro virtuální počítač.
- **Clusterované sdílené svazky**
    - **Celková propustnost** Zobrazuje součet čtení i zápisů na clusterovaných sdílených svazcích.
    - **Celkový počet iops** Zobrazuje součet vstupních a výstupních operací za sekundu na clusterovaných sdílených svazcích.
    - **Celková latence** Zobrazuje celkovou latenci na clusterovaných sdílených svazcích.
- **Hustota hostitele** Horní dlaždice zobrazuje celkový počet hostitelů a virtuálních počítačů, které jsou k dispozici pro řešení. Kliknutím na horní dlaždici zobrazíte další podrobnosti při hledání protokolu. Také uvádí všechny hostitele a počet virtuálních počítačů, které jsou hostované. Kliknutím na hostitele přejdete k podrobnostem o výsledcích virtuálního soudu při hledání protokolu.


![řídicí panel Hosts blade](./media/capacity-performance/dashboard-hosts.png)

![okno virtuálních počítačů řídicího panelu](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Vyhodnocení výkonu

Produkční výpočetní prostředí se v jednotlivých organizacích značně liší. Úlohy kapacity a výkonu mohou také záviset na tom, jak jsou vaše virtuální počítače spuštěny a co považujete za normální. Konkrétní postupy, které vám pomohou měřit výkon by pravděpodobně neplatí pro vaše prostředí. Proto je pro pomoc vhodnější zobecnější normativní vedení. Společnost Microsoft publikuje různé normativní pokyny články, které vám pomohou měřit výkon.

Chcete-li shrnout, řešení shromažďuje data o kapacitě a výkonu z různých zdrojů, včetně čítačů výkonu. Použijte data o kapacitě a výkonu, která se v řešení prezentují na různých površích, a porovnejte výsledky s výsledky v článku [Měření výkonu na technologii Hyper-V.](https://msdn.microsoft.com/library/cc768535.aspx) Přestože byl článek publikován před nějakou dobou, metriky, důležité informace a pokyny jsou stále platné. Článek obsahuje odkazy na další užitečné zdroje.


## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

Následující tabulka obsahuje ukázkové hledání protokolů pro údaje o kapacitě a výkonu shromážděné a vypočtené tímto řešením.


| Dotaz | Popis |
|:--- |:--- |
| Všechny konfigurace paměti hostitele | Perf &#124; kde ObjectName == "Kapacita a výkon" a CounterName == "Hostitel přiřazená paměť MB" &#124; shrnout MB = avg(CounterValue) podle InstanceName |
| Všechny konfigurace paměti virtuálního počítače | Perf &#124; kde ObjectName == "Kapacita a výkon" a CounterName == "VM přiřazená paměť MB" &#124; shrnout MB = avg(CounterValue) podle InstanceName |
| Rozdělení celkových viop ů disku na všechny virtuální počítače | Perf &#124; kde ObjectName == "Kapacita a výkon" a (CounterName == "VHD čte/s" nebo CounterName == "VHD zápisy/s") &#124; shrnout AggregatedValue = avg(CounterValue) podle bin(TimeGenerated, 1h), CounterName, InstanceName |
| Rozdělení celkové propustnosti disku na všechny virtuální počítače | Perf &#124; kde ObjectName == "Kapacita a výkon" a (CounterName == "VHD Read MB/s" nebo CounterName == "VHD Write MB/s") &#124; shrnout AggregatedValue = avg(CounterValue) podle bin(TimeGenerated, 1h), CounterName, InstanceName |
| Rozdělení celkových vipos na všechny CSV | Perf &#124; kde ObjectName == "Kapacita a výkon" a (CounterName == "CSV reads/s" nebo CounterName == "CSV zápisy/s") &#124; shrnout AggregatedValue = avg(CounterValue) podle bin(TimeGenerated, 1h), CounterName, InstanceName |
| Rozdělení celkové propustnosti mezi všemi csvy | Perf &#124; kde ObjectName == "Kapacita a výkon" a (CounterName == "CSV reads/s" nebo CounterName == "CSV zápisy/s") &#124; shrnout AggregatedValue = avg(CounterValue) podle bin(TimeGenerated, 1h), CounterName, InstanceName |
| Rozdělení celkové latence na všechny csv | Perf &#124; kde ObjectName == "Kapacita a výkon" a (CounterName == "CSV Read Latency" nebo CounterName == "CSV Write Latency") &#124; shrnout AggregatedValue = avg(CounterValue) podle bin(TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>Další kroky
* Pomocí [vyhledávání protokolů v log analytics](../../azure-monitor/log-query/log-query-overview.md) zobrazit podrobné kapacity a výkonu dat.
