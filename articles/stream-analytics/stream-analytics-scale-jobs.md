---
title: Škálování úlohy Stream Analytics, pokud chcete zvýšit propustnost | Microsoft Docs
description: Postup konfigurace vstupní oddíly, ladění definice dotazu a nastavení úlohu streamování jednotky škálování úlohy Stream Analytics.
keywords: data streamování, streamování zpracování dat, optimalizovat analytics
services: stream-analytics
documentationcenter: ''
author: JSeb225
manager: ryanw
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: jeanb
ms.openlocfilehash: 2e0487a9e4cd6346312c6817ef2768556cba72ba
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="scale-azure-stream-analytics-jobs-to-increase--throughput"></a>Škálování služby Stream Analytics ke zvýšení propustnosti
Tento článek ukazuje, jak ladit dotaz služby Stream Analytics chcete zvýšit propustnost pro úlohy streamování Analytics. V následujícím průvodci můžete škálovat vaše úloha zpracování větší zátěže a využít výhod více systémových prostředků (například větší šířku pásma, další prostředky procesoru, paměť).
Předpokladem je budete muset přečíst v následujících článcích:
-   [Principy a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md)
-   [Vytvořte paralelní úlohy](stream-analytics-parallelization.md)


## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Případ 1 – dotazu je ze své podstaty plně může běžet paralelně napříč vstupní oddíly
Pokud váš dotaz je ze své podstaty plně může běžet paralelně přes vstupní oddíly, můžete provést následující kroky:
1.  Vytváření dotazu na paralelně zpracovatelné pomocí **PARTITION BY** – klíčové slovo. Více informací najdete v části Trapně paralelní úlohy [na této stránce](stream-analytics-parallelization.md).
2.  V závislosti na typech výstup použité v dotazu, některé výstup, může být buď není může běžet paralelně, nebo potřebujete další konfigurace, aby byla paralelně zpracovatelné. Například SQL, SQL DW a PowerBI výstupů nejsou může běžet paralelně. Výstupy jsou vždy sloučit před odesláním do výstupní jímku. Objekty BLOB, tabulek, ADLS, Service Bus a funkce Azure jsou automaticky paralelizovaná málo. CosmosDB a Centrum událostí musí mít nastavená tak, aby odpovídaly s konfigurace PartitionKey **PARTITION BY** pole (obvykle PartitionId). Centra událostí taky věnujte zvláštní pozornost tak, aby odpovídaly počet oddílů pro všechny vstupy a výstupy všechny předejdete mezi převzetí mezi oddílů. 
3.  Spusťte dotaz s **6 SU** (což je úplná kapacitu jednom výpočetním uzlu) k měření maximální možná propustnost, a pokud používáte **GROUP BY**, měřit, kolik skupin (mohutnost) úlohy může Obslužná rutina. Obecné příznaky úlohy nedosáhli limitů prostředků systému jsou následující.
    - Metrika využití SU % je více než 80 %. To znamená, že je vysoké využití paměti. Faktory, které přispívají k zvýšení tato metrika jsou popsány [zde](stream-analytics-streaming-unit-consumption.md). 
    -   Výstup časové razítko je spadajících s ohledem na skutečný čas. V závislosti na logice dotaz pravděpodobně časové razítko výstup logiku posun od skutečný čas. By však průběhu přibližně stejnou rychlostí. Pokud výstupní časové razítko je návratem další a další za, je indikátor, který je overworking systému. Může být způsobeno podřízené výstupní jímku omezení nebo vysoké využití procesoru. Jsme neposkytují metriky využití procesoru v tomto okamžiku tak může být obtížné rozlišit dva.
        - Pokud je problém z důvodu omezení jímka, budete muset zvýšit počet oddílů výstup (a také vstupní oddíly zachovat úlohy plně může běžet paralelně), nebo zvětšete velikost prostředky jímky (například počet jednotek žádosti pro CosmosDB).
    - V diagramu úlohy, je každý oddíl nevyřízených událostí Metrika pro každý vstupní. Pokud metriku nevyřízených událostí i nadále, je také jako ukazatel, že systémový prostředek je omezena (buď z důvodu omezení výstupní jímku nebo vysoké využití procesoru).
4.  Jakmile zjistíte, jaké úlohu 6 SU dosáhnout omezení, že můžete odvodit lineárně kapacity zpracování úlohy a přidáte další služby SUs, za předpokladu, že nemáte žádná data zkreslit, provede určité oddíl "horkých".
>[!Note]
> Vyberte správný počet jednotek streamování: protože Stream Analytics vytvoří uzel zpracování pro přidání jednotlivých 6 SU, je nejvhodnější počet uzlů dělitele počet vstupních oddílů, takže můžete oddíly rovnoměrně rozdělené mezi uzly.
> Například máte měří vaší 6 SU úlohy můžete dosáhnout 4 MB/s zpracování rychlost a spočítat vstupní oddílu je 4. Můžete spustit úlohu s 12 SU k dosažení rychlost zpracování přibližně 8 MB/s nebo 24 SU k dosažení 16 MB/s. Potom se můžete rozhodnout, kdy se má zvýšit číslo SU pro úlohu na jakou hodnotu, v závislosti na vaší vstupní míry.



## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Případ 2 – Pokud váš dotaz není paralelně zpracovatelné.
Pokud váš dotaz není paralelně zpracovatelné, můžete provést následující kroky pro.
1.  Začněte s dotazem bez **PARTITION BY** nejprve do Vyhněte se vytváření oddílů složitost a spusťte dotaz s 6 SU k měření maximální zatížení jako v [případ 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Pokud v období propustnost můžete dosáhnout předpokládaného zatížení, budete mít. Alternativně můžete měřit stejné úlohy spuštěné v 3 SU a 1 SU, a zjistěte, minimální počet SU, který je použitelný pro váš scénář.
3.  Pokud nelze dosáhnout požadovaného propustnost, zkuste rozdělit dotaz na několik kroků, pokud je to možné, pokud není již je několik kroků a přidělit SU až 6 pro každý krok v dotazu. Například pokud máte 3 kroky, přidělit 18 SU v možnosti "Škálování".
4.  Při spuštění takovou úlohu, Stream Analytics převádí každého kroku na vlastní uzel s vyhrazených prostředcích 6 SU. 
5.  Pokud stále ještě dosáhnout cílových zatížení, můžete se pokusit použít **PARTITION BY** od blíže kroky na vstup. Pro **GROUP BY** operátor, který nemusí být přirozeně rozdělený, můžete použít místní, globální agregační vzor provést oddílů **GROUP BY** následuje bez oddílů **GROUP BY** . Například pokud chcete spočítat kolik aut projít každý projedou stánek každé 3 minuty a objem dat, je nad rámec co může zpracovávat 6 SU.

Dotaz:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Ve výše uvedené dotazu jsou počítání automobilů na projedou stánek na oddíly a přidání ze všech oddílů počet společně.

Jakmile rozdělena na oddíly, pro každý oddíl v kroku přidělit až 6 SU, každý oddíl s 6 SU je maximální, takže každý oddíl můžete umístit na vlastní uzel zpracování.

> [!Note]
> Pokud váš dotaz nelze rozdělit na oddíly, přidávání dalších SU v dotazu více kroků nemusí vždy zlepšit propustnost. Jeden způsob, jak získat výkonu je snížit svazek na počáteční kroky pomocí místní, globální agregační vzoru, jak je popsáno výše v kroku 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Případ 3 - velké množství nezávislé dotazy běží v rámci úlohy.
U některých ISV používat případech, kdy je cenově výhodnější zpracovat data z více klientů v rámci jedné úlohy, pro každého klienta pomocí samostatné vstupy a výstupy můžete dospět systémem poměrně pár (například 20) nezávislé dotazy v jediné úlohy. Předpokladem je, že každé takové poddotazu na zatížení je poměrně malý. V takovém případě můžete provést následující kroky.
1.  V takovém případě nepoužívejte **PARTITION BY** v dotazu
2.  Snižte počet vstupních oddílů na nejnižší možnou hodnotu 2, pokud používáte centra událostí.
3.  Spusťte dotaz s 6 SU. S očekávanou zátěž pro každý poddotazu přidejte tolik takové poddotazy jako možný, dokud je úloha nedosáhli limitů prostředků systému. Odkazovat na [případ 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) pro příznaky v takovém případě.
4.  Jakmile jste nedosáhli limitu poddotazu měří výše, začít přidávat poddotaz novou úlohu. Počet úloh spuštěn jako funkce počet dotazů, nezávislé by měl být poměrně lineární, za předpokladu, že nemáte žádné zatížení zkreslit. Potom můžete prognózy kolik 6 SU úloh je třeba spustit v závislosti na počtu klientů, které byste chtěli obsluhovat.
5.  Při použití referenční data spojení s takové dotazy, měli byste – typ union, které vstupy společně, před připojením se stejnými daty odkaz, potom rozdělit na události v případě potřeby. Každý referenční data spojení, jinak hodnota uchovává kopii referenční data v paměti, pravděpodobně pěnicí až využití paměti zbytečně.

> [!Note] 
> Kolik klientů uvést do každé úlohy?
> Tento vzor dotazu často má velký počet poddotazy a výsledkem velmi velké a komplexní topologie. Řadič úlohy nemusí být schopna zpracovávat velké topologie. Jako existuje pravidlo zůstanou v části 40 klienty pro 1 SU úlohu a 60 klienty pro 3 SU a 6 SU úlohy. Když jsou překročení kapacity řadiče, nebude úloha úspěšně spustit.


## <a name="an-example-of-stream-analytics-throughput-at-scale"></a>Příkladem Stream Analytics propustnost ve velkém měřítku
Abyste lépe pochopili, jak úlohy Stream Analytics škálování, jsme provedli experimentu založené na vstupu z malin platformy zařízení. Tento experiment dejte nám najdete v části vliv na propustnost několika streamování jednotky a oddíly.

V tomto scénáři zařízení odesílá data snímačů (klientů) do centra událostí. Streamování Analytics zpracovává data a odešle výstrahu nebo statistiky jako výstup do jiného centra událostí. 

Klient odešle data snímačů ve formátu JSON. Výstup dat je také ve formátu JSON. Data vypadá takto:

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Následující dotaz se používá k odesílání výstrahu, pokud je vypnuté světlý:

    SELECT AVG(lght), "LightOff" as AlertText
    FROM input TIMESTAMP BY devicetime 
    PARTITION BY PartitionID
    WHERE lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Míra propustnost

V tomto kontextu propustnost je množství vstupních dat zpracovávaných Stream Analytics pevné množství času. (Jsme měří 10 minut.) K dosažení nejlepší propustnost zpracování vstupních dat, byly oddíly vstup datového streamu a dotazu. Jsme zahrnuté **COUNT()** v dotazu k měření počtu vstupních událostí byly zpracovány. Pokud chcete mít jistotu, že úloha nebyla jednoduše čekání vstupních událostech pochází, každý oddíl centra vstupní událostí se předem načtou přibližně 300 MB vstupní data.

V následující tabulce jsou uvedeny výsledků, které jsme viděli při odpovídající oddíl počty ve službě event hubs a jsme zvýšit počet jednotek streamování.  

<table border="1">
<tr><th>Vstupní oddíly</th><th>Výstup oddíly</th><th>Jednotky streamování</th><th>Propustnost
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/s</td>
</tr>
</table>

A následující graf ukazuje vizualizaci vztah mezi službou SUs a propustnosti.

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Podpora
Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

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

