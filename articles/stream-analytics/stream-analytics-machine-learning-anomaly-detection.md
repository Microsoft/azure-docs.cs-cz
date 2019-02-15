---
title: Detekce anomálií ve službě Azure Stream Analytics (Náhled)
description: Tento článek popisuje, jak používat Azure Stream Analytics a Azure Machine Learning společně k detekci anomálií.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.custom: seodec18
ms.openlocfilehash: 9ea9cc116a13aac2dca9edf8ba86c933310b5198
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269633"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detekce anomálií ve službě Azure Stream Analytics

K dispozici v cloudu a Azure IoT Edge, Azure Stream Analytics nabízí integrovanou strojového učení na základě anomálií zjišťování, které lze použít k monitorování dvě nejčastěji se vyskytující anomálií: dočasné a trvalé. S **AnomalyDetection_SpikeAndDip** a **AnomalyDetection_ChangePoint** funkce, můžete provádět detekci anomálií přímo ve vaší úlohy Stream Analytics.

Modely strojového učení předpokládají rovnoměrně vzorky časové řady. Pokud časové řady není jednotné, vložíte na krok agregace se aktivační událost pro přeskakující okno před voláním detekce anomálií.

Operace machine learning nepodporuje sezónnosti trendů nebo více s různými variantami korelace.

## <a name="model-accuracy-and-performance"></a>Přesnost modelů a výkon

Obecně platí jeho přesnost zlepšuje se další data v posuvné okno. Data v zadané posuvné okno je považován za součást své normální rozsah hodnot pro toto časové období. Historie událostí modelu uvažuje pouze přes posuvného okna zkontrolujte, jestli je aktuální událost neobvyklé. Při přesunu posuvné okno, staré hodnoty se vyřadí z trénování modelu.

Funkce pracují tím, že některé normální založené na co se viděli zatím. Odlehlé hodnoty jsou označeny porovnání oproti zavedené normální, v rámci úroveň spolehlivosti. Velikost okna by měla vycházet z minimální události potřebné pro trénování modelu pro běžné chování tak, aby když se objeví anomálie, mohl by být schopen rozpoznat.

Mějte na paměti, že doba odezvy model zvyšuje velikost historie, protože musí porovnávat s vyšší počet minulých událostech. Zahrnout pouze nezbytné počet událostí pro zajištění lepšího výkonu se doporučuje.

Mezery v časové řady může být výsledkem modelu není příjem událostí v určitých bodech v čase. Tato situace zařizuje služba Stream Analytics s využitím imputace. Velikost historie, stejně jako doba trvání pro stejný posuvné okno se používá k výpočtu průměrnou rychlost, jakou se očekává doručení událostí.

## <a name="spike-and-dip"></a>(Špičky) a vyhrazené IP adresy

Dočasné anomálie v datovém proudu událostí řady času jsou označované jako špičky a poklesy. Provozní špičky a vyhrazené IP adresy je možné monitorovat pomocí operátoru využívajících Machine Learning [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Příklad anomálií ve špičce a vyhrazené IP adresy](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Ve stejném posuvné okno Pokud je menší než první, druhý zásobníku počítaný skóre pro menší zásobníku není pravděpodobně důležité dostatek ve srovnání s skóre pro první zásobníku v rámci hladina zadaný. Můžete zkusit snížení nastavení úrovně modelu důvěru k zachytávání těchto anomálie. Ale pokud začnete získat příliš mnoho výstrah, můžete použít vyšší interval spolehlivosti.

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

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

