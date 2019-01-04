---
title: Principy a úpravy jednotek streamování ve službě Azure Stream Analytics
description: Tento článek popisuje nastavení jednotky streamování a dalších faktorů, které ovlivňují výkon ve službě Azure Stream Analytics.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: 84f0c000f54852bbab60a53ecb686656ac86b3de
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002650"
---
# <a name="understand-and-adjust-streaming-units"></a>Principy a úpravy jednotek streamování

Streamování (su) jednotky představuje výpočetní prostředky, které jsou přiděleny k provedení úlohy. Čím vyšší je počet SU, tím více prostředků CPU a paměti se úloze přidělí. Tato kapacita vám umožní soustředit na logiku dotazu a přehledů nutnosti spravovat hardware pro spuštění vašeho Stream Analytics úlohy včas.

Aby se dosáhlo nízké latence zpracování streamů, provádějí úlohy Stream Analytics veškeré zpracování v paměti. Při spuštění nedostatek paměti, úloha streamování se nezdaří. V důsledku toho pro produkční úlohy, je důležité monitorovat využití prostředků úlohy streamování a ujistěte se, že není dostatek prostředků přidělených zachovat úloh spuštěných 24 hodin denně 7.

Metriky využití SU %, která od 0 % do 100 %, popisuje využití paměti vaší úlohy. Pro úlohy streamování s minimálními nároky na místo tato metrika je obvykle mezi 10 až 20 %. Pokud dochází % využití SU a získat vstupní události v backlogu, úloha pravděpodobně vyžaduje další výpočetní prostředky, které je potřeba zvýšit počet SUs. Doporučujeme zachovat metrika SU nižší než 80 % pro občasné špičky. Společnost Microsoft doporučuje nastavení upozornění na metriku 80 % využití SU zabránit vyčerpání prostředků. Další informace najdete v tématu [kurzu: Nastavení upozornění pro úlohy Azure Stream Analytics](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurace Stream Analytics Streaming jednotek (su)
1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. V seznamu prostředků najdete úlohu Stream Analytics, kterou chcete škálovat a pak ho otevřete. 

3. Na stránce úlohy v části **konfigurovat** záhlaví, vyberte **škálování**. 

    ![Azure portal konfigurace úlohy Stream Analytics][img.stream.analytics.preview.portal.settings.scale]
    
4. Nastavení služby SUs úlohy pomocí posuvníku. Všimněte si, že jste omezeni na konkrétní nastavení SU. 

## <a name="monitor-job-performance"></a>Výkon úlohy monitorování
Pomocí webu Azure portal, můžete sledovat propustnost úlohy:

![Monitorování úloh Azure Stream Analytics][img.stream.analytics.monitor.job]

Vypočítá očekávané propustnosti zatížení. Pokud se propustnost je menší než se očekávalo, vyladit vstupního oddílu, odladění dotazu a přidat su pro vaši úlohu.

## <a name="how-many-sus-are-required-for-a-job"></a>Počtu jednotek su jsou požadovány pro úlohu?

Volba počet požadovaných SUs pro konkrétní úlohy závisí na konfiguraci oddílů pro vstupy a dotaz, který je definován v rámci úlohy. **Škálování** stránka umožňuje nastavit správný počet su. Doporučuje se přidělit další su, než je potřeba. Modul pro zpracování Stream Analytics optimalizuje latenci a propustnost za cenu přidělování další paměti.

Obecně platí, osvědčeným postupem je začít s 6 SUs pro dotazy, které nepoužívají **PARTITION BY**. Pak zjistěte sladkost místě pomocí omyl a metody, ve kterém můžete upravit počet SUs po předání reprezentativního objemu dat a zkontrolovat metriku % využití SU. Maximální počet jednotek streamování, které je možné úlohu Stream Analytics, závisí na počtu kroků v dotazu definovaném pro úlohy a počtu oddílů v každém kroku. Další informace o omezeních [tady](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Další informace o výběru správné číslo su najdete na této stránce: [Škálování služby Stream Analytics pro zvýšení prostupnosti](stream-analytics-scale-jobs.md)

> [!Note]
> Určení počtu jednotek su jsou požadovány pro konkrétní úlohy závisí na konfiguraci oddílů pro vstupy a na dotazu definovaném pro příslušnou úlohu. Můžete vybrat až do vaší kvóty služby SUs pro úlohu. Každé předplatné Azure má ve výchozím nastavení se kvóta až 200 SUs pro všechny úlohy analýzy v určité oblasti. Pro vaše předplatné nad tuto kvótu zvýšit su, obraťte se na [Microsoft Support](https://support.microsoft.com). Platné hodnoty pro služby SUs na úlohu jsou 1, 3, 6 a až v přírůstcích po 6.

## <a name="factors-that-increase-su-utilization"></a>Faktory, které zvyšují % využití SU 

Základní sada operátorů stavová služba Stream Analytics k dispozici jsou prvky dočasných dotazů (orientované na čas). Stream Analytics spravuje stav tyto operace interně jménem uživatele tím, že spravuje spotřebu paměti, vytváření kontrolních bodů pro zajištění odolnosti a obnovení stavu během upgradu služby. I když Stream Analytics plně řídí stavy, existují některé z doporučených osvědčených postupů, které uživatelé měli zvážit.

Mějte na paměti, že úloha s logikou složitého dotazu může mít vysokou % využití SU i v případě, že není přijímání průběžně vstupních událostí. K tomu může dojít po náhlá Špička v vstupní a výstupní události. Úloha může nadále zachovat stav v paměti, pokud je složitý dotaz.

## <a name="stateful-query-logicin-temporal-elements"></a>Stavových dotazů logiky v elementech dočasné
Jeden jedinečné funkce úlohy Azure Stream Analytics je stavové zpracování, jako jsou agregace v okně, dočasné spojení a dočasné analytických funkcí. Každý z těchto operátorů uchovává informace o stavu. Je maximální velikost okna pro tyto elementy dotazu je sedm dní. 

Koncept dočasné okno se zobrazí v několika elementy dotazu Stream Analytics:
1. Agregace v okně: SKUPINY podle z Přeskakujícího, kdy se skok provádí a klouzavé systému windows

2. Dočasná spojení: PŘIPOJENÍ pomocí funkce DATEDIFF

3. Dočasné analytických funkcí: ISFIRST, LAST a PRODLEVA s dobou trvání LIMIT

Paměť použitá ovlivňují následující faktory (součást metrika jednotek streamování) pomocí úloh Stream Analytics:

## <a name="windowed-aggregates"></a>Agregace v okně
Využitá paměť (velikost stavu) pro oddílové agregace není vždy přímo úměrná velikosti okna. Místo toho využitá paměť je přímo úměrná Kardinalita dat nebo počet skupin v každé časový interval.


Například v následujícím dotazu číslo přidružené k `clusterid` je kardinalitou dotazu. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Pokud chcete zmírnit potíže způsobené vysokou kardinalitou v předchozím dotazu, můžete odesílat události do centra událostí, které jsou rozdělené podle `clusterid`a horizontální navýšení kapacity dotazu tím, že systém ke zpracování jednotlivých vstupních oddílů samostatně pomocí **oddílu PODLE** jak je znázorněno v následujícím příkladu:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. V důsledku toho počet `clusterid` hodnoty přicházejících na jednotlivé uzly a tím se zmenší kardinalitou této skupině operátorem. 

Oddílů centra událostí by měly být rozdělené podle klíče seskupení nemuseli snižte kroku. Další informace najdete v tématu [Přehled služby Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Dočasná spojení
Využitá paměť (velikost stavu) dočasná spojení je přímo úměrný počtu událostí v místnosti otáčením dočasných spojení, což je vstupní frekvence událostí vícenásobně podle velikosti otáčením místnosti. Jinými slovy je úměrná časový rozsah DateDiff vynásobené průměrný počet událostí paměti spotřebované ve spojení.

Počet nespárovaných událostí ve spojení vliv na využití paměti v aplikaci pro dotaz. Následující dotaz hledá imprese reklamy, které generují kliknutí:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

V tomto příkladu je možné, že jsou uvedeny zobrazuje spousta reklamy a několik lidí, klikněte na něj a je nutné zachovat všechny události v časovém intervalu. Využitá paměť je přímo úměrná velikosti tohoto okna a frekvenci událostí. 

Pokud to pokud chcete napravit, odesílání událostí do centra událostí rozdělit na oddíly tak, že spojení klíče (id v tomto případě) a horizontální navýšení kapacity dotazu tím, že systém ke zpracování jednotlivých vstupních oddílů samostatně pomocí **PARTITION BY** uvedeno:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. V důsledku počet událostí přicházejících na jednotlivé uzly a tím se zmenší velikost stavu, v okně připojení. 

## <a name="temporal-analytic-functions"></a>Dočasné analytických funkcí
Využitá paměť (velikost stavu) dočasné analytické funkce je přímo úměrný frekvence událostí vícenásobně DURATION. Paměti používané analytických funkcí není přímo úměrná velikosti okna, ale spíše oddílu počet v každé časové okno.

Náprava se podobá dočasná spojení. Můžete horizontálně navýšit dotazu pomocí **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Vyrovnávací paměť mimo pořadí 
Uživatel může konfigurovat velikost vyrovnávací paměti mimo pořadí, v případě, že řazení konfigurační podokno. Vyrovnávací paměť se používá pro uložení vstupy po dobu trvání okna, změnit jejich pořadí. Velikost vyrovnávací paměti je úměrný vstupní frekvence událostí vícenásobně podle velikosti okna mimo pořadí. Výchozí velikost okna je 0. 

Oprava přetečení vyrovnávací paměti pro mimo pořadí, horizontální navýšení kapacity dotazu pomocí **PARTITION BY**. Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. V důsledku toho počet událostí přicházejících na jednotlivé uzly a tím se zmenší počet událostí v každé označovaném jako vyrovnávací paměť. 

## <a name="input-partition-count"></a>Počet vstupních oddílů 
Každý oddíl vstupu úlohy vstup má vyrovnávací paměť. Větší počet vstupních oddílů, další zdroj úloha spotřebovává. Pro každou jednotku streamování Azure Stream Analytics může zpracovat přibližně 1 MB/s vstupu. Proto můžete optimalizovat to provede spárováním odpovídajících počet Stream Analytics streaming jednotek s počtu oddílů v Centru událostí. 

Úlohu nakonfigurovanou jednu jednotku streamování, obvykle stačí pro Centrum událostí s dva oddíly (což je minimum pro centra událostí). Pokud více oddílů centra událostí, vaší úlohy Stream Analytics využívá více prostředků, ale používá nemusí doplňující propustnost k dispozici v Centru událostí. 

Pro úlohu s 6 jednotkami streamování možná bude nutné 4 nebo 8 oddíly z centra událostí. Se však vyhněte příliš mnoho oddílů nepotřebné vzhledem k tomu, který způsobí, že nadměrného využití prostředků. Například centra událostí s 16 oddíly nebo větší úlohu Stream Analytics, která má 1 jednotka streamování. 

## <a name="reference-data"></a>Referenční data 
Referenční data v Azure Stream Analytics jsou načtena do paměti pro rychlé vyhledávání. S aktuální implementace každé operace spojení s referenčními daty udržuje kopie referenčních dat v paměti, i když se do programu se stejnými daty odkaz na více než jednou. Pro dotazy s **PARTITION BY**, každý oddíl má kopie referenčních dat tak, aby byly plně samostatné oddíly. S účinností multiplikátor využití paměti můžete rychle získat velmi vysoké, když se do programu s referenčními daty více než jednou s více oddílů.  

### <a name="use-of-udf-functions"></a>Použití funkcí UDF
Při přidání funkce UDF, Azure Stream Analytics načítá modulu runtime jazyka JavaScript do paměti. To ovlivní SU %.

## <a name="next-steps"></a>Další postup
* [Vytváření paralelizovat dotazů ve službě Azure Stream Analytics](stream-analytics-parallelization.md)
* [Škálování služby Stream Analytics pro zvýšení prostupnosti](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
