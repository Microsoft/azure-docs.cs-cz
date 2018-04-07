---
title: Detekce anomálií v Azure Stream Analytics (Preview)
description: Tento článek popisuje, jak používat Azure Stream Analytics a Azure Machine Learning společně k zjišťovat anomálie.
services: stream-analytics
author: dubansal
ms.author: dubansal
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: cda5c26d4256720a8cf9af0e9abd604c979422a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detekce anomálií v Azure Stream Analytics

> [!IMPORTANT]
> Tato funkce je ve verzi preview, není doporučujeme používat s produkčním prostředí.

**AnomalyDetection** operátor slouží ke zjištění různých typů anomálie v datových proudů událostí. Například může být pomalé poklesu volná paměť nad dlouhou dobu naznačuje výslednou nevrácené paměti nebo počet žádosti webové služby, které jsou v rozsahu stabilní může výrazně zvýšit nebo snížit.  

Operátor AnomalyDetection detekuje tři typy anomálií: 

* **Změna úrovně obousměrný**: dlouhodobě zvětšit nebo zmenšit na úrovni hodnot směrem nahoru a dolů. Tato hodnota se liší od špičky a vyhrazené IP adresy, které jsou zajistí okamžité vynucování nebo krátkodobou změny.  

* **Pomalé pozitivní Trend**: na pomalých růst trend v čase.  

* **Pomalé negativní Trend**: pomalé poklesu trend v čase.  

Při použití operátoru AnomalyDetection, je nutné zadat **Limit Duration** klauzule. Tuto klauzuli Určuje že časový interval (jak daleko zpět v historii z aktuální událost) považovat za při zjišťování anomálií. Tento operátor. volitelně může být omezen na jenom události, které odpovídají určité vlastnosti nebo podmínku pomocí **při** klauzule. Tento operátor. volitelně může zpracovat skupiny událostí odděleně podle je klíč zadaný v **oddílu pomocí** klauzule. Školení a předpovědi dojít nezávisle pro každý oddíl. 

## <a name="syntax-for-anomalydetection-operator"></a>Syntaxe pro AnomalyDetection – operátor

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Příklad použití**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Argumenty

* **skalární_výraz** -skalární výraz, za které se provádí detekce anomálií. Povolené hodnoty pro tento parametr zahrnout Float nebo Bigint datové typy to návratový jednu hodnotu (skalární). Výraz se zástupnými znaky **\*** není povolen. Skalární výraz, který nemůže obsahovat jiné analytické funkce nebo externí funkce. 

* **partition_by_clause** – `PARTITION BY <partition key>` klauzule rozděluje učení a školení napříč samostatné oddíly. Jinými slovy, samostatného modelu se použije na hodnotu `<partition key>` a jenom události s hodnotou, kterou by se používat pro učení a školení v tomto modelu. Například následující dotaz vlacích a skóre a čtení proti jiných odečty stejné senzoru pouze:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **klauzule limit_duration** `DURATION(<unit>, <length>)` -Určuje časový interval (jak daleko zpět v historii z aktuální událost) považovat za při zjišťování anomálií. V tématu [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) podrobný popis podporované jednotky a jejich zkratky. 

* **when_clause** -určuje boolean podmínku pro události za v výpočet detekce anomálií.

### <a name="return-types"></a>Návratové typy

Operátor AnomalyDetection vrátí záznam obsahující všechny tři skóre jako výstup. Vlastnosti související s různými typy anomálií detektory jsou:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Chcete-li extrahovat jednotlivé hodnoty mimo záznam, použijte **GetRecordPropertyValue** funkce. Příklad:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

Anomálií typu je zjištěna, když jeden skóre anomálií překračuje prahovou hodnotu. Prahová hodnota může být jakékoli číslo s plovoucí desetinnou čárkou > = 0. Prahová hodnota je kompromis mezi velkých a malých písmen a spolehlivosti. Například by nižší prahové hodnotě zkontrolujte detekce více citlivé na změny a generovat více výstrah, zatímco vyšší prahová hodnota může zvýšit detekce méně citlivou a větší jistotu, ale některé anomálií maskování. Přesný prahová hodnota používat závisí na scénáři. Neexistuje žádné horní limit, ale je doporučené rozsah 3,25 5. 

## <a name="anomaly-detection-algorithm"></a>Algoritmus detekce anomálií

* Operátor AnomalyDetection používá **supervize** přístup, kde se nepředpokládá jakýkoli typ distribuce v události. Obecně platí dva modely jsou zachována ve paralelní v každém okamžiku, kdy jeden z nich se používá pro výpočet skóre a dalších je trénink na pozadí. Modely detekce anomálií probíhá Trénink pomocí dat z aktuální datového proudu, nikoli pomocí mechanismus out-of-band. Množství dat používá pro školení závisí na d velikost okna, zadanou uživatelem v rámci parametr Limit Duration. Každý model nakonec získá o získávání cvičení podle d 2d za události. Doporučuje se mít alespoň 50 události v každé okna pro dosažení co nejlepších výsledků. 

* Používá operátor AnomalyDetection **posuvné okno sémantiku** ke cvičení modely a skóre události. Což znamená, že všechny události vyhodnotí anomálií a skóre je vrácen. Skóre udává, že anomálií úroveň spolehlivosti. 

* Poskytuje AnomalyDetection operátor **opakovatelnosti záruku** stejné vstupní vždy vytváří stejný skóre bez ohledu na výstup úlohy počáteční čas. Čas spuštění úlohy výstupu představuje čas, kdy je první událost výstupu produkovaný úlohy. Je nastavena podle uživateli aktuální čas, na vlastní hodnotu nebo čas posledního výstup (Pokud je úloha měl dříve vytvořeného výstup). 

### <a name="training-the-models"></a>Cvičení modely 

V průběhu času modely probíhá Trénink s odlišnými daty. Chcete-li smysl skóre, pomáhá pochopit základní mechanismus, pomocí kterého probíhá Trénink modely. Zde **t<sub>0</sub>**  je **výstup úlohy počáteční čas** a **d** je **velikost okna** z Limit Duration parametr. Předpokládejme, který čas je rozdělená do **směrování velikost d**, počínaje okamžikem `01/01/0001 00:00:00`. Následující postup slouží k natrénování modelu a skóre události:

* Po spuštění úlohy přečte data od času t<sub>0</sub> – 2d.  
* Když čas dosáhne další směrování, nový model M1 se vytvoří a spustí získávání cvičení.  
* Pokud čas přejde podle další směrování, nový model M2 se vytvoří a spustí získávání cvičení.  
* Pokud čas dosáhne t<sub>0</sub>, M1 přišla aktivní a jeho skóre spustí získávání výstupem.  
* V dalším místě směrování tři věci nastat současně:  

  * Již není potřeba M1 a bude ignorován.  
  * M2 má dostatečně cvičena, použije se pro vyhodnocování.  
  * Nový model M3 se vytvoří a spustí získávání trénink na pozadí.  

* Tento cyklus opakuje pro každý směrování, kde je zrušené active modelu, přepněte do paralelní modelu a spustit cvičení třetí modelu na pozadí. 

Kroky diagramem, vypadat takto: 

![Školení modely](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Model** | **Čas zahájení školení** | **Čas spuštění pomocí jeho skóre** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* Model M1 spustí školení v 11:20 am, což je další směrování, po spuštění úlohy čtení v 11:13 am. První výsledek z M1 v 11:33 am po školení s 13 minut data. 

* Nový model M2 také spustí školení v 11:30 am, ale jeho skóre získat nepoužívá až 11:40 am, což je po byl vyzkoušen s 10 minut data. 

* M3 následující stejné jako M2. 

### <a name="scoring-with-the-models"></a>Vyhodnocování s modely 

Algoritmus detekce anomálií na úrovni Machine Learning, vypočítá hodnotu strangeness pro všechny příchozí události porovnáním s událostmi v okně historie. Výpočet strangeness se liší pro jednotlivé typy anomálií.  

Pojďme si strangeness výpočet podrobně (předpokládá existuje sada historických windows s událostmi): 

1. **Změna úrovně obousměrný:** založená na okno historie, normální pracovní rozsah se vypočítá jako [10th percentilu, 90. percentil] tedy 10 percentilu hodnotu jako hodnota percentilu dolní mez a 90th jako horní mez. Hodnotu strangeness pro aktuální událost je vypočítán jako:  

   - 0, pokud je event_value v běžném provozním rozsahu  
   - Pokud event_value/90th_percentile event_value > 90th_percentile  
   - 10th_percentile/event_value, pokud je event_value < 10th_percentile  

2. **Pomalé pozitivní trend:** se počítá trendu přes hodnoty události v okně historie a podíváme pro pozitivní trend v řádku. Strangeness hodnota je vypočítána jako:  

   - Sklon, pokud je kladné sklon  
   - 0, jinak hodnota 

1. **Pomalé negativní trend:** se počítá trendu přes hodnoty události v okně historie a budeme hledat negativní trend v řádku. Strangeness hodnota je vypočítána jako: 

   - Sklon, pokud sklon je záporná.  
   - 0, jinak hodnota  

Jakmile hodnota strangeness pro příchozí události je vypočítána, martingale hodnota se vypočítá podle hodnota strangeness (najdete v článku [blog Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) podrobnosti o tom, jak martingale hodnota je vypočítána). Tato hodnota martingale je retuned jako skóre anomálií. Hodnota martingale zvyšuje pomalu v reakci na neobvyklé hodnoty, což umožňuje detektor zůstat robustní na výskyt občasný změny a snižuje false výstrahy. Je také užitečné vlastnost: 

Pravděpodobnost [existuje t takové této M<sub>t</sub> > λ] < 1 nebo λ, kde M<sub>t</sub> je hodnota martingale v rychlých t a λ je skutečné hodnoty. Například, pokud při dáme M<sub>t</sub>> 100 a potom pravděpodobnost falešně pozitivních je menší než 1/100.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Pokyny pro použití úrovně obousměrný změnit detektor 

Změna úrovně obousměrný detektor lze použít ve scénářích, například power výpadku a obnovení nebo expresní hodinu provoz atd. Ale funguje tak, že model je trénink na některá data, další změnou úrovně je neobvyklé Pokud nová hodnota je vyšší než předchozí horní limit (zvýšení úrovně velká písmena) nebo nižší než předchozí dolní limit (klesající úroveň velká písmena). Proto byste neměli vidět model data hodnoty v rozsahu novou úroveň (směrem nahoru nebo dolů) v okně jeho školení, aby se dalo považovat za neobvyklé. 

Následující body považovat při použití této detektor: 

1. Když časové řady najednou uvidí zvětšit nebo vyřadit v hodnotě, operátor AnomalyDetection zjistí ho. Ale zjišťování návratu do normální vyžaduje další plánování. Pokud během stabilního stavu před anomálií, které lze dosáhnout nastavení operátor AnomalyDetection detekce okno na maximálně poloviční délka anomálií se časové řady. Tento scénář předpokládá, že se dá odhadnout minimální trvání anomálií dopředu a nejsou k dispozici dostatek události v tomto časovém rámci pro trénování modelu dostatečně (alespoň 50 událostí). 

   Můžete se podívat na obrázcích 1 a 2 níže pomocí o změnu horní limit (stejná logika platí pro nižší limit změny). Na obou obrázcích jsou tvarový průběh o neobvyklé změnu úrovně. Svislé čáry oranžové označují směrování hranice a velikost skoku je stejný jako okno detekce zadaný v operátoru AnomalyDetection. Zelená řádky označují velikost okna školení. Velikost skoku na obrázku 1 je stejný jako doba, kterou vydrží anomálií. Na obrázku 2 je velikost skoku poloviny doby, pro kterou anomálií platnost. Ve všech případech je zjištěna vzestupnou změnu protože model používá pro vyhodnocování naučil na normální data. Ale podle toho, jak funguje detektor Změna úrovně obousměrný, jsme musí vyloučit normální hodnoty z okna školení použít pro model, který skóre návratu do normální. Na obrázku 1 školení vyhodnocování model obsahuje některé běžné události, vraťte se na normální nebyl nalezen. Ale na obrázku 2 zahrnuje školení jenom neobvyklé části, které zajišťuje rozpoznání návratu do normální. Nic menší než polovinu funguje i pro ze stejného důvodu, že něco větší dojdete včetně bit normální událostí. 

   ![AD s délkou rovna anomálií velikost okna](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![AD s velikost okna rovná polovině anomálií délky](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. V případech, kdy nelze předpovědět délka anomálií tento detektor funguje nejlépe úsilí. Však při výběru že užší časový interval omezení jsou Cvičná data, která by zvýšit pravděpodobnost detekce návratu do normální. 

3. V následujícím scénáři nebyla zjištěna delší anomálií jako okno školení již obsahuje anomálií stejnou vysokou hodnotu. 

   ![Anomálie se stejnou velikostí](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>Ukázkový dotaz zjišťovat anomálie 

Následující dotaz slouží k vypsání výstrahu v případě zjištění anomálií.
Pokud vstupní datový proud není uniform, může pomoci krok agregace transformují je na uniform časové řady. V příkladu používá průměr ale specifický typ agregace závisí na scénáři uživatele. Kromě toho časové řady má větší než interval agregace mezery, nejsou k dispozici žádné události časové řady detekce anomálií aktivační události (podle posuvné okno sémantiku). V důsledku toho se předpokládá jednotnost je poškozená po příchodu následující události. V takových případech by mělo být zadáno mezer v časové řady. Jeden z možných přístupů je převést na poslední událost do všech oken směrování, jak je uvedeno níže.

```sql
    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25
```

## <a name="performance-guidance"></a>Průvodce výkonem

* Použijte pro úlohy šest jednotek streamování. 
* Odesílání událostí alespoň jedna sekunda od sebe.
* Dotaz bez oddílů, který používá operátor AnomalyDetection může vytvářet výsledky v průměru s latencí výpočtu o 25 ms.
* Latence zasáhla oddílů dotazu se mírně liší s počet oddílů, jako počet výpočty je vyšší. Latence je však o stejný jako bez oddílů případ porovnatelný z hlediska celkový počet událostí pro všechny oddíly.
* Při čtení dat v reálném čase, velké množství dat požity rychle. Zpracování tato data je aktuálně nižší. Latence v takových případech byl nalezen lineárně zvyšte počet datových bodů v okně spíše než interval okna velikost nebo událostí. Chcete-li snížit latenci v reálném čase případech, doporučujeme použít menší velikost okna. Případně zvažte úlohu od aktuální čas. Několik příkladů latence v dotazu bez oddílů: 
    - 60 datových bodů v okně detekce může mít za následek latence 10 sekund s propustností 3 MB/s. 
    - Na 600 datových bodů latence dosáhnout přibližně 80 sekund s propustností 0,4 MB/s.

## <a name="limitations-of-the-anomalydetection-operator"></a>Omezení AnomalyDetection operátor 

* Tento operátor aktuálně nepodporuje zjišťování Špička a vyhrazené IP adresy. Špičky a vyhrazené IP adresy jsou spontánních nebo krátkodobou změn v časové řady. 

* Tento operátor aktuálně zpracovává vzory sezónnosti. Vzory sezónnosti jsou opakovaných vzorů v datech, například o jiných webových přenosů chování během víkendu nebo jiné nákupní trendy během černé pátek, které nejsou anomálií, ale očekávané vzor v chování. 

* Tento operátor očekává být uniform vstupní časové řady. Datového proudu událostí můžete provedeny uniform agregování přes přeskakující nebo posílání okno. Ve scénářích, kdy mezery mezi událostmi vždy menší než okno agregace přeskakující okno stačí aby uniform časové řady. Při mezera může být větší, může být vyplněna mezer opakováním poslední hodnotu pomocí skákající okno. 

## <a name="references"></a>Odkazy

* [Detekce anomálií – pomocí strojového učení zjišťovat anomálie v časových řad dat](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Strojového učení detekce anomálií rozhraní API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Detekce anomálií časové řady](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

