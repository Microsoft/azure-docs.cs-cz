---
title: Horizontální navýšení a zmenšení kapacity Azure Stream Analytics úloh
description: Tento článek popisuje, jak škálovat Stream Analytics úlohy rozdělením vstupních dat, vyladěním dotazu a nastavením jednotek streamování úloh.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: e3d4fd6b6b83681284278d10409a1c16394db31f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018679"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Škálování Azure Stream Analytics úlohy za účelem zvýšení propustnosti
V tomto článku se dozvíte, jak vyladit Stream Analytics dotaz, abyste zvýšili propustnost pro úlohy Stream Analytics. Následující průvodce vám umožní škálovat úlohy tak, aby zpracovávala větší zátěž a využila více systémových prostředků (například větší šířku pásma, více prostředků procesoru, více paměti).
Je možné, že budete potřebovat přečíst si následující články:
-   [Principy a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md)
-   [Vytváření paralelizovat úloh](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Případ 1 – váš dotaz je ze své podstaty plně paralelizovat napříč vstupními oddíly
Pokud je váš dotaz ze své podstaty plně paralelizovat napříč vstupními oddíly, můžete postupovat podle následujících kroků:
1.  Vytvořte dotaz tak, aby se zpracovatelné paralelně pomocí klíčového slova **partition by** . Další podrobnosti najdete v části zpracovatelné Parallel Jobs [na této stránce](stream-analytics-parallelization.md).
2.  V závislosti na typech výstupu použitých v dotazu nemusí být některé výstupy buď paralelizovat, nebo musí být další konfigurace zpracovatelné paralelně. Výstup PowerBI není například paralelizovat. Výstupy se vždycky sloučí před odesláním do výstupní jímky. Objekty blob, tabulky, ADLS, Service Bus a Azure Functions jsou automaticky paralelismud. Výstupy SQL a Azure synapse Analytics mají možnost paralelního zpracování. V centru událostí musí být konfigurace PartitionKey shodná s polem **partition by** (obvykle PartitionID). V centru událostí taky věnujte mimořádnou pozornost, která bude odpovídat počtu oddílů pro všechny vstupy a výstupy, aby nedocházelo k přecházení mezi oddíly. 
3.  Spusťte dotaz s **6 Su** (což je plná kapacita jediného výpočetního uzlu), abyste měřili maximální dosažitelnou propustnost, a pokud používáte **Group by**, změřte si, kolik skupin (mohutnosti) může úloha zpracovat. V tomto případě jsou k dishlavnímu příznaků omezení systémových prostředků v úloze
    - Metrika využití SU% má více než 80%. To značí, že využití paměti je vysoké. Faktory přispívající k navýšení této metriky jsou popsány [zde](stream-analytics-streaming-unit-consumption.md). 
    -   Na výstupní časové razítko se zachází s ohledem na čas v chodu na zdi. V závislosti na vaší logice dotazu může mít výstupní časové razítko posunutí logiky od času chodu na zdi. Nicméně by měli postupovat přibližně na stejnou sazbu. Pokud je výstupní časové razítko ještě dál a ještě dál, je indikátorem, že systém je přepracovaná. Může to být výsledkem omezení pro výstup z výstupní jímky nebo vysokého využití procesoru. V tuto chvíli neposkytujeme metriku využití procesoru, takže to může být obtížné odlišit tyto dvě.
        - Pokud k problému dochází z důvodu omezování jímky, možná budete muset zvýšit počet výstupních oddílů (a také vstupní oddíly, aby úloha zůstala plně paralelizovat), nebo zvýšit množství prostředků jímky (například počet jednotek žádostí pro CosmosDB).
    - V diagramu úloh se pro každý vstup nachází metrika události na jednotlivých oddílech. Pokud se metrika události nevyřízených položek stále zvyšuje, je také indikátorem, že systémový prostředek je omezený (z důvodu omezení výstupní jímky nebo vysokého procesoru).
4.  Jakmile určíte omezení, k čemu má úloha 6. SU přístup, můžete lineárně odvodit kapacitu zpracování úlohy při přidávání větší služby SUs za předpokladu, že nemáte žádná zešikmení dat, která vytváří určitý oddíl "Hot".

> [!NOTE]
> Volba správného počtu jednotek streamování: vzhledem k tomu, že Stream Analytics vytvoří uzel zpracování pro každý 6 přidaných, je nejlepší nastavit počet uzlů na dělitele počtu vstupních oddílů, aby se oddíly rovnoměrně rozdělují mezi uzly.
> Například jste si vyhodnotí, že úloha 6. SU může dosáhnout velikosti 4 MB/s a počet vstupních oddílů je 4. Můžete zvolit, že se má úloha spustit s 12 SU, aby se dosáhlo přibližně 8 MB/s rychlost zpracování, nebo 24 SU pro dosažení 16 MB/s. Pak se můžete rozhodnout, kdy se má u úlohy zvýšit počet SU, na jakou se má vyhodnotit, jako funkce vaší vstupní sazby.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Případ 2 – Pokud Váš dotaz není zpracovatelné paralelně.
Pokud se dotaz nezpracovatelné paralelně, můžete postupovat podle následujících kroků.
1.  Začněte s dotazem bez **oddílu** a zabraňte tak složitosti oddílů a spusťte dotaz s 6 su pro měření maximálního zatížení v [případě 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Pokud můžete dosáhnout očekávaného zatížení v době propustnosti, jste hotovi. Případně se můžete rozhodnout, že chcete změřit stejnou úlohu spuštěnou se 3. SU a 1 SU, abyste zjistili minimální počet SU, který pro váš scénář funguje.
3.  Pokud nemůžete dosáhnout požadované propustnosti, zkuste dotaz přerušit na více kroků, pokud to je možné, pokud již nemá více kroků, a pro každý krok dotazu přidělte až 6 SU. Pokud máte například 3 kroky, přidělte v možnosti škálování 18 SU.
4.  Při spuštění takové úlohy Stream Analytics vloží každý krok do vlastního uzlu s vyhrazenými prostředky 6 SU. 
5.  Pokud jste ještě nedosáhli cíle zatížení, můžete se pokusit použít **oddíl** , a to tak, že začnete od kroků blíž ke vstupu. Pro operátor **Group by** , který nemusí být přirozeně rozdělený, můžete použít místní a globální agregovaný vzor k provedení dělené **skupiny** následovaný nedělenou **skupinou by**. Například pokud chcete spočítat, kolik automobilů prochází každým telefonním prostorem každé 3 minuty, a objem dat je nad rámec toho, co může být zpracováno 6. SU.

Dotaz:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
V dotazu výše počítáte automobily za telefonní počet na oddíl a potom tento počet přidáte ze všech oddílů dohromady.

Po dělení pro každý oddíl kroku přidělte až 6 SU, každý oddíl s 6 SU je maximální, takže každý oddíl lze umístit do svého vlastního zpracovatelského uzlu.

> [!Note]
> Pokud dotaz nelze rozdělit do oddílů, přidání dalších SU v dotazu s více kroky nemusí vždy vylepšit propustnost. Jedním ze způsobů, jak získat výkon, je snížit objem v počátečních krocích pomocí místních/globálních agregačních vzorů, jak je popsáno výše v kroku 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Případ 3: v úloze běží spousta nezávislých dotazů.
V některých případech použití nezávislého výrobce softwaru, kde je cenově výhodnější zpracovávat data z více tenantů v rámci jedné úlohy, a to s využitím samostatných vstupů a výstupů pro každého tenanta, může běžet poměrně pár (například 20) nezávislých dotazů v jedné úloze. Předpokladem je, že každý takový zátěž poddotazu je poměrně malý. V takovém případě můžete postupovat podle následujících kroků.
1.  V takovém případě nepoužívejte v dotazu **partition by** .
2.  Pokud používáte centrum událostí, snižte počet vstupních oddílů na nejnižší možnou hodnotu 2.
3.  Spusťte dotaz s 6 SU. S očekávaným zatížením pro každý poddotaz přidejte tolik poddotazů, kolik je možné, až do doby, kdy úloha zasáhne omezení systémových prostředků. Pokud k tomu dojde, přečtěte si téma [případ 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) .
4.  Po dosažení výše uvedeného limitu poddotazu začněte přidávat poddotaz do nové úlohy. Počet úloh, které se mají spustit jako funkce počtu nezávislých dotazů, by měl být poměrně lineární a za předpokladu, že nemáte žádné zešikmení zatížení. Pak můžete odhadnout, kolik úloh 6. SU potřebujete spustit jako funkci počtu klientů, které chcete zajišťovat.
5.  Při použití referenčních dat spojených s takovými dotazy sjednotte vstupy před spojením se stejnými referenčními daty. Pak v případě potřeby rozdělte události. V opačném případě se každý odkaz na referenční data uchovává jako kopie referenčních dat v paměti, což nejspíš zbytečně vystavuje využití paměti.

> [!Note] 
> Kolik klientů se má umístit do každé úlohy?
> Tento vzor dotazu často obsahuje velký počet poddotazů a výsledkem je velmi velká a složitá topologie. Kontroler úlohy nemusí být schopný zvládnout takovou velkou topologii. Jako pravidlo pro palec můžete zůstat pod 40 klienty pro 1 úlohu SU a pro klienty 60 pro 3 SU a 6 SU. Pokud překračujete kapacitu řadiče, úloha se nespustí úspěšně.



## <a name="get-help"></a>Získání pomoci
Pokud chcete získat další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/