---
title: Detekce anomálií ve službě Azure Stream Analytics
description: Tento článek popisuje, jak používat Azure Stream Analytics a Azure Machine Learning společně k detekci anomálií.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 706311e2895f311c228b55db971eb88a859530f5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441684"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detekce anomálií ve službě Azure Stream Analytics

K dispozici v cloudu a Azure IoT Edge, Azure Stream Analytics nabízí integrovanou strojového učení na základě anomálií zjišťování, které lze použít k monitorování dvě nejčastěji se vyskytující anomálií: dočasné a trvalé. S **AnomalyDetection_SpikeAndDip** a **AnomalyDetection_ChangePoint** funkce, můžete provádět detekci anomálií přímo ve vaší úlohy Stream Analytics.

Modely strojového učení předpokládají rovnoměrně vzorky časové řady. Pokud časové řady není jednotné, vložíte na krok agregace se aktivační událost pro přeskakující okno před voláním detekce anomálií.

Operace machine learning nepodporují sezónnosti trendů nebo více s různými variantami korelace v tuto chvíli.

## <a name="model-behavior"></a>Chování modelu

Obecně platí jeho přesnost zlepšuje se další data v posuvné okno. Data v zadané posuvné okno je považován za součást své normální rozsah hodnot pro toto časové období. Historie událostí modelu uvažuje pouze přes posuvného okna zkontrolujte, jestli je aktuální událost neobvyklé. Při přesunu posuvné okno, staré hodnoty se vyřadí z trénování modelu.

Funkce pracují tím, že některé normální založené na co se viděli zatím. Odlehlé hodnoty jsou označeny porovnání oproti zavedené normální, v rámci úroveň spolehlivosti. Velikost okna by měla vycházet z minimální události potřebné pro trénování modelu pro běžné chování tak, aby když se objeví anomálie, mohl by být schopen rozpoznat.

Doba odezvy modelu se zvyšuje s velikost historie, protože se musí porovnávat s vyšší počet minulých událostech. Zahrnout pouze nezbytné počet událostí pro zajištění lepšího výkonu se doporučuje.

Mezery v časové řady může být výsledkem modelu není příjem událostí v určitých bodech v čase. Tato situace zařizuje služba Stream Analytics s využitím imputace logiku. Velikost historie, stejně jako doba trvání pro stejný posuvné okno se používá k výpočtu průměrnou rychlost, jakou se očekává doručení událostí.

Generátor anomálií, která je k dispozici [tady](https://aka.ms/asaanomalygenerator) umožňuje kanálu služby Iot Hub s daty s jinou anomálií vzory. Úlohu Azure Stream Analytics můžete nastavit pomocí těchto funkcí detekce anomálií ke čtení z centra Iot Hub a detekci anomálií.

## <a name="spike-and-dip"></a>(Špičky) a vyhrazené IP adresy

Dočasné anomálie v datovém proudu událostí řady času jsou označované jako špičky a poklesy. Provozní špičky a vyhrazené IP adresy je možné monitorovat pomocí operátoru využívajících Machine Learning [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Příklad anomálií ve špičce a vyhrazené IP adresy](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Ve stejném posuvné okno Pokud je menší než první, druhý zásobníku počítaný skóre pro menší zásobníku není pravděpodobně důležité dostatek ve srovnání s skóre pro první zásobníku v rámci hladina zadaný. Můžete vyzkoušet, snížení spolehlivosti modelu k detekci anomálií takové. Ale pokud začnete získat příliš mnoho výstrah, můžete použít vyšší interval spolehlivosti.

Následující příklad dotazu předpokládá jednotné vstupní sazba jednu událost za každou sekundu do posuvného okna 2 s historii událostí na 120. Poslední příkaz SELECT extrahuje a vypíše skóre a anomálií stavu hladina spolehlivosti 95 %.

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

## <a name="change-point"></a>Změnit bod

Trvalé anomálie v datovém proudu událostí řady času jsou změny v distribuci hodnot v datovém proudu událostí, jako jsou změny na úrovni a trendy. Ve službě Stream Analytics tyto anomálie se zjišťují pomocí služby Machine Learning na základě [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) operátor.

Trvalé změny mnohem delší než špičky a poklesy trvat a může znamenat katastrofické události. Trvalé změny se obvykle nezobrazí pouhým okem, ale s můžete zjistit **AnomalyDetection_ChangePoint** operátor.

Na následujícím obrázku je příklad Změna úrovně:

![Příklad Změna úrovně anomálií](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Na následujícím obrázku je příklad změny trendů:

![Příklad trendu změn anomálií](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Následující příklad dotazu předpokládá jednotné vstupní sazba jednu událost za každou sekundu v posuvné okno po 20 minutách se velikost historie událostí 1200. Poslední příkaz SELECT extrahuje a vypíše skóre a anomálií stavu úroveň spolehlivosti 80 %.

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

## <a name="performance-characteristics"></a>Výkonové charakteristiky

Výkon těchto modelů závisí na velikost historie, doba trvání okna, události zatížení, a určuje, zda se používá funkce úrovně dělení. Tato část popisuje tyto konfigurace a obsahuje ukázky, jak udržovat ingestování míry 1 kB, 5 kB a 10 tisíc událostí za sekundu.

* **Velikost historie** – tyto modely provádět lineárně s **velikost historie**. Čím déle v velikost historie, čím déle trvat modely ke stanovení skóre pro nová událost. Je to proto, že modely porovnání novou událost s každým z minulých událostech v historii příkazů.
* **Doba trvání okna** – **doba trvání okna** by měly odrážet, jak dlouho trvá příjem tolik událostí uvedená velikost historie. Bez daný počet událostí v okně by dává Azure Stream Analytics chybějící hodnoty. Využití procesoru, proto je funkce velikost historie.
* **Událost zatížení** – větší **zatížení událostmi**, tím více práce, která se provádí pomocí modely, které má vliv na využití procesoru. Úlohy lze škálovat tak, že jednoduše paralelně zpracovatelné, za předpokladu, že dává smysl pro obchodní logiku používat více vstupních oddíly.
* **Funkce úrovně dělení** - **funkce úrovně dělení** pomocí provádí ```PARTITION BY``` ve volání funkce detekce anomálií. Tento typ dělení přidá režijní náklady, protože stav musí být zachovány pro více modelů ve stejnou dobu. Funkce úrovně dělení se používá v scénáře, jako jsou dělení na úrovni zařízení.

### <a name="relationship"></a>Relace
Velikost historie, doba trvání okna a celkový počet událostí load jsou související s následujícím způsobem:

windowDuration (v ms) = 1 000 * historySize / (celkem vstup událostí za sekundu / počet oddílů vstup)

Když funkci dělení podle ID zařízení, přidejte "ROZDĚLIT podle ID zařízení" volání funkce, detekce anomálií.

### <a name="observations"></a>Poznámky
Následující tabulka obsahuje pozorování propustnosti pro jednotlivé uzly (6 SU) pro případ bez oddílů:

| Velikost historie (události) | Doba trvání okna (ms) | Celkový počet vstupních událostí za sekundu |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

Následující tabulka obsahuje pozorování propustnosti pro jednotlivé uzly (6 SU) pro dělené případ:

| Velikost historie (události) | Doba trvání okna (ms) | Celkový počet vstupních událostí za sekundu | Počet zařízení |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

Ukázkový kód pro spuštění konfigurace bez oddílů výše se nachází v [streamování na škálování úložiště](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) ukázek Azure. Kód vytvoří úlohy stream analytics se žádná funkce úrovně dělení, který používá jako vstup a výstup z centra událostí. Vstupní zatížení je generováno pomocí testovacích klientů. Každá událost vstupu je 1KB dokumentu json. Události simulace zařízení IoT odesílání dat JSON (pro zařízení s až 1 kB). Nad 2 vstupní oddíly jsou nastaveny velikost historie, doba trvání okna a celkový počet událostí zatížení.

> [!Note]
> Pro přesnější odhad upravit ukázky, aby vyhovovala vašemu scénáři.

### <a name="identifying-bottlenecks"></a>Nalezení problémových míst
Použití pokokna metriky v úloze Azure Stream Analytics identifikovat problémová místa ve vašem kanálu. Kontrola **vstupní a výstupní události** propustnost a ["Vodoznak zpoždění"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) nebo **události v Backlogu** zobrazíte, pokud je úloha uchovávání vstupní sazba. Metriky Event Hub, vyhledejte **omezuje požadavky** a odpovídajícím způsobem upravit jednotky prahovou hodnotu. Metriky služby Cosmos DB najdete v tématu **maximální počet spotřebovaných RU/s na rozsah klíče oddílu** pod propustnost pro zajištění rozsahy klíčů oddílů se rovnoměrně spotřebuje. Pro službu Azure SQL DB, sledovat **PROTOKOLOVACÍ** a **procesoru**.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Detekce anomálií pomocí strojového učení v Azure Stream Analytics

Následující video ukazuje, jak detekovat anomálie v reálném čase s využitím funkce machine learningu v Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

