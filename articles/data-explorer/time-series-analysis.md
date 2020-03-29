---
title: Analýza dat časových řad pomocí Průzkumníka dat Azure
description: Zjistěte, jak analyzovat data časových řad v cloudu pomocí Azure Data Exploreru.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 3873b25394f91ce1c1601c348de2098198ba7fdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765479"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analýza časových řad v Průzkumníku dat Azure

Azure Data Explorer (ADX) provádí on-li pokračující shromažďování telemetrických dat z cloudových služeb nebo zařízení IoT. Tato data lze analyzovat pro různé poznatky, jako je monitorování stavu služby, fyzické výrobní procesy a trendy využití. Analýza se provádí na časové řady vybraných metrik najít odchylku ve vzoru ve srovnání s jeho typickým vzorem základní hodnoty.
ADX obsahuje nativní podporu pro vytváření, manipulaci a analýzu více časových řad. V tomto tématu se dozvíte, jak se adx používá k vytváření a analýze **tisíců časových řad v sekundách**, což umožňuje téměř v reálném čase řešení monitorování a pracovní postupy.

## <a name="time-series-creation"></a>Vytvoření časové řady

V této části vytvoříme velkou sadu pravidelných časových řad `make-series` jednoduše a intuitivně pomocí operátora a podle potřeby vyplníme chybějící hodnoty.
Prvním krokem v analýze časových řad je rozdělení a transformace původní tabulky telemetrie na sadu časových řad. Tabulka obvykle obsahuje sloupec časového razítka, kontextové dimenze a volitelné metriky. Dimenze se používají k rozdělení dat. Cílem je vytvořit tisíce časových řad na oddíl v pravidelných časových intervalech.

Vstupní tabulka *demo_make_series1* obsahuje 600 kS záznamů libovolného provozu webové služby. Pomocí níže uvedeného příkazu můžete ochutnat 10 záznamů:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA)**\]**

```kusto
demo_make_series1 | take 10 
```

Výsledná tabulka obsahuje sloupec časového razítka, tři sloupce kontextových dimenzí a žádné metriky:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Časové razítko | ProhlížečVer | OsVer | Země/region |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Spojené království |
|   | 2016-08-25 09:12:41.1120000 | Chrom 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrom 52.0 | Windows 7 | Spojené království |
|   | 2016-08-25 09:12:46.5100000 | Chrom 52.0 | Windows 10 | Spojené království |
|   | 2016-08-25 09:12:46.5570000 | Chrom 52.0 | Windows 10 | Litevská republika |
|   | 2016-08-25 09:12:47.0470000 | Chrom 52.0 | Windows 8.1 | Indie |
|   | 2016-08-25 09:12:51.3600000 | Chrom 52.0 | Windows 10 | Spojené království |
|   | 2016-08-25 09:12:51.6930000 | Chrom 52.0 | Windows 7 | Nizozemsko |
|   | 2016-08-25 09:12:56.4240000 | Chrom 52.0 | Windows 10 | Spojené království |
|   | 2016-08-25 09:13:08.7230000 | Chrom 52.0 | Windows 10 | Indie |

Vzhledem k tomu, že neexistují žádné metriky, můžeme vytvořit pouze sadu časových řad představující samotný počet přenosů, rozdělený oS pomocí následujícího dotazu:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Pomocí [`make-series`](/azure/kusto/query/make-seriesoperator) operátoru vytvořte sadu tří časových řad, kde:
    - `num=count()`: časové řady dopravních
    - `range(min_t, max_t, 1h)`: časové řady jsou vytvořeny v 1hodinových přihrádkách v časovém rozsahu (nejstarší a nejnovější časová razítka záznamů tabulek)
    - `default=0`: Určete metodu vyplnění chybějících přihrádek, abyste vytvořili pravidelné časové řady. Alternativně [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction)použití [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction) [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) , [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) a pro změny
    - `byOsVer`: rozdělení oS
- Skutečná datová struktura časových řad je číselné pole agregované hodnoty pro každou přihrádku. Používáme `render timechart` pro vizualizaci.

Ve výše uvedené tabulce máme tři oddíly. Můžeme vytvořit samostatnou časovou řadu: Windows 10 (červená), 7 (modrá) a 8.1 (zelená) pro každou verzi operačního systému, jak je vidět v grafu:

![Oddíl časové řady](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Funkce analýzy časových řad

V této části budeme provádět typické funkce zpracování řad.
Jakmile je vytvořena sada časových řad, ADX podporuje rostoucí seznam funkcí pro jejich zpracování a analýzu, které lze nalézt v [dokumentaci časových řad](/azure/kusto/query/machine-learning-and-tsa). Popíšeme několik reprezentativních funkcí pro zpracování a analýzu časových řad.

### <a name="filtering"></a>Filtrování

Filtrování je běžnou praxí při zpracování signálu a užitečné pro úlohy zpracování časových řad (například vyhlazení hlučného signálu, detekce změn).
- Existují dvě obecné funkce filtrování:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): Použití fir filtru. Používá se pro jednoduchý výpočet klouzavého průměru a diferenciace časových řad pro detekci změn.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): Použití filtru IIR. Používá se pro exponenciální vyhlazení a kumulativní součet.
- `Extend`časové řady nastavené přidáním nové řady klouzavých průměrů o velikosti 5 (s názvem *ma_num)* do dotazu:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Filtrování časových řad](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Regresní analýza

ADX podporuje segmentologickou lineární regresní analýzu k odhadu trendu časových řad.
- Použijte [series_fit_line()](/azure/kusto/query/series-fit-linefunction) tak, aby se nejvhodnější řádek vešel do časové řady pro obecnou detekci trendů.
- Pomocí [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) můžete zjistit změny trendu vzhledem k směrnému plánu, které jsou užitečné ve scénářích monitorování.

Příklad `series_fit_line()` a `series_fit_2lines()` funkce v dotazu časové řady:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=)**\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Regrese časových řad](media/time-series-analysis/time-series-regression.png)

- Modrá: původní časová řada
- Zelená: přiléhavá linka
- Červená: dvě osazené linky

> [!NOTE]
> Funkce přesně detekovala bod skoku (změna úrovně).

### <a name="seasonality-detection"></a>Detekce sezónnosti

Mnoho metrik se řídí sezónními (periodickými) vzory. Uživatelský provoz cloudových služeb obvykle obsahuje denní a týdenní vzorce, které jsou nejvyšší kolem poloviny pracovního dne a nejnižší v noci a přes víkend. Senzory IoT měří v pravidelných intervalech. Fyzická měření, jako je teplota, tlak nebo vlhkost, mohou také vykazovat sezónní chování.

Následující příklad použije detekci sezónnosti u měsíčního provozu webové služby (2hodinové přihrádky):

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA)**\]**

```kusto
demo_series3
| render timechart 
```

![Sezónnost časových řad](media/time-series-analysis/time-series-seasonality.png)

- Pomocí [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) můžete automaticky zjistit období v časových řadách. 
- Použijte [series_periods_validate(),](/azure/kusto/query/series-periods-validatefunction) pokud víme, že metrika by měla mít konkrétní odlišné období a chceme ověřit, že existují.

> [!NOTE]
> Je to anomálie, pokud konkrétní odlišné období neexistují

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA)**\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | Období | Skóre | Dní |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

Funkce detekuje denní a týdenní sezónnost. Denní skóre méně než týdenní, protože víkendové dny se liší od všední dny.

### <a name="element-wise-functions"></a>Funkce z hlediska elementů

Aritmetické a logické operace lze provádět v časové řadě. Pomocí [series_subtract()](/azure/kusto/query/series-subtractfunction) můžeme vypočítat zbytkové časové řady, to znamená rozdíl mezi původní hrubou metrikou a vyhlazenou metrikou a hledat anomálie zbytkového signálu:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=)**\]**

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

![Operace časových řad](media/time-series-analysis/time-series-operations.png)

- Modrá: původní časová řada
- Červená: vyhlazená časová řada
- Zelená: zbytková časová řada

## <a name="time-series-workflow-at-scale"></a>Pracovní postup časové řady ve velkém měřítku

Následující příklad ukazuje, jak tyto funkce lze spustit ve velkém měřítku na tisíce časových řad v sekundách pro detekci anomálií. Chcete-li zobrazit několik ukázkových záznamů telemetrie metriky počtu čtení služby DB za čtyři dny, spusťte následující dotaz:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==)**\]**

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | Časové razítko | Loc | anonOp | DB | Čtení dat |
|   | 2016-09-11 21:00:00.0000000 | Místo k místu 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Místo k místu 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Místo k místu 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Místo k místu 9 | 371921734563783410 | 255 | 0 |

A jednoduché statistiky:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA)**\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Vytvoření časových řad v 1hodinových přihrádkách metriky pro čtení (celkem čtyři dny * 24 hodin = 96 bodů) má za následek normální kolísání vzorku:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Časové řady ve velkém měřítku](media/time-series-analysis/time-series-at-scale.png)

Výše uvedené chování je zavádějící, protože jeden normální časové řady je agregována z tisíců různých instancí, které mohou mít abnormální vzory. Proto vytvoříme časové řady pro instanci. Instance je definována Loc (umístění), anonOp (operace) a DB (konkrétní stroj).

Kolik časových řad můžeme vytvořit?

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=)**\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Počet |
|   | 18339 |

Nyní vytvoříme sadu časové řady 18339 metriky počtu čtení. Přidáme `by` klauzuli do příkazu make-series, použijeme lineární regresi a vybereme dvě horní časové řady, které měly nejvýznamnější klesající trend:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Časové řady top dva](media/time-series-analysis/time-series-top-2.png)

Zobrazení instancí:

**\[**[**Klepnutím spustíte dotaz.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Loc | Op | DB | Svahu |
|   | Místo 15 | 37 | 1151 | -102743.910227889 |
|   | Místo 13 | 37 | 1249 | -86303.2334644601 |

Za méně než dvě minuty, ADX analyzoval téměř 20.000 časových řad a zjistil dvě abnormální časové řady, ve kterých čtení náhle klesla.

Tyto pokročilé funkce v kombinaci s rychlým výkonem ADX poskytují jedinečné a výkonné řešení pro analýzu časových řad.

## <a name="next-steps"></a>Další kroky

* Další informace o [detekci anomálií časové řady a prognózování](/azure/data-explorer/anomaly-detection) v Průzkumníku dat Azure.
* Přečtěte si o [možnostech strojového učení](/azure/data-explorer/machine-learning-clustering) v Azure Data Exploreru.