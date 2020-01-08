---
title: Běžné vzory dotazů v Azure Stream Analytics
description: Tento článek popisuje řadu běžných vzorů a návrhů dotazů, které jsou užitečné v Azure Stream Analytics úlohách.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 61f9e128fa9299a743012e18882fe32591fdd3f0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369945"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Příklady dotazů pro běžné vzorce použití Stream Analytics

Dotazy v Azure Stream Analytics jsou vyjádřeny v dotazovacím jazyce podobném SQL. Jazykové konstrukce jsou zdokumentovány v referenční příručce [jazyka Stream Analytics dotazů](/stream-analytics-query/stream-analytics-query-language-reference) . 

Návrh dotazu může vyjádřit jednoduchou předávací logiku pro přesun dat událostí z jednoho vstupního datového proudu do výstupního úložiště dat, nebo může provádět bohatou porovnávání vzorů a dočasnou analýzu pro výpočet agregovaných hodnot v různých časových oknech, jako v [sestavení řešení IoT pomocí průvodce Stream Analytics](stream-analytics-build-an-iot-solution-using-stream-analytics.md) . K kombinování událostí streamování můžete spojit data z několika vstupů a můžete provádět vyhledávání na základě statických referenčních dat a rozšířit hodnoty událostí. Můžete také zapisovat data do více výstupů.

Tento článek popisuje řešení několika běžných vzorů dotazů založených na scénářích reálného světa.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Práce s komplexními datovými typy ve formátu JSON a AVRO

Azure Stream Analytics podporuje zpracování událostí v datových formátech CSV, JSON a Avro.

JSON a Avro mohou obsahovat komplexní typy, jako jsou například vnořené objekty (záznamy) nebo pole. Další informace o práci s těmito komplexními datovými typy najdete v článku [Analýza JSON a data Avro](stream-analytics-parsing-json.md) .

## <a name="query-example-convert-data-types"></a>Příklad dotazu: Převod datových typů

**Popis**: Definujte typy vlastností ve vstupním datovém proudu. Například váha automobilu přichází na vstupní datový proud jako řetězce a musí být převedena na **int** , aby bylo možné provést **součet**.

**Vstup**:

| Vytvoření | Time | Hmotnost |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000 Z |"2000" |

**Výstup**:

| Vytvoření | Hmotnost |
| --- | --- |
| Honda |3000 |

**Řešení:**

```SQL
    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Vysvětlení**: použijte příkaz **cast** v poli **váha** k určení jeho datového typu. Seznam podporovaných datových typů najdete v [datových typech (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Příklad dotazu: použití LIKE/NOT jako pro porovnávání vzorů

**Popis**: Ověřte, zda hodnota pole na události odpovídá určitému vzoru.
Například ověřte, že výsledek vrátí sady licenses, které začínají a a končí na 9.

**Vstup**:

| Vytvoření | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Výstup**:

| Vytvoření | LicensePlate | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Řešení:**

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**Vysvětlení**: k zkontrolování hodnoty pole **LicensePlate** použijte příkaz **Like** . Měl by začínat písmenem A a pak obsahovat libovolný řetězec nula nebo více znaků a pak končit číslem 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Příklad dotazu: zadejte logiku pro různé případy nebo hodnoty (příkazy CASE).

**Popis**: zadejte pro pole jiný výpočet na základě konkrétního kritéria. Zadejte například popis řetězce pro to, kolik automobilů stejné provede, se speciálním případem pro 1.

**Vstup**:

| Vytvoření | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Výstup**:

| CarsPassed | Time |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000 Z |
| 2 Toyota |2015-01-01T00:00:10.0000000 Z |

**Řešení:**

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS AsaTime
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Vysvětlení**: výraz **case** porovnává výraz se sadou jednoduchých výrazů pro určení výsledku. V tomto příkladu vozidlo prochází pomocí počtu 1, který vrátil jiný popis řetězce než vozidlo, s jiným počtem než 1.

## <a name="query-example-send-data-to-multiple-outputs"></a>Příklad dotazu: odeslání dat do více výstupů

**Popis**: odešlete data do více výstupních cílů z jedné úlohy. Můžete například analyzovat data pro výstrahu na základě prahové hodnoty a archivovat všechny události do úložiště objektů BLOB.

**Vstup**:

| Vytvoření | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Output1**:

| Vytvoření | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Output2**:

| Vytvoření | Time | Počet |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000 Z |3 |

**Řešení:**

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp() AS AsaTime,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3
```

**Vysvětlení**: klauzule **into** určuje Stream Analytics, které výstupy mají zapisovat data z tohoto příkazu. První dotaz je průchozí data přijatá do výstupu s názvem **ArchiveOutput**. Druhý dotaz provede několik jednoduchých agregací a filtrování a pošle výsledky do systému s výstrahami pro příjem dat **AlertOutput**.

Všimněte si, že můžete také znovu použít výsledky běžných výrazů tabulek (CTEs) (například **s** příkazy) ve více příkazech Output. Tato možnost má přidané výhody otevření menšího počtu čtenářů ke vstupnímu zdroji.

Příklad: 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>Příklad dotazu: počet jedinečných hodnot

**Popis**: spočítá počet jedinečných hodnot polí, které se zobrazí v datovém proudu v časovém intervalu. Například kolik jedinečných vozidel předávaných přes telefonní stánku v okně 2 sekundy?

**Vstup**:

| Vytvoření | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Výstup:**

| CountMake | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1\. místo |2015-01-01T00:00:04.000 Z |

**Řešení:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS AsaTIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Vysvětlení:** 
**Count (DISTINCT)** vrátí počet jedinečných hodnot ve sloupci **zpřístupnit** v časovém intervalu.

## <a name="query-example-determine-if-a-value-has-changed"></a>Příklad dotazu: určení, zda došlo ke změně hodnoty

**Popis**: Podívejte se na předchozí hodnotu, abyste zjistili, jestli se liší od aktuální hodnoty. Například je předchozí automobil na cestách linky stejný jako aktuální automobil?

**Vstup**:

| Vytvoření | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |

**Výstup**:

| Vytvoření | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000 Z |

**Řešení:**

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**Vysvětlení**: pomocí **prodlevy** můžete zobrazit vstupní datový proud jednu událost zpátky a **získat hodnotu.** Pak ji porovnejte s **hodnotou pro** aktuální událost a výstupem události, pokud se liší.

## <a name="query-example-find-the-first-event-in-a-window"></a>Příklad dotazu: vyhledá první událost v okně.

**Popis**: vyhledá první auto v intervalu 10 minut.

**Vstup**:

| LicensePlate | Vytvoření | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015 – 07 – 27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015 – 07 – 27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015 – 07 – 27T00:05:01.0000000 Z |
| YHN 6970 |Toyota |2015 – 07 – 27T00:06:00.0000000 Z |
| VFE 1616 |Toyota |2015 – 07 – 27T00:09:31.0000000 Z |
| QYF 9358 |Honda |2015 – 07 – 27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015 – 07 – 27T00:13:45.0000000 Z |

**Výstup**:

| LicensePlate | Vytvoření | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015 – 07 – 27T00:00:05.0000000 Z |
| QYF 9358 |Honda |2015 – 07 – 27T00:12:02.0000000 Z |

**Řešení:**

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

Teď tento problém Navedeme a vyhledáme první auto z konkrétního příběhu v intervalu 10 minut.

| LicensePlate | Vytvoření | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015 – 07 – 27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015 – 07 – 27T00:02:17.0000000 Z |
| YHN 6970 |Toyota |2015 – 07 – 27T00:06:00.0000000 Z |
| QYF 9358 |Honda |2015 – 07 – 27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015 – 07 – 27T00:13:45.0000000 Z |

**Řešení:**

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>Příklad dotazu: najde poslední událost v okně.

**Popis**: vyhledá poslední auto v intervalu 10 minut.

**Vstup**:

| LicensePlate | Vytvoření | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015 – 07 – 27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015 – 07 – 27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015 – 07 – 27T00:05:01.0000000 Z |
| YHN 6970 |Toyota |2015 – 07 – 27T00:06:00.0000000 Z |
| VFE 1616 |Toyota |2015 – 07 – 27T00:09:31.0000000 Z |
| QYF 9358 |Honda |2015 – 07 – 27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015 – 07 – 27T00:13:45.0000000 Z |

**Výstup**:

| LicensePlate | Vytvoření | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015 – 07 – 27T00:09:31.0000000 Z |
| MDR 6128 |BMW |2015 – 07 – 27T00:13:45.0000000 Z |

**Řešení:**

```SQL
    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**Vysvětlení**: dotaz obsahuje dva kroky. První z nich najde poslední časové razítko v oknech o 10 minutách. Druhý krok spojí výsledky prvního dotazu s původním datovým proudem, aby bylo možné najít události, které se shodují s posledními časovými razítky v jednotlivých oknech. 

## <a name="query-example-locate-correlated-events-in-a-stream"></a>Příklad dotazu: Vyhledání korelačních událostí v datovém proudu

**Popis**: vyhledá korelační události v datovém proudu. Můžete mít například 2 po sobě jdoucí automobily ze stejné, které vstoupily do linky za posledních 90 sekund?

**Vstup**:

| Vytvoření | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**Výstup**:

| Vytvoření | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000 Z |

**Řešení:**

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**Vysvětlení**: pomocí **prodlevy** můžete zobrazit vstupní datový proud jednu událost zpátky a **získat hodnotu.** Porovná ho s **hodnotou v** aktuální události a pak vypíše výstup události, pokud jsou stejné. Pomocí **prodlevy** můžete také získat data o předchozím automobilu.

## <a name="query-example-detect-the-duration-between-events"></a>Příklad dotazu: zjištění trvání mezi událostmi

**Popis**: Najděte dobu trvání dané události. Například s ohledem na webovou navštívených určete čas strávený na funkci.

**Vstup**:  

| Uživatel | Funkce | Událost | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Začátek |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |Konec |2015-01-01T00:00:08.0000000 Z |

**Výstup**:  

| Uživatel | Funkce | Délka |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Řešení:**

```SQL
    SELECT
        [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**Vysvětlení**: pomocí **Poslední** funkce načte poslední hodnotu **času** při **spuštění**typu události. **Poslední** funkce používá **oddíl podle [user]** k označení toho, že výsledek je vypočítán na jedinečného uživatele. Dotaz má maximální hodnotu 1 hodiny pro časový rozdíl mezi událostmi **spuštění** a **zastavení** , ale dá se nakonfigurovat podle potřeby **(limit trvání (Hour, 1)** .

## <a name="query-example-detect-the-duration-of-a-condition"></a>Příklad dotazu: zjištění doby trvání podmínky
**Popis**: Zjistěte, jak dlouho vznikla podmínka.
Předpokládejme například, že chyba byla způsobena tím, že všechna auta mají nesprávnou váhu (nad 20 000 libry) a že se musí vypočítat doba trvání této chyby.

**Vstup**:

| Vytvoření | Time | Hmotnost |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000 Z |25000 |
| Honda |2015-01-01T00:00:03.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000 Z |25000 |
| Honda |2015-01-01T00:00:05.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000 Z |25000 |
| Honda |2015-01-01T00:00:07.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000 Z |2000 |

**Výstup**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

**Řešení:**

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**Vysvětlení**: pomocí **prodlevy** můžete zobrazit vstupní datový proud 24 hodin a vyhledat instance, kde **StartFault** a **StopFault** jsou rozloženy o váhu < 20000.

## <a name="query-example-fill-missing-values"></a>Příklad dotazu: vyplnění chybějících hodnot

**Popis**: pro datový proud událostí, které mají chybějící hodnoty, vytvoří datový proud událostí s pravidelnými intervaly. Vygenerujte například událost každých 5 sekund, která hlásí poslední zjištěný datový bod.

**Vstup**:

| t | hodnota |
| --- | --- |
| "2014-01-01T06:01:00" |1\. místo |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Výstup (prvních 10 řádků)** :

| windowend | lastevent. t | lastevent. Value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000 Z |2014-01-01T14:01:00.000 Z |1\. místo |
| 2014-01-01T14:01:05.000 Z |2014-01-01T14:01:05.000 Z |2 |
| 2014-01-01T14:01:10.000 Z |2014-01-01T14:01:10.000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000 Z |2014-01-01T14:01:30.000 Z |5 |
| 2014-01-01T14:01:35.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:45.000 Z |2014-01-01T14:01:35.000 Z |6 |

**Řešení:**

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Vysvětlení**: Tento dotaz generuje události každých 5 sekund a vypíše poslední událost, která byla dříve přijata. Doba trvání [okna skákající](/stream-analytics-query/hopping-window-azure-stream-analytics) určuje, jak daleko se má dotaz najít poslední událost (v tomto příkladu je to 300 sekund).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Příklad dotazu: korelace dvou typů událostí v rámci stejného datového proudu

**Popis**: někdy je potřeba výstrahy generovat na základě několika typů událostí, ke kterým došlo v určitém časovém rozsahu. Například ve scénáři IoT pro domovské trouby musí být vygenerována výstraha, když je teplota ventilátoru menší než 40 a maximální výkon během posledních 3 minut je menší než 10.

**Vstup**:

| time | deviceId | senzor | hodnota |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | názvem |120 |
| "2018-01-01T16:01:00" | "Oven1" | Vypněte |15 |
| "2018-01-01T16:02:00" | "Oven1" | názvem |100 |
| "2018-01-01T16:02:00" | "Oven1" | Vypněte |15 |
| "2018-01-01T16:03:00" | "Oven1" | názvem |70 |
| "2018-01-01T16:03:00" | "Oven1" | Vypněte |15 |
| "2018-01-01T16:04:00" | "Oven1" | názvem |50 |
| "2018-01-01T16:04:00" | "Oven1" | Vypněte |15 |
| "2018-01-01T16:05:00" | "Oven1" | názvem |30 |
| "2018-01-01T16:05:00" | "Oven1" | Vypněte |8 |
| "2018-01-01T16:06:00" | "Oven1" | názvem |20 |
| "2018-01-01T16:06:00" | "Oven1" | Vypněte |8 |
| "2018-01-01T16:07:00" | "Oven1" | názvem |20 |
| "2018-01-01T16:07:00" | "Oven1" | Vypněte |8 |
| "2018-01-01T16:08:00" | "Oven1" | názvem |20 |
| "2018-01-01T16:08:00" | "Oven1" | Vypněte |8 |

**Výstup**:

| eventTime | deviceId | temp | Zadaná hodnota alertmessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Elementy pro ohřev krátkých okruhů" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Elementy pro ohřev krátkých okruhů" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Elementy pro ohřev krátkých okruhů" |15 |

**Řešení:**

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**Vysvětlení**: první dotaz `max_power_during_last_3_mins`, používá [posuvné okno](/stream-analytics-query/sliding-window-azure-stream-analytics) k vyhledání maximální hodnoty senzoru napájení pro každé zařízení během posledních 3 minut. Druhý dotaz je připojen k prvnímu dotazu a zjistí hodnotu napájení v nejnovějším okně, které je relevantní pro aktuální událost. A za předpokladu, že jsou splněné podmínky, se pro zařízení vygeneruje výstraha.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Příklad dotazu: zpracování událostí nezávisle na časovém posunu zařízení (podproudy)

**Popis**: události můžou docházet pozdě nebo mimo pořadí z důvodu výpadků hodin mezi výrobci událostí, hodinovým zkosením mezi oddíly nebo latencí sítě. V následujícím příkladu je čas zařízení TollID 2 5 sekund po TollID 1 a hodiny zařízení pro TollID 3 jsou deset sekund po hodnotě TollID 1. 

**Vstup**:

| LicensePlate | Vytvoření | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015 – 07 – 27T00:00:01.0000000 Z | 1\. místo |
| YHN 6970 |Toyota |2015 – 07 – 27T00:00:05.0000000 Z | 1\. místo |
| QYF 9358 |Honda |2015 – 07 – 27T00:00:01.0000000 Z | 2 |
| GXF 9462 |BMW |2015 – 07 – 27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Toyota |2015 – 07 – 27T00:00:10.0000000 Z | 1\. místo |
| RMV 8282 |Honda |2015 – 07 – 27T00:00:03.0000000 Z | 3 |
| MDR 6128 |BMW |2015 – 07 – 27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Ford |2015 – 07 – 27T00:00:07.0000000 Z | 3 |

**Výstup**:

| TollID | Počet |
| --- | --- |
| 1\. místo | 2 |
| 2 | 2 |
| 1\. místo | 1\. místo |
| 3 | 1\. místo |
| 2 | 1\. místo |
| 3 | 1\. místo |

**Řešení:**

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**Vysvětlení**: klauzule [timestamp by over](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) prohledává každou časovou osu zařízení samostatně pomocí podproudů. Výstupní události pro každý TollID jsou generovány při jejich výpočtu, což znamená, že události jsou v souladu s jednotlivými TollIDy namísto přeřazení, jako kdyby byla všechna zařízení ve stejné hodině.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Příklad dotazu: odstranění duplicitních událostí v okně

**Popis**: při provádění operace, jako je například výpočet průměru pro události v daném časovém intervalu, by měly být filtrovány duplicitní události. V následujícím příkladu je druhá událost duplikátem prvního.

**Vstup**:  

| DeviceId | Time | Atribut | Hodnota |
| --- | --- | --- | --- |
| 1\. místo |2018-07-27T00:00:01.0000000 Z |Teplota |50 |
| 1\. místo |2018-07-27T00:00:01.0000000 Z |Teplota |50 |
| 2 |2018-07-27T00:00:01.0000000 Z |Teplota |40 |
| 1\. místo |2018-07-27T00:00:05.0000000 Z |Teplota |60 |
| 2 |2018-07-27T00:00:05.0000000 Z |Teplota |50 |
| 1\. místo |2018-07-27T00:00:10.0000000 Z |Teplota |100 |

**Výstup**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1\. místo |
|45 | 2 |

**Řešení:**

```SQL
With Temp AS (
    SELECT
        COUNT(DISTINCT Time) AS CountTime,
        Value,
        DeviceId
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Value,
        DeviceId,
        SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Vysvětlení**: [Count (jedinečný čas)](/stream-analytics-query/count-azure-stream-analytics) vrátí počet jedinečných hodnot ve sloupci čas v časovém intervalu. Pak můžete použít výstup tohoto kroku k výpočtu průměru na zařízení tím, že zahodíte duplicity.

## <a name="geofencing-and-geospatial-queries"></a>Geografická a geoprostorové dotazy
Azure Stream Analytics poskytuje integrované geoprostorové funkce, které se dají použít k implementaci scénářů, jako je Správa loďstva, nasdílení po evidenci, připojená auta a sledování prostředků. Geoprostorové údaje lze ingestovat v Well formátech nebo v rámci datového proudu událostí nebo referenčních dat. Další informace najdete v článku [scénáře použití geografických zón a geoprostorové agregace s Azure Stream Analytics](geospatial-scenarios.md) článkem.

## <a name="language-extensibility-through-javascript-and-c"></a>Rozšiřitelnost jazyka prostřednictvím JavaScriptu aC#
Langugae dotazů Azure Stream Ananlytics se dá rozšířit o vlastní funkce napsané v JavaScriptu C# nebo jazycích. Další informace najdete v článcích foolowing:
* [Azure Stream Analytics uživatelsky definovaných funkcí jazyka JavaScript](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics uživatelsky definovaných agregací jazyka JavaScript](stream-analytics-javascript-user-defined-aggregates.md)
* [Vývoj .NET Standard uživatelsky definovaných funkcí pro úlohy Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>Získání nápovědy

Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

