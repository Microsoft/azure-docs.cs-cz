---
title: Horizontální funkce Machine Learning Studio (Classic) v Azure Stream Analytics
description: Tento článek popisuje, jak škálovat Stream Analytics úlohy, které používají funkce Machine Learning Studio (Classic), konfigurací oddílů a jednotek streamování.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 1ee1411aba7724d76ed8626de9b8b038d02339dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574250"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Škálování Stream Analytics úlohy pomocí funkcí Azure Machine Learning Studio (Classic)

> [!TIP]
> Pro zlepšení výkonu a spolehlivosti doporučujeme použít [Azure Machine Learning UDF](machine-learning-udf.md) místo Azure Machine Learning Studio (Classic) UDF.

Tento článek popisuje, jak efektivně škálovat Azure Stream Analytics úlohy, které používají funkce Azure Machine Learning Studio (Classic). Informace o tom, jak škálovat úlohy Stream Analytics obecně, najdete v článku o [škálování úloh](stream-analytics-scale-jobs.md).

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>Co je funkce studia (Classic) v Stream Analytics?

Funkce Machine Learning Studio (Classic) v Stream Analytics může být použita jako regulární volání funkce v dotazovacím jazyce Stream Analytics. Na pozadí jsou však tato volání funkcí skutečně dotazy webové služby Studio (Classic).

Pomocí dávkového zpracování více řádků ve stejném volání rozhraní API webové služby můžete vylepšit propustnost požadavků webové služby studia (Classic). Toto seskupení se nazývá mini-Batch. Další informace najdete v tématu [webové služby Azure Machine Learning Studio (Classic)](../machine-learning/classic/consume-web-services.md). Podpora pro Studio (Classic) v Stream Analytics.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>Konfigurace úlohy Stream Analytics s funkcemi studia (Classic)

Existují dva parametry pro konfiguraci funkce studia (klasické) používané úlohou Stream Analytics:

* Velikost dávky volání funkcí studia (Classic).
* Počet jednotek streamování (SUs) zřízených pro úlohu Stream Analytics.

Chcete-li určit příslušné hodnoty pro službu SUs, rozhodněte se, zda chcete optimalizovat latenci Stream Analytics úlohy nebo propustnosti každého SU. Služba SUs může být vždy přidána do úlohy, aby se zvýšila propustnost dobře rozděleného Stream Analyticsho dotazu. Další služba SUs zvyšuje náklady na spuštění úlohy.

Určete *toleranci* latence pro vaši úlohu Stream Analytics. Zvýšením velikosti dávky se zvýší latence požadavků studia (Classic) a latence úlohy Stream Analytics.

Zvýšení velikosti dávky umožní úloze Stream Analytics zpracovávat **Další události** se **stejným počtem** požadavků webové služby Studio (Classic). Zvýšení latence webové služby Studio (Classic) je obvykle lineárně lineární na zvýšení velikosti dávky. 

V každé konkrétní situaci je důležité vzít v úvahu největší cenově efektivní velikost dávky pro webovou službu studia (Classic). Výchozí velikost dávky pro žádosti webové služby je 1000. Tuto výchozí velikost můžete změnit pomocí [REST API Stream Analytics](/previous-versions/azure/mt653706(v=azure.100) "Rozhraní REST API služby Stream Analytics") nebo [klienta powershellu pro Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Jakmile se rozhodnete pro velikost dávky, můžete nastavit počet jednotek streamování (SUs) na základě počtu událostí, které funkce musí zpracovat za sekundu. Další informace o jednotkách streamování najdete v tématu [Stream Analytics škálování úloh](stream-analytics-scale-jobs.md).

Každých 6 služeb SUs získá 20 souběžných připojení k webové službě studia (Classic). 1 úlohy SU a 3. SU ale získají 20 souběžných připojení.  

Pokud vaše aplikace generuje 200 000 událostí za sekundu a velikost dávky je 1000, bude výsledná latence webové služby 200 ms. Tato rychlost znamená, že každé připojení může každou sekundu udělat pět požadavků na webovou službu studia (Classic). S 20 připojeními může Stream Analytics úloha zpracovávat 20 000 událostí v 200 MS a 100 000 událostí za sekundu.

Aby bylo možné zpracovat události 200 000 za sekundu, Stream Analytics úlohy potřebují 40 souběžných připojení, které se nacházejí na 12 SUs. Následující diagram znázorňuje požadavky z Stream Analytics úlohy na koncový bod webové služby Studio (Classic) – každých 6 SUs má na maximum 20 současných připojení k webové službě studia (Classic).

![Stream Analytics škály s funkcemi studia (klasické) – příklad dvou úloh](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Stream Analytics škály s funkcemi studia (klasické) – příklad dvou úloh")

Obecně platí, že ***B** _ pro velikost dávky, _*_L_*_ pro latenci webové služby ve velikosti dávky B v milisekundách, propustnost Stream Analytics úlohy s _ *_N_** službou SUs je:

![Škálování Stream Analytics se vzorcem funkcí studia (Classic)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Škálování Stream Analytics se vzorcem funkcí studia (Classic)")

U webové služby Studio (Classic) můžete také nakonfigurovat maximální počet souběžných volání. Doporučuje se nastavit tento parametr na maximální hodnotu (v současné době 200).

Další informace o tomto nastavení najdete v článku o [škálování pro webové služby Machine Learning Studio (Classic)](../machine-learning/classic/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Příklad – Analýza mínění
Následující příklad obsahuje úlohu Stream Analytics pomocí funkce mínění Analysis Studio (Classic), jak je popsáno v [kurzu integrace Stream Analytics Machine Learning Studio (Classic)](stream-analytics-machine-learning-integration-tutorial.md).

Dotaz je jednoduchý plně rozdělený dotaz následovaný funkcí **mínění** , jak je znázorněno v následujícím příkladu:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Pojďme se podívat na konfiguraci nutnou k vytvoření Stream Analytics úlohy, která analyzuje mínění tweety rychlostí 10 000 tweety za sekundu.

Když použijete 1 SU, mohl by tato úloha Stream Analytics zpracovat provoz? Úloha může udržovat se vstupem s použitím výchozí velikosti dávky 1000. Výchozí latence webové služby mínění Analysis Studio (Classic) (s výchozí velikostí dávky 1000) nevytváří více než sekundu latence.

**Celková** nebo koncová latence úlohy Stream Analytics by obvykle představovala několik sekund. Přečtěte si podrobnější informace o této Stream Analytics úlohy, *zejména* o voláních funkce studia (Classic). S velikostí dávky 1000, propustnost 10 000 událostí přebírá přibližně 10 požadavků na webovou službu. I s jedním SU je k dispozici dostatek souběžných připojení, aby bylo možné tento vstupní provoz přizpůsobit.

Pokud se míra vstupních událostí zvyšuje o 1 – ROM, úloha Stream Analytics potřebuje zpracovat 1 000 000 tweety za sekundu. Existují dvě možnosti, jak dosáhnout zvýšeného rozsahu:

1. Zvyšte velikost dávky.
2. Rozdělit vstupní datový proud na paralelní zpracování událostí.

Při první možnosti se zvyšuje **latence** úlohy.

Po druhé možnosti bude nutné zřídit více služeb SUs, aby měly více souběžných žádostí o webovou službu Studio (Classic). Tento větší počet služby SUs zvyšuje **náklady** na úlohu.

Pojďme se podívat na měřítko s využitím následujících měření latence pro každou velikost dávky:

| Latence | Velikost dávky |
| --- | --- |
| 200 MS | 1000 – dávky událostí nebo nižší |
| 250 ms | 5 000 – dávky událostí |
| 300 MS | 10 000 – dávky událostí |
| 500 ms | 25 000 – dávky událostí |

1. Pomocí první **Možnosti (** nezřizování služby SUs). Velikost dávky se dá zvýšit na **25 000**. Zvýšení velikosti dávky tímto způsobem umožní úloze zpracovávat 1 000 000 události s 20 souběžnými připojeními k webové službě studia (Classic) (s latencí 500 ms na volání). Proto se další latence Stream Analytics úlohy z důvodu požadavků funkce mínění proti žádostem o webovou službu Studio (Classic) zvýšila z **200 MS** na **500 ms**. Velikost dávky se ale **nedá** navýšit nekonečně, protože webové služby studia (Classic) vyžadují, aby velikost datové části žádosti byla 4 MB nebo menší, a po 100 sekundách operace vypršel časový limit požadavků webové služby.
1. Při použití druhé možnosti je velikost dávky ponechána v 1000, s latencí webové služby 200-ms, každých 20 souběžných připojení k webové službě by bylo možné zpracovat 1000 × 20 * 5 událostí = 100 000 za sekundu. Aby bylo možné zpracovávat 1 000 000 událostí za sekundu, úloha by potřebovala 60 SUs. V porovnání s první možností by služba Stream Analytics Job prováděla více požadavků na dávku webových služeb a zároveň vygenerovala zvýšené náklady.

Níže je tabulka propustnosti Stream Analytics úlohy pro různé velikosti služby SUs a dávky (v počtu událostí za sekundu).

| velikost dávky (latence ML) | 500 (200 ms) | 1 000 (200 ms) | 5 000 (250 ms) | 10 000 (300 MS) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1. SU** |2,500 |5 000 |20 000 |30 000 |50,000 |
| **3 služba SUs** |2,500 |5 000 |20 000 |30 000 |50,000 |
| **6 služby SUs** |2,500 |5 000 |20 000 |30 000 |50,000 |
| **12. SUs** |5 000 |10 000 |40,000 |60 000 |100 000 |
| **18 SUs** |7 500 |15 000 |60 000 |90,000 |150 000 |
| **24 SUs** |10 000 |20 000 |80,000 |120 000 |200 000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25 000 |50,000 |200 000 |300 000 |500 000 |

Teď byste měli mít dobrou představu o tom, jak funkce studia (Classic) v Stream Analytics fungují. Pravděpodobně také rozumíte tomu, že Stream Analytics úlohy "vyžádané" data ze zdrojů dat a každá "pull" vrací dávku událostí pro zpracování úlohy Stream Analytics. Jak tento model vyžádaného zpracování ovlivňuje požadavky webové služby Studio (Classic)?

Velikost dávky, kterou nastavíme pro funkce studia (Classic), se běžně neprojeví na základě počtu událostí vrácených jednotlivými Stream Analytics úlohou "pull". Pokud k tomu dojde, Webová služba Studio (Classic) se zavolá s "částečnými" dávkami. Použití částečných dávek zabraňuje dalšímu zvýšení režijních nákladů úlohy v slučovacích událostech z akce Pull na vyžádání.

## <a name="new-function-related-monitoring-metrics"></a>Nové metriky monitorování související s funkcí
V oblasti monitorování Stream Analytics úlohy byly přidány tři metriky související s funkcí. Jsou to **žádosti o funkce**, **události funkcí** a **neúspěšné žádosti o funkce**, jak je znázorněno na obrázku níže.

![Škálování Stream Analytics s využitím metriky funkcí studia (Classic)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Škálování Stream Analytics s využitím metriky funkcí studia (Classic)")

Jsou definovány následujícím způsobem:

**Požadavky na funkce**: počet žádostí o funkce.

**Události funkcí**: počet událostí v požadavcích funkce.

**Neúspěšné požadavky na funkce**: počet neúspěšných žádostí o funkce.

## <a name="key-takeaways"></a>Key poznatky

Pokud chcete škálovat úlohu Stream Analytics pomocí funkcí studia (Classic), vezměte v úvahu následující faktory:

1. Míra vstupních událostí.
2. Tolerováná latence pro spuštěnou úlohu Stream Analytics (a tedy velikost dávky požadavků webové služby Studio (Classic)).
3. Zřízená služba Stream Analytics SUs a počet požadavků webové služby Studio (Classic) (další náklady související s funkcí).

Jako příklad se použil plně rozdělený Stream Analytics dotaz. Pokud je potřeba složitější dotaz, je [stránka Microsoft Q&otázka pro Azure Stream Analytics](/answers/topics/azure-stream-analytics.html) skvělým prostředkem pro získání další užitečné informace od týmu Stream Analytics.

## <a name="next-steps"></a>Další kroky
Další informace o Stream Analytics najdete v těchto tématech:

* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)
