---
title: Analýza dat časových řad pomocí Azure Průzkumník dat
description: Naučte se analyzovat data časových řad v cloudu pomocí Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 3873b25394f91ce1c1601c348de2098198ba7fdd
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765479"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analýza časových řad v Azure Průzkumník dat

Azure Průzkumník dat (ADX) provádí průběžnou shromažďování dat telemetrie z cloudových služeb nebo zařízení IoT. Tato data je možné analyzovat pro různé přehledy, jako je monitorování stavu služby, fyzických produkčních procesů a trendů využití. Analýza se provádí v časové řadě vybraných metrik za účelem nalezení odchylky ve vzoru v porovnání s běžným vzorem standardních hodnot.
ADX obsahuje nativní podporu pro vytváření, manipulaci a analýzu více časových řad. V tomto tématu se dozvíte, jak se ADX používá k vytváření a analýze **tisíců časových řad v řádu sekund**, což umožňuje monitorování a pracovní postupy pro monitorování téměř v reálném čase.

## <a name="time-series-creation"></a>Vytváření časových řad

V této části vytvoříme rozsáhlou sadu pravidelných časových řad jednoduše a intuitivním pomocí operátoru `make-series` a podle potřeby vyplní chybějící hodnoty.
Prvním krokem analýzy časových řad je rozdělit a transformovat původní tabulku telemetrie na sadu časových řad. Tabulka obvykle obsahuje sloupec časového razítka, kontextové dimenze a volitelné metriky. Dimenze se používají k rozdělení dat do oddílů. Cílem je vytvořit tisíce časových řad na oddíl v pravidelných časových intervalech.

Vstupní tabulka *demo_make_series1* obsahuje záznamy 600K libovolného provozu webové služby. Pomocí následujícího příkazu můžete vzorkovat 10 záznamů:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA) **\]**

```kusto
demo_make_series1 | take 10 
```

Výsledná tabulka obsahuje sloupec časového razítka, tři sloupce kontextových dimenzí a žádné metriky:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Časové razítko | BrowserVer | OsVer | Země/oblast |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51,0 | Windows 7 | Spojené království |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52,0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52,0 | Windows 7 | Spojené království |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52,0 | Windows 10 | Spojené království |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52,0 | Windows 10 | Lotyšskoská republika |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52,0 | Windows 8.1 | Indie |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52,0 | Windows 10 | Spojené království |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52,0 | Windows 7 | Nizozemsko |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52,0 | Windows 10 | Spojené království |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52,0 | Windows 10 | Indie |

Vzhledem k tomu, že neexistují žádné metriky, můžeme sestavit jenom sadu časových řad, které představují samotný počet přenosů rozdělený podle OS, pomocí následujícího dotazu:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Použijte operátor [`make-series`](/azure/kusto/query/make-seriesoperator) k vytvoření sady tří časových řad, kde:
    - `num=count()`: časová řada provozu
    - `range(min_t, max_t, 1h)`: časová řada se vytvoří v intervalu od 1 hodiny (nejstarší a nejnovější časové razítko záznamů tabulky).
    - `default=0`: Zadejte metodu Fill pro chybějící přihrádky pro vytvoření pravidelných časových řad. Případně použijte [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction), [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction), [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) a [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) pro změny
    - `byOsVer`: dělení podle OS
- Skutečná datová struktura časové řady je číselné pole agregované hodnoty pro každou časovou přihrádku. Pro vizualizaci používáme `render timechart`.

V tabulce výše máme tři oddíly. Pro každou verzi operačního systému, jak je vidět v grafu, můžeme vytvořit samostatnou časovou řadu: Windows 10 (Red), 7 (Blue) a 8,1 (zelenou):

![Oddíl časových řad](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Funkce analýzy časových řad

V této části provedeme typické funkce pro zpracování řad.
Po vytvoření sady časových řad ADX podporuje rostoucí seznam funkcí pro zpracování a analýzu, které najdete v [dokumentaci k časové řadě](/azure/kusto/query/machine-learning-and-tsa). Popíšeme několik reprezentativních funkcí pro zpracování a analýzu časových řad.

### <a name="filtering"></a>Filtrování

Filtrování je běžný postup při zpracování signálu a užitečný pro úlohy zpracování časových řad (například vyhlazení signálu s vysokou zátěží, zjišťování změn).
- Existují dvě obecné funkce filtrování:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): použití filtru FIR. Slouží k jednoduchému výpočtu klouzavého průměru a odlišení časové řady pro detekci změn.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): použití filtru IIR. Používá se k exponenciálnímu vyhlazení a kumulativnímu součtu.
- `Extend` nastavení časové řady přidáním nové klouzavý průměr řady o velikosti 5 přihrádek (s názvem *ma_num*) do dotazu:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=) **\]**

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

ADX podporuje analýzu segmentované lineární regrese k odhadu trendu časových řad.
- Použijte [series_fit_line ()](/azure/kusto/query/series-fit-linefunction) , chcete-li přizpůsobit nejlepší linii na časovou řadu pro obecné zjišťování trendů.
- Použijte [series_fit_2lines ()](/azure/kusto/query/series-fit-2linesfunction) k detekci změn trendů vzhledem ke standardním hodnotám, které jsou užitečné při monitorování scénářů.

Příklad funkcí `series_fit_line()` a `series_fit_2lines()` v dotazu Time Series:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=) **\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Regrese časové řady](media/time-series-analysis/time-series-regression.png)

- Blue: původní časová řada
- Zelená: nainstalovaná čára
- Červená: dva montované čáry

> [!NOTE]
> Funkce přesně zjistila odkaz (změnu úrovně).

### <a name="seasonality-detection"></a>Detekce sezónnost

Mnoho metrik sleduje sezónní (pravidelné) vzory. Uživatelský provoz Cloud Services většinou obsahuje denní a týdenní vzory, které jsou nejnižšími středními částmi pracovního dne a nejnižší v noci a za víkend. Měření senzorů IoT v pravidelných intervalech. Fyzická měření, jako je teplota, tlak nebo vlhkost, mohou také ukazovat na sezónní chování.

Následující příklad aplikuje sezónnost detekci na jeden měsíc provozu webové služby (2 – hodiny přihrádky):

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA) **\]**

```kusto
demo_series3
| render timechart 
```

![Time Series sezónnost](media/time-series-analysis/time-series-seasonality.png)

- K automatické detekci časových řad použijte [series_periods_detect ()](/azure/kusto/query/series-periods-detectfunction) . 
- Pokud víme, že by měla metrika obsahovat konkrétní konkrétní tečky, použijte [series_periods_validate ()](/azure/kusto/query/series-periods-validatefunction) , aby bylo možné ověřit, že existují.

> [!NOTE]
> Je to anomálie, pokud konkrétní odlišná období neexistují.

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA) **\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | době | počty | denní |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1\. místo |

Funkce detekuje denní a týdenní sezónnost. Denní skóre méně než týdně, protože víkendové dny se liší od pracovních dnů.

### <a name="element-wise-functions"></a>Funkce pro prvky

Aritmetické a logické operace lze provést v časové řadě. Pomocí [series_subtract ()](/azure/kusto/query/series-subtractfunction) můžeme vypočítat reziduální časovou řadu, tj. rozdíl mezi původní nezpracovaný metrikou a nahladkou a vyhledat anomálie v reziduálním signálu:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=) **\]**

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

![Operace s časovou řadou](media/time-series-analysis/time-series-operations.png)

- Blue: původní časová řada
- Red: hladká časová řada
- Zelená: zbytková časová řada

## <a name="time-series-workflow-at-scale"></a>Pracovní postup ve škále časových řad

Níže uvedený příklad ukazuje, jak se tyto funkce můžou spouštět ve velkém měřítku na tisících časových řad v sekundách pro detekci anomálií. Pokud chcete zobrazit několik ukázkových záznamů telemetrie metriky počtu čtení služby DB za čtyři dny, spusťte následující dotaz:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==) **\]**

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | ČASOVÉ razítko | Loc | anonOp | INŽENÝR | Čtení z |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

A jednoduché statistiky:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA) **\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | počet | minimální\_t | maximální\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Výsledkem sestavování časové řady v rámci 1 hodinové přihrádky metriky čtení (celkem čtyři dny × 24 hodin = 96 bodů), výsledkem je normální kolísání vzoru:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Časové řady ve velkém měřítku](media/time-series-analysis/time-series-at-scale.png)

Výše uvedené chování je zavádějící, protože jedna normální časová řada je agregována z tisíců různých instancí, které mohou mít neobvyklé vzory. Proto vytvoříme časovou řadu na instanci. Instance je definována v umístění (location), anonOp (operace) a DB (konkrétní počítač).

Kolik časových řad můžeme vytvořit?

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=) **\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Počet |
|   | 18339 |

Nyní vytvoříme sadu 18339 časových řad metriky Count pro čtení. Do příkazu make-Series přidáte klauzuli `by`, aplikujete lineární regresi a vyberete horní dvě časové řady, které mají nejvýznamnější trend.

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Time Series – horní 2](media/time-series-analysis/time-series-top-2.png)

Zobrazit instance:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA) **\]**

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
|   | Loc | Evřít | INŽENÝR | sklon |
|   | Loc 15 | 37 | 1151 | -102743,910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

Za méně než dvě minuty ADXa analýza blízko až 20 000 časové řady a zjistila se dvě neobvyklé časové řady, ve kterých se počet čtení náhle vynechá.

Tyto pokročilé funkce kombinované s ADXm vysokým výkonem poskytují jedinečné a výkonné řešení pro analýzu časových řad.

## <a name="next-steps"></a>Další kroky

* Seznamte se s [detekcí anomálií a prognózou časových řad](/azure/data-explorer/anomaly-detection) v Azure Průzkumník dat.
* Seznamte se s [možnostmi strojového učení](/azure/data-explorer/machine-learning-clustering) v Azure Průzkumník dat.