---
title: Rozšiřování a vyzvětání v pracovních úlohách Azure Stream Analytics
description: Tento článek popisuje, jak škálovat úlohu Stream Analytics rozdělením vstupních dat, laděním dotazu a nastavením jednotek streamování úloh.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: d828103bef8e57f5d0cdfe6c243c52e2d0526663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257542"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Škálování úlohy Azure Stream Analytics za účelem zvýšení propustnosti
V tomto článku se ukazuje, jak vyladit dotaz Stream Analytics, abyste zvýšili propustnost pro úlohy Streaming Analytics. Pomocí následujícípříručky můžete škálovat úlohu tak, aby zvládla vyšší zatížení a využila více systémových prostředků (například větší šířku pásma, více prostředků procesoru, více paměti).
Jako předpoklad možná budete muset přečíst následující články:
-   [Principy a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md)
-   [Vytváření paralelizovatelných úloh](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Případ 1 – dotaz je ze své podstaty plně paralelizovat napříč vstupními oddíly
Pokud je dotaz ze své podstaty plně paralelizovatelný mezi vstupními oddíly, můžete postupovat podle následujících kroků:
1.  Author váš dotaz být trapně paralelní pomocí **PARTITION BY** klíčové slovo. Další podrobnosti naleznete v části Trapně paralelní úlohy [na této stránce](stream-analytics-parallelization.md).
2.  V závislosti na výstupní typy použité v dotazu některé výstupy může být buď nelze paralelizovat, nebo je třeba další konfiguraci, které mají být trapně paralelní. Například výstup PowerBI není paralelizovatelný. Výstupy jsou vždy sloučeny před odesláním do jímky výstupu. Objekty BLOB, tabulky, ADLS, Service Bus a Azure Function jsou automaticky paralelizovány. Výstupy SQL a SQL DW mají možnost paralelizace. Centrum událostí musí mít konfiguraci PartitionKey nastavenou tak, aby **odpovídala** poli PARTITION BY (obvykle PartitionId). Pro Event Hub také věnovat zvláštní pozornost, aby odpovídaly počet oddílů pro všechny vstupy a všechny výstupy, aby se zabránilo křížení mezi oddíly. 
3.  Spusťte dotaz s **6 SU** (což je plná kapacita jednoho výpočetního uzlu) pro měření maximální dosažitelné propustnosti a pokud používáte **GROUP BY**, změřte, kolik skupin (mohutnost) úloha zvládne. Obecné příznaky úlohy, které dosáhly omezení systémových prostředků, jsou následující.
    - Metrika využití SU % je více než 80 %. To znamená, že využití paměti je vysoká. Faktory, které přispívají ke zvýšení této metriky, jsou popsány [zde](stream-analytics-streaming-unit-consumption.md). 
    -   Výstupní časové razítko zaostává s ohledem na čas nástěnných hodin. V závislosti na logice dotazu může mít časové razítko výstupu odsazení logiky od času nástěnných hodin. Měly by však postupovat zhruba stejným tempem. Pokud časové razítko výstupu klesá dále a dále za sebou, je to indikátor, že systém je přepracování. Může být výsledkem omezení jímky výstupu příjem dat nebo vysoké využití procesoru. V tuto chvíli neposkytujeme metriku využití procesoru, takže může být obtížné je rozlišit.
        - Pokud je problém kvůli omezení jímky, budete muset zvýšit počet výstupních oddílů (a také vstupní oddíly zachovat úlohu plně paralelizovat), nebo zvýšit množství prostředků jímky (například počet jednotek požadavku pro CosmosDB).
    - V diagramu úloh je metrika událostí nevyřízených položek za oddíl pro každý vstup. Pokud se metrika událostí nevyřízených položek neustále zvyšuje, je také indikátorem, že systémový prostředek je omezen (buď z důvodu omezení výstupního jímky, nebo vysokého procesoru).
4.  Jakmile jste určili limity toho, co může dosáhnout úloha 6 SU, můžete lineárně extrapolovat kapacitu zpracování úlohy při přidávání dalších su, za předpokladu, že nemáte žádné zkosení dat, které činí určitý oddíl "horkým".

> [!NOTE]
> Zvolte správný počet jednotek streamování: Vzhledem k tomu, že Stream Analytics vytvoří výpočetní uzel pro každých 6 SU přidáno, je nejlepší, aby počet uzlů dělitel počtu vstupních oddílů, takže oddíly mohou být rovnoměrně rozloženy mezi uzly.
> Například jste naměřili 6 SU úlohy můžete dosáhnout 4 MB / s rychlost zpracování a počet vstupních oddílů je 4. Můžete si vybrat spuštění úlohy s 12 SU k dosažení zhruba 8 MB / s rychlost zpracování, nebo 24 SU k dosažení 16 MB / s. Poté se můžete rozhodnout, kdy zvýšit číslo SU pro úlohu na jakou hodnotu, jako funkci vstupní rychlosti.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Případ 2 - Pokud váš dotaz není trapně paralelní.
Pokud váš dotaz není trapně paralelní, můžete postupovat podle následujících kroků.
1.  Začněte s dotazem bez **ODDÍLU BY** nejprve, aby se zabránilo rozdělení složitosti a spusťte dotaz s 6 SU pro měření maximální zatížení jako v [případě 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Pokud můžete dosáhnout očekávanézatížení z hlediska propustnost, jste hotovi. Případně se můžete rozhodnout měřit stejnou úlohu spuštěnou na 3 SU a 1 SU, abyste zjistili minimální počet SU, který funguje pro váš scénář.
3.  Pokud nemůžete dosáhnout požadované propustnosti, pokuste se rozdělit dotaz na několik kroků, pokud je to možné, pokud již nemá více kroků, a přidělit až 6 SU pro každý krok v dotazu. Například pokud máte 3 kroky, přidělit 18 SU v "Měřítko" možnost.
4.  Při spuštění takové úlohy, Stream Analytics umístí každý krok na svůj vlastní uzel s vyhrazenými prostředky 6 SU. 
5.  Pokud jste stále nedosáhli cíle zatížení, můžete se pokusit použít **PARTITION BY** počínaje kroky blíže k vstupu. Pro **group by** operátor, který nemusí být přirozeně rozdělitelný, můžete použít místní/globální agregační vzor k provedení **rozdělené GROUP BY** následuje non-partitioned GROUP **BY**. Například, pokud chcete spočítat, kolik aut prochází každou mýtnou budku každé 3 minuty, a objem dat je nad rámec toho, co může být zpracováno 6 SU.

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
Ve výše uvedeném dotazu počítáte vozy na mýtnou budku na oddíl a poté přidáte počet ze všech oddílů dohromady.

Po rozdělení na oddíl, pro každý oddíl kroku, přidělit až 6 SU, každý oddíl s 6 SU je maximum, takže každý oddíl může být umístěn na vlastní uzel zpracování.

> [!Note]
> Pokud dotaz nelze rozdělit, přidání další SU v dotazu s více kroky nemusí vždy zlepšit propustnost. Jedním ze způsobů, jak zvýšit výkon, je snížit objem počátečních kroků pomocí místního/globálního agregačního vzoru, jak je popsáno výše v kroku 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Případ 3 - V úloze je spuštěna spousta nezávislých dotazů.
Pro některé případy použití nezávislých nezávislých odběratelů, kde je nákladově efektivnější zpracování dat z více klientů v jedné úloze, pomocí samostatných vstupů a výstupů pro každého klienta, můžete skončit spuštěním poměrně málo (například 20) nezávislé dotazy v jedné úloze. Předpoklad je každý takový poddotaz zatížení je relativně malý. V takovém případě můžete postupovat podle následujících kroků.
1.  V takovém případě nepoužívejte **partition BY** v dotazu
2.  Snižte počet vstupních oddílů na nejnižší možnou hodnotu 2, pokud používáte Centrum událostí.
3.  Spusťte dotaz s 6 SU. S očekávaným zatížením pro každý poddotaz přidejte co nejvíce takových poddotazů, dokud úloha nenarazí na limity systémových prostředků. Podívejte se na [případ 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) příznaky, když k tomu dojde.
4.  Jakmile najdete výše uvedený limit poddotazu, začněte přidávat poddotaz do nové úlohy. Počet úloh spustit jako funkce počtu nezávislých dotazů by měla být poměrně lineární, za předpokladu, že nemáte žádné zatížení zkosení. Potom můžete předpovědět, kolik úloh 6 SU je třeba spustit jako funkci počtu klientů, které chcete obsluhovat.
5.  Při použití referenční data spojit s těmito dotazy, sjednocení vstupy dohromady před spojením se stejnými referenčními daty. Potom rozdělit události v případě potřeby. V opačném případě každé spojení referenčních dat uchovává kopii referenčních dat v paměti, pravděpodobně zbytečně vyhodí využití paměti do povětří.

> [!Note] 
> Kolik nájemníků dát do každé úlohy?
> Tento vzor dotazu má často velký počet poddotazů a výsledkem je velmi rozsáhlá a složitá topologie. Řadič úlohy nemusí být schopen zpracovat tak velkou topologii. Jako pravidlo, pobyt pod 40 nájemníků pro 1 SU práci, a 60 nájemníků pro 3 SU a 6 SU pracovních míst. Pokud překračujete kapacitu řadiče, úloha se nespustí úspěšně.



## <a name="get-help"></a>Podpora
Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

