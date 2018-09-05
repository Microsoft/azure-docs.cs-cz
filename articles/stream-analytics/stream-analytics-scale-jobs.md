---
title: Škálování i horizontální navyšování kapacity v úlohách Azure Stream Analytics
description: Tento článek popisuje, jak můžete škálovat úlohy Stream Analytics dělení vstupních dat, optimalizaci dotazu a nastavení úlohu jednotek streamování.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: 4da97d708f8db2dcee406645a0eee409fa111012
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696798"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Škálovat úlohy Azure Stream Analytics pro zvýšení prostupnosti
Tento článek ukazuje, jak ladit dotazu Stream Analytics pro zvýšení propustnosti pro úlohy Stream Analytics. Následující příručky můžete použít ke škálování úlohy zpracování větší zátěže a využijte výhod více systémových prostředků (například větší šířku pásma, další prostředky procesoru, větší množství paměti).
Předpokladem je je nutné v následujících článcích:
-   [Principy a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md)
-   [Vytváření paralelizovat úloh](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Případ 1 – dotazu je ze své podstaty plně paralelizovat napříč oddíly vstupu
Pokud napříč oddíly vstupu je ze své podstaty plně paralelizovat dotaz, můžete postupovat podle následujících kroků:
1.  Upravit dotaz tak, aby jednoduše paralelně zpracovatelné pomocí **PARTITION BY** – klíčové slovo. Další podrobnosti najdete v části úlohy jednoduše paralelně zpracovatelné [na této stránce](stream-analytics-parallelization.md).
2.  V závislosti na výstupní typy použité v dotazu, některé výstupní buď možná není paralelizovat, nebo potřebujete další konfiguraci, aby se jednoduše paralelně zpracovatelné. Například nejsou paralelizovat výstupy SQL, SQL data Warehouse a Power BI. Výstupy jsou vždy sloučeny před odesláním do výstupní jímky. Objekty BLOB, tabulek, ADLS, Service Bus a funkce Azure Functions jsou automaticky paralelizována. Cosmos DB a Centrum událostí musí mít PartitionKey konfigurace nastavená tak, aby odpovídaly s **PARTITION BY** pole (obvykle PartitionId). Pro Centrum událostí také věnujte zvláštní pozornost odpovídat počtu oddílů pro všechny vstupy a všechny výstupy, aby se zabránilo selhání mezi mezi oddíly. 
3.  Spusťte dotaz s **6 SU** (což je kapacita jednu výpočetní uzel) k měření maximální možná propustnost, a pokud používáte **Group**, měření, kolik skupin (Kardinalita) úlohy může Obslužná rutina. Obecné příznaky úlohy dosažení omezení prostředků systému jsou uvedeny níže.
    - Metriky využití SU je více než 80 %. To znamená, že je vysoké využití paměti. Faktory přispívající k navýšení této metriky jsou popsány [tady](stream-analytics-streaming-unit-consumption.md). 
    -   Výstup časového razítka se opožďuje s ohledem na skutečný čas. V závislosti na logice dotazu může mít časové razítko výstupního logiky posun od skutečný čas. Musí však průběh přibližně stejnou rychlostí. Výstup časového razítka se opožďuje a pozadí, je indikátor, který overworking systému. Může být výsledkem podřízené výstupní jímky omezení nebo vysoké využití procesoru. Neposkytujeme metriky využití procesoru v tuto chvíli, takže může být obtížné k rozlišení dvou.
        - Pokud je problém způsobený jímky omezování, budete muset zvýšit počet oddílů výstupu (a také vstupní oddílů, aby úloha zachovat plně paralelizovat), nebo zvýšit množství prostředků jímky (například počet jednotek žádostí pro službu cosmos DB).
    - V diagramu úloh se na oddíl nevyřízených položek událostí metriku pro každý vstupní. Pokud metrika nevyřízených položek událostí narůstá, je také indikátor, že systémový prostředek je omezená (buď z důvodu omezení výstupní jímky nebo vysoké využití procesoru).
4.  Po určení omezení 6 SU úlohy, které je dosáhnout, je lze potom údaje extrapolovat lineárně kapacitu zpracování u úlohy přidávat další služby SUs, za předpokladu, že nemáte žádné nerovnoměrnou distribucí dat., které provádí některé oddíl "horkými".

> [!NOTE]
> Zvolte správný počet jednotek streamování: protože Stream Analytics vytvoří uzel zpracování pro přidání jednotlivých 6 SU, je nejlepší počet uzlů dělitel počtu vstupních oddílů, aby oddíly můžete rovnoměrně rozdělené mezi uzly.
> Například máte měří vaše 6 SU úloh lze dosáhnout 4 MB/s a spočítat vstupního oddílu a rychlost zpracování je 4. Je možné spustit úlohu s 12 SU k dosažení zpracovaných přibližně 8 MB/s nebo 24 SU k dosažení 16 MB/s. Potom se můžete rozhodnout, kdy se má zvýšit číslo SU pro úlohu na jakou hodnotu jako funkce vstupní frekvence.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Případ 2 - Pokud váš dotaz není jednoduše paralelně zpracovatelné.
Pokud váš dotaz není jednoduše paralelně zpracovatelné, provedením následujících kroků.
1.  Začněte s dotazu bez **PARTITION BY** nejprve k vyhnout rozdělení do oddílů složitost a spusťte dotaz s 6 SU k měření maximálního zatížení, stejně jako v [případ 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Pokud v období propustnosti můžete dosáhnout očekávané zatížení, budete hotovi. Alternativně můžete měřit stejná úloha spuštěná na 3 SU a 1 SU, přečtěte si minimální počet SU, který se dá použít pro váš scénář.
3.  Pokud nelze dosáhnout požadovaného propustnost, zkuste dotaz přerušit několik kroků, pokud je to možné, pokud není již je několik kroků a přidělení SU až 6 pro každý krok v dotazu. Například pokud máte kroky 3, přidělit 18 SU v možnosti "Škálování".
4.  Při spuštění takovou úlohu Stream Analytics vloží každý krok na vlastní uzel se vyhrazených prostředcích 6 SU. 
5.  Pokud stále ještě dosáhne cílové zatížení, může pokusit použít **PARTITION BY** od kroky blíže na vstup. Pro **Group** operátor, který nemusí být přirozeně rozdělený, můžete použít místní nebo globální agregační vzor provádět dělené **Group** za nímž následuje bez oddílů **GROUP BY** . Například pokud budete chtít zjistit počet aut probíhá každý z mýtných bran linka každé 3 minuty a objemu dat je nad rámec co půjde vyřešit 6 SU.

Dotaz:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Ve výše uvedený dotaz jsou počítání automobilů na stánku linka na oddíl a přidání ze všech oddílů počet společně.

Jakmile oddíly pro každý oddíl v kroku přidělit až 6 SU, každý oddíl s 6 SU je maximum, takže každý oddíl může být umístěn v jeho vlastní zpracování uzlu.

> [!Note]
> Pokud váš dotaz nelze rozdělit na oddíly, přidání dalších SU v dotazu s více kroky nemusí vždy zlepšit propustnost. Jedním způsobem, jak získat výkonu je snížit na první kroky, pomocí místní nebo globální agregační vzoru, jak je popsáno výše v kroku 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Případ 3 - používáte velké množství nezávislé dotazy v rámci úlohy.
Pro případy, kde je cenově výhodnější ke zpracování dat z více tenantů v jedné úlohy, použijte určité nezávislých výrobců softwaru pomocí samostatných vstupy a výstupy pro jednotlivé tenanty, můžete uvíznout systémem poměrně pár (třeba 20) nezávislé dotazy v rámci jedné úlohy. Předpokladem je, že každý takový poddotaz zatížení je poměrně málo početnému. V takovém případě postupujte podle následujících kroků.
1.  V tomto případě nepoužívejte **PARTITION BY** v dotazu
2.  Pokud používáte Event Hub, snížení počtu vstupního oddílu na nejnižší možná hodnota 2.
3.  Spusťte dotaz s 6 SU. Očekávané zatížení pro každý poddotaz přidáte libovolný počet takových poddotazy nejvíce, dokud úloha dosahuje limity prostředků systému. Odkazovat na [případ 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) příznaků, pokud k tomu dojde.
4.  Jakmile dosahujete poddotaz hranicí výše, začněte přidávat poddotazu na nové úlohy. Počet úloh ke spuštění jako funkce počtu nezávislých dotazů, které by měl být poměrně lineární, za předpokladu, že nemáte žádné zatížení zkosení. Potom můžete prognózy kolik 6 SU úlohy budete muset spustit jako funkce počtu klientů, které chcete pro obsluhu.
5.  Při použití metody join referenční data pomocí těchto dotazů, union vstupy dohromady před spojením s stejný referenční data. Pak oddělit události v případě potřeby. Jinak každý referenční data spojení udržuje kopie referenčních dat v paměti, pravděpodobně pěnicí si využití paměti zbytečně.

> [!Note] 
> Kolik klientů pro umístění v jednotlivých úlohách?
> Tento vzor dotazu často má velký počet poddotazy a má za následek velmi velké a složité topologie. Kontroler úlohy nemusí být schopna zpracovávat velké topologie. Jako říci zůstanou v části 40 tenantů pro úlohu 1 SU a 60 tenantů 3 SU a 6 úlohy SU. Když jsou překročení kapacity řadiče, nebude úspěšně spuštění úlohy.



## <a name="get-help"></a>Podpora
Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

