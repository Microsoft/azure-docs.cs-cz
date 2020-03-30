---
title: Detekce anomálií ve službě Azure Stream Analytics
description: Tento článek popisuje, jak používat Azure Stream Analytics a Azure Machine Learning společně ke zjišťování anomálií.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 51b9c827d453eef2e2e75e1aa5222204eaa38d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525528"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detekce anomálií ve službě Azure Stream Analytics

Azure Stream Analytics, který je k dispozici v cloudu i v Azure IoT Edge, nabízí integrované funkce detekce anomálií založené na strojovém učení, které lze použít ke sledování dvou nejčastěji se vyskytujících anomálií: dočasné a trvalé. Pomocí **funkcí AnomalyDetection_SpikeAndDip** a **AnomalyDetection_ChangePoint** můžete provádět detekci anomálií přímo v úloze Služby Stream Analytics.

Modely strojového učení předpokládají rovnoměrně vzorkované časové řady. Pokud časové řady není jednotná, můžete vložit krok agregace s omílání okna před voláním detekce anomálií.

Operace strojového učení v současné době nepodporují trendy sezónnosti ani multivariační korelace.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Detekce anomálií pomocí strojového učení ve službě Azure Stream Analytics

Následující video ukazuje, jak zjistit anomálii v reálném čase pomocí funkcí strojového učení ve službě Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Chování modelu

Obecně platí, že přesnost modelu se zlepšuje s více dat v posuvném okně. Data v zadaném posuvném okně jsou považována za součást normálního rozsahu hodnot pro daný časový rámec. Model bere v úvahu pouze historii událostí přes posuvné okno ke kontrole, pokud aktuální událost je neobvyklé. Při pohybu posuvné okno, staré hodnoty jsou vyřazeny z trénování modelu.

Funkce fungují tak, že stanoví určitý normál na základě toho, co dosud viděli. Odlehlé hodnoty jsou identifikovány porovnáním se zavedeným normálem v rámci úrovně spolehlivosti. Velikost okna by měla být založena na minimální události potřebné k trénování modelu pro normální chování tak, aby při dojde k anomálii, bude schopen rozpoznat.

Doba odezvy modelu se zvětšuje s velikostí historie, protože je třeba porovnat s vyšším počtem minulých událostí. Doporučuje se zahrnout pouze potřebný počet událostí pro lepší výkon.

Mezery v časových řadách mohou být důsledkem toho, že model nepřijímá události v určitých časových bodech. Tato situace je zpracována Stream Analytics pomocí logiky imputace. Velikost historie, stejně jako doba trvání, pro stejné posuvné okno se používá k výpočtu průměrné rychlosti, jakou se očekává, že události dorazí.

Generátor anomálií, [který je k dispozici,](https://aka.ms/asaanomalygenerator) lze použít k navádění centra Iot Hub s daty s různými vzory anomálií. Úloha ASA může být nastavena s těmito funkcemi detekce anomálií číst z tohoto centra Iot Hub a zjistit anomálie.

## <a name="spike-and-dip"></a>Špice a dip

Dočasné anomálie v datovém proudu událostí časové řady se nazývají špičky a poklesy. Špičky a poklesy lze sledovat pomocí operátora založeného na strojovém učení [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Příklad anomálie hrotu a poklesu](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Ve stejném posuvné okno, pokud druhý zásobník je menší než první, vypočítané skóre pro menší špičku pravděpodobně není dostatečně významné ve srovnání s skóre pro první špičku v rámci úroveň spolehlivosti zadané. Můžete zkusit snížit úroveň spolehlivosti modelu k detekci těchto anomálií. Pokud však začnete dostávat příliš mnoho výstrah, můžete použít vyšší interval spolehlivosti.

Následující příklad dotazu předpokládá jednotnou vstupní rychlost jedné události za sekundu v posuvném okně 2 minuty s historií 120 událostí. Konečný příkaz SELECT extrahuje a vypočtou stav skóre a anomálií s úrovní spolehlivosti 95 %.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Bod změny

Trvalé anomálie v datovém proudu událostí časové řady jsou změny v rozložení hodnot v datovém proudu událostí, jako jsou změny úrovně a trendy. V Stream Analytics jsou tyto anomálie detekovány pomocí operátora [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) založeného na strojovém učení.

Trvalé změny trvají mnohem déle než špičky a poklesy a mohou naznačovat katastrofické události. Trvalé změny nejsou obvykle viditelné pouhým okem, ale mohou být detekovány u **AnomalyDetection_ChangePoint** operátora.

Následující obrázek je příkladem změny úrovně:

![Příklad anomálie změny úrovně](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Následující obrázek je příkladem změny trendu:

![Příklad anomálie změny trendu](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Následující příklad dotazu předpokládá jednotnou vstupní rychlost jedné události za sekundu v posuvném okně 20 minut s velikostí historie 1200 událostí. Konečný příkaz SELECT extrahuje a vypočtou stav skóre a anomálií s úrovní spolehlivosti 80 %.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Výkonnostní charakteristiky

Výkon těchto modelů závisí na velikosti historie, trvání okna, zatížení událostí a na tom, zda se používá dělení na úrovni funkce. Tato část popisuje tyto konfigurace a poskytuje ukázky, jak udržet rychlost ingestování událostí 1 kB, 5 kB a 10 kB za sekundu.

* **Velikost historie** - Tyto modely provádějí lineárně s **velikostí historie**. Čím delší je velikost historie, tím déle modely trvat skóre novou událost. Důvodem je, že modely porovnat novou událost s každou z minulých událostí ve vyrovnávací paměti historie.
* **Doba trvání** **okna** - Doba trvání okna by měla odrážet, jak dlouho trvá příjem tolik událostí, jak je určeno velikost historie. Bez tolika událostí v okně azure stream analytics by přitírá chybějící hodnoty. Proto je spotřeba procesoru funkcí velikosti historie.
* **Zatížení událostí** – čím větší je **zatížení událostí**, tím více práce, která se provádí modely, což má vliv na spotřebu procesoru. Úloha může být škálovat tím, že je trapně paralelní, za předpokladu, že má smysl pro obchodní logiku používat více vstupních oddílů.
* **Rozdělení úrovně** - funkce na úrovni funkce ```PARTITION BY``` **se** provádí pomocí v rámci volání funkce detekce anomálií. Tento typ dělení přidá režii, jako stav musí být udržována pro více modelů současně. Dělení na úrovni funkce se používá ve scénářích, jako je dělení na úrovni zařízení.

### <a name="relationship"></a>Relace
Velikost historie, doba trvání okna a celkové zatížení událostí souvisejí následujícím způsobem:

windowDuration (v ms) = 1000 * historySize / (Celkový počet vstupních událostí za sekundu / Počet vstupních oddílů)

Při rozdělení funkce podle deviceId, přidejte "PARTITION BY deviceId" do volání funkce detekce anomálií.

### <a name="observations"></a>Pozorování
Následující tabulka obsahuje pozorování propustnost pro jeden uzel (6 SU) pro případ bez oddílů:

| Velikost historie (události) | Doba trvání okna (ms) | Celkový počet vstupních událostí za sekundu |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2 200 |
| 600 | 728 | 1,650 |
| 6 000 | 10,910 | 1 100 |

Následující tabulka obsahuje pozorování propustnost pro jeden uzel (6 SU) pro oddíly případu:

| Velikost historie (události) | Doba trvání okna (ms) | Celkový počet vstupních událostí za sekundu | Počet zařízení |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1 100 | 10 |
| 600 | 10,910 | 1 100 | 10 |
| 6 000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6 000 | 2,181,819 | <550 | 100 |

Ukázkový kód pro spuštění výše nerozdělených konfigurací je umístěn v [zásobníku datových proudů ve velkém měřítku](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) ukázek Azure. Kód vytvoří úlohu analýzy datového proudu bez dělení na úrovni funkce, která používá Event Hub jako vstup a výstup. Vstupní zatížení je generováno pomocí testovacích klientů. Každá vstupní událost je 1KB json dokumentu. Události simulují zařízení IoT odesílající data JSON (až pro zařízení 1K). Velikost historie, doba trvání okna a celkové zatížení událostí se liší přes 2 vstupní oddíly.

> [!Note]
> Chcete-li získat přesnější odhad, přizpůsobte vzorky tak, aby vyhovovaly vašemu scénáři.

### <a name="identifying-bottlenecks"></a>Identifikace kritických míst
Podokno Metriky v úloze Azure Stream Analytics slouží k identifikaci kritických míst ve vašem kanálu. Zkontrolujte **vstupní a výstupní události** pro propustnost a ["Zpoždění vodoznaku"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) nebo **Backlogged Události** a zjistěte, zda úloha drží krok se vstupní rychlostí. Pro metriky Event Hub **vyhledejte omezené požadavky** a odpovídajícím způsobem upravte jednotky prahových hodnot. Pro metriky Cosmos DB zkontrolujte **Maximální spotřebované RU/s na oddíl rozsah klíčů** v rámci propustnosti zajistit rozsahy klíče oddílu jsou jednotně spotřebované. V Azure SQL DB **monitorujte IO protokolu** a **procesor**.

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

