---
title: Škálování funkcí Machine Learning v Azure Stream Analytics
description: Tento článek popisuje, jak škálovat úlohy Stream Analytics, které používají funkce Machine learningu, tím, že nakonfigurujete jednotek pro dělení a datového proudu.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3d478c2421066c8347622f9064c479bb8255b112
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621752"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>Škálovat úlohy Stream Analytics s funkcemi Azure Machine Learning Studio

Tento článek popisuje, jak efektivně škálovat úlohy Azure Stream Analytics, které funkce Azure Machine Learning. Informace o tom, jak škálovat úlohy Stream Analytics obecně najdete v článku [škálování úloh](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Co je Azure Machine Learning funkce ve Stream Analytics?

Funkci strojového učení ve službě Stream Analytics můžete použít jako normální volání funkce v dotazovací jazyk Stream Analytics. Na pozadí ale volání těchto funkcí jsou ve skutečnosti žádosti webové služby Azure Machine Learning.

"Dávkování" více řádků společně ve stejném volání rozhraní API webové služby, lze vylepšit propustnost žádosti webové služby Machine Learning. Toto seskupení je volána zkrácené služby batch. Další informace najdete v tématu [webových služeb Azure Machine Learning Studio](../machine-learning/studio/consume-web-services.md). Podpora pro Azure Machine Learning Studio ve službě Stream Analytics je ve verzi preview.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurace úlohy Stream Analytics s funkcemi Machine Learning

Existují dva parametry konfigurace funkce Machine Learning používané vaší úlohy Stream Analytics:

* Velikost dávky volání funkce Machine Learning.
* Počet jednotek streamování (su) zřízené pro úlohu Stream Analytics.

K určení příslušné hodnoty pro su, rozhodněte, jestli chcete optimalizovat latenci úlohy Stream Analytics nebo propustnost každá jednotka Streamování. Služba SUs může být vždy přidána na úlohu chcete zvýšit propustnost dobře oddílů dotazu Stream Analytics. Další služby SUs zvýšit náklady na spuštění úlohy.

Určení latence *tolerance* pro svou úlohu Stream Analytics. Zvětšení velikosti dávky zvýší latence žádostí o služby Azure Machine Learning a latenci úloh Stream Analytics.

Zvětšení velikosti dávky umožňuje úlohy Stream Analytics ke zpracování **další události** s **stejné číslo** služby Machine Learning webové žádosti o služby. Zvýšení latence webové služby Machine Learning je obvykle pod řádkem zvýšení velikosti dávky. 

Je důležité vzít v úvahu nejvíce cenově efektivní velikost dávky pro webovou službu Machine Learning v jakékoli dané situaci. Výchozí velikost dávky pro žádosti webové služby je 1000. Můžete změnit pomocí této výchozí velikost [rozhraní REST API pro Stream Analytics](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "rozhraní REST API pro Stream Analytics") nebo [klienta Powershellu pro Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Jakmile jste se rozhodli na velikost dávky, můžete nastavit počet jednotek streamování (su), na základě počtu událostí, které funkce je potřeba zpracovat za sekundu. Další informace o jednotek streamování najdete v tématu [škálovat úlohy Stream Analytics](stream-analytics-scale-jobs.md).

Každých 6 SUs získáte 20 souběžných připojení k webové službě Machine Learning. Však 1 SU úlohy a úlohy 3 SU získáte 20 souběžných připojení.  

Pokud vaše aplikace generuje 200 000 událostí za sekundu a velikost dávky je 1000, výsledný latence webové služby je 200 ms. Tato sazba znamená, že všechna připojení můžete provádět pět požadavky na webovou službu Machine Learning každou sekundu. Protože 20 připojení úlohy Stream Analytics zpracovávat 20 000 událostí za 200 ms a 100 000 událostí za sekundu.

Úlohy Stream Analytics ke zpracování 200 000 událostí za sekundu, musí 40 souběžných připojení, které přijdete na 12 su. Následující diagram znázorňuje požadavky z úlohy Stream Analytics na koncový bod webové služby Machine Learning – každých 6 SUs má 20 souběžných připojení k webové službě Machine Learning na maximum.

![Škálování pomocí funkce Machine learningu příklad dvě úlohy Stream Analytics](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "škálování Stream Analytics s ukázkou dvě úlohy funkce Machine learningu")

Obecně platí ***B*** pro velikost dávky ***L*** pro webové služby čekací doba na velikost dávky B v milisekundách, propustnost Stream Analytics úlohy s ***N*** SUs je:

![Stream Analytics se službou Machine Learning funkce vzorec škálování](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics se službou Machine Learning funkce vzorec škálování")

Můžete také nakonfigurovat 'maximální počet souběžných volání' na webovou službu Machine Learning. Doporučujeme nastavit tento parametr na maximální hodnotu (aktuálně 200).

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

Podívejme se na konfiguraci potřebné k vytvoření úlohy Stream Analytics, která nepodporuje analýzu subjektivního hodnocení ve tweety rychlostí 10 000 tweety na druhý.

Pomocí 1 SU, tato úloha Stream Analytics dokáže zpracovat provoz? Úlohu můžete držet krok s použitím výchozí velikosti dávky 1 000 vstupu. Vytvoří výchozí čekací doba analýzy subjektivního hodnocení webové služby Machine Learning (s výchozí velikost dávky 1000) nesmí být větší než druhý latence.

Úlohy Stream Analytics **celkové** nebo na celkovou latenci by obvykle několik sekund. Podrobnější podívejte se do této úlohy Stream Analytics *zejména* volání funkce Machine Learning. S velikost dávky 1 000 propustnost 10 000 událostí trvá asi 10 požadavků k webové službě. I přes jeden SU jsou dost souběžných připojení tak, aby vyhovovaly tento vstupní provoz.

Počet vstupních událostí se zvyšuje 100 x, potom úlohu Stream Analytics musí zpracovat 1 000 000 tweety za sekundu. Existují dvě možnosti, jak dosáhnout většího rozsahu:

1. Zvětšete velikost dávky.
2. Rozdělení vstupního datového proudu pro zpracování událostí současně.

První možnost, úloha **latence** zvyšuje.

S druhou možnost budete muset zřídit další služby SUs mít více souběžných žádosti webové služby Machine Learning. Tato větší počet su, zvyšuje úlohy **náklady**.

Pojďme se podívat na škálování pomocí následující měření latence pro jednotlivé velikosti dávky:

| Latence | Velikost dávky |
| --- | --- |
| 200 ms | dávky 1 000 událostí a nižší |
| 250 ms | 5 000 událostí dávky |
| 300 ms | 10 000 událostí dávky |
| 500 ms | 25 000 událostí dávky |

1. První možnost (**není** zřizování další su). Velikost dávky by mohla být zvýšena na **25 000**. Zvětšení velikosti dávky tímto způsobem umožní úlohu pro zpracování 1 000 000 událostí s 20 souběžných připojení k webové službě Machine Learning (s latencí 500 ms za volání). Proto další latence úlohy Stream Analytics z důvodu požadavky na funkce mínění proti žádosti webové služby Machine Learning by zvýšil z **200 ms** k **500 ms**. Velikost dávky však **nelze** zvýší neomezeně webové služby Machine Learning vyžaduje velikost datové části požadavku 4 MB nebo menší a webových časový limit žádosti o službu po 100 sekund operace.
1. Druhá možnost necháte na 1000, velikost dávky s latencí 200 ms webové služby, každých 20 souběžných připojení k webové službě by se měl události procesu 1000 * 20 * 5 = 100 000 za sekundu. Proto zpracovat 1 000 000 událostí za sekundu, úloha potřebuje 60 su. Ve srovnání s první možnost, úlohy Stream Analytics žádným další žádosti webové služby batch, pak generování vyšší náklady.

Tady je tabulka pro propustnost úlohy Stream Analytics pro různé služby SUs a velikostí dávky (v počtu událostí za sekundu).

| velikost dávky (ML čekací doba) | 500 (200 ms) | 1 000 (200 ms) | 5 000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 služby SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 služby SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 služby SUs** |5,000 |10,000 |40,000 |60,000 |100,000 |
| **18 služby SUs** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24 služby SUs** |10,000 |20,000 |80,000 |120,000 |200 000 |
| **…** |… |… |… |… |… |
| **60 služby SUs** |25,000 |50,000 |200 000 |300,000 |500,000 |

Nyní máte již dobrý přehled o fungování funkce Machine learningu ve službě Stream Analytics. Pravděpodobně také chápete, že úlohy Stream Analytics "pull" data ze zdrojů dat a každý "pull" vrátí služby batch pro úlohu Stream Analytics ke zpracování událostí. Jak tento dopad o přijetí změn model Machine Learning webové žádosti o služby

Za normálních okolností velikost dávky, kterou jsme nastavili pro funkce Machine learningu nebudou přesně dělitelná počet událostí vrácených každou úlohu Stream Analytics "pull". Pokud k tomu dojde, webové služby Machine Learning je volána s "částečné" dávky. Použití částečné dávky se vyhnete bez dalších úloh latence režijní náklady v události sloučení z o přijetí změn na vyžádání.

## <a name="new-function-related-monitoring-metrics"></a>Nové funkce související s monitorovací metriky
V oblasti sledování úloh Stream Analytics byly přidány tři další metriky související funkce. Jsou **požadavky na funkce**, **události funkcí** a **NEÚSPĚŠNÉ požadavky funkce**, jak je znázorněno na následujícím obrázku.

![Škálování Stream Analytics se službou Machine Learning funkce metriky](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "škálování Stream Analytics s metrikami funkce Machine Learning")

Jsou definovány takto:

**POŽADAVKY NA FUNKCE**: Počet požadavků na funkce.

**UDÁLOSTI FUNKCÍ**: Počet událostí v žádosti o funkce.

**NEZDAŘENÉ POŽADAVKY NA FUNKCE**: Počet Nezdařené požadavky na funkce.

## <a name="key-takeaways"></a>Stěžejní zjištění

Pokud chcete škálovat úlohy Stream Analytics s funkcemi Machine Learning, vezměte v úvahu následující faktory:

1. Míra vstupních událostí.
2. Tolerovaný latence pro běžící úlohu Stream Analytics (a tedy velikost dávky žádosti webové služby Machine Learning).
3. Zřízené SUs Stream Analytics a počet žádosti webové služby Machine Learning (Další funkce související náklady).

Plně oddílů dotazu Stream Analytics byla použita jako příklad. V případě potřeby složitějšího dotazu [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) je skvělý prostředek pro získání další pomoc od týmu služby Stream Analytics.

## <a name="next-steps"></a>Další postup
Další informace o službě Stream Analytics najdete v tématu:

* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
