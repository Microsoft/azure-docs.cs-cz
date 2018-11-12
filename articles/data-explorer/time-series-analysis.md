---
title: Analýzu Azure Průzkumník dat časových řad
description: 'Další informace o analýzu časových řad v Průzkumníku dat Azure '
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 49aa1226de53a1d8f13e0f4f1e79f37f6bfa21ee
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300485"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analýzu časových řad v Průzkumníku dat Azure

Průzkumník Azure dat (ADX) provádí probíhající shromažďování telemetrických dat ze zařízení IoT nebo cloudové služby. Tato data můžete analyzovat různé přehledy, jako je monitorování stavu služby, fyzické produkční procesy a trendy využití. Analýza provádí v časové řadě vybrané metriky najít odchylky vzoru porovnává se vzorem jeho typické směrného plánu.
ADX obsahuje nativní podporu pro vytváření, manipulaci a analýze několika časovými řadami. V tomto tématu, dozvíte se, jak ADX slouží k vytvoření a analýza **tisíce časové řady během několika sekund**, takže téměř v reálném čase sledování řešeními a pracovními postupy.

## <a name="time-series-creation"></a>Průběžné vytváření řady

V této části vytvoříme velkou sadu pravidelné časové řady snadnou a použití `make-series` operátor a vyplnit chybějící hodnoty podle potřeby.
Prvním krokem při analýzu časových řad je rozdělí do oddílů a transformovat původní tabulku telemetrických dat na sadu časové řady. Tabulka obvykle obsahuje sloupec časového razítka, kontextové dimenze a volitelné metriky. Dimenze se používají při vytváření oddílů data. Cílem je vytvořit tisíce časové řady na oddíl v pravidelných intervalech.

Vstupní tabulka *demo_make_series1* obsahuje 600 tisíc záznamů z libovolného webového provozu služby. Pro vzorkování 10 záznamů pomocí následujícího příkazu:

```kusto
demo_make_series1 | take 10 
```

Výsledná tabulka obsahuje sloupec časového razítka, tři sloupce kontextové dimenzí a žádné metriky:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Časové razítko | BrowserVer | OsVer | Země |
|   | 09:12:35.4020000 2016-08-25 | Chrome 51.0 | Windows 7 | Spojené království |
|   | 09:12:41.1120000 2016-08-25 | Chrome 52.0 | Windows 10 |   |
|   | 09:12:46.2300000 2016-08-25 | Chrome 52.0 | Windows 7 | Spojené království |
|   | 09:12:46.5100000 2016-08-25 | Chrome 52.0 | Windows 10 | Spojené království |
|   | 09:12:46.5570000 2016-08-25 | Chrome 52.0 | Windows 10 | Litevská republika |
|   | 09:12:47.0470000 2016-08-25 | Chrome 52.0 | Windows 8.1 | Indie |
|   | 09:12:51.3600000 2016-08-25 | Chrome 52.0 | Windows 10 | Spojené království |
|   | 09:12:51.6930000 2016-08-25 | Chrome 52.0 | Windows 7 | Nizozemsko |
|   | 09:12:56.4240000 2016-08-25 | Chrome 52.0 | Windows 10 | Spojené království |
|   | 09:13:08.7230000 2016-08-25 | Chrome 52.0 | Windows 10 | Indie |

Vzhledem k tomu, že neexistují žádné metriky, můžeme vytvořit pouze sadu časové řady představující počet přenosů, samostatně, rozdělený podle operačního systému pomocí následujícího dotazu:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Použití [ `make-series` ](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator) operátoru pro vytvoření sady tři časové řady, kde:
    - `num=count()`: time series provozu
    - `range(min_t, max_t, 1h)`: časové řady proběhne do 1 hodiny přihrádek v časovém rozmezí (nejstarší a nejnovější časová razítka tabulky záznamů)
    - `default=0`: Zadejte metody fill pro chybějící přihrádky vytvoření pravidelné časové řady. Můžete také použít [ `series_fill_const()` ](https://docs.microsoft.com/azure/kusto/query/series-fill-constfunction), [ `series_fill_forward()` ](https://docs.microsoft.com/azure/kusto/query/series-fill-forwardfunction), [ `series_fill_backward()` ](https://docs.microsoft.com/azure/kusto/query/series-fill-backwardfunction) a [ `series_fill_linear()` ](https://docs.microsoft.com/azure/kusto/query/series-fill-linearfunction) pro změny
    - `byOsVer`: oddíl podle operačního systému
- Struktura dat řady skutečný čas je číselné pole agregované hodnoty za každý interval času. Používáme `render timechart` pro vizualizaci.

V tabulce výše máme tři oddíly. Můžeme vytvořit samostatné časové řady: Windows 10 (červená), 7 (modrá) a 8.1 (zelená) pro každou verzi operačního systému, jak je vidět v grafu:

![Čas řady oddílu](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Časové řady analýza funkce

V této části provádíme obvyklé řady funkce na zpracování.
Po vytvoření sadu časové řady ADX podporuje poskytovaná rostoucím seznamem zpracovávat a analyzovat je funkce, které najdete v [čas dokumentace k úložišti řady](https://docs.microsoft.com/azure/kusto/query/machine-learning-and-tsa). Článků popíšeme několik reprezentativní funkce pro zpracování a analýzu časové řady.

### <a name="filtering"></a>Filtrování

Filtrování je běžnou praxí při signálu zpracování a užitečné pro časové řady úloh zpracování (například smooth hlučného signál, změnit zjišťování).
- Existují dvě obecné funkce filtrování:
    - [`series_fir()`](https://docs.microsoft.com/azure/kusto/query/series-firfunction): Používá se filtr do části. Používá pro jednoduchý výpočet klouzavý průměr a rozdílů mezi časové řady pro zjišťování změn.
    - [`series_iir()`](https://docs.microsoft.com/azure/kusto/query/series-iirfunction): Používá se filtr IIR. Používá pro exponenciální vyhlazování a kumulativní součet.
- `Extend` časové řady přidáním nové klouzavý průměr řady nastavit velikost přihrádky 5 (s názvem *ma_num*) k dotazu:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Časové řady filtrování](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Regresní analýzy

Podporuje ADX segmentované lineární regrese analýzy k odhadu trend časové řady.
- Použití [series_fit_line()](https://docs.microsoft.com/azure/kusto/query/series-fit-linefunction) podle osvědčené čáry časové řady pro zjišťování obecnému trendu.
- Použití [series_fit_2lines()](https://docs.microsoft.com/azure/kusto/query/series-fit-2linesfunction) ke zjišťování trend změn, vzhledem k standardních hodnot, které jsou užitečné pro scénáře monitorování.

Příklad `series_fit_line()` a `series_fit_2lines()` funkce v dotazu řady čas:

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart
```

![Čas řady regrese](media/time-series-analysis/time-series-regression.png)

- Modrá: původní časové řady
- Zelená: namontováno řádku
- Red: dva řádky vybavené

> [!NOTE]
> Funkce přesně zjistili bodu jump (Změna úrovně).

### <a name="seasonality-detection"></a>Sezónnost detekce

Mnoho metriky podle sezónní vzory (pravidelných). Uživatelský provoz cloudové služby obvykle obsahuje denní nebo týdenní vzory, které jsou v noci a přes víkend pokazil nejvyšší po nejnižší a střední firmy dne. Míra senzory IoT v pravidelných intervalech. Fyzické měření, jako je například teploty a tlaku, vlhkosti lze také zobrazit sezónní chování.

Následující příklad se vztahuje zjišťování sezónnosti na jeden měsíc provozu webové služby (přihrádkami 2 hodiny):

```kusto
demo_series3
| render timechart 
```

![Sezónnost řady čas](media/time-series-analysis/time-series-seasonality.png)

- Použití [series_periods_detect()](https://docs.microsoft.com/azure/kusto/query/series-periods-detectfunction) automaticky zjišťovat období v časové řadě. 
- Použití [series_periods_validate()](https://docs.microsoft.com/azure/kusto/query/series-periods-validatefunction) Pokud víme, že metriku by měl mít odlišné konkrétní období a chceme ověřit, že existují.
> [!NOTE]
> Pokud neexistuje konkrétní různých období je anomálií

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mvexpand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | období | Skóre | dny |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

Funkce zjistí denní nebo týdenní sezónnosti. Denní stanoví skóre menší než týdenní, protože se liší od dny v týdnu víkendové dny.

### <a name="element-wise-functions"></a>Element-Wise funkce

Aritmetické a logické operace lze provádět na časové řady. Pomocí [series_subtract()](https://docs.microsoft.com/azure/kusto/query/series-subtractfunction) můžeme vypočítat zbývající časové řady, který je, rozdíl mezi původní nezpracovaná metriky a vyhlazenými jeden a vyhledat anomálie v plyne ze zbytkových signál:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![Časové řady operace](media/time-series-analysis/time-series-operations.png)

Modrá: původní časové řady Red: vyhlazené časové řady zelená: plyne ze zbytkových časové řady

## <a name="time-series-workflow-at-scale"></a>Čas řady pracovní postup ve velkém měřítku

Následující příklad ukazuje, jak můžete tyto funkce spouštět ve velkém měřítku na tisíce časových řad v sekundách pro detekci anomálií. Chcete-li zobrazit několik záznamů telemetrie ukázkové metriky počet čtení databáze služby čtyři dny spuštěním následujícího dotazu:

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | ČASOVÉ RAZÍTKO | Umístění | anonOp | DB | Přečtená data |
|   | 21:00:00.0000000 2016-09-11 | LOC 9 | 5117853934049630089 | 262 | 0 |
|   | 21:00:00.0000000 2016-09-11 | LOC 9 | 5117853934049630089 | 241 | 0 |
|   | 21:00:00.0000000 2016-09-11 | LOC 9 | -865998331941149874 | 262 | 279862 |
|   | 21:00:00.0000000 2016-09-11 | LOC 9 | 371921734563783410 | 255 | 0 |

A jednoduché statistiky:

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | počet | min\_t | maximální počet\_t |
|   | 2177472 | 00:00:00.0000000 2016-09-08 | 23:00:00.0000000 2016-09-11 |

Vytváření časové řady do přihrádky 1 hodinu čtení metriky (celkem čtyři dny * 24 hodin = 96 bodů), výsledkem kolísání normální vzoru:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart 
```

![Časové řady ve velkém měřítku](media/time-series-analysis/time-series-at-scale.png)

Výše uvedené chování je zavádějící, protože jeden normální časové řady se shromažďuje od tisíce různých instancí, které můžou mít abnormálních vzorů. Proto vytvoříme časové řady na jednu instanci. Instance je definována umístění (umístění), anonOp (operace) a databáze (konkrétního počítače).

Kolik časové řady můžeme vytvořit?

```kusto
demo_many_series1
| summarize by Loc, anonOp, DB
| count
```

|   |   |
| --- | --- |
|   | Počet |
|   | 23115 |

Teď vytvoříme vytvořit sadu 23115 časových řad pro metriku čtení počtu. Přidáme `by` klauzule příkazu zkontrolujte series, použití lineární regrese a vyberte horní dva časové řady, do kterých se nejvýznamnější snížení trendů:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, anonOp, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 23115)')
```

![Časové řady prvních dvou](media/time-series-analysis/time-series-top-2.png)

Zobrazení instancí:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, anonOp, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, anonOp, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Umístění | anonOp | DB | křivka |
|   | LOC 15 | -3207352159611332166 | 1151 | -102743.910227889 |
|   | LOC 13 | -3207352159611332166 | 1249 | -86303.2334644601 |

Za méně než dvě minuty zjištěn ADX dvě nadměrné časové řady (mimo 23115) ve kterém se počet čtení náhle vyřadit.

Tyto pokročilé funkce v kombinaci s rychlý výkon ADX zadat jedinečný a výkonné řešení pro analýzu časových řad.
