---
title: Úlohy škálování s Azure Stream Analytics & AzureML funkce | Microsoft Docs
description: Zjistěte, jak se správně škálovat úlohy Stream Analytics (rozdělení do oddílů, SU množství a více) při použití funkce Azure Machine Learning.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 47ce7c5e-1de1-41ca-9a26-b5ecce814743
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: dd6effab3ba0b411131414bd757ffe8cc54e49d2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Škálovat vaše úloha Stream Analytics s funkcemi Azure Machine Learning
Často je snadné nastavení úlohy Stream Analytics a spusťte ukázková data přes něj. Co můžeme dělat, když budeme potřebovat spuštění stejné úlohy s vyšší datový svazek? To vyžaduje nám pochopit, jak nakonfigurovat úlohy služby Stream Analytics tak, aby se škáluje. V tomto dokumentu zaměříme na zvláštní aspekty škálování úlohy Stream Analytics s funkcemi, Machine Learning. Informace o tom, jak obecně škálování úlohy Stream Analytics najdete v článku [škálování úlohy](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Co je Azure Machine Learning funkce v Stream Analytics?
Funkce Machine Learning v Stream Analytics lze použít jako normální funkce volání v jazyce dotaz služby Stream Analytics. Ale za scény, volání funkce je ve skutečnosti žádostí o webové službě Azure Machine Learning. Webové služby Machine Learning podporovat "dávkování" více řádků, která se nazývá zkrácená batch, ve stejném volání webové služby rozhraní API, ke zlepšení celkovou propustnost. Najdete v následujících článcích podrobnosti; [Funkce azure Machine Learning v Stream Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) a [webové služby Azure Machine Learning](../machine-learning/studio/consume-web-services.md).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Úloha Stream Analytics nakonfigurovat funkce Machine Learning
Při konfiguraci funkce Machine Learning úlohy Stream Analytics, existují dva parametry vzít v úvahu, velikost dávky volání funkce Machine Learning a jednotky streamování (SUs) zřízené pro úlohu služby Stream Analytics. Pokud chcete zjistit, na odpovídající hodnoty pro tyto, nejprve musí být přijato rozhodnutí mezi latence a propustnosti, tedy latence úlohy služby Stream Analytics a propustnost každý SU. Služba SUs mohou být přidány do úlohu chcete zvýšit propustnost dobře oddílů dotazu Stream Analytics, vždy, i když další služby SUs zvýšit náklady na provozování úlohy.

Proto je důležité určit *tolerance* latence ve spuštění úlohy Stream Analytics. Další spuštění žádosti o služby Azure Machine Learning zvýší se latence přirozeně s velikost dávky, která sloučeniny latence úlohu služby Stream Analytics. Na druhé straně zvýšit velikost dávky umožňuje úlohu služby Stream Analytics ke zpracování * další události s *stejné číslo* nástroje Machine Learning webové žádosti o služby. Často je zvýšení Machine Learning webové služby latence pod řádkem pro zvýšení velikost dávky, takže je důležité vzít v úvahu nejvíce cenově efektivní velikost dávky pro webové služby Machine Learning v jakékoliv jiné situaci s danou. Výchozí velikost dávky pro webovou službu požadavky je 1000 a může být změněno buď pomocí [Stream Analytics REST API](https://msdn.microsoft.com/library/mt653706.aspx "Stream Analytics REST API") nebo [klienta PowerShell pro Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md "klienta PowerShell pro Stream Analytics").

Po zjištění velikost dávky počet streaming jednotky (SUs) může být určené, podle počtu událostí, které potřebuje funkce procesů za sekundu. Další informace o jednotky streamování najdete v tématu [škálování úlohy Stream Analytics](stream-analytics-scale-jobs.md).

Obecně platí je 20 souběžných připojení k webové službě Machine Learning pro každých 6 služby SUs s tím rozdílem, že 1 SU úlohy a úlohy 3 SU získat 20 souběžných připojení také.  Například pokud míra vstupních dat je 200 000 událostí za sekundu a velikost dávky je ponecháno na výchozí hodnotu 1 000 výsledné latence webové služby pomocí služby batch zkrácená 1000 událostí je 200 ms. To znamená, že každé připojení provádět pět požadavky k webové službě Machine Learning za sekundu. S 20 připojení může úloha Stream Analytics zpracovat 20 000 událostí v 200 ms a proto 100 000 událostí za sekundu. Ke zpracování 200 000 událostí za sekundu, takže úlohu služby Stream Analytics musí 40 souběžných připojení, které přicházejí 12 služby SUS. Následující diagram znázorňuje žádosti z úlohy Stream Analytics, které chcete koncový bod webové služby Machine Learning – každých 6 SUs má 20 souběžných připojení k webové službě Machine Learning na maximum.

![Škálování služby Stream Analytics se Machine Learning funkce dvě úlohy příklad](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "škálování Stream Analytics příklad dvě úlohy funkce Machine Learning")

Obecně platí ***B*** pro velikost dávky ***L*** pro webové služby s latencí na velikost dávky B v milisekundách, propustnost Stream Analytics úlohy s ***N*** služby SUs je:

![Škálování služby Stream Analytics s Machine Learning funkce vzorec](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "škálování služby Stream Analytics s vzorec funkce Machine Learning")

Další aspekt mohou být 'maximální počet souběžných volání' na straně webové služby Machine Learning, se doporučuje tuto možnost nastavíte na maximální hodnotu (aktuálně 200).

Další informace o tomto nastavení najdete [škálování najdete v článku webové služby Machine Learning](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Příklad – postojích analýzy
Následující příklad obsahuje úlohy Stream Analytics s analýzou postojích funkce Machine Learning, jak je popsáno v [Stream Analytics Machine Learning integrace kurzu](stream-analytics-machine-learning-integration-tutorial.md).

Dotaz je jednoduchý plně oddíly dotazu, za nímž následuje **postojích** fungovat jako uvedené následující:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Vezměte v úvahu následující scénář; s propustností 10 000 tweetů za sekundu úloha Stream Analytics musí být vytvořený provést analýzu postojích tweetů (událostí). Pomocí 1 SU, může tato úloha Stream Analytics moci provoz zvládla? Pomocí výchozí velikost dávky 1000 by mohli udržovat tempo s vstupu úlohy. Další přidané funkce Machine Learning má více než jedna sekunda latence, což je obecný generovat výchozí latence postojích analýzy webové službě Machine Learning (s výchozí velikost dávky 1000). Úloha Stream Analytics **celkové** nebo latence začátku do konce by obvykle na několik sekund. Podrobnější podívejte se do této úlohy Stream Analytics *zejména* volání funkce Machine Learning. Velikost dávky s jako 1000, propustnost 10 000 událostí trvat asi 10 požadavky k webové službě. I když 1 SU jsou dostatečný počet souběžných připojení přizpůsobená tento vstupní provoz.

Ale co když rychlost těchto událostí vstupní zvyšuje úroveň 100 x a teď je potřeba úlohu služby Stream Analytics ke zpracování 1 000 000 tweetů za sekundu? Existují dvě možnosti:

1. Zvětšete velikost dávky, nebo
2. Oddíl vstupního datového proudu se zpracovat události paralelně

S parametrem první úlohu **latence** zvyšuje.

Druhá možnost se další služby SUs by nutné zřídit a proto generovat více souběžných žádosti webové služby Machine Learning. To znamená úlohu **náklady** zvyšuje.

Předpokládají, že latence postojích analýzy webové službě Machine Learning je 200 ms pro dávky 1000 událostí nebo níže 250 ms pro dávky 5 000 událostí, 300 ms pro dávky 10 000 událostí nebo 500 ms pro dávky 25 000 událostí.

1. Pomocí možnosti první (**není** zřizování další služby SUs), velikost dávky by mohla být zvýšena na **25 000**. To zase umožní úlohu pro zpracování 1 000 000 událostí s 20 souběžných připojení k webové službě Machine Learning (s latencí 500 ms za hovor). Tak další latence úlohu služby Stream Analytics z důvodu požadavků funkce postojích na žádosti webové služby Machine Learning by být zvýšena z **200 ms** k **500 ms**. Velikost dávky však **nelze** zvýší nekonečnou strojového učení webové služby vyžaduje, aby velikost datové části žádosti o 4 MB volného místa nebo menší časový limit po 100 sekundách operace žádosti o webovou službu.
2. Pomocí druhou možnost, velikost dávky je ponechány na 1000, s 200 ms webové služby latencí, každých 20 souběžných připojení k webové službě, budou moci události procesu 1000 * 20 * 5 = 100 000 za sekundu. Ke zpracování 1 000 000 událostí za sekundu, takže úlohy by měli 60 služby SUs. Úlohy služby Stream Analytics by ve srovnání s první možnost, zkontrolujte další žádosti webové služby batch, zase generování vyšší náklady.

Níže je tabulka pro propustnost úlohu služby Stream Analytics pro různé služby SUs a velikostí dávky (v počtu událostí za sekundu).

| velikost dávky (ML latence) | 500 (200 ms) | 1 000 (200 ms) | 5 000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 služby SUs** |5 000 |10 000 |40,000 |60,000 |100,000 |
| **18 služby SUs** |7 500 |15,000 |60,000 |90,000 |150,000 |
| **24 SUs** |10 000 |20,000 |80,000 |120,000 |200 000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200 000 |300,000 |500,000 |

Nyní by byste již měli mít dostatečné povědomí o tom, jak funkce Machine Learning v Stream Analytics fungovat. Je pravděpodobně také pochopit, že úlohy Stream Analytics "načítat" data ze zdroje dat a každý "vyžádání" vrátí dávky události pro úlohu služby Stream Analytics ke zpracování. Jak tomuto vlivu model vyžádání Machine Learning webové žádosti o služby?

Za normálních okolností velikost dávky nastavený pro Machine Learning funkce nebudou přesně dělitelná počet událostí vrácených Každá úloha Stream Analytics "vyžádání". Když k tomu dojde, že webové službě Machine Learning je volán s "částečné" dávky. To slouží k není zpoplatněná režijní náklady na latenci dalších úloh v slučování událostí z vyžádání obsahu na vyžádání.

## <a name="new-function-related-monitoring-metrics"></a>Nové funkce související s monitorování metriky
V oblasti monitorování úlohy Stream Analytics byly přidány tři další metriky týkající se funkce. Jak je znázorněno na následujícím obrázku jsou žádosti o funkce, funkce události a NEÚSPĚŠNÉ požadavky funkce.

![Škálování služby Stream Analytics s Machine Learning funkce metriky](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "škálování služby Stream Analytics s Machine Learning funkce metriky")

Jsou definovány takto:

**POŽADAVKY funkce**: počet požadavků funkce.

**Funkce události**: číslo události v žádosti o funkce.

**NEÚSPĚŠNÉ požadavky funkce**: počet požadavků neúspěšné funkce.

## <a name="key-takeaways"></a>Klíče Takeaways
To Shrneme hlavní body, aby bylo možné škálovat úloha Stream Analytics s funkcemi, Machine Learning, je třeba zvážit následující položky:

1. Rychlost, jakou vstupní událost
2. . Povolená latence pro spuštěná úloha Stream Analytics (a tedy velikost dávky žádosti webové služby Machine Learning)
3. Zřízené SUs analýzy datového proudu a počet žádosti webové služby Machine Learning (Další funkce související náklady na)

Jako příklad byl použit dotaz služby Stream Analytics plně oddílů. Pokud je potřeba komplexnější dotaz [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) je skvělým zdrojem pro získávání potřebujete další pomoc od týmu Stream Analytics.

## <a name="next-steps"></a>Další postup
Další informace o Stream Analytics najdete v tématu:

* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
