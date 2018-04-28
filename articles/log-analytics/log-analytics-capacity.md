---
title: Kapacitu a výkon řešení v Azure Log Analytics | Microsoft Docs
description: Použijte řešení kapacitu a výkon v analýzy protokolů vám pomohou pochopit kapacitu serverů Hyper-V.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: magoedte
ms.openlocfilehash: db38678a05afbc764dec20f2a475e00856a1aeee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-preview"></a>Plánování kapacity virtuálního počítače technologie Hyper-V s řešením kapacitu a výkon (Preview)

![Symbol kapacitu a výkon](./media/log-analytics-capacity/capacity-solution.png)

> [!NOTE]
> Řešení kapacitu a výkon je zastaralá.  Zákazníci, kteří již nainstalovali řešení můžete nadále používat ji, ale kapacitu a výkon nelze přidat do žádné nové pracovní prostory.

Řešení kapacitu a výkon v analýzy protokolů můžete použít vám pomohou pochopit kapacitu serverů Hyper-V. Řešení poskytuje přehledy o prostředí Hyper-V ukazuje celkové využití (procesoru, paměti a disku) hostitelů a virtuálních počítačů spuštěných na těchto hostitelích Hyper-V. Metriky se shromažďují pro procesoru, paměti a disky mezi všechny hostitele a virtuální počítače spuštěné na ně.

Řešení:

-   Zobrazí hostitelů s nejnižší a nejvyšší využití procesoru a paměti
-   Zobrazuje virtuální počítače s nejnižší a nejvyšší využití procesoru a paměti
-   Zobrazuje virtuální počítače s nejnižší a nejvyšší využití IOPS a propustnosti
-   Zobrazuje, které běží na které hostitele virtuálních počítačů
-   Zobrazuje nejvyšší disky s vysokou propustnost, IOPS a latence ve sdílených svazků clusteru
- Umožňuje přizpůsobit a filtrovat podle skupiny

> [!NOTE]
> V předchozí verzi kapacitu a výkon řešení správy kapacity volá vyžaduje System Center Operations Manager a System Center Virtual Machine Manager. Toto řešení aktualizované nemá těchto závislostí.


## <a name="connected-sources"></a>Připojené zdroje

Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podpora | Popis |
|---|---|---|
| [Agenti systému Windows](log-analytics-windows-agent.md) | Ano | Řešení shromažďuje informace kapacitu a výkon data z agentů v systému Windows. |
| [Agenti systému Linux](log-analytics-linux-agents.md) | Ne    | Řešení neshromažďuje data informace kapacitu a výkon od agentů přímé Linux.|
| [Skupiny správy nástroje SCOM](log-analytics-om-agents.md) | Ano |Řešení shromažďuje údaje o kapacitu a výkon od agentů v připojené skupině pro správu SCOM. Přímé připojení z agenta nástroje SCOM k analýze protokolů se nevyžaduje.|
| [Účet služby Azure Storage](log-analytics-azure-storage.md) | Ne | Úložiště Azure nezahrnuje data kapacitu a výkon.|

## <a name="prerequisites"></a>Požadavky

- Windows nebo agenty nástroje Operations Manager musí být nainstalován na Windows Server 2012 nebo vyšší hostitelů Hyper-V, není virtuálních počítačů.


## <a name="configuration"></a>Konfigurace

Proveďte následující krok a přidejte kapacitu a výkon řešení do pracovního prostoru.

- Přidat kapacitu a výkon řešení do pracovního prostoru analýzy protokolů pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).

## <a name="management-packs"></a>Sady Management Pack

Pokud skupinu správy nástroje SCOM je připojen do pracovního prostoru analýzy protokolů, pak následující sady management Pack bude nainstalována v aplikaci SCOM při přidání tohoto řešení. Není potřeba žádná konfigurace ani údržba těchto sad Management Pack.

- Microsoft.IntelligencePacks.CapacityPerformance

Podobná událost 1201:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Při aktualizaci řešení kapacitu a výkon, změní se číslo verze.

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](log-analytics-om-agents.md).

## <a name="using-the-solution"></a>Použití řešení

Když přidáte kapacitu a výkon řešení do pracovního prostoru, kapacitu a výkon, se přidá na řídicí panel Přehled. Tuto dlaždici zobrazí počet aktuálně aktivních hostitelů Hyper-V a vybrán počet aktivních virtuálních počítačů, které byly monitorovány pro toto časové období.

![Dlaždice kapacitu a výkon](./media/log-analytics-capacity/capacity-tile.png)


### <a name="review-utilization"></a>Zkontrolujte využití

Klikněte na dlaždici kapacitu a výkon Otevřete řídící panel kapacitu a výkon. Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec uvádí až deset položek odpovídajících kritériím tohoto sloupce pro zadaný obor a časový rozsah. Kliknutím na **Zobrazit vše** v dolní části sloupce nebo na záhlaví sloupce můžete spustit hledání v protokolu, které vrátí všechny záznamy.

- **Hostitelé**
    - **Využití procesoru hostitele** zobrazuje grafické zobrazení trendu využití procesoru hostitele počítače a seznamu hostitelů, v závislosti na zvolené časové období. Podržte ukazatel nad spojnicový graf, chcete-li zobrazit podrobnosti pro určitý bod v čase. Klikněte na graf a zobrazit další podrobnosti v protokolu vyhledávání. Klikněte na libovolný název hostitele a otevřete hledání protokolů a zobrazit podrobnosti čítače procesoru pro hostované virtuální počítače.
    - **Hostování využití paměti** zobrazuje grafické zobrazení trendu využití paměti v hostitelských počítačích a seznamu hostitelů, v závislosti na zvolené časové období. Podržte ukazatel nad spojnicový graf, chcete-li zobrazit podrobnosti pro určitý bod v čase. Klikněte na graf a zobrazit další podrobnosti v protokolu vyhledávání. Klikněte na libovolný název hostitele a otevřete hledání protokolů a zobrazit podrobnosti čítače paměti pro hostované virtuální počítače.
- **Virtual Machines**
    - **Využití procesoru virtuálního počítače** zobrazuje grafické zobrazení trendu využití procesoru virtuálního počítače a seznam virtuálních počítačů, v závislosti na zvolené časové období. Podržte ukazatel nad spojnicový graf zobrazit podrobnosti pro určitý bod v čase pro hlavní 3 virtuální počítače. Klikněte na graf a zobrazit další podrobnosti v protokolu vyhledávání. Klikněte na libovolný název virtuálního počítače a otevřete protokolu vyhledávání a zobrazení agregovat podrobnosti čítače procesoru pro virtuální počítač.
    - **Využití paměti virtuálního počítače** zobrazuje grafické zobrazení trendu využití paměti v aplikaci virtuální počítače a seznam virtuálních počítačů, v závislosti na zvolené časové období. Podržte ukazatel nad spojnicový graf zobrazit podrobnosti pro určitý bod v čase pro hlavní 3 virtuální počítače. Klikněte na graf a zobrazit další podrobnosti v protokolu vyhledávání. Klikněte na libovolný název virtuálního počítače a otevřete hledání protokolů a zobrazit podrobnosti čítače agregované paměti pro virtuální počítač.
    - **Celkový počet IOPS disku virtuálního počítače** zobrazuje grafické zobrazení trendu disku celkový počet IOPS pro virtuální počítače a seznam virtuálních počítačů s IOPS pro každou, podle zvolené časové období. Podržte ukazatel nad spojnicový graf zobrazit podrobnosti pro určitý bod v čase pro hlavní 3 virtuální počítače. Klikněte na graf a zobrazit další podrobnosti v protokolu vyhledávání. Klikněte na libovolný název virtuálního počítače a otevřete protokolu vyhledávání a zobrazení agregované disku IOPS čítač podrobnosti pro virtuální počítač.
    - **Celková propustnost disku virtuálního počítače** zobrazuje grafické zobrazení trendu propustnosti celkový disku pro virtuální počítače a seznam virtuálních počítačů s propustností celkový disku, podle zvolené časové období. Podržte ukazatel nad spojnicový graf zobrazit podrobnosti pro určitý bod v čase pro hlavní 3 virtuální počítače. Klikněte na graf a zobrazit další podrobnosti v protokolu vyhledávání. Klikněte na libovolný název virtuálního počítače a otevřete hledání protokolů a zobrazit podrobnosti čítače propustnost agregované celkový disku pro virtuální počítač.
- **Sdílené svazky clusteru**
    - **Celková propustnost** zobrazí součet obou čte a zapisuje na sdílené svazky clusteru.
    - **Celkový počet IOPS** zobrazí součet vstupně-výstupních operací za sekundu na sdílené svazky clusteru.
    - **Celková latence** zobrazuje nízkou celkovou latenci na sdílené svazky clusteru.
- **Hostování hustotu** dlaždici nejvyšší zobrazuje celkový počet hostitelů a virtuálních počítačů, které jsou k dispozici pro řešení. Kliknutím na horní dlaždici zobrazit další podrobnosti v protokolu vyhledávání. Také uvádí všechny hostitele a počet virtuálních počítačů, které jsou hostovány. Klikněte na tlačítko pro hostitele a přejít k podrobnostem výsledků virtuálních počítačů do protokolu hledání.


![okno hostitele řídicí panel](./media/log-analytics-capacity/dashboard-hosts.png)

![okno řídicího panelu virtuální počítače](./media/log-analytics-capacity/dashboard-vms.png)


### <a name="evaluate-performance"></a>Vyhodnocení výkonu

Provozní výpočetní prostředí se liší výrazně z jedné organizace. Navíc může kapacitu a výkon úloh závisí na tom, jak vaše virtuální počítače jsou spuštěné, a co byste zvážit normální. Konkrétní postupy vám pomohou měr výkonu by pravděpodobně nebude použitelná pro prostředí. Tak více zobecněn závazné pokyny je vhodnější pomohou. Společnost Microsoft publikuje celou řadu doporučený postup články, které umožňují měřit výkon.

To Shrneme, shromažďuje řešení kapacitu a výkon data z různých zdrojů včetně čítače výkonu. Použít kapacitu a výkon data, která se zobrazí v různých ploch v řešení a porovnejte výsledky na ty, na [měření výkonu u Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) článku. I když článek byl publikován dřívějška, metriky, důležité informace a pokyny jsou stále platné. Článek obsahuje odkazy na další užitečné zdroje.


## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

Následující tabulka obsahuje ukázkový protokol hledání pro kapacitu a výkon data shromážděna a vypočítána toto řešení.


| Dotaz | Popis |
|:--- |:--- |
| Všechny konfigurace paměti hostitele | Výkonu &#124; kde ObjectName == "Kapacitu a výkon" a název_čítače == "Hostitele přiřazené MB paměti" &#124; shrnout MB = avg(CounterValue) podle InstanceName |
| Všechny konfigurace paměti virtuálního počítače | Výkonu &#124; kde ObjectName == "Kapacitu a výkon" a název_čítače == "Virtuální počítač přiřazen MB paměti" &#124; shrnout MB = avg(CounterValue) podle InstanceName |
| Rozdělení celkový počet IOPS Disk napříč všechny virtuální počítače | Výkonu &#124; kde ObjectName == "Kapacitu a výkon" a (název_čítače == "Čtení virtuálního pevného disku/s" nebo název_čítače == "Zápisy virtuálního pevného disku/s") &#124; shrnout AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 1 hod), název_čítače, InstanceName |
| Rozdělení celková propustnost disku napříč všechny virtuální počítače | Výkonu &#124; kde ObjectName == "Kapacitu a výkon" a (název_čítače == "Pro čtení virtuálního pevného disku MB/s" nebo název_čítače == "VHD zápisu MB/s") &#124; shrnout AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 1 hod), název_čítače, InstanceName |
| Rozdělení celkový počet IOPS napříč všechny sdílené svazky clusteru | Výkonu &#124; kde ObjectName == "Kapacitu a výkon" a (název_čítače == "Čtení sdíleného svazku clusteru nebo s" nebo název_čítače == "CSV zápisy nebo s") &#124; shrnout AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 1 hod), název_čítače, InstanceName |
| Rozdělení celková propustnost mezi všechny sdílené svazky clusteru | Výkonu &#124; kde ObjectName == "Kapacitu a výkon" a (název_čítače == "Čtení sdíleného svazku clusteru nebo s" nebo název_čítače == "CSV zápisy nebo s") &#124; shrnout AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 1 hod), název_čítače, InstanceName |
| Rozdělení nízkou celkovou latenci mezi všechny sdílené svazky clusteru | Výkonu &#124; kde ObjectName == "Kapacitu a výkon" a (název_čítače == "Latenci pro čtení sdíleného svazku clusteru" nebo název_čítače == "CSV zápisu latence") &#124; shrnout AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 1 hod), název_čítače, InstanceName |


## <a name="next-steps"></a>Další postup
* Použití [hledání přihlásit analýzy protokolů](log-analytics-log-search.md) na podrobnější data kapacitu a výkon.
