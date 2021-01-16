---
title: Řešení potíží s automatické škálování Azure
description: Sledování problémů pomocí automatického škálování Azure používaného v Service Fabric, Virtual Machines, Web Apps a cloudových službách.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 8c4589acd17e76d1341d5aceada67e565c8f8c37
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251263"
---
# <a name="troubleshooting-azure-autoscale"></a>Řešení potíží s automatické škálování Azure
 
Azure Monitor automatické škálování vám pomůže zajistit správné množství prostředků, na kterých běží, aby bylo možné zpracovat zatížení vaší aplikace. Umožňuje přidat prostředky pro zvýšení zátěže a také ušetřit peníze odebráním nečinných prostředků. Můžete škálovat na základě plánu, pevného času nebo metriky prostředků, kterou si zvolíte. Další informace najdete v tématu [Přehled automatického škálování](autoscale-overview.md).

Služba automatického škálování poskytuje metriky a protokoly, které vám porozumí, k jakým akcím škálování došlo, a vyhodnocení podmínek, které vedly k těmto akcím. Můžete najít odpovědi na otázky, jako jsou:

- Proč se škálovatelné nebo v mé službě změnily?
- Proč se moje služba nemění?
- Proč se akce automatického škálování nedaří?
- Proč je akce automatického škálování trvalá při škálování?
  
## <a name="autoscale-metrics"></a>Automatické škálování metrik

Automatické škálování nabízí [čtyři metriky](metrics-supported.md#microsoftinsightsautoscalesettings) pro pochopení jeho provozu. 

- **Zaznamenaná hodnota metriky** – hodnota metriky, na které jste zvolili akci škálování, jak je vidět nebo je vypočítána modulem automatického škálování. Vzhledem k tomu, že jedno nastavení automatického škálování může mít několik pravidel, a proto více zdrojů metriky, můžete filtrovat pomocí "zdroje metrik" jako dimenzi.
- **Prahová hodnota metriky** – prahová hodnota, kterou jste nastavili pro provedení akce škálování. Vzhledem k tomu, že jedno nastavení automatického škálování může mít několik pravidel, a proto více zdrojů metriky, můžete filtrovat pomocí metrikové pravidlo jako dimenzi.
- **Pozorovaná kapacita** – aktivní počet instancí cílového prostředku zobrazený modulem automatického škálování.
- **Zahájené akce škálování** – Počet akcí horizontálního navýšení a snížení kapacity zahájených modulem automatického škálování. Můžete filtrovat podle škály na více instancí vs. škálování v akcích.

Můžete použít [Průzkumník metrik](metrics-getting-started.md) k vytvoření grafu výše uvedených metrik na jednom místě. Graf by měl zobrazovat:

  - Skutečná metrika
  - Metrika zobrazená/vypočítaná modulem automatického škálování
  - prahová hodnota pro akci škálování
  - Změna kapacity 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Příklad 1 – Analýza jednoduchého pravidla automatického škálování 

Pro sadu škálování virtuálního počítače máme jednoduché nastavení automatického škálování, které:

- Škálujte, pokud je průměrné procento procesoru sady větší než 70% po dobu 10 minut. 
- Škálujte v případě, že procento procesoru sady je méně než 5% po dobu více než 10 minut. 

Pojďme se podívat na metriky ze služby automatického škálování.
 
![Snímek obrazovky ukazuje příklad procesoru v procentech sady škálování virtuálního počítače.](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Příklad procenta využití sady škálování virtuálního počítače](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

**_Obrázek 1a – procentuální metrika procesoru pro sadu škálování virtuálního počítače a metriku metriky hodnoty metriky pro nastavení automatického škálování_* _

![Prahová hodnota metriky a zjištěná kapacita](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

_*_Obrázek 1b – prahová hodnota metriky a zjištěná kapacita_*_

U pravidla škálování na více instancí je na obrázku 1b *prahová hodnota* _ x (světle modrá čára) 70.  **Zjištěná kapacita** (tmavě modrá čára) zobrazuje počet aktivních instancí, které jsou aktuálně 3. 

> [!NOTE]
> **Prahovou hodnotu metriky** můžete vyfiltrovat podle pravidla triggeru metrika dimenze vyměnit (zvětšit), aby se zobrazila prahová hodnota pro horizontální navýšení kapacity a škálování v pravidle (snížení). 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Příklad 2 – rozšířené automatické škálování pro sadu škálování virtuálního počítače

Máme nastavení automatického škálování, které umožňuje prostředku škálované sady virtuálních počítačů škálovat horizontální navýšení kapacity na základě vlastních **odchozích toků** metriky. Všimněte si, že je zaškrtnuta možnost **dělení metriky podle počtu instancí** pro prahovou hodnotu metriky. 

Pravidlo akce škálování je: 

Pokud je hodnota **odchozího toku na instanci** větší než 10, služba automatického škálování by měla škálovat na 1 instanci. 

V tomto případě se hodnota metriky metriky modulu automatického škálování počítá jako skutečná hodnota metriky dělená počtem instancí. Pokud je získaná metrika menší než prahová hodnota, není zahájena žádná akce horizontálního navýšení kapacity. 
 
![Snímek obrazovky zobrazující stránku Průměrná odchozí toky s příkladem grafu metriky automatického škálování sady škálování virtuálního počítače.](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Příklad grafu metriky automatického škálování sady škálování virtuálního počítače](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

**_Obrázek 2 – graf metriky automatického škálování sady škálování virtuálního počítače – příklad_* _

Na obrázku 2 vidíte dva grafy metrik. 

Graf nahoře zobrazuje skutečnou hodnotu metriky "*výstupní toky*" _. Skutečná hodnota je 6. 

Graf v dolní části zobrazuje několik hodnot. 
 - **Zjištěná hodnota metriky** (světle modrá) je 3, protože existují 2 aktivní instance a 6 děleno 2 je 3. 
 - **Zjištěná kapacita** (fialová) zobrazuje počet instancí zobrazených modulem automatického škálování. 
 - **Prahová hodnota metriky** (světle zelená) je nastavena na hodnotu 10. 

Pokud existuje více pravidel pro akce škálování, můžete použít možnost rozdělení nebo **Přidat filtr** v grafu Průzkumníka metrik pro zobrazení metriky podle konkrétního zdroje nebo pravidla. Další informace o rozdělení grafu metriky najdete v tématu [Pokročilé funkce metrik grafů – rozdělení](metrics-charts.md#apply-splitting)

## <a name="example-3---understanding-autoscale-events"></a>Příklad 3 – porozumění událostem automatického škálování

Na obrazovce nastavení automatického škálování přejděte na kartu **historie spuštění** a podívejte se na nejnovější akce škálování. Na kartě se také zobrazuje Změna **zjištěné kapacity** v čase. Další podrobnosti o všech akcích automatického škálování včetně operací, jako je například aktualizace nebo odstranění nastavení automatického škálování, zobrazení protokolu aktivit a filtrování podle operací automatického škálování.

![Historie spuštění nastavení automatického škálování](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Automatické škálování protokolů prostředků

Služba automatického škálování, která je stejná jako u jakéhokoli jiného prostředku Azure, poskytuje [protokoly prostředků](platform-logs-overview.md). Existují dvě kategorie protokolů.

- **Vyhodnocení automatického škálování** – modul automatického škálování zaznamenává záznamy protokolu pro každé vyhodnocení jedné podmínky pokaždé, když provede kontrolu.  Položka obsahuje podrobnosti o pozorovaných hodnotách metrik, vyhodnocených pravidlech a v případě, že výsledkem vyhodnocení je akce škálování nebo ne.

- **Akce škálování automatického škálování** – modul zaznamenává události akcí škálování na základě služby automatického škálování a výsledků těchto akcí škálování (úspěch, selhání a velikost zjištěného škálování podle služby automatického škálování).

Stejně jako u libovolné Azure Monitor podporované služby, můžete k směrování těchto protokolů použít [nastavení diagnostiky](diagnostic-settings.md) :

- pro podrobnou analýzu do svého pracovního prostoru Log Analytics
- Postup Event Hubs a pak na nástroje jiné než Azure
- k vašemu účtu úložiště Azure pro archivaci  

![Automatické škálování nastavení diagnostiky](media/autoscale-troubleshoot/diagnostic-settings.png)

Předchozí obrázek ukazuje nastavení diagnostiky automatického škálování Azure Portal. Tady můžete vybrat kartu Diagnostika/protokoly prostředků a povolit shromažďování a směrování protokolů. Stejnou akci můžete provést také pomocí REST API, CLI, PowerShellu, šablon Správce prostředků šablon pro nastavení diagnostiky tak, že vyberete typ prostředku jako *Microsoft. Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Řešení potíží pomocí protokolů automatického škálování 

Pro lepší prostředí pro řešení potíží doporučujeme při vytváření nastavení automatického škálování protokolovat protokoly do Azure Monitor protokolů (Log Analytics) prostřednictvím pracovního prostoru. Tento proces se zobrazuje na obrázku v předchozí části. Vyhodnocení a akce škálování můžete lépe ověřit pomocí Log Analytics.

Po nakonfigurování protokolů automatického škálování, které se mají odeslat do Log Analytics pracovního prostoru, můžete spustit následující dotazy a ověřit protokoly. 

Začněte tím, že si tento dotaz zobrazíte nejnovější protokoly pro vyhodnocování automatického škálování:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Nebo vyzkoušejte následující dotaz pro zobrazení nejaktuálnějších protokolů akcí škálování:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Tyto otázky použijte v následujících částech. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Došlo k akci škálování, která se neočekávala.

Nejdřív spusťte dotaz pro akci škálování, abyste našli akci škálování, o kterou vás zajímáte. Pokud se jedná o nejnovější akci škálování, použijte následující dotaz:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Vyberte pole ID korelace z protokolu akcí škálování. K vyhledání správného protokolu vyhodnocení použijte ID korelace. Provedením následujícího dotazu zobrazíte všechna pravidla a podmínky vyhodnocené jako vedoucí k této akci škálování.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Jaký profil způsobil akci škálování?

Došlo k navýšení akce, ale překrývající se pravidla a profily a potřebujete je sledovat, což způsobilo tuto akci. 

Najděte ID korelace akce škálování (jak je vysvětleno v příkladu 1) a potom spusťte dotaz na protokoly vyhodnocení, kde se dozvíte víc o profilu.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

Hodnocení celého profilu lze také pochopit lépe pomocí následujícího dotazu.

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Nedošlo k akci škálování.

Očekávala se akce škálování, která neproběhla. Nemusí existovat žádné události a protokoly akce škálování.

Pokud používáte pravidlo škálování založené na metrikách, zkontrolujte metriky automatického škálování. Je možné, že **zaznamenaná hodnota metriky** nebo **zjištěná kapacita** není to, co jste očekávali, a proto se pravidlo škálování neaktivoval. Pořád se zobrazí vyhodnocení, ale ne pravidlo škálování na více instancí. Je také možné, že čas v době běhu udržel akci škálování od výskytu. 

 Zkontrolujte protokoly vyhodnocení automatického škálování během období, ve kterém jste očekávali, že dojde k akci škálování. Projděte si všechna vyhodnocení a proč se rozhodla neaktivovat akci škálování.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Neúspěšná akce škálování

Může se jednat o případ, kdy služba automatického škálování trvalou akci škálování, ale systém nedokázal škálovat nebo se nepovedlo dokončit akci škálování. Pomocí tohoto dotazu můžete najít akce škálování, které selhaly.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Vytvořte pravidla upozornění pro upozornění na akce nebo chyby automatického škálování. Můžete také vytvořit pravidla upozornění pro upozornění na události automatického škálování.

## <a name="schema-of-autoscale-resource-logs"></a>Schéma protokolů prostředků automatického škálování

Další informace najdete v tématu [protokoly prostředků automatického škálování](autoscale-resource-log-schema.md) .

## <a name="next-steps"></a>Další kroky
Přečtěte si informace o [osvědčených postupech automatického škálování](autoscale-best-practices.md). 
