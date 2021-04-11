---
title: Analýza výkonu
titleSuffix: Azure Cognitive Search
description: Bude doplněno
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 298508f8068b47cbfc720f1d1e8ddf370323ed28
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582230"
---
# <a name="analyze-performance-in-azure-cognitive-search"></a>Analýza výkonu v Azure Kognitivní hledání

Tento článek popisuje nástroje, chování a přístupy k analýze výkonu dotazů a indexování v Kognitivní hledání.

## <a name="develop-baseline-numbers"></a>Vývoj základních čísel

V jakékoli velké implementaci je velmi důležité provést test srovnávacích testů výkonu služby Kognitivní hledání před tím, než je převedete do produkčního prostředí. Měli byste otestovat jak očekávané zatížení vyhledávacího dotazu, ale také očekávané úlohy příjmu dat (Pokud je to možné, spouštějte je současně). Používání srovnávacích čísel pomáhá ověřit správnou [úroveň vyhledávání](search-sku-tier.md), [konfiguraci služby](search-capacity-planning.md)a očekávanou [latenci dotazů](search-performance-analysis.md#average-query-latency).

Pro vývoj srovnávacích testů doporučujeme nástroj [Azure-Search-Performance-Testing (GitHub)](https://github.com/Azure-Samples/azure-search-performance-testing) .

Chcete-li izolovat účinky architektury distribuované služby, zkuste provést test na konfiguracích služby jedné repliky a jednom oddílu.

> [!NOTE]
> Pro vrstvy optimalizované pro úložiště (L1 a L2) byste měli očekávat nižší propustnost dotazů a vyšší latenci než na úrovni Standard.
>

## <a name="use-diagnostic-logging"></a>Použití protokolování diagnostiky

Nejdůležitějším nástrojem, který správce diagnostikuje o potenciálních problémech s výkonem, je [diagnostické protokolování](search-monitor-logs.md) , které shromažďuje provozní data a metriky o službě vyhledávání. Protokolování diagnostiky je povolené prostřednictvím [Azure monitor](../azure-monitor/overview.md). Existují náklady spojené s používáním Azure Monitor a ukládání dat, ale pokud ji pro vaši službu povolíte, může být pro zkoumání problémů s výkonem instrumentická.

K dispozici je více příležitostí pro latenci, ať už během přenosu v síti, nebo během zpracování obsahu ve vrstvě služeb App Services nebo ve vyhledávací službě. Klíčovou výhodou protokolování diagnostiky je, že aktivity se protokolují z perspektivy služby vyhledávání, což znamená, že protokol vám pomůže určit, jestli jsou problémy s výkonem způsobené problémy s dotazem nebo indexováním nebo nějakým jiným bodem selhání.

:::image type="content" source="media/search-performance/perf-log-event-chain.png" alt-text="Řetěz protokolovaných událostí" border="true":::

Protokolování diagnostiky poskytuje možnosti pro ukládání protokolovaných informací. Doporučujeme použít [Log Analytics](../azure-monitor/logs/log-analytics-overview.md) , abyste mohli provádět rozšířené dotazy Kusto na data, abyste mohli odpovědět na mnoho otázek týkajících se využití a výkonu. 

Na stránkách portálu služby Search můžete povolit protokolování prostřednictvím **nastavení diagnostiky** a pak vydávat dotazy Kusto proti Log Analytics tím, že vyberete **protokoly**. Další informace o nastavení najdete v tématu [shromažďování a analýza dat protokolu](search-monitor-logs.md).

:::image type="content" source="media/search-performance/perf-log-menu-options.png" alt-text="Možnosti nabídky protokolování" border="true":::

## <a name="throttling-behaviors"></a>Chování omezování

K omezování dochází, když vyhledávací služba dosáhla limitu toho, co dokáže zvládnout z perspektivy prostředků. Omezování může probíhat během dotazů nebo indexování. Na straně klienta při volání rozhraní API dojde k tomu, že při omezení dojde k odpovědi na 503 HTTP. Při indexování je také možné přijmout odpověď HTTP 207, která indikuje, že se nepovedlo indexovat jednu nebo více položek. Tato chyba je indikátorem, že vyhledávací služba blíží kapacitu. 

Jako pravidlo pro palec je nejlepší vyčíslit množství omezení, které vidíte. Například pokud je jeden vyhledávací dotaz z 500 000 omezený, nemusí se jednat o velký problém. Pokud je však v určité lhůtě omezeno vysoké procento dotazů, bude to mít větší obavy. Když si vyhledáte omezení za určitou dobu, pomůže vám také identifikovat časové rámce, ve kterém může být větší omezení, a pomůže vám rozhodnout se, jak to nejlépe přizpůsobit.

Jednoduchou opravou většiny problémů s omezením je vyvolávat ve službě vyhledávání více prostředků (obvykle repliky pro omezování založené na dotazech nebo oddíly pro omezování založené na indexování). Zvýšením nebo oddílů repliky ale zvýšíme náklady, což je důvod, proč je důležité znát důvod, proč k omezování dochází. Zkoumání podmínek, které způsobují omezení, bude vysvětleno v následujících několika částech. 

Níže je uveden příklad dotazu Kusto, který může identifikovat rozpis odpovědí HTTP ze služby vyhledávání, která byla načtena. Při dotazování v průběhu 7 dnů ukazuje vykreslený pruhový graf, že poměrně velké procento vyhledávacích dotazů bylo omezeno porovnáním s počtem úspěšných (200) odpovědí.

```kusto
AzureDiagnostics
| where TimeGenerated > ago(7d)
| summarize count() by resultSignature_d 
| render barchart 
```

:::image type="content" source="media/search-performance/perf-bar-chart-http-errors.png" alt-text="Pruhový graf počtů chyb http" border="true":::

V rámci kontroly omezování v určitém časovém období vám může poznat čas, kdy se omezení může vyskytnout častěji. V níže uvedeném příkladu je graf časové řady použit k zobrazení počtu omezených dotazů, k nimž došlo během zadaného časového rámce. V tomto případě bylo provedeno omezené dotazy v relaci s časy v porovnání s výkonem.

```kusto
let ['_startTime']=datetime('2021-02-25T20:45:07Z');
let ['_endTime']=datetime('2021-03-03T20:45:07Z');
let intervalsize = 1m; 
AzureDiagnostics 
| where TimeGenerated > ago(7d)
| where resultSignature_d != 403 and resultSignature_d != 404 and OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete")
| summarize 
  ThrottledQueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete") and resultSignature_d == 503)/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart   
```

:::image type="content" source="media/search-performance/perf-line-chart-throttled-queries.png" alt-text="Spojnicový graf omezených dotazů" border="true":::

## <a name="measure-individual-queries"></a>Měření jednotlivých dotazů

V některých případech může být užitečné otestovat jednotlivé dotazy a zjistit, jak se provádějí. K tomu je důležité, abyste viděli, jak dlouho služba vyhledávání potřebuje dokončit práci, a jak dlouho trvá, aby se odeslal požadavek na zpáteční dobu od klienta a zpátky klientovi. Diagnostické protokoly je možné použít k vyhledání jednotlivých operací, ale to může být jednodušší z klientského nástroje, jako je například post.

V následujícím příkladu byl proveden vyhledávací dotaz založený na REST. Kognitivní hledání obsahuje v každé reakci počet milisekund potřebný k dokončení dotazu, který je viditelný na kartě hlavičky v části "uplynulý čas". Vedle pole stav v horní části odpovědi zjistíte dobu trvání odezvy. v tomto případě 418 milisekund. V části Results se zvolila karta Headers (hlavičky). Pomocí těchto dvou hodnot zvýrazněných červeným polem na obrázku níže vidíte, že vyhledávací služba trvala od 21 MS, aby dokončila vyhledávací dotaz, a celý požadavek na zpáteční cestu klienta trval 125 ms. Odčítáním těchto dvou čísel můžeme určit, že trvalo 104 MS čas odeslání vyhledávacího dotazu do služby Search a přenést výsledky hledání zpátky do klienta.

To může být velmi užitečné, pokud chcete zjistit, jestli existují latence sítě nebo jiné faktory ovlivňující výkon dotazů.

:::image type="content" source="media/search-performance/perf-elapsed-time.png" alt-text="Metriky doby trvání dotazu" border="true":::

## <a name="query-rates"></a>Tarify dotazů

Jedním z možných důvodů, proč vaše vyhledávací služba omezuje požadavky, je Sheer počet dotazů, které jsou prováděny v případě, že je svazek zachycen jako dotaz za sekundu (QPS) nebo dotazy za minutu (QPM). Vzhledem k tomu, že vaše vyhledávací služba přijímá více QPS, obvykle trvá déle a déle reagovat na tyto dotazy, dokud je nenebudete moct dál uchovávat, jak pošle zpět omezení 503 HTTP odpovědi. 

Následující dotaz Kusto ukazuje svazek dotazu měřený v QPM a průměrnou dobu trvání dotazu v milisekundách (AvgDurationMS) a průměrný počet dokumentů (AvgDocCountReturned) vrácených v každém z nich.

```kusto
AzureDiagnostics
| where OperationName == "Query.Search" and TimeGenerated > ago(1d)
| extend MinuteOfDay = substring(TimeGenerated, 0, 16) 
| project MinuteOfDay, DurationMs, Documents_d, IndexName_s
| summarize QPM=count(), AvgDuractionMs=avg(DurationMs), AvgDocCountReturned=avg(Documents_d)  by MinuteOfDay
| order by MinuteOfDay desc 
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-queries-per-minute.png" alt-text="Graf znázorňující dotazy za minutu" border="true":::

> [!TIP]
> Chcete-li zobrazit data za tímto grafem, odeberte řádek `| render timechart` a pak znovu spusťte dotaz.

## <a name="impact-of-indexing-on-queries"></a>Dopad indexování na dotazy

Důležitým faktorem, který byste měli vzít v úvahu při prohlížení výkonu, je, že indexování používá stejné prostředky jako vyhledávací dotazy. Pokud indexuje velké množství obsahu, můžete očekávat zvýšení latence, protože se služba pokusí přizpůsobit obě úlohy.

Pokud se dotazy zpomalují, podívejte se na časování aktivity indexování, abyste viděli, jestli se shoduje se snížením výkonu dotazů. Například indexer používá denní nebo hodinovou úlohu, která se koreluje se sníženým výkonem vyhledávacích dotazů. 

Tato část poskytuje sadu dotazů, které vám pomohou vizualizovat míry hledání a indexování. V těchto příkladech je časový rozsah nastaven v dotazu. Při spouštění dotazů v Azure Portal Nezapomeňte zadat **nastavení v dotazu** .

:::image type="content" source="media/search-performance/perf-set-query-time-range.png" alt-text="Nastavení časových rozsahů v nástroji pro dotazování" border="true":::

<a name="average-query-latency"></a>

### <a name="average-query-latency"></a>Průměrná latence dotazů

V níže uvedeném dotazu se pro zobrazení průměrné latence vyhledávacích dotazů používá hodnota intervalu 1 minuta. V grafu vidíte, že průměrná latence byla nízká až do 5:45pm a poslední až do 5:53pm.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime']..['_endTime']) // Time range filtering
| summarize AverageQueryLatency = avgif(DurationMs, OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))
    by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-query-latency.png" alt-text="Graf znázorňující průměrnou latenci dotazů" border="true":::

### <a name="average-queries-per-minute-qpm"></a>Průměrný počet dotazů za minutu (QPM)

Následující dotaz vám umožní podívat se na průměrný počet dotazů za minutu, aby se zajistilo, že se v požadavcích na hledání nemusely vyřadit špičky, které by mohly mít vliv na latenci. V grafu vidíte určitou odchylku, ale nic k označení špičky v počtu požadavků.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize QueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-queries-per-minute.png" alt-text="Graf znázorňující průměrných dotazů za minutu" border="true":::

### <a name="indexing-operations-per-minute-opm"></a>Operace indexování za minutu (a)

Tady se podíváme na počet operací indexování za minutu. V tomto grafu vidíte, že indexování velkého množství dat bylo zahájeno v 5:42 pm a skončilo v 5:50pm. Toto indexování začalo 3 minuty před zahájením vyhledávacích dotazů, které se staly nelatentními a skončilo 3 minuty před tím, než vyhledávací dotazy již nejsou latentní.

Z tohoto článku jsme zjistili, že trvalo zhruba 3 minuty, než je služba Search dostatečně zaneprázdněná, aby bylo možné začít ovlivnit latenci vyhledávacích dotazů. Můžeme také vidět, že po dokončení indexování trvalo další 3 minuty, aby vyhledávací služba dokončila veškerou práci z nově indexovaného obsahu, dokud vyhledávací dotazy nezačínají na latentní.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize IndexingOperationsPerSecond=bin(countif(OperationName == "Indexing.Index")/ (intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart 
```

:::image type="content" source="media/search-performance/perf-line-chart-indexing-operations.png" alt-text="Graf znázorňující operace indexování za minutu" border="true":::

## <a name="background-service-processing"></a>Zpracování služby na pozadí

Nezvykle zobrazovat pravidelné špičky v dotazech nebo latenci indexování. Špičky se můžou vyskytnout v reakci na indexování nebo vysoké míry dotazů, ale můžou se taky vyskytnout během slučovacích operací. Vyhledávací indexy se ukládají do bloků dat nebo horizontálních oddílů. Systém pravidelně slučuje menší horizontálních oddílů do velkých horizontálních oddílů, což může přispět k optimalizaci výkonu služby. Tento proces sloučení také vyčistí dokumenty, které byly dříve označeny k odstranění z indexu, což vede k obnovení prostoru úložiště. 

Sloučení horizontálních oddílů je rychlé, ale také náročné na prostředky, takže má potenciál snížit výkon služby. Z tohoto důvodu, pokud vidíte krátké shluky latence dotazů a tyto shluky se shodují s nedávnémi změnami v indexovaném obsahu, je pravděpodobně atribut, který latence horizontálních oddílů operace sloučení. 

## <a name="next-steps"></a>Další kroky

Projděte si tyto další články, které souvisejí s analýzou výkonu služby.

+ [Tipy pro zvýšení výkonu](search-performance-tips.md)
+ [Zvolit úroveň služby](search-sku-tier.md)
+ [Správa kapacity](search-capacity-planning.md)
