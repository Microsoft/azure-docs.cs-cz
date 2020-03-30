---
title: Škálování funkcí strojového učení ve službě Azure Stream Analytics
description: Tento článek popisuje, jak škálovat úlohy Stream Analytics, které používají funkce Machine Learning, konfigurací jednotek dělení a datových proudů.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5b08625d055063b3804a35a3344ff01c7edb79de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067001"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Škálování úlohy Stream Analytics pomocí funkcí Azure Machine Learning Studio (klasické)

> [!TIP]
> Důrazně doporučujeme používat [UDF Azure Machine Learning](machine-learning-udf.md) místo Azure Machine Learning Studio (klasické) UDF pro lepší výkon a spolehlivost.

Tento článek popisuje, jak efektivně škálovat úlohy Azure Stream Analytics, které používají funkce Azure Machine Learning. Informace o tom, jak škálovat úlohy Služby Stream Analytics obecně, naleznete v článku [Škálování úloh](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Co je funkce Azure Machine Learning v Stream Analytics?

Funkci Machine Learning v Stream Analytics lze použít jako běžné volání funkce v dotazovacím jazyce Stream Analytics. Na pozadí jsou však tyto volání funkcí ve skutečnosti požadavky azure machine learningwebové služby.

Můžete zlepšit propustnost požadavků webové služby Machine Learning "dávkování" více řádků společně ve stejném volání rozhraní API webové služby. Toto seskupení se nazývá minidávka. Další informace najdete v [tématu Azure Machine Learning Studio (klasické) webové služby](../machine-learning/studio/consume-web-services.md). Podpora pro Azure Machine Learning Studio (klasické) v Stream Analytics je ve verzi preview.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurace úlohy Stream Analytics pomocí funkcí Strojového učení

Existují dva parametry konfigurace funkce Machine Learning používané úlohou Stream Analytics:

* Velikost dávky volání funkce Machine Learning.
* Počet jednotek streamování (SU) zřízených pro úlohu Stream Analytics.

Chcete-li určit příslušné hodnoty pro su, rozhodněte se, zda chcete optimalizovat latenci úlohy Stream Analytics nebo propustnost každého SU. SU mohou být vždy přidány do úlohy, aby se zvýšila propustnost dobře rozděleného dotazu Stream Analytics. Další su zvýšit náklady na spuštění úlohy.

Určete *toleranci* latence pro úlohu Stream Analytics. Zvětšení velikosti dávky zvýší latenci vašich požadavků na Azure Machine Learning a latenci úlohy Stream Analytics.

Zvětšení velikosti dávky umožňuje úloze Stream Analytics zpracovat **více událostí** se **stejným počtem** požadavků na webové služby Machine Learning. Zvýšení latence webové služby Machine Learning je obvykle sublineární na zvýšení velikosti dávky. 

Je důležité zvážit nákladově nejefektivnější velikost dávky pro webovou službu Machine Learning v dané situaci. Výchozí velikost dávky pro požadavky webové služby je 1000. Tuto výchozí velikost můžete změnit pomocí [rozhraní API REST pro Stream Analytics](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Rozhraní REST API služby Stream Analytics") nebo [klienta PowerShellu pro Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Jakmile se rozhodnete pro velikost dávky, můžete nastavit počet jednotek streamování (SU) na základě počtu událostí, které funkce potřebuje zpracovat za sekundu. Další informace o jednotkách streamování najdete v [tématu Úlohy škálování Stream Analytics](stream-analytics-scale-jobs.md).

Každých 6 su získat 20 souběžných připojení k webové službě Machine Learning. Však 1 úlohy SU a 3 úlohy SU získat 20 souběžných připojení.  

Pokud aplikace generuje 200 000 událostí za sekundu a velikost dávky je 1000, je výsledná latence webové služby 200 ms. Tato rychlost znamená, že každé připojení může provést pět požadavků na webové služby Machine Learning každou sekundu. S 20 připojeními může úloha Stream Analytics zpracovat 20 000 událostí za 200 ms a 100 000 událostí za sekundu.

Ke zpracování 200 000 událostí za sekundu potřebuje úloha Stream Analytics 40 souběžných připojení, která se objeví na 12 su. Následující diagram znázorňuje požadavky z úlohy Stream Analytics na koncový bod webové služby Machine Learning – každých 6 su má 20 souběžných připojení k webové službě Machine Learning na max.

![Škálování analýzy datového proudu pomocí funkcí strojového učení dva příklady úloh](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Škálování analýzy datového proudu pomocí funkcí strojového učení dva příklady úloh")

Obecně platí ***B*** pro velikost dávky ***L*** pro latenci webové služby při velikosti dávky B v milisekundách, propustnost úlohy Stream Analytics s ***N*** SU je:

![Škálování analýzy datových proudů pomocí vzorce funkcí strojového učení](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Škálování analýzy datových proudů pomocí vzorce funkcí strojového učení")

Můžete také nakonfigurovat "maximální souběžné hovory" ve webové službě Machine Learning. Doporučujeme nastavit tento parametr na maximální hodnotu (aktuálně 200).

Další informace o tomto nastavení naleznete v [článku škálování pro webové služby strojového učení](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Příklad – analýza mínění
Následující příklad zahrnuje úlohu Stream Analytics s funkcí Machine Learning analýzy mínění, jak je popsáno v [kurzu integrace strojového učení Stream Analytics](stream-analytics-machine-learning-integration-tutorial.md).

Dotaz je jednoduchý plně rozdělený dotaz následovaný funkcí **mínění,** jak je znázorněno v následujícím příkladu:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Podívejme se na konfiguraci potřebnou k vytvoření úlohy Stream Analytics, která provádí analýzu mínění tweetů rychlostí 10 000 tweetů za sekundu.

Pomocí 1 SU, mohla by tato úloha Stream Analytics zvládnout provoz? Úloha může držet krok se vstupem pomocí výchozí velikosti dávky 1000. Výchozí latence webové služby Machine Learning analysis (s výchozí velikostí dávky 1000) nevytvoří více než sekundu latence.

**Celková** nebo end-to-end latence úlohy Stream Analytics by obvykle byla několik sekund. Podívejte se podrobněji na tuto úlohu Stream Analytics, *zejména* na volání funkce Machine Learning. S velikostí dávky 1000 propustnost 10 000 událostí trvá asi 10 požadavků na webové služby. Dokonce i s jedním SU, existuje dostatek souběžných připojení pro tento vstupní provoz.

Pokud se rychlost vstupních událostí zvýší o 100x, úloha Stream Analytics musí zpracovat 1 000 000 tweetů za sekundu. Existují dvě možnosti, jak dosáhnout zvýšeného měřítka:

1. Zvětšete velikost dávky.
2. Rozdělte vstupní datový proud a zpracujte události paralelně.

S první možností se **zvyšuje latence úlohy.**

S druhou možností budete muset zřídit více su mít více souběžných požadavků na webové služby Machine Learning. Tento větší počet su, zvyšuje **náklady**na úlohu .

Podívejme se na škálování pomocí následujících měření latence pro každou velikost dávky:

| Latence | Velikost dávky |
| --- | --- |
| 200 ms | 1000-event dávky nebo nižší |
| 250 ms | 5 000 šarží událostí |
| 300 ms | 10 000 šarží událostí |
| 500 ms | 25 000 šarží událostí |

1. Použití první možnosti ( nezřizování více su).**not** Velikost dávky může být zvýšena na **25 000**. Zvětšení velikosti dávky tímto způsobem umožní úloze zpracovat 1 000 000 událostí s 20 souběžnými připojeními k webové službě Machine Learning (s latencí 500 ms na volání). Další latence úlohy Stream Analytics z důvodu požadavků na funkci mínění proti požadavkům webové služby Machine Learning by se tedy zvýšila z **200 ms** na **500 ms**. Velikost dávky však **nelze** zvýšit nekonečně jako webové služby Machine Learning vyžaduje velikost datové části požadavku být 4 MB nebo menší a webové služby požaduje časový rámec po 100 sekundách provozu.
1. Pomocí druhé možnosti je velikost dávky ponechána na 1000, s latencí webové služby 200 ms, každých 20 souběžných připojení k webové službě by bylo schopno zpracovat 1000 * 20 * 5 událostí = 100 000 za sekundu. Takže ke zpracování 1 000 000 událostí za sekundu by úloha potřebovala 60 su. Ve srovnání s první možností by úloha Stream Analytics vytvořila více dávkových požadavků webové služby, což by následně vyvolalo zvýšené náklady.

Níže je tabulka pro propustnost úlohy Stream Analytics pro různé su a velikosti dávek (v počtu událostí za sekundu).

| velikost dávky (latence ML) | 500 (200 ms) | 1 000 (200 ms) | 5 000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5 000 |20 000 |30,000 |50 000 |
| **3 SU** |2,500 |5 000 |20 000 |30,000 |50 000 |
| **6 SU** |2,500 |5 000 |20 000 |30,000 |50 000 |
| **12 SU** |5 000 |10 000 |40,000 |60 000 |100 000 |
| **18 SU** |7 500 |15 000 |60 000 |90,000 |150 000 |
| **24 SU** |10 000 |20 000 |80,000 |120,000 |200 000 |
| **…** |… |… |… |… |… |
| **60 SU** |25 000 |50 000 |200 000 |300,000 |500 000 |

Teď už byste měli mít dobré znalosti o tom, jak funguje Machine Learning v Stream Analytics. Pravděpodobně také chápete, že úlohy Stream Analytics "vytahují" data ze zdrojů dat a každé "vyžádat" vrátí dávku událostí pro úlohu Stream Analytics ke zpracování. Jak tento model vyžádat vliv na požadavky webové služby Machine Learning?

Za normálních okolností velikost dávky, kterou jsme nastavili pro funkce strojového učení, nebude přesně dělitelná počtem událostí vrácených každou úlohou Stream Analytics "pull". V takovém případě je volána webová služba Machine Learning s "částečné" listy. Použití částečné dávky zabraňuje vzniku další chod úlohy latence režie v slučování událostí z vyžádat na vyžádat.

## <a name="new-function-related-monitoring-metrics"></a>Nové metriky monitorování související s funkcemi
V oblasti Monitorování úlohy Stream Analytics byly přidány tři další metriky související s funkcemi. Jedná **se o požadavky na funkce**, události **funkce** a neúspěšné požadavky **na funkce**, jak je znázorněno na obrázku níže.

![Škálování analýzy streamů pomocí metrik funkcí strojového učení](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Škálování analýzy streamů pomocí metrik funkcí strojového učení")

Jsou definovány takto:

**POŽADAVKY NA FUNKCI**: Počet požadavků na funkce.

**UDÁLOSTI FUNKCE**: Počet událostí v požadavcích na funkce.

**POŽADAVKY NA NEPODAŘILO SE SELHÁNÍ FUNKCE**: Počet neúspěšných požadavků na funkci.

## <a name="key-takeaways"></a>Klíčové stánek s jídlem

Chcete-li úlohu Stream Analytics škálovat pomocí funkcí Machine Learningu, zvažte následující faktory:

1. Rychlost vstupní události.
2. Tolerovaná latence pro spuštěnou úlohu Stream Analytics (a tedy velikost dávky požadavků webové služby Machine Learning).
3. Zřízené streamové analytické su a počet požadavků na webové služby Machine Learning (dodatečné náklady související s funkcí).

Jako příklad byl použit plně rozdělený dotaz Stream Analytics. Pokud je potřeba složitější dotaz, [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) je skvělý mů e být zdrojem informací o tom, jak získat další nápovědu od týmu Stream Analytics.

## <a name="next-steps"></a>Další kroky
Další informace o Stream Analytics najdete v tématu:

* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
