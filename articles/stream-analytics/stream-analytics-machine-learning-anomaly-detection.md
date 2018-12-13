---
title: Detekce anomálií ve službě Azure Stream Analytics (Náhled)
description: Tento článek popisuje, jak používat Azure Stream Analytics a Azure Machine Learning společně k detekci anomálií.
services: stream-analytics
author: dubansal
ms.author: dubansal
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: df1010be8c9f41684af806885db7587bfcf1c540
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091216"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detekce anomálií v Azure Stream Analytics

> [!IMPORTANT]
> Tato funkce se právě přestanou používat, ale nahradí se nové funkce. Další informace najdete [osm nových funkcí ve službě Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/) blogový příspěvek.

**Anomalydetection, který** operátor se používá ke zjišťování různé typy anomálií v datových proudů událostí. Například pomalá pokles volná paměť nad dlouhou dobu může být indikátorem nevracení paměti nebo může výrazně zvýšit nebo snížit počet žádosti webové služby, které jsou stabilní v rozsahu.  

Operátor anomalydetection, který rozpoznává tři typy anomálií: 

* **Změna úrovně obousměrné**: trvalé zvýšení nebo snížení úrovně hodnoty nahoru a dolů. Tato hodnota se liší od provozní špičky a vyhrazené IP adresy, které jsou změny zajistí okamžité vynucování nebo krátkodobou.  

* **Zpomalit pozitivní Trend**: pomalé zvýšení trend v čase.  

* **Zpomalit negativní Trend**: pomalé pokles trend v čase.  

Při použití operátor anomalydetection, který je nutné zadat **Limit Duration** klauzuli. Tato klauzule určuje že časový interval (jak daleko zpět v historii z aktuální události) by měl být při detekci anomálií. Tento operátor volitelně může být omezen na pouze události, které odpovídají určité vlastnosti nebo podmínku s použitím **při** klauzuli. Tento operátor může také v případě potřeby zpracovat zvýrazňující skupiny událostí samostatně na základě klíče zadaný v **rozdělit podle** klauzuli. Trénování a predikcí dojít nezávisle pro každý oddíl. 

## <a name="syntax-for-anomalydetection-operator"></a>Syntaxe pro operátor anomalydetection, který

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Příklad použití**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Argumenty

* **skalární_výraz** -skalární výraz, přes které se provádí pro detekci anomálií. Povolené hodnoty pro tento parametr obsahovat plovoucí desetinnou čárkou nebo Bigint datových typů, který návratový (skalární) hodnotu single. Výraz se zástupnými znaky **\*** není povolený. Skalární výraz, který nemůže obsahovat další analytických funkcí nebo externí funkce. 

* **partition_by_clause** – `PARTITION BY <partition key>` klauzule rozděluje vzdělávání a odbornou v samostatných oddílech. Jinými slovy, samostatného modelu se použije na hodnotu `<partition key>` a pouze události s touto hodnotou se použije pro vzdělávání a odbornou v takovém modelu. Například následující dotaz železniční a vyhodnocování čtení pro další čtení stejné senzor:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **klauzule limit_duration**  `DURATION(<unit>, <length>)` -Určuje časový interval (jak daleko zpět v historii z aktuální události) by měl být při detekci anomálií. Zobrazit [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) podrobný popis podporované jednotky a jejich zkratkami. 

* **when_clause** -určuje je logická podmínka pro události v výpočet detekce anomálií.

### <a name="return-types"></a>Návratové typy

Operátor anomalydetection, který vrátí záznam obsahující všechny tři výsledky jako výstup. Vlastnosti související s různými typy anomálií detektory jsou:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Chcete-li extrahovat jednotlivé hodnoty mimo tento záznam, použijte **GetRecordPropertyValue** funkce. Příklad:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

Anomálií typu se zjistí, pokud jeden z skóre anomálií překročí mezní hodnotu. Prahová hodnota může být jakékoli číslo s plovoucí desetinnou čárkou > = 0. Prahová hodnota je kompromis mezi citlivosti a spolehlivosti. Nižší prahové hodnotě by například provést zjišťování citlivější na změny a generovat další výstrahy, že vyšší prahová hodnota může být detekce míň citlivými informacemi a větší jistotu ale maskování anomálie některé. Hodnotu přesný práh, závisí na scénáři. Neexistuje žádná horní hranice, ale doporučené rozsah je 3,25 – 5. 

Hodnota 3,25 je znázorněno v příkladu je právě navrhované výchozí bod. Vyladění hodnotu spuštěním operace na datovou sadu a sledujte výstupní hodnota, dokud se nedostanete přípustné prahové hodnoty.

## <a name="anomaly-detection-algorithm"></a>Algoritmus detekce anomálií

* Operátor anomalydetection, který použije **supervize** přístup, ve kterém se nepředpokládá jakýkoli typ distribuce v události. Obecně platí dva modely se zachovají paralelní v daném okamžiku, kdy jeden z nich se používá k vyhodnocení a druhý se trénuje na pozadí. Modely detekce anomálií se trénuje pomocí dat z aktuální datový proud, spíše než pomocí mechanismu out-of-band. Data ukládaná za účelem školení závisí na d velikost okna zadanou uživatelem v rámci parametru Limit Duration. Každý model končí získávání školení podle d 2d za událostí. Doporučuje se mít alespoň 50 událostí v každé okno pro dosažení co nejlepších výsledků. 

* Operátor anomalydetection, který používá **posuvné okno sémantiku** k trénování modelů a skóre události. Což znamená, že všechny události se vyhodnocuje pro anomálií a vrátí se skóre. Skóre je údaj o spolehlivosti této anomálií. 

* Operátor anomalydetection, který poskytuje **opakovatelnosti záruku** stejný vstup vždy vytváří stejný výsledek bez ohledu na to, výstup úlohy počáteční čas. Čas spuštění výstupu úlohy představuje čas, kdy je první událost výstup vytvořený úlohou. Je nastavena podle uživatele na aktuální čas, vlastní hodnotu nebo čas posledního výstupu (Pokud je úloha měla výstup vytvořený dříve). 

### <a name="training-the-models"></a>Trénování modelů 

V průběhu času jsou trénované modely s různými daty. Dávat smysl skóre, dobré pochopit základní mechanismus, pomocí kterého jsou trénované modely. Tady **t<sub>0</sub>**  je **čas spuštění výstupu úlohy** a **d** je **velikost okna** z Limit Duration parametr. Předpokládejme, čas je rozdělena do **směrování velikost d**od `01/01/0001 00:00:00`. Následující postup slouží k natrénování modelu a skóre události:

* Při spuštění úlohy, čte data od času t<sub>0</sub> – 2d.  
* Po uplynutí další směrování, nový model M1 se vytvoří a spustí získávání školení.  
* Při posunutí času o další směrování, nový model M2 se vytvoří a spustí získávání školení.  
* Pokud čas dosáhne t<sub>0</sub>M1 se stane aktivní a spustí jeho skóre získávání výstupu.  
* Další směrování dojde ke třem věcem ve stejnou dobu:  

  * Už nepotřebujete M1 a se zahodí.  
  * M2 byla dostatečně školení, používá se pro vyhodnocení.  
  * Nový model M3 se vytvoří a spustí, získat školení na pozadí.  

* Tento cyklus se opakuje pro každý segment směrování tam, kde je zrušené active vzor, přepněte na paralelní modelu a spusťte trénujete model pro třetí na pozadí. 

Kroky diagramem, vypadat takto: 

![Trénování modelů strojového učení](media/stream-analytics-machine-learning-anomaly-detection/machine-learning-training-model.png)

|**Model** | **Čas zahájení školení** | **Pokud chcete začít používat jeho skóre čas** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* Model M1 spustí školení v 11:20, což je další segment směrování po zahájení úlohy čtení v 11:13. První výstup je vytvořen z M1 v 11:33 po trénováním 13 minut data. 

* Nový model M2 také začne školení v 11:30 upgradujete, ale jeho skóre získat nepoužívá až do 11:40 am, což je po byla vyškolila s 10 minut data. 

* M3 používá stejný vzor jako M2. 

### <a name="scoring-with-the-models"></a>Vyhodnocování s modely 

Na úrovni Machine Learning vypočítá algoritmus detekce anomálií strangeness hodnotu pro všechny příchozí události porovnáním s událostmi v okně historie. Výpočet strangeness se liší pro jednotlivé typy anomálií.  

Pojďme se podívat na výpočet strangeness podrobně (Předpokládejme existuje sada historických windows s událostmi): 

1. **Změna úrovně obousměrné:** založené na okno historie, normální provozní rozsah je vypočítán jako [10th percentil 90. percentil] tedy 10th percentil hodnoty jako dolní mez a 90. percentil hodnoty percentilu jako horní mez. Hodnota strangeness aktuální události se vypočítává jako:  

   - 0, pokud je event_value v běžném provozním rozsahu  
   - Pokud event_value/90th_percentile event_value > 90th_percentile  
   - 10th_percentile/event_value, pokud je event_value < 10th_percentile  

2. **Pomalé pozitivní trend:** trendové linie vedoucí přes hodnoty událostí v okně historie se počítá a operace hledá pozitivní trend v rámci řádku. Hodnota strangeness se vypočítává jako:  

   - Sklon, pokud křivka je pozitivní  
   - 0, jinak 

3. **Pomalé negativní trend:** trendové linie vedoucí přes hodnoty událostí v okně historie se počítá a operace hledá negativní trend v rámci řádku. Hodnota strangeness se vypočítává jako: 

   - Sklon, pokud je záporné sklon  
   - 0, jinak  

Jakmile je vypočítán strangeness hodnotu Příchozí události, je vypočítán na základě hodnoty strangeness martingale hodnotu (najdete v článku [blog o Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) podrobnosti o jak martingale hodnota byla vypočítána). Tato hodnota martingale se vrátí jako hodnocení anomálie. Hodnota martingale zvýší pomalu v reakci na neobvyklé hodnoty, což umožňuje detektor k zajištění robustní sporadické změny a snižuje falešným výstrahám. Obsahuje také užitečné vlastnost: 

Pravděpodobnost [existuje t takové této M<sub>t</sub> > λ] < 1/λ, kde M<sub>t</sub> martingale hodnotu rychlé t a λ je skutečné hodnoty. Například, pokud je výstraha při M<sub>t</sub>> 100 a pravděpodobnosti výskytu falešně pozitivních testů je menší než 1/100.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Pokyny k používání obousměrné úroveň změňte detectoru 

Detektor Změna úrovně obousměrné lze použít ve scénářích, jako je výpadek a obnovení nebo špičce provoz power atd. Však funguje tak, že jakmile model se trénuje pomocí určitá data, jiná změna úrovně je neobvyklé pouze v případě nová hodnota je vyšší než předchozí horní limit (stoupající úroveň změnit velikost písmen) nebo nižší než předchozí dolní mez (klesající úroveň Změňte velikost písmen). Model proto neměli vidět datové hodnoty v rozsahu novou úroveň (směrem nahoru nebo dolů) v okně školení pro ně považuje za neobvyklé. 

Při použití této detektor by měl úvahu tyto body: 

1. Když časové řady náhle uvidí zvýšení nebo vyřadit v hodnotě, operátor anomalydetection, který zjistí. Ale zjišťování vrátit do normálního vyžaduje další plánování. Pokud se v stabilního stavu před anomálií, které lze dosáhnout nastavením interval zjišťování operátor anomalydetection, který nejvíce poloviční délku anomálii časové řady. Tento scénář předpokládá, že minimální doba trvání anomálii se dá odhadnout předem domluvili a nejsou k dispozici dostatek akce v tomto časovém rámci pro trénování modelu dostatečně (nejméně 50 událostí). 

   To je ukázáno v obrázcích 1 a 2 níže pomocí o změnu horní limit (stejná logika platí na nižší limit změnu). Na obou obrázcích tvarový průběh jsou neobvyklé Změna úrovně. Oranžové svislé čáry označují hranice směrování a velikost skoku je stejný jako zadaný v operátor anomalydetection, který intervalu zjišťování. Zelená čáry označují velikost okna školení. Na obrázku 1 velikost skoku je stejné jako čas, pro které má platnost anomálií. Na obrázku 2 je velikost skoku poloviční dobu, pro které anomálii trvá. Ve všech případech je detekovat stoupající změnit, protože model použít pro vyhodnocování se trénuje na normální data. Ale založené na fungování detektor obousměrné Změna úrovně, je nutné vyloučit běžných hodnot z okna školení pro model, který stanoví skóre vrátit do normálního. Na obrázku 1 cvičení vyhodnocení modelu obsahuje některé běžné události, vraťte se na normální nebyl nalezen. Ale na obrázku 2 se na školení pouze zahrnuje neobvyklé část, která zajistí, že se detekuje vrácené výsledky na normální. Nic menšího než polovinu funguje taky ze stejného důvodu, že něco větší skončí včetně hodně normální události. 

   ![AD s délkou rovná anomálií velikost okna](media/stream-analytics-machine-learning-anomaly-detection/windowsize-equal-anomaly-length.png)

   ![Polovinu anomálií délka se rovná AD s velikost okna](media/stream-analytics-machine-learning-anomaly-detection/windowsize-equal-half-anomaly-length.png)

2. V případech, kde nemůže být předpovězen délka anomálii tento detektor funguje nejlépe úsilí. Ale při výběru že užší časový interval omezení trénovací data, která zvýší pravděpodobnost detekce vrátit do normálního. 

3. V následujícím scénáři delší anomálií nebyl rozpoznán jako okno školení již obsahuje anomálií stejnou vysokou hodnotu. 

   ![Detekované anomálie se stejnou velikostí](media/stream-analytics-machine-learning-anomaly-detection/anomalies-with-same-length.png)

## <a name="example-query-to-detect-anomalies"></a>Příklad dotazu k detekci anomálií 

Následující dotaz je možné do výstupního výstrahu, pokud se detekuje anomálie.
Pokud vstupní datový proud není jednotné, může pomoct krok agregace transformovali je do řady jednotným časem. V příkladu se používá AVG, ale konkrétní typ agregace, závisí na uživatelský scénář. Kromě toho pokud časové řady má větší než interval agregace mezery, nejsou k dispozici žádné události v časové řadě detekce anomálií aktivační událost (podle posuvné okno sémantiku). V důsledku toho předpokladů uniformita je přerušeno při obdržení další události. V takových situacích by mělo být zadáno mezer v časové řady. Jedním z možných způsobů je provedení poslední událost v každé okno směrování, jak je znázorněno níže.

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

* Použití šest jednotek streamování pro úlohy. 
* Odesílání událostí alespoň jedné sekundy od sebe.
* Dotaz bez oddílů, který používá operátor anomalydetection, který může vést k výsledkům s latencí výpočtu přibližně 25 ms v průměru.
* Latence zaznamenal dělená dotazem se mírně liší se počet oddílů, je vyšší počet výpočty. Latence se ale o stejné jako v případě bez oddílů srovnatelné celkový počet událostí na všechny oddíly.
* Při čtení dat v reálném čase, velký objem dat přijatých rychle. Zpracování těchto dat je aktuálně pomalejší. Latence v takových scénářích bylo zjištěno zvýšení lineárně s počtem datových bodů v okně spíše než interval okna velikost nebo události. Pokud chcete snížit latenci v reálném čase případy, zvažte použití menší velikost okna. Můžete také začít úlohy můžete od aktuálního času. Několik příkladů latenci v dotazu bez oddílů: 
    - 60 datových bodů v intervalu zjišťování může vést k latenci 10 sekund s propustností 3 MB/s. 
    - Latence na 600 datových bodů, dosáhnout přibližně 80 sekund s propustností 0.4 MB/s.

## <a name="limitations-of-the-anomalydetection-operator"></a>Omezení operátor anomalydetection, který 

* Tento operátor v současné době nepodporuje zjišťování (špičky) a vyhrazené IP adresy. Provozní špičky a vyhrazené IP adresy jsou spontánních nebo krátkodobou změny v časové řady. 

* Tento operátor aktuálně nezpracovává vzory sezónnosti. Sezónnost vzorky se opakující se vzory v datech, například jiný webový provoz chování během víkendu nebo jiné nákupní trendy během černý pátek, které nejsou anomálie, ale očekávaný vzor chování. 

* Tento operátor očekává, že vstupní časové řady být jednotné. Datového proudu událostí je možné provádět jednotné pomocí agregaci přes aktivační událost pro přeskakující nebo kdy se skok provádí okna. Ve scénářích, kde je mezera mezi událostmi vždy menší než časový interval agregace aktivační událost pro přeskakující okno stačí provést jednotné časové řady. Když mezery, může být větší, lze také zopakováním posledních hodnotu pomocí skákajícím oknem vyplnit mezery. 

## <a name="references"></a>Odkazy

* [Detekce anomálií – použití strojového učení pro detekci anomálií v datech časové řady](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Ve službě Machine learning API pro detekci anomálií](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Čas detekce anomálií řady](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

