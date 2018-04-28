---
title: Rady pro pochopení a upravit jednotek streamování v Azure Stream Analytics
description: Tento článek popisuje nastavení jednotek streamování a dalších faktorů, které mají vliv výkon v Azure Stream Analytics.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: c96e9825cddd0b60e67cd4752fab8f440ceaae76
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="understand-and-adjust-streaming-units"></a>Rady pro pochopení a upravit jednotek streamování

Azure Stream Analytics agreguje výkonu "váhu" běžící úlohy do jednotek streamování (SUs). Služba SUs představují výpočetní prostředky, které jsou využívat k provedení úlohy. Jednotky SU umožňují popsat relativní kapacitu zpracování událostí na základě výkonu procesoru, paměti a rychlosti čtení a zápisu. Tento kapacita vám umožní soustředit na logice dotazu a přehledů potřeba spravovat hardwaru ke spuštění Stream Analytics úlohy v časovém limitu.

Aby bylo možné dosáhnout s nízkou latencí streamování zpracování, provádět úlohy Azure Stream Analytics veškeré zpracování v paměti. Když spustíte nedostatek paměti, úloha streamování se nezdaří. V důsledku toho pro provozní úlohy, je důležité monitorovat využití prostředků úlohu streamování a ujistěte se, není dostatek prostředků přidělené abychom zachovali úloh spuštěných 24 hodin denně 7.

Metrika je procento číslo od 0 % až 100 %. Pro úlohu streamování s minimální nároky metriky využití SU % je obvykle mezi 10 až 20 %. Je nejlepší mít metrika nižší než 80 %, aby se zohlednily příležitostně špičky. Společnost Microsoft doporučuje nastavení upozornění na 80 % využití SU metrika, aby se zabránilo vyčerpání prostředků. Další informace najdete v tématu [kurz: nastavení výstrah pro úlohy Azure Stream Analytics](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurace služby Stream Analytics streamování jednotky (SUs)
1. Přihlaste se k [portálu Azure](http://portal.azure.com/)

2. V seznamu prostředků najděte úlohu služby Stream Analytics, kterou chcete škálovat a potom ho otevřete. 

3. Na stránce úlohy v části **konfigurace** záhlaví, vyberte **škálování**. 

    ![Azure konfigurace portálu úlohy Stream Analytics][img.stream.analytics.preview.portal.settings.scale]
    
4. K nastavení služby SUs úlohy použijte posuvníku. Všimněte si, že jste omezeni na konkrétní nastavení SU. 

## <a name="monitor-job-performance"></a>Monitorování výkonu úlohy
Pomocí portálu Azure, můžete sledovat propustnost úlohy:

![Azure Stream Analytics monitorování úloh][img.stream.analytics.monitor.job]

Vypočítejte očekávané propustnost zatížení. Pokud propustnost je menší než se očekávalo, ladit vstupní oddílu, odladění dotazu a přidejte služby SUs na úlohu.

## <a name="how-many-sus-are-required-for-a-job"></a>Kolik služby SUs jsou potřeba pro úlohu?

Volba číslo požadované služby SUs pro konkrétní úlohy závisí na konfiguraci oddílů pro vstupy a dotaz, který je definován v rámci úlohy. **Škálování** stránce můžete nastavit správné počet služby SUs. Je osvědčeným postupem přidělit další služby SUs, než je potřeba. Modul služby Stream Analytics zpracování optimalizuje latence a propustnosti cenu přidělování další paměť.

Obecně platí, osvědčeným postupem je začínat 6 služby SUs pro dotazy, které nepoužívají **PARTITION BY**. Pak určete místo paprika pomocí metody omyl a ve kterém můžete změnit počet SUs po předání reprezentativní objemy dat a prozkoumat metriky využití SU %.

Další informace o volbě správného počtu služby SUs, zobrazí tato stránka: [úlohy škálování Azure Stream Analytics ke zvýšení propustnosti](stream-analytics-scale-jobs.md)

> [!Note]
> Výběr kolik služby SUs jsou požadovány pro konkrétní úlohy závisí na konfiguraci oddílů pro vstupy a na dotaz definovaný pro úlohu. Můžete vybrat až vaší kvóty v služby SUs pro úlohu. Ve výchozím nastavení má každé předplatné Azure kvótu až 200 služby SUs pro všechny úlohy analýzy v určité oblasti. Chcete-li zvýšit služby SUs pro vaše předplatné nad rámec této kvóty, obraťte se na [Microsoft Support](http://support.microsoft.com). Platné hodnoty pro služby SUs na úlohu jsou 1, 3, 6 a až v přírůstcích po 6.

## <a name="factors-that-increase-su-utilization"></a>Faktory, které se zvyšují % využití SU 

Dočasné (čas orientace) dotazu prvky jsou základní sady stavová operátory poskytované Stream Analytics. Stream Analytics spravuje stav těchto operací interně jménem uživatele, pomocí správy využití paměti, vytváření kontrolních bodů pro zajištění odolnosti a obnovení stavu během upgradu služby. I když Stream Analytics plně spravuje stavy, existuje několik doporučených postupů, které uživatelé měli zvážit.

## <a name="stateful-query-logic-in-temporal-elements"></a>Stavová dotazu logiky v elementech dočasné
Jedním z jedinečné možnosti úlohy Azure Stream Analytics je provést stavová zpracování, např. agregací v časových oknech, dočasných spojení a dočasné analytické funkce. Každý z těchto operátorů udržuje informace o stavu. Je maximální velikost okna pro tyto elementy dotazu je sedm dní. 

Koncept dočasné okno se zobrazí v několika elementy dotaz služby Stream Analytics:
1. Oddílové agregace: skupiny podle z Přeskakujícího, posílání a klouzavé windows

2. Dočasné spojení: připojení pomocí funkce DATEDIFF

3. Dočasné analytické funkce: ISFIRST, poslední a PRODLEVA s LIMIT DURATION

Tyto faktory ovlivňují paměť použitá (část streamování jednotky metrika) pomocí úlohy Stream Analytics:

## <a name="windowed-aggregates"></a>Oddílové agregace
Paměť spotřebované (velikost stavu) pro oddílové agregace není vždy přímo úměrná velikosti okna. Využitá paměť místo toho je úměrná Kardinalita data nebo počet skupin v každé časové okno.


Například v následujícím dotazu číslo přiřazené `clusterid` je Kardinalita dotazu. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Chcete-li ameliorate problémů způsobených vysokou kardinalitou v předchozí dotaz, může odesílat události do centra událostí rozdělena na oddíly pomocí `clusterid`a tím, že systém pro každý vstupní oddíl samostatně pomocí zpracovat dotaz s více instancemi **oddílu PODLE** jak je znázorněno v následujícím příkladu:

   ```sql
   SELECT count(*) 
   FROM PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. V důsledku toho počet `clusterid` hodnoty, než dorazí do každého uzlu se snižuje a snižují se mohutnost skupiny operátorem. 

Oddíly centra událostí by měl pomocí klíče seskupení, abyste nemuseli pro fází reduce rozdělit na oddíly. Další informace najdete v tématu [Přehled služby Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Dočasné spojení
Paměť spotřebované (velikost stavu) dočasných spojení je úměrná počtu událostí v místnosti dočasné rychlý pohyb spojení, která je vstupní rychlost událostí násobkem velikostí rychlý pohyb místnosti. Jinými slovy paměť spotřebovávají spojení je úměrná časové rozmezí DateDiff násobí hodnotou průměrný počet událostí.

Využití paměti pro dotaz ovlivnit počet neodpovídající události ve spojení. Následující dotaz hledá imprese reklamy, které generují kliknutí:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

V tomto příkladu je možné, že se zobrazují spoustu reklamy a několik lidí, klikněte na možnost na něm a je potřeba zachovat všechny události v časovém intervalu. Využitá paměť je přímo úměrná velikosti tohoto okna a frekvenci událostí. 

Toto řešení, odesílání událostí do centra událostí rozdělena na oddíly tak, že spojení klíče (id v tomto případě) a škálování dotaz tím, že systém pro každý vstupní oddíl samostatně pomocí zpracování **PARTITION BY** znázorněné:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. Výsledkem je snížen počet události pocházející do každého uzlu, a snižují se velikost stavu uchovává se v okně připojení. 

## <a name="temporal-analytic-functions"></a>Dočasné analytické funkce
Paměť spotřebované (velikost stavu) dočasné analytické funkce je úměrná rychlost těchto událostí násobkem DURATION. Paměťové nároky analytické funkce není přímo úměrná velikosti okna, ale spíš oddílu počet v každé časové okno.

Oprava je podobná dočasné připojení. Je možné škálovat pomocí dotazu **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Mimo pořadí vyrovnávací paměti 
Uživatel může konfigurovat velikost vyrovnávací paměti mimo pořadí, v případě, že řazení podokně Konfigurace. Vyrovnávací paměti se používá pro uložení vstupy pro dobu trvání okna, změnit jejich pořadí. Velikost vyrovnávací paměti je úměrná vstupní rychlost těchto událostí násobkem velikostí okno mimo pořadí. Výchozí velikost okna je 0. 

K nápravě přetečení mimo pořadí vyrovnávací paměti, škálovat pomocí dotazu **PARTITION BY**. Jakmile je dotaz rozdělený, rozprostře se mezi několik uzlů. V důsledku toho se snižuje počet událostí, než dorazí do každého uzlu, a snižují počet událostí ve vyrovnávací paměti jednotlivých změnit pořadí. 

## <a name="input-partition-count"></a>Počet vstupních oddílů 
Každý oddíl vstupní vstupu úlohy má vyrovnávací paměť. Větší počet vstupních oddílů, více prostředků spotřebuje úlohy. Pro každou jednotku streamování Azure Stream Analytics může zpracovat přibližně 1 MB/s vstupu. Proto můžete optimalizovat odpovídající počet Stream Analytics streamování jednotky s počet oddílů v Centru událostí. 

Úlohu nakonfigurované jednu jednotku streamování obvykle stačí pro centra událostí s dva oddíly (což je minimální pro Centrum událostí). Pokud centra událostí má více oddíly, vaše úlohy služby Stream Analytics využívá více prostředků, ale používá nemusí navíc propustnost poskytované centra událostí. 

Pro úlohu s 6 jednotek streamování může být nutné 4 nebo 8 oddíly z centra událostí. Vzhledem k tomu, které způsobí, že využití prostředků nadměrné-li však příliš mnoho nepotřebné oddíly. Například centra událostí s 16 oddíly nebo větší v úloze Stream Analytics, který má 1 jednotka streamování. 

## <a name="reference-data"></a>Referenční data 
Referenční data v ASA jsou načtena do paměti pro rychlé vyhledávání. S aktuální implementace každé operace spojení u referenčních dat zachová kopii referenční data v paměti, i v případě, že připojení pomocí stejné referenční data vícekrát. Pro dotazy s **PARTITION BY**, každý oddíl obsahuje kopii referenční data, takže jsou plně odpojeného oddíly. S násobitel platit využití paměti můžete rychle získat velmi vysoké, pokud připojíte u referenčních dat vícekrát s více oddílů.  

### <a name="use-of-udf-functions"></a>Použití funkce UDF
Když přidáte funkce UDF, načte Azure Stream Analytics prostředí JavaScript runtime do paměti. Tato akce ovlivní SU %.

## <a name="next-steps"></a>Další postup
* [Vytvoření paralelní dotazy v Azure Stream Analytics](stream-analytics-parallelization.md)
* [Škálování služby Stream Analytics ke zvýšení propustnosti](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
