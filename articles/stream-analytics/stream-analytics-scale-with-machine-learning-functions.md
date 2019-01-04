---
title: Škálování funkcí Machine Learning v Azure Stream Analytics
description: Tento článek popisuje, jak škálovat úlohy Stream Analytics, které používají funkce Machine learningu, tím, že nakonfigurujete jednotek pro dělení a datového proudu.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 216ce32997a4114f4f2684b14338b4e36d9afd03
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558001"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Škálovat úlohy Stream Analytics s funkcemi Azure Machine Learning
Nastavení úlohy Stream Analytics a spuštění ukázkových dat přes něj je snadná. Co máme dělat, když potřebujeme spustit stejná úloha s vyšší objem dat? Abychom mohli pochopit, jak nakonfigurovat úlohu Stream Analytics tak, aby škáluje se vyžaduje. V tomto dokumentu se zaměříme na zvláštní aspekty škálovat úlohy Stream Analytics s funkcemi Machine Learning. Informace o tom, jak škálovat úlohy Stream Analytics obecně najdete v článku [škálování úloh](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Co je Azure Machine Learning funkce ve Stream Analytics?
Funkci strojového učení ve službě Stream Analytics můžete použít jako normální volání funkce v dotazovací jazyk Stream Analytics. Za scénu, jsou volání funkcí ve skutečnosti žádosti webové služby Azure Machine Learning. Webové služby Machine Learning podporuje "dávkování" více řádků, volá zkrácené batch ve stejném volání webové služby rozhraní API, ke zlepšení celkovou propustnost. Další informace najdete v tématu [funkce Azure Machine Learning ve službě Stream Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) a [Azure Machine Learning Web Services](../machine-learning/studio/consume-web-services.md).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurace úlohy Stream Analytics s funkcemi Machine Learning
Když konfigurujete funkci strojového učení pro úlohu Stream Analytics, existují dva parametry, které byste měli zvážit, velikost dávky volání funkce Machine Learning a jednotky streamování (su) zřízené pro úlohu Stream Analytics. K určení příslušné hodnoty pro su, nejprve rozhodnutí musí provádět mezi latence a propustnosti, to znamená, latenci úlohy Stream Analytics a propustnost všechny SU. Služby SUs může být vždy přidána na úlohy pro zvýšení prostupnosti dobře oddílů dotazu Stream Analytics, i když další služby SUs zvýšit náklady na spuštění úlohy.

Proto je důležité určit, *tolerance* latence ve spuštěném úlohu Stream Analytics. Další spuštění žádosti o služby Azure Machine Learning zvýší se latence přirozeně se velikost dávky, která sloučeniny latence úlohy Stream Analytics. Na druhé straně zvýšit velikost dávky umožňuje úlohy Stream Analytics ke zpracování * další události s *stejné číslo* služby Machine Learning webové žádosti o služby. Často je pod řádkem zvýšení velikosti dávky zvýšení latence webové služby Machine Learning, takže je důležité vzít v úvahu nejvíce cenově efektivní velikost dávky pro webovou službu Machine Learning v jakékoli dané situaci. Výchozí velikost dávky pro webovou službu požadavků je 1000 a mohou být upraveny buď pomocí [rozhraní REST API pro Stream Analytics](https://msdn.microsoft.com/library/mt653706.aspx "rozhraní REST API pro Stream Analytics") nebo [klienta Powershellu pro Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md "klienta Powershellu pro Stream Analytics").

Když velikost dávky bylo zjištěno, počet datových proudů jednotky (su) mohou být určeny, podle počtu událostí, které funkce je potřeba zpracovat za sekundu. Další informace o jednotek streamování najdete v tématu [škálovat úlohy Stream Analytics](stream-analytics-scale-jobs.md).

Obecně platí existují 20 souběžných připojení k webové službě Machine Learning pro každých 6 SUs s tím rozdílem, že jeden SU úlohy a 3 SU získáte 20 souběžných připojení také.  Například pokud míra vstupních dat je 200 000 událostí za sekundu a velikost dávky je ponecháno na výchozí hodnotu 1 000 výsledný latence webové služby pomocí služby batch zkrácené 1000 událostí je 200 ms. To znamená, že všechna připojení můžete provádět pět požadavky na webovou službu Machine Learning za sekundu. Protože 20 připojení úlohy Stream Analytics zpracovávat 20 000 událostí v 200 ms a proto 100 000 událostí za sekundu. Proto zpracovat 200 000 událostí za sekundu, úlohy Stream Analytics musí 40 souběžných připojení, které přijdete na 12 su. Následující diagram znázorňuje požadavky z úlohy Stream Analytics na koncový bod webové služby Machine Learning – každých 6 SUs má 20 souběžných připojení k webové službě Machine Learning na maximum.

![Škálování pomocí funkce Machine learningu příklad dvě úlohy Stream Analytics](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "škálování Stream Analytics s ukázkou dvě úlohy funkce Machine learningu")

Obecně platí ***B*** pro velikost dávky ***L*** pro webové služby čekací doba na velikost dávky B v milisekundách, propustnost Stream Analytics úlohy s ***N*** SUs je:

![Stream Analytics se službou Machine Learning funkce vzorec škálování](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics se službou Machine Learning funkce vzorec škálování")

Další pozornost mohou být 'maximální počet souběžných volání' na straně webové služby Machine Learning, doporučujeme nastavit maximální hodnotu (aktuálně 200).

Další informace o tomto nastavení najdete v tématu [článku škálování pro Machine Learning Web Services](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Příklad: Analýza mínění
Následující příklad obsahuje úlohu Stream Analytics s analýzou mínění funkci strojového učení, jak je popsáno v [integrací Stream Analytics Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Dotaz je jednoduchý plně dělené dotazu, za nímž následuje **mínění** fungovat, jak je znázorněno v následujícím příkladu:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```
Vezměte v úvahu následující scénář; s propustností 10 000 tweety za sekundu je nutné vytvořit úlohu Stream Analytics, provádět analýzu subjektivního hodnocení ve tweetech (události). Pomocí 1 SU, může tuto úlohu Stream Analytics být schopná zpracovat provoz? Použitím výchozí velikosti dávky 1 000 úlohu měli být schopni držet krok s vstupu. Další přidaná funkce Machine Learning má více než jedna sekunda latenci, která je obecná generovat výchozí latence analýzy subjektivního hodnocení webové služby Machine Learning (s výchozí velikost dávky 1000). Úlohy Stream Analytics **celkové** nebo na celkovou latenci by obvykle několik sekund. Podrobnější podívejte se do této úlohy Stream Analytics *zejména* volání funkce Machine Learning. Velikost dávky s jako 1 000, propustnost 10 000 událostí trvat asi 10 požadavků k webové službě. I přes jeden SU jsou dost souběžných připojení tak, aby vyhovovaly tento vstupní provoz.

Počet vstupních událostí se zvyšuje 100 x, potom úlohu Stream Analytics musí zpracovat 1 000 000 tweety za sekundu. Existují dvě možnosti, jak dosáhnout většího rozsahu:

1. Zvětšete velikost dávky, nebo
2. Vstupní datový proud pro zpracování událostí v paralelních oddílů

První možnost, úloha **latence** zvyšuje.

S druhou možnost Další služby SUs potřebuje zřídí a proto generovat více souběžných žádosti webové služby Machine Learning. To znamená, že úloha **náklady** zvyšuje.

Předpokládejme, že latence analýza mínění webové služby Machine Learning je 200 ms pro 1 000 událostí dávky nebo níže, 250 ms pro dávky 5 000 událostí, 300 ms pro 10 000 událostí dávky nebo 500 ms pro dávky 25 000 událostí.

1. První možnost (**není** zřizování další su). Velikost dávky by mohla být zvýšena na **25 000**. To pak umožní úlohu pro zpracování 1 000 000 událostí s 20 souběžných připojení k webové službě Machine Learning (s latencí 500 ms za volání). Proto další latence úlohy Stream Analytics z důvodu požadavky na funkce mínění proti žádosti webové služby Machine Learning by zvýšil z **200 ms** k **500 ms**. Velikost dávky však **nelze** zvýší neomezeně webové služby Machine Learning vyžaduje velikost datové části požadavku 4 MB nebo menší časový limit po 100 sekund operaci žádosti o službu web.
2. Druhá možnost necháte na 1000, velikost dávky s latencí 200 ms webové služby, každých 20 souběžných připojení k webové službě by se měl události procesu 1000 * 20 * 5 = 100 000 za sekundu. Proto zpracovat 1 000 000 událostí za sekundu, úloha potřebuje 60 su. Ve srovnání s první možnost, úlohy Stream Analytics žádným další žádosti webové služby batch, pak generování vyšší náklady.

Tady je tabulka pro propustnost úlohy Stream Analytics pro různé služby SUs a velikostí dávky (v počtu událostí za sekundu).

| velikost dávky (ML čekací doba) | 500 (200 ms) | 1 000 (200 ms) | 5 000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 služby SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 služby SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 služby SUs** |5 000 |10 000 |40,000 |60,000 |100 000 |
| **18 služby SUs** |7 500 |15,000 |60,000 |90,000 |150,000 |
| **24 služby SUs** |10 000 |20,000 |80,000 |120,000 |200 000 |
| **…** |… |… |… |… |… |
| **60 služby SUs** |25,000 |50,000 |200 000 |300,000 |500,000 |

Nyní máte již dobrý přehled o fungování funkce Machine learningu ve službě Stream Analytics. Pravděpodobně také chápete, že úlohy Stream Analytics "pull" data ze zdrojů dat a každý "pull" vrátí služby batch pro úlohu Stream Analytics ke zpracování událostí. Jak tento dopad o přijetí změn model Machine Learning webové žádosti o služby

Za normálních okolností velikost dávky, kterou jsme nastavili pro funkce Machine learningu nebudou přesně dělitelná počet událostí vrácených každou úlohu Stream Analytics "pull". Pokud k tomu dojde, webové služby Machine Learning je volána s "částečné" dávky. To slouží k nejsou spojené režijní náklady na latenci dalších úloh ve slučovací události z o přijetí změn na vyžádání.

## <a name="new-function-related-monitoring-metrics"></a>Nové funkce související s monitorovací metriky
V oblasti sledování úloh Stream Analytics byly přidány tři další metriky související funkce. Jak je znázorněno na následujícím obrázku jsou požadavky na funkce, funkce události a požadavky na funkce se nezdařilo.

![Škálování Stream Analytics se službou Machine Learning funkce metriky](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "škálování Stream Analytics s metrikami funkce Machine Learning")

Jsou definovány takto:

**POŽADAVKY NA FUNKCE**: Počet požadavků na funkce.

**UDÁLOSTI FUNKCÍ**: Počet událostí v žádosti o funkce.

**NEZDAŘENÉ POŽADAVKY NA FUNKCE**: Počet Nezdařené požadavky na funkce.

## <a name="key-takeaways"></a>Stěžejní zjištění
Slouží ke shrnutí hlavních bodů, aby bylo možné škálovat úlohy Stream Analytics s funkcemi Machine Learning, musí být považována za následující položky:

1. Míra vstupních událostí
2. Tolerovaný latence pro běžící úlohu Stream Analytics (a tedy velikost dávky žádosti webové služby Machine Learning)
3. Zřízené SUs Stream Analytics a počet žádosti webové služby Machine Learning (Další funkce související náklady)

Plně oddílů dotazu Stream Analytics byla použita jako příklad. V případě potřeby složitějšího dotazu [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) je skvělý prostředek pro získání další pomoc od týmu služby Stream Analytics.

## <a name="next-steps"></a>Další postup
Další informace o službě Stream Analytics najdete v tématu:

* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
