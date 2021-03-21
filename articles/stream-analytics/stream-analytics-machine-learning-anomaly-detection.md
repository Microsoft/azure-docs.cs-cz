---
title: Detekce anomálií v Azure Stream Analytics
description: Tento článek popisuje, jak používat Azure Stream Analytics a Azure Machine Learning společně ke zjištění anomálií.
ms.service: stream-analytics
author: jseb225
ms.author: jeanb
ms.topic: how-to
ms.date: 06/21/2019
ms.openlocfilehash: ec37ea6cbb1c1c6693aab1f6855948d32b85e95b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441189"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detekce anomálií v Azure Stream Analytics

K dispozici v cloudu i v Azure IoT Edge Azure Stream Analytics nabízí integrované možnosti detekce anomálií založené na strojovém učení, které se dají použít k monitorování dvou nejčastěji se vyskytujících se anomálií: dočasné a trvalé. Pomocí funkcí **AnomalyDetection_SpikeAndDip** a **AnomalyDetection_ChangePoint** můžete detekci anomálií provádět přímo v rámci úlohy Stream Analytics.

Modely strojového učení předpokládají jednotnou ukázkovou časovou řadu. Pokud časová řada není jednotná, můžete před vyvoláním detekce anomálií vložit krok agregace s bubnovým oknem.

Operace Machine Learningu v tuto chvíli nepodporují sezónnost trendy ani kovariate korelace.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Detekce anomálií pomocí strojového učení v Azure Stream Analytics

Následující video ukazuje, jak detekovat anomálii v reálném čase pomocí funkcí strojového učení v Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Chování modelu

Obecně se přesnost modelu vylepšuje s dalšími daty v posuvných oknech. Data v zadaném posuvných oknech jsou považována za součást jejich normálního rozsahu hodnot pro daný časový rámec. Model považuje jenom historii událostí přes posuvné okno, aby zkontroloval, jestli je aktuální událost neobvyklé. Při přesunu klouzavého okna se staré hodnoty vyloučí z školení modelu.

Funkce fungují tak, že na základě toho, co ještě viděli, vytvoří určitý normální stav. Odlehlé hodnoty se identifikují porovnáním se standardem, který je v rámci úrovně spolehlivosti stanovený jako normální. Velikost okna by měla být založená na minimálních událostech, které jsou potřebné ke výukě modelu pro normální chování, aby při výskytu anomálie bylo možné ho rozpoznat.

Doba odezvy modelu se zvětšuje s velikostí historie, protože je potřeba ji porovnat s vyšším počtem minulých událostí. Doporučuje se zahrnout jenom potřebný počet událostí pro lepší výkon.

Mezery v časové řadě můžou být výsledkem modelu, který nepřijímá události v určitých okamžicích v čase. Tato situace je zpracována Stream Analytics pomocí logiky imputace. Velikost historie a také časový interval pro stejné posuvné okno se používá k výpočtu průměrné frekvence, s jakou se očekává, že se události dorazí.

[K dispozici je](https://aka.ms/asaanomalygenerator) generátor anomálií, který je možné použít k zakládání služby IoT Hub s daty s různými vzory anomálií. Úlohu ASA je možné nastavit s těmito funkcemi detekce anomálií pro čtení z tohoto centra IoT a zjišťování anomálií.

## <a name="spike-and-dip"></a>Špička a DIP

Dočasné anomálie v datovém proudu událostí časové řady se označují jako špičky a DIP. Špičky a DIP lze monitorovat pomocí operátoru založeného na Machine Learning [AnomalyDetection_SpikeAndDip](/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Příklad anomálií špičky a DIP](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Pokud je druhé špičky menší než první špička v jednom posuvných oknech, vypočítané skóre pro menší špičku se pravděpodobně ve srovnání se zadaným stupněm spolehlivosti nedostatečně významně neshoduje s skóre pro první špičku. Můžete zkusit snížit úroveň spolehlivosti modelu a detekovat takové anomálie. Pokud ale začnete dostávat příliš mnoho výstrah, můžete použít vyšší interval spolehlivosti.

Následující příklad dotazu předpokládá jednotnou vstupní sazbu jedné události za sekundu v posuvných oknech po dvou minutách s historií 120 událostí. Finální příkaz SELECT extrahuje a vypíše skóre a stav anomálie s úrovní spolehlivosti 95%.

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

Trvalé anomálie v datovém proudu událostí v časové řadě jsou změny v distribuci hodnot v datovém proudu událostí, jako jsou například změny úrovně a trendy. V Stream Analytics se tyto anomálie zjišťují pomocí operátoru [AnomalyDetection_ChangePoint](/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) založeného na Machine Learning.

Trvalé změny jsou poslední mnohem delší než špičky a DIP a můžou označovat závažné události. Trvalé změny nejsou obvykle viditelné pro holé oči, ale lze je zjistit pomocí operátoru **AnomalyDetection_ChangePoint** .

Následující obrázek je příkladem změny úrovně:

![Příklad anomálie změny úrovně](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Následující obrázek je příkladem změny trendu:

![Příklad anomálie změny trendu](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Následující příklad dotazu předpokládá jednotnou vstupní sazbu jedné události za sekundu v posuvných oknech po 20 minutách s velikostí historie 1200 událostí. Finální příkaz SELECT extrahuje a vypíše skóre a stav anomálie s úrovní spolehlivosti 80%.

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

## <a name="performance-characteristics"></a>Charakteristiky výkonu

Výkon těchto modelů závisí na velikosti historie, trvání okna, zatížení události a na tom, zda je použito dělení na úrovni funkcí. Tato část pojednává o těchto konfiguracích a obsahuje ukázky, jak tolerovat rychlosti přijímání 1 tisíc, 5K a 10 000 událostí za sekundu.

* **Velikost historie** – tyto modely fungují lineárně s **velikostí historie**. Čím dál je velikost historie, tím déle bude trvat, než se v modelu vyhodnotí nová událost. Je to proto, že modely porovnávají novou událost s každou poslední událostí ve vyrovnávací paměti historie.
* **Doba trvání okna** – **Doba trvání okna** by měla odrážet, jak dlouho trvá přijímání tolika událostí, kolik je určeno velikostí historie. Bez tohoto počtu událostí v okně Azure Stream Analytics by nedošlo ke imputaci chybějících hodnot. Proto je spotřeba procesoru funkcí velikosti historie.
* **Zatížení událostí** – větší **zatížení události**, více práce prováděné modely, které mají dopad na spotřebu procesoru. Úlohu je možné škálovat tak, že ji zpracovatelné paralelně, což předpokládá, že obchodní logika bude používat víc vstupních oddílů.
* Dělení na úrovni **funkcí**  -  **Dělení na úrovni funkcí** se provádí pomocí ```PARTITION BY``` volání funkce detekce anomálií. Tento typ dělení přináší režii, protože stav musí být udržován pro více modelů současně. Dělení na úrovni funkcí se používá ve scénářích, jako je vytváření oddílů na úrovni zařízení.

### <a name="relationship"></a>Relace
Velikost historie, doba trvání okna a celková zátěž události jsou v souvislosti s následujícím způsobem:

windowDuration (v MS) = 1000 * historySize/(celkový počet vstupních událostí za sekundu/počet vstupních oddílů)

Při dělení funkce podle deviceId přidejte "PARTITION BY deviceId" do volání funkce detekce anomálií.

### <a name="observations"></a>Výsledky
Následující tabulka obsahuje pozorování propustnosti pro jeden uzel (6 SU) pro případ bez oddílů:

| Velikost historie (události)    | Doba trvání okna (MS) | Celkový počet událostí vstupu za sekundu |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2 200 |
| 600 | 728 | 1 650 |
| 6 000 | 10 910 | 1 100 |

Následující tabulka obsahuje pozorování propustnosti pro jeden uzel (6 SU) pro rozdělený případ:

| Velikost historie (události) | Doba trvání okna (MS) | Celkový počet událostí vstupu za sekundu | Počet zařízení |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1 091 | 1 100 | 10 |
| 600 | 10 910 | 1 100 | 10 |
| 6 000 | 218 182 | <550 | 10 |
| 60 | 21 819 | 550 | 100 |
| 600 | 218 182 | 550 | 100 |
| 6 000 | 2 181 819 | <550 | 100 |

Vzorový kód pro spuštění výše uvedených konfigurací, které nejsou rozdělené do oddílů, se nachází ve [streamování v úložišti](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) ukázek Azure ve velkém měřítku. Kód vytvoří úlohu Stream Analytics bez vytváření oddílů na úrovni funkcí, která používá centrum událostí jako vstup a výstup. Vstupní zatížení je generováno pomocí testovacích klientů. Každá vstupní událost je dokument 1 KB JSON. Události simulují zařízení IoT odesílající data JSON (až do zařízení 1 tisíc). Velikost historie, doba trvání okna a celková zátěž události se mění ve dvou vstupních oddílech.

> [!Note]
> Pokud chcete přesnější odhad, přizpůsobte si ukázky podle svého scénáře.

### <a name="identifying-bottlenecks"></a>Identifikace kritických bodů
Pomocí podokna metrik v Azure Stream Analytics úlohy můžete identifikovat kritická místa ve vašem kanálu. Zkontrolujte **vstupní/výstupní události** pro propustnost a ["zpoždění vodoznaku"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) nebo **nevyřízené události** , abyste viděli, jestli úloha nepracuje se vstupní sazbou. V případě metrik centra událostí vyhledejte **omezené požadavky** a odpovídajícím způsobem upravte prahové jednotky. V případě Cosmos DB metriky si přečtěte **maximální počet spotřebovaných ru/s na rozsah klíče oddílu** propustnost, abyste zajistili, že rozsahy klíčů oddílu budou jednotně spotřebovány. V případě služby Azure SQL DB Sledujte **protokol IO** a **CPU**.

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)