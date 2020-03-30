---
title: Jednotky streamování v Azure Stream Analytics
description: Tento článek popisuje nastavení jednotek streamování a další faktory, které ovlivňují výkon ve službě Azure Stream Analytics.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 397e455c8b6a1097e2a32473036e1acd2bbdf2eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267349"
---
# <a name="understand-and-adjust-streaming-units"></a>Principy a úpravy jednotek streamování

Jednotky streamování (SU) představují výpočetní prostředky, které jsou přiděleny ke spuštění úlohy Stream Analytics. Čím vyšší je počet SU, tím více prostředků CPU a paměti se úloze přidělí. Tato kapacita umožňuje zaměřit se na logiku dotazu a abstrahuje potřebu spravovat hardware pro včasné spuštění úlohy Stream Analytics.

Aby se dosáhlo nízké latence zpracování streamů, provádějí úlohy Stream Analytics veškeré zpracování v paměti. Při nedostatku paměti se úloha streamování nezdaří. V důsledku toho je pro produkční úlohu důležité sledovat využití prostředků úlohy streamování a ujistěte se, že je přidělen dostatek prostředků, aby úlohy běžely 24 hodin denně, 7 dní v řadě.

Metrika využití SU %, která se pohybuje od 0 % do 100 %, popisuje spotřebu paměti vašeho pracovního vytížení. U úlohy streamování s minimálním nárokem na půdu je tato metrika obvykle mezi 10 % až 20 %. Pokud su% využití je nízká a vstupní události získat backlogged, vaše úloha pravděpodobně vyžaduje více výpočetních prostředků, což vyžaduje zvýšení počtu su. Je nejlepší udržet metriku SU pod 80%, aby se zohlednily občasné špičky. Společnost Microsoft doporučuje nastavit výstrahu na metriku využití 80 % su, aby se zabránilo vyčerpání prostředků. Další informace najdete [v tématu Kurz: Nastavení výstrah pro úlohy Azure Stream Analytics](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurace jednotek streamování analýzy datového proudu (SU)
1. Přihlášení na [Portál Azure](https://portal.azure.com/)

2. V seznamu zdrojů najděte úlohu Stream Analytics, kterou chcete škálovat, a pak ji otevřete. 

3. Na stránce úlohy vyberte v části **Konfigurovat** **nadpis Měřítko**. 

    ![Konfigurace úloh Azure portálu Stream Analytics][img.stream.analytics.preview.portal.settings.scale]
    
4. Pomocí posuvníku nastavte su pro úlohu. Všimněte si, že jste omezeni na konkrétní nastavení SU. 
5. Počet su přiřazených k úloze můžete změnit i v případě, že je spuštěna. To není možné, pokud vaše úloha používá [výstup bez oddílů](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#query-using-non-partitioned-output) nebo má [vícekrokový dotaz s různými hodnotami PARTITION BY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#multi-step-query-with-different-partition-by-values). Možná omezena na výběr ze sady hodnot SU při spuštění úlohy. 

## <a name="monitor-job-performance"></a>Sledování výkonu úlohy
Pomocí portálu Azure můžete sledovat propustnost úlohy:

![Azure Stream Analytics monitoruje úlohy][img.stream.analytics.monitor.job]

Vypočítejte očekávanou propustnost pracovního vytížení. Pokud je propustnost menší, než bylo očekáváno, vylaďte vstupní oddíl, vylaďte dotaz a přidejte sou do úlohy.

## <a name="how-many-sus-are-required-for-a-job"></a>Kolik SU je pro úlohu potřeba?

Výběr počtu požadovaných su pro konkrétní úlohu závisí na konfiguraci oddílu pro vstupy a dotaz, který je definován v rámci úlohy. Stránka **Měřítko** umožňuje nastavit správný počet su. Je osvědčeným postupem přidělit více su, než je potřeba. Modul zpracování Stream Analytics optimalizuje latenci a propustnost za cenu přidělení další paměti.

Obecně platí, že osvědčeným postupem je začít s 6 su pro dotazy, které nepoužívají **PARTITION BY**. Poté určete sweet spot pomocí metody pokusu a omylu, ve které změníte počet su po předání reprezentativních množství dat a prohlédnete metriku Využití SU%. Maximální počet jednotek streamování, které lze použít úlohu Stream Analytics, závisí na počtu kroků v dotazu definovaném pro úlohu a na počtu oddílů v každém kroku. Další informace o limitech naleznete [zde](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Další informace o výběru správného počtu su najdete na této stránce: [Škálování úloh Azure Stream Analytics pro zvýšení propustnosti](stream-analytics-scale-jobs.md)

> [!Note]
> Výběr počtu su jsou požadovány pro konkrétní úlohu závisí na konfiguraci oddílu pro vstupy a na dotaz udefinovaný pro úlohu. Pro úlohu můžete vybrat až kvótu v su. Ve výchozím nastavení má každé předplatné Azure kvótu až 500 su pro všechny analytické úlohy v určité oblasti. Chcete-li zvýšit su pro vaše předplatná nad tuto kvótu, obraťte se na [podporu společnosti Microsoft](https://support.microsoft.com). Platné hodnoty pro su na úlohu jsou 1, 3, 6 a nahoru v krocích po 6.

## <a name="factors-that-increase-su-utilization"></a>Faktory, které zvyšují procentuální využití SU 

Časové (časově orientované) dotazovací prvky jsou základní sadou stavových operátorů poskytovaných službou Stream Analytics. Stream Analytics spravuje stav těchto operací interně jménem uživatele, správou spotřeby paměti, kontrolními body pro odolnost proti chybám a obnovení stavu během upgradů služby. I když Stream Analytics plně spravuje stavy, existuje řada doporučení osvědčených postupů, které by uživatelé měli zvážit.

Všimněte si, že úloha s logikou složitýdotaz může mít vysoké využití SU% i v případě, že není neustále příjem vstupní události. K tomu může dojít po náhlém nárůstu vstupních a výstupních událostí. Úloha může nadále udržovat stav v paměti, pokud je dotaz složitý.

SU% využití může náhle klesnout na 0 na krátkou dobu před návratem na očekávané úrovně. K tomu dochází z důvodu přechodných chyb nebo inovací iniciovaných systémem. Zvýšení počtu jednotek streamování pro úlohu nemusí snížit využití SU % pokud dotaz není [zcela paralelní](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization).

Při porovnávání využití za určité časové období použijte [metriky míry výskytu událostí](stream-analytics-monitoring.md). InputEvents a OutputEvents metriky ukazují, kolik událostí byly přečteny a zpracovány. Existují metriky, které také označují počet chybových událostí, jako jsou například chyby při rekonstrukci. Když se počet událostí na jednotku času zvyšuje, SU% zvyšuje ve většině případů.

## <a name="stateful-query-logicin-temporal-elements"></a>Logika stavového dotazu v časových prvcích
Jednou z jedinečných funkcí úlohy Azure Stream Analytics je provádět stavové zpracování, jako jsou agregace v okně, časová spojení a časové analytické funkce. Každý z těchto operátorů uchovává informace o stavu.Maximální velikost okna pro tyto prvky dotazu je sedm dní. 

Koncept časového okna se zobrazí v několika prvcích dotazu Služby Stream Analytics:
1. Okenní agregáty: GROUP BY of Tumbling, Hopping, a Posuvné okna

2. Časová spojení: SPOJENÍ s funkcí DATEDIFF

3. Časové analytické funkce: ISFIRST, LAST a MAS s LIMIT DURATION

Následující faktory ovlivňují paměť používanou (součást metriky jednotek streamování) úlohy Stream Analytics:

## <a name="windowed-aggregates"></a>Okno agregáty
Spotřebovaná paměť (velikost stavu) pro windowed agregát není vždy přímo úměrná velikosti okna. Místo toho spotřebované paměti je úměrná mohutnost dat nebo počet skupin v každém časovém okně.


Například v následujícím dotazu číslo `clusterid` spojené s je mohutnost dotazu. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Chcete-li zmírnit všechny problémy způsobené vysokou mohutnost v předchozím dotazu, `clusterid`můžete odeslat události do centra událostí rozdělené do aplikace a škálovat dotaz tím, že systém udělí težce zpracovat každý vstupní oddíl samostatně pomocí **funkce PARTITION BY,** jak je znázorněno v níže uvedeném příkladu:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. V důsledku toho je `clusterid` snížen počet hodnot přicházejících do každého uzlu, čímž se sníží mohutnost skupiny operátorem. 

Oddíly centra událostí by měly být rozděleny pomocí klíče seskupení, aby se zabránilo nutnosti snížit krok. Další informace naleznete v [tématu Přehled centra událostí](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Časové spojení
Spotřebovaná paměť (velikost stavu) časového spojení je úměrná počtu událostí v časové místnosti spojení, což je vstupní rychlost události vynásobená velikostí místnosti manévrovacího prostoru. Jinými slovy, paměť spotřebovaná spojením je úměrná časovému rozsahu DateDiff vynásobenému průměrnou rychlostí událostí.

Počet neodpovídající události ve spojení ovlivnit využití paměti pro dotaz. Následující dotaz hledá imprese reklamy, které generují kliknutí:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

V tomto příkladu je možné, že se zobrazí spousta reklam a jen málo lidí na ně klikne a je nutné zachovat všechny události v časovém okně. Využitá paměť je přímo úměrná velikosti tohoto okna a frekvenci událostí. 

Chcete-li tento problém napravit, odešlete události do centra událostí rozdělené klíči spojení (v tomto případě ID) a škálujte dotaz tím, že systému umožníte zpracovat každý vstupní oddíl samostatně pomocí **oddílu PARTITION BY,** jak je znázorněno:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. V důsledku toho se sníží počet událostí přicházejících do každého uzlu, čímž se sníží velikost stavu uchovávaného v okně spojení. 

## <a name="temporal-analytic-functions"></a>Časové analytické funkce
Spotřebovaná paměť (velikost stavu) časové analytické funkce je úměrná rychlosti výskytu událostí vynásobením dobou trvání.Paměť spotřebovaná analytickými funkcemi není úměrná velikosti okna, ale spíše počet oddílů v každém časovém okně.

Sanace je podobná časové spojení. Dotaz můžete škálovat pomocí **funkce PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Vyrovnávací paměť mimo pořadí 
Uživatel může nakonfigurovat velikost vyrovnávací paměti mimo pořadí v konfiguračním podokně Řazení událostí. Vyrovnávací paměť se používá k uložení vstupů po dobu trvání okna a jejich pořadí. Velikost vyrovnávací paměti je úměrná vstupní rychlosti události vynásobí velikost okna mimo pořadí. Výchozí velikost okna je 0. 

Chcete-li napravit přetečení vyrovnávací paměti mimo pořadí, horizontální navýšení kapacity dotazu pomocí **partition BY**. Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. V důsledku toho je počet událostí přicházejících do každého uzlu snížen, čímž se sníží počet událostí v každé vyrovnávací paměti při objednání. 

## <a name="input-partition-count"></a>Počet vstupních oddílů 
Každý vstupní oddíl vstupu úlohy má vyrovnávací paměť. Čím větší počet vstupních oddílů, tím více prostředků úloha spotřebovává. Pro každou jednotku streamování azure stream analytics můžete zpracovat zhruba 1 MB/s vstupu. Proto můžete optimalizovat porovnáním počtu streamovacích jednotek Stream Analytics s počtem oddílů v centru událostí. 

Úloha nakonfigurovaná s jednou jednotkou streamování je obvykle dostatečná pro centrum událostí se dvěma oddíly (což je minimum pro Centrum událostí). Pokud centrum událostí obsahuje více oddílů, vaše úloha Stream Analytics spotřebovává více prostředků, ale nemusí nutně využívat další propustnost poskytovanou centrem událostí. 

Pro úlohu se 6 streamovacími jednotkami můžete potřebovat 4 nebo 8 oddílů z centra událostí. Vyhněte se však příliš mnoho zbytečné oddíly, protože to způsobuje nadměrné využití prostředků. Například Centrum událostí s 16 oddíly nebo větší v úloze Stream Analytics, která má 1 jednotku streamování. 

## <a name="reference-data"></a>Referenční údaje 
Referenční data v ASA jsou načteny do paměti pro rychlé vyhledávání. S aktuální implementací každá operace spojení s referenčními daty uchovává kopii referenčních dat v paměti, i když se připojíte ke stejným referenčním datům vícekrát. Pro dotazy s **PARTITION BY**má každý oddíl kopii referenčních dat, takže oddíly jsou plně odděleny. S multiplikačním efektem může využití paměti rychle získat velmi vysoké, pokud se připojíte s referenčními daty vícekrát s více oddíly.  

### <a name="use-of-udf-functions"></a>Použití funkcí UDF
Když přidáte funkci UDF, Azure Stream Analytics načte javascriptový runtime do paměti. To bude mít vliv na SU%.

## <a name="next-steps"></a>Další kroky
* [Vytváření paralelizovatelných dotazů v Azure Stream Analytics](stream-analytics-parallelization.md)
* [Škálování úloh Azure Stream Analytics pro zvýšení propustnosti](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
