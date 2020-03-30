---
title: Poradce při potížích s automatickým škálováním Azure
description: Sledování dolů problémy s Azure automatické škálování používané v Service Fabric, virtuální počítače, webové aplikace a cloudové služby.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751328"
---
# <a name="troubleshooting-azure-autoscale"></a>Poradce při potížích s automatickým škálováním Azure
 
Automatické škálování Azure Monitor vám pomůže mít správné množství prostředků spuštěných pro zpracování zatížení vaší aplikace. Umožňuje přidat prostředky pro zpracování zvýšení zatížení a také ušetřit peníze odebráním prostředků, které jsou nečinné. Škálování můžete škálovat na základě plánu, pevného data a času nebo metriky zdroje, kterou zvolíte. Další informace naleznete v [tématu Přehled automatického škálování](autoscale-overview.md).

Služba automatického škálování poskytuje metriky a protokoly, abyste pochopili, k jakým akcím škálování došlo, a vyhodnocení podmínek, které vedly k těmto akcím. Můžete najít odpovědi na otázky, jako jsou:

- Proč se moje služba škálování-out nebo v?
- Proč se moje služba nezvětšila?
- Proč akce automatického škálování selhala?
- Proč akce automatického škálování trvá čas na škálování?
  
## <a name="autoscale-metrics"></a>Metriky automatického škálování

Automatické škálování poskytuje [čtyři metriky](metrics-supported.md#microsoftinsightsautoscalesettings) pochopit jeho provoz. 

- **Zjištěná metrická hodnota** – hodnota metriky, kterou jste se rozhodli provést akci měřítka, jak je vidět nebo vypočítat modul automatického škálování. Vzhledem k tomu, že jedno nastavení automatického škálování může mít více pravidel a proto více zdrojů metriky, můžete filtrovat pomocí "zdroje metriky" jako dimenze.
- **Prahová hodnota metriky** – prahová hodnota, kterou nastavíte tak, aby se akce škálování. Vzhledem k tomu, že jedno nastavení automatického škálování může mít více pravidel, a proto více zdrojů metriky, můžete filtrovat pomocí "metrické pravidlo" jako dimenze.
- **Pozorovaná kapacita** – aktivní počet instancí cílového prostředku, jak je vidět v modulu automatického škálování.
- **Zahájené akce škálování** – Počet akcí horizontálního navýšení a snížení kapacity zahájených modulem automatického škálování. V akcích můžete filtrovat podle horizontálního navýšení kapacity vs. měřítka.

[Pomocí Průzkumníka metrik](metrics-getting-started.md) můžete výše uvedené metriky zmapovat na jednom místě. Graf by měl zobrazovat:

  - skutečná metrika
  - metrika, jak je vidět/vypočítat motorem s automatickým škálování
  - prahová hodnota pro akci v měřítku
  - změna kapacity 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Příklad 1 – Analýza jednoduchého pravidla automatického škálování 

Pro škálovací sadu virtuálních strojů máme jednoduché nastavení automatického škálování, které:

- se škáluje, když je průměrné procento procesoru v sadě větší než 70 % po dobu 10 minut 
- pokud je procento procesoru sady menší než 5 % po dobu delší než 10 minut. 

Podívejme se na metriky ze služby automatického škálování.
 
![Příklad procenta škálování virtuálního počítače](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Příklad procenta škálování virtuálního počítače](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Obrázek 1a – metrika procento procesoru pro škálovací sadu virtuálních strojů a metrika Pozorovaná metrická hodnota pro nastavení automatického škálování***

![Metrický práh a zjištěná kapacita](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Obrázek 1b – metrický práh a zjištěná kapacita***

Na obrázku 1b je **prahová hodnota metriky** (světle modrá čára) pro pravidlo horizontálního navýšení kapacity 70.  **Pozorovaná kapacita** (tmavě modrá čára) zobrazuje počet aktivních instancí, což je aktuálně 3. 

> [!NOTE]
> Chcete-li zobrazit prahovou hodnotu horizontálního navýšení kapacity a měřítko v pravidle (snížení) je nutné filtrovat **prahovou hodnotu metriky** podle pravidla dimenzionální hospo- 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Příklad 2 – Pokročilé automatické škálování pro škálovací sadu virtuálních strojů

Máme nastavení automatického škálování, které umožňuje prostředek škálovací sady virtuálních strojů škálovat na základě vlastní metriky **odchozích toků**. Všimněte si, že je zaškrtnuta možnost **rozdělit metriku podle počtu instancí** pro prahovou hodnotu metriky. 

Pravidlo akce měřítka je: 

Pokud je hodnota **odchozího toku na instanci** větší než 10, pak by měla služba automatického škálování horizontální navýšení kapacity o 1 instanci. 

V tomto případě se hodnota metriky modulu automatického škálování vypočítá jako skutečná hodnota metriky vydělená počtem instancí. Pokud je zjištěná hodnota metriky menší než prahová hodnota, není zahájena žádná akce horizontálního navýšení kapacity. 
 
![Příklad grafů metrik automatickéškálování škálovací sady virtuálních strojů](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Příklad grafů metrik automatickéškálování škálovací sady virtuálních strojů](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Obrázek 2 – Příklad grafů automatických škálování virtuálních strojů***

Na obrázku 2 můžete vidět dva metrické grafy. 

Graf nahoře zobrazuje skutečnou hodnotu metriky **Odchozí toky.** Skutečná hodnota je 6. 

Graf v dolní části zobrazuje několik hodnot. 
 - Hodnota **Pozorovaná metrika** (světle modrá) je 3, protože existují 2 aktivní instance a 6 děleno 2 je 3. 
 - **Pozorovaná kapacita** (fialová) zobrazuje počet instancí, který je vidět motorem automatického škálování. 
 - **Prahová hodnota metriky** (světle zelená) je nastavena na 10. 

Pokud existuje více pravidel akce škálování, můžete použít možnost rozdělení nebo **přidat filtr** v grafu průzkumníkmetriky a podívat se na metriku podle konkrétního zdroje nebo pravidla. Další informace o rozdělení metrického grafu najdete v [tématu Pokročilé funkce metrických grafů – rozdělení](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>Příklad 3 – Principy událostí automatického škálování

Na obrazovce nastavení automatického škálování přejděte na kartu **Spustit historii** a podívejte se na nejnovější akce měřítka. Karta také zobrazuje změnu **pozorované kapacity** v průběhu času. Chcete-li najít další podrobnosti o všech akcích automatického škálování, včetně operací, jako je aktualizace nebo odstranění nastavení automatického škálování, zobrazte protokol aktivit a filtrujte podle operací automatického škálování.

![Nastavení automatického škálování spustit historii](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Automatické škálování protokolů prostředků

Stejně jako jakýkoli jiný prostředek Azure, služba automatického škálování poskytuje [protokoly prostředků](platform-logs-overview.md). Existují dvě kategorie protokolů.

- **Automatické škálování Hodnocení** - autoškálování motoru záznamy položky protokolu pro každý jednotlivý stav hodnocení pokaždé, když to dělá kontrolu.  Položka obsahuje podrobnosti o pozorovaných hodnotách metrik, vyhodnocených pravidlech a o tom, zda hodnocení vedlo k akci měřítka či nikoli.

- **Akce automatického škálování -** modul zaznamenává události akce škálování iniciované službou automatického škálování a výsledky těchto akcí škálování (úspěch, selhání a množství škálování došlo, jak je vidět služby automatického škálování).

Stejně jako u všech podporovaných služeb Azure Monitor, můžete použít [nastavení diagnostiky](diagnostic-settings.md) ke směrování těchto protokolů:

- do pracovního prostoru Log Analytics pro podrobnou analýzu
- do event hubů a pak na nástroje, které nejsou azure
- do vašeho účtu úložiště Azure pro archivaci  

![Automatické nastavení diagnostiky](media/autoscale-troubleshoot/diagnostic-settings.png)

Předchozí obrázek znázorňuje nastavení diagnostiky automatického škálování portálu Azure. Zde můžete vybrat kartu Protokoly diagnostiky/prostředků a povolit shromažďování a směrování protokolů. Stejnou akci můžete provést také pomocí rozhraní REST API, CLI, powershellu, šablony Správce prostředků pro diagnostická nastavení výběrem typu prostředku jako *Microsoft.Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Poradce při potížích s protokoly automatického škálování 

Pro nejlepší řešení potíží doporučujeme směrování protokolů do protokolů monitorování Azure (Log Analytics) prostřednictvím pracovního prostoru při vytváření nastavení automatického škálování. Tento proces je zobrazen na obrázku v předchozí části. Můžete ověřit hodnocení a škálování akce lépe pomocí Log Analytics.

Jakmile nakonfigurujete protokoly automatického škálování, které mají být odeslány do pracovního prostoru Analýzy protokolů, můžete spustit následující dotazy a zkontrolovat protokoly. 

Chcete-li začít, zkuste tento dotaz zobrazit nejnovější protokoly vyhodnocení automatického škálování:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Nebo zkuste zobrazit nejnovější protokoly akcí škálování následující dotaz:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

K těmto otázkám použijte následující části. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Došlo k akci měřítka, kterou jsem nečekal

Nejprve spusťte dotaz pro akci škálování a najděte akci škálování, která vás zajímá. Pokud se jedná o nejnovější akci měřítka, použijte následující dotaz:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Vyberte pole CorrelationId z protokolu akcí měřítka. Pomocí CorrelationId najít správný protokol hodnocení. Provedení níže uvedeného dotazu zobrazí všechna pravidla a podmínky vyhodnoceny vedoucí k této akci škálování.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Jaký profil způsobil akci měřítka?

Došlo k akci s měřítkem, ale máte překrývající se pravidla a profily a potřebujete sledovat, který akci způsobil. 

Najděte correlationId akce škálování (jak je vysvětleno v příkladu 1) a potom spusťte dotaz na protokoly hodnocení, abyste se dozvěděli více o profilu.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

Celé hodnocení profilu lze také lépe chápat pomocí následujícího dotazu

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Nedošlo k akci měřítka.

Očekával jsem akci v měřítku a nedošlo k ní. Pravděpodobně neexistují žádné události akce škálování nebo protokoly.

Zkontrolujte metriky automatického škálování, pokud používáte pravidlo měřítka založené na metrikách. Je možné, že **pozorovaná metrická hodnota** nebo **pozorovaná kapacita** nejsou takové, jaké jste očekávali, a proto pravidlo měřítka nevystřelilo. Stále byste viděli hodnocení, ale ne pravidlo škálování. Je také možné, že doba ochlazení zabránila vzniku akce v měřítku. 

 Zkontrolujte protokoly automatického hodnocení během časového období, které jste očekávali, že dojde k akci měřítka. Projděte si všechna hodnocení, která provedla, a proč se rozhodla nezahájit akci v měřítku.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Akce škálování se nezdařila.

Může napovědět případ, kdy služba automatického škálování provedla akci škálování, ale systém se rozhodl neškálovat nebo se nepodařilo dokončit akci škálování. Tento dotaz slouží k vyhledání neúspěšných akcí škálování.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Vytvořte pravidla výstrah, abyste získali upozornění na akce nebo selhání automatického škálování. Můžete také vytvořit pravidla výstrah, která budou upozorňována na události automatického škálování.

## <a name="schema-of-autoscale-resource-logs"></a>Schéma protokolů prostředků automatického škálování

Další informace naleznete v [tématu Automatické škálování protokolů prostředků](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>Další kroky
Přečtěte si informace o [osvědčených postupech automatického škálování](autoscale-best-practices.md). 
