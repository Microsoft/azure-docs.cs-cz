---
title: Běžné vzory dotazů v Azure Stream Analytics
description: Tento článek popisuje několik běžných vzorů a návrhů dotazů, které jsou užitečné v Azure Stream Analytics úlohách.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-js
ms.openlocfilehash: 8fcda77858b3feb78a04971a7ad1f20c0ea51fa1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019988"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Běžné vzory dotazů v Azure Stream Analytics

Dotazy v Azure Stream Analytics jsou vyjádřeny v dotazovacím jazyce podobném SQL. Jazykové konstrukce jsou zdokumentovány v referenční příručce [jazyka Stream Analytics dotazů](/stream-analytics-query/stream-analytics-query-language-reference) . 

Návrh dotazu může vyjádřit jednoduchou předávací logiku pro přesun dat událostí z jednoho vstupního datového proudu do výstupního úložiště dat, nebo může provádět bohatou porovnávání vzorů a dočasnou analýzu pro výpočet agregovaných hodnot v různých časových oknech, jako v [sestavení řešení IoT pomocí průvodce Stream Analytics](stream-analytics-build-an-iot-solution-using-stream-analytics.md) . K kombinování událostí streamování můžete spojit data z několika vstupů a můžete provádět vyhledávání na základě statických referenčních dat a rozšířit hodnoty událostí. Můžete také zapisovat data do více výstupů.

Tento článek popisuje řešení několika běžných vzorů dotazů založených na scénářích reálného světa.

## <a name="supported-data-formats"></a>Podporované formáty dat

Azure Stream Analytics podporuje zpracování událostí v datových formátech CSV, JSON a Avro.

JSON a Avro mohou obsahovat komplexní typy, jako jsou například vnořené objekty (záznamy) nebo pole. Další informace o práci s těmito komplexními datovými typy najdete v článku [Analýza JSON a data Avro](stream-analytics-parsing-json.md) .

## <a name="send-data-to-multiple-outputs"></a>Odesílání dat do více výstupů

Pro výstup dat do různých výstupních umyvadel lze použít vícenásobné příkazy **Select** . Například jedna **možnost vybrat** může vygenerovat výstrahu na základě prahové hodnoty, zatímco jiná může vygenerovat události do úložiště objektů BLOB.

**Vstup**:

| Značka | Čas |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Výstupní ArchiveOutput**:

| Značka | Čas |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Výstupní AlertOutput**:

| Značka | Čas | Počet |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000 Z |3 |

**Dotaz**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
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

Klauzule **into** oznamuje Stream Analytics, na které výstupy mají zapisovat data. První **Výběr** definuje předávací dotaz, který přijímá data ze vstupu a odesílá je do výstupu s názvem **ArchiveOutput**. Druhý dotaz provede několik jednoduchých agregací a filtrování před odesláním výsledků do výstupního systému výstrah pro příjem dat s názvem **AlertOutput**.

Všimněte si, že klauzuli **with** lze použít k definování více bloků dílčího dotazu. Tato možnost je výhodou otevření menšího počtu čtenářů ke vstupnímu zdroji.

**Dotaz**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Další informace najdete [ **v tématu s klauzulí with**](/stream-analytics-query/with-azure-stream-analytics).

## <a name="simple-pass-through-query"></a>Jednoduchý předávací dotaz

Pomocí jednoduchého předávacího dotazu můžete zkopírovat data vstupního datového proudu do výstupu. Například pokud se datový proud dat, který obsahuje informace o vozidlech v reálném čase, musí uložit do databáze SQL pro analýzu dopisů, provede jednoduchý předávací dotaz úlohu.

**Vstup**:

| Značka | Čas | Hmotnost |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Výstup**:

| Značka | Čas | Hmotnost |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Dotaz**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

Dotaz **Select** * vyprojektech všechna pole příchozí události a odesílá je do výstupu. Stejný způsob, jak **Vybrat** , lze také použít pouze k projektu povinných polí ze vstupu. V tomto příkladu, pokud je *vytvářená* a *Časová a časová* pole, která se mají uložit, je možné tato pole zadat v příkazu **Select** .

**Vstup**:

| Značka | Čas | Hmotnost |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1 500 |

**Výstup**:

| Značka | Čas |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:04.0000000 Z |

**Dotaz**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>Shoda řetězců s PODOBNÝm a nepodobným

**Podobně jako** a **nikoli** , lze použít k ověření, zda pole odpovídá určitému vzoru. Můžete například vytvořit filtr, který vrátí pouze ty licenční štítky, které začínají písmenem "A" a končí číslem 9.

**Vstup**:

| Značka | License_plate | Čas |
| --- | --- | --- |
| Make1 |ABC – 123 |2015-01-01T00:00:01.0000000 Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC – 369 |2015-01-01T00:00:03.0000000 Z |

**Výstup**:

| Značka | License_plate | Čas |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC – 369 |2015-01-01T00:00:03.0000000 Z |

**Dotaz**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Použijte příkaz **Like** pro kontrolu hodnoty pole **License_plate** . Měl by začínat písmenem A a pak mít libovolný řetězec nula nebo více znaků, který končí číslem 9.

## <a name="calculation-over-past-events"></a>Výpočet za minulé události

Funkci **Lag** lze použít k zobrazení minulých událostí v časovém intervalu a jejich porovnání s aktuální událostí. Například aktuální automobilový výstup může být výstupem, pokud se liší od posledního automobilu, který se přes placená linka provedl.

**Vstup**:

| Značka | Čas |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Výstup**:

| Značka | Čas |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Dotaz**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Pomocí **prodlevy** můžete prohlížet vstupní datový proud jednu událost zpět, načíst hodnotu a porovnávat ji *s hodnotou pro* aktuální událost a *vydávat* výstup události.

Další informace najdete v tématu [**Prodleva**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Vrátí poslední událost v okně.

Když systém spotřebovává události v reálném čase, není k dispozici žádná funkce, která by mohla určit, jestli bude událost poslední pro doručení daného časového období. Aby se to dosáhlo, musí se vstupní datový proud připojit k druhému, kde čas události je maximální doba pro všechny události v tomto okně.

**Vstup**:

| License_plate | Značka | Čas |
| --- | --- | --- |
| DXE 5291 |Make1 |2015 – 07 – 27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015 – 07 – 27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015 – 07 – 27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015 – 07 – 27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015 – 07 – 27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015 – 07 – 27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015 – 07 – 27T00:13:45.0000000 Z |

**Výstup**:

| License_plate | Značka | Čas |
| --- | --- | --- |
| VFE 1616 |Make2 |2015 – 07 – 27T00:09:31.0000000 Z |
| MDR 6128 |Make4 |2015 – 07 – 27T00:13:45.0000000 Z |

**Dotaz**:

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
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

První krok v dotazu najde maximální časové razítko v oknech o velikosti 10 minut, což je časové razítko poslední události pro toto okno. Druhý krok spojí výsledky prvního dotazu s původním datovým proudem, aby bylo možné najít událost, která se shoduje s posledními časovými razítky v jednotlivých oknech. 

**DateDiff** je funkce specifická pro datum, která porovnává a vrátí časový rozdíl mezi dvěma poli DateTime. Další informace najdete v tématu [Date Functions](/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Další informace o spojování datových proudů najdete v tématu věnovaném [**připojení**](/stream-analytics-query/join-azure-stream-analytics).

## <a name="data-aggregation-over-time"></a>Agregace dat v průběhu času

Chcete-li vypočítat informace v časovém intervalu, lze data agregovat dohromady. V tomto příkladu je počet vypočítán za posledních 10 sekund času pro všechny konkrétní auto.

**Vstup**:

| Značka | Čas | Hmotnost |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1 500 |

**Výstup**:

| Značka | Počet |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Dotaz**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Tato agregace seskupuje *automobily pomocí a* počítá je každých 10 sekund. Výstup má na začátku a na *počtu* vozidel, která se *provedla* prostřednictvím mýtné.

TumblingWindow je funkce Window, která slouží k seskupení událostí dohromady. Agregaci lze použít pro všechny seskupené události. Další informace najdete v tématu [funkce pro okna](stream-analytics-window-functions.md).

Další informace o agregaci naleznete v tématu [agregační funkce](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="periodically-output-values"></a>Periodické výstupní hodnoty

V případě nepravidelných nebo chybějících událostí lze výstup pravidelného intervalu vygenerovat z zhuštěného datového vstupu. Vygenerujte například událost každých 5 sekund, která hlásí poslední zjištěný datový bod.

**Vstup**:

| Čas | Hodnota |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Výstup (prvních 10 řádků)**:

| Window_end | Last_event. Interval | Last_event. Osa |
| --- | --- | --- |
| 2014-01-01T14:01:00.000 Z |2014-01-01T14:01:00.000 Z |1 |
| 2014-01-01T14:01:05.000 Z |2014-01-01T14:01:05.000 Z |2 |
| 2014-01-01T14:01:10.000 Z |2014-01-01T14:01:10.000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000 Z |2014-01-01T14:01:30.000 Z |5 |
| 2014-01-01T14:01:35.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:45.000 Z |2014-01-01T14:01:35.000 Z |6 |

**Dotaz**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Tento dotaz generuje události každých 5 sekund a vypíše poslední událost, která byla dříve přijata. Doba trvání **HOPPINGWINDOW** určuje, jak daleko se má dotaz Hledat v poslední události.

Další informace najdete v [okně skákající](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="correlate-events-in-a-stream"></a>Korelace událostí v datovém proudu

Korelace událostí ve stejném datovém proudu se dá udělat tak, že si prohlížíte minulé události pomocí funkce **Lag** . Výstup lze například vygenerovat pokaždé, když dvě po sobě jdoucí automobily ze *stejné linky* projdou za posledních 90 sekund.

**Vstup**:

| Značka | License_plate | Čas |
| --- | --- | --- |
| Make1 |ABC – 123 |2015-01-01T00:00:01.0000000 Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make2 |DEF – 987 |2015-01-01T00:00:03.0000000 Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**Výstup**:

| Značka | Čas | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC – 123 |2015-01-01T00:00:01.0000000 Z |

**Dotaz**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

Funkce **Lag** může ve vstupním datovém proudu vyhledat jednu událost zpět a načíst *hodnotu,* a to porovnáním s *hodnotou pro* aktuální událost.  Po splnění podmínky lze data z předchozí události promítnout pomocí příkazu **Lag** v příkazu **Select** .

Další informace najdete v tématu [Prodleva](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Zjištění trvání mezi událostmi

Dobu trvání události lze vypočítat vyhledáním poslední události spuštění po přijetí události end. Tento dotaz může být užitečný k určení času stráveného uživatelem na stránce nebo funkci.

**Vstup**:  

| User | Funkce | Událost | Čas |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Spustit |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000 Z |

**Výstup**:  

| User | Funkce | Doba trvání |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Dotaz**:

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

**Poslední** funkce se dá použít k načtení poslední události v konkrétní podmínce. V tomto příkladu je podmínka událost typu Start, dělení hledání podle **oddílů podle** uživatele a funkce. V takovém případě se každý uživatel a funkce při hledání události Start nezávisle považovat za nezávisle. **Doba omezení** : omezí dobu hledání v čase na 1 hodinu mezi koncovými a počátečními událostmi.

## <a name="count-unique-values"></a>Počet jedinečných hodnot

**Počet** a **rozdíl** lze použít k výpočtu počtu jedinečných hodnot polí, které se zobrazí v datovém proudu v časovém intervalu. Je možné vytvořit dotaz, který *vypočítá, kolik jedinečných* vozidel předávaných přes telefonní stánku v okně 2 sekund.

**Vstup**:

| Značka | Čas |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Výkonem**

| Count_make | Čas |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**Dotaz:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**Count (jedinečné)** vrátí počet jedinečných hodnot ve sloupci **zpřístupnit** v časovém intervalu.
Další informace najdete v tématu funkce [ **Count** Aggregate](/stream-analytics-query/count-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Načtení první události v okně

Možnost- **First** se dá použít k načtení první události v časovém intervalu. Například je třeba uvést první informace o automobilu do intervalu 10 minut.

**Vstup**:

| License_plate | Značka | Čas |
| --- | --- | --- |
| DXE 5291 |Make1 |2015 – 07 – 27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015 – 07 – 27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015 – 07 – 27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015 – 07 – 27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015 – 07 – 27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015 – 07 – 27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015 – 07 – 27T00:13:45.0000000 Z |

**Výstup**:

| License_plate | Značka | Čas |
| --- | --- | --- |
| DXE 5291 |Make1 |2015 – 07 – 27T00:00:05.0000000 Z |
| QYF 9358 |Make1 |2015 – 07 – 27T00:12:02.0000000 Z |

**Dotaz**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**Je možné také** rozdělit data na oddíly a vypočítat první událost u každého konkrétního auta *,* který byl nalezen v každém intervalu 10 minut.

**Výstup**:

| License_plate | Značka | Čas |
| --- | --- | --- |
| DXE 5291 |Make1 |2015 – 07 – 27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015 – 07 – 27T00:02:17.0000000 Z |
| YHN 6970 |Make2 |2015 – 07 – 27T00:06:00.0000000 Z |
| QYF 9358 |Make1 |2015 – 07 – 27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015 – 07 – 27T00:13:45.0000000 Z |

**Dotaz**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Další informace najdete v části co [**nejdřív**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="remove-duplicate-events-in-a-window"></a>Odstranění duplicitních událostí v okně

Při provádění operace, jako je výpočet průměru pro události v daném časovém intervalu, by se měly filtrovat duplicitní události. V následujícím příkladu je druhá událost duplikátem prvního.

**Vstup**:  

| DeviceId | Čas | Atribut | Hodnota |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000 Z |Teplota |50 |
| 1 |2018-07-27T00:00:01.0000000 Z |Teplota |50 |
| 2 |2018-07-27T00:00:01.0000000 Z |Teplota |40 |
| 1 |2018-07-27T00:00:05.0000000 Z |Teplota |60 |
| 2 |2018-07-27T00:00:05.0000000 Z |Teplota |50 |
| 1 |2018-07-27T00:00:10.0000000 Z |Teplota |100 |

**Výstup**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Dotaz**:

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

**Počet (jedinečný čas)** vrátí počet jedinečných hodnot ve sloupci čas v časovém intervalu. Výstup prvního kroku se pak dá použít k výpočtu průměru na zařízení tím, že zahodí duplicity.

Další informace najdete v tématu o [počtu (jedinečný čas)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Zadejte logiku pro různé případy nebo hodnoty (příkazy CASE).

Příkazy **case** můžou poskytovat různé výpočty pro různá pole na základě konkrétního kritéria. Například přiřaďte Lane ' A ' do auta *Make1* a Lane ' B ' do jakékoli jiné.

**Vstup**:

| Značka | Čas |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Výstup**:

| Značka |Dispatch_to_lane | Čas |
| --- | --- | --- |
| Make1 |Určitého |2015-01-01T00:00:01.0000000 Z |
| Make2 |B |2015-01-01T00:00:02.0000000 Z |

**Řešení:**

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

Výraz **case** porovná výraz se sadou jednoduchých výrazů k určení jeho výsledku. V tomto příkladu se vozidla *Make1* odesílají na pás "A", zatímco vozidlo kterékoli jiné z nich bude přiřazeno Lane ' B '.

Další informace najdete v tématu [výraz Case](/stream-analytics-query/case-azure-stream-analytics).

## <a name="data-conversion"></a>Převod dat

Data je možné přetypovat v reálném čase pomocí metody **cast** . Například Tloušťka auta může být převedena z typu **nvarchar (max)** na typ **bigint** a lze ji použít pro číselný výpočet.

**Vstup**:

| Značka | Čas | Hmotnost |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Výstup**:

| Značka | Hmotnost |
| --- | --- |
| Make1 |3000 |

**Dotaz**:

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

Použijte příkaz **cast** k určení jeho datového typu. Podívejte se na seznam podporovaných datových typů u [datových typů (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Další informace o [funkcích pro převod dat](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="detect-the-duration-of-a-condition"></a>Zjištění doby trvání podmínky

Pro podmínky, které jsou v rozsahu více událostmi, lze funkci **Lag** použít k identifikaci doby trvání této podmínky. Předpokládejme například, že chyba byla způsobena tím, že všechna auta mají nesprávnou váhu (nad 20 000 libry) a že se musí vypočítat doba trvání této chyby.

**Vstup**:

| Značka | Čas | Hmotnost |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000 Z |250 000 |
| Make1 |2015-01-01T00:00:03.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |250 000 |
| Make1 |2015-01-01T00:00:05.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000 Z |250 000 |
| Make1 |2015-01-01T00:00:07.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000 Z |2000 |

**Výstup**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

**Dotaz**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
První příkaz **Select** koreluje aktuální váhu s předchozí měřením a propojuje je s aktuální měření. Druhý **Výběr** se vrátí k poslední události, kde je *previous_weight* menší než 20000, přičemž aktuální váha je menší než 20000 a *previous_weight* aktuální události byla větší než 20000.

End_fault je aktuální nepoškozená událost, u které došlo k chybě předchozí události, a Start_fault je poslední nepoškozená událost.

## <a name="process-events-with-independent-time-substreams"></a>Zpracování událostí s nezávislým časem (podproudy)

Události mohou docházet pozdě nebo mimo pořadí z důvodu výpadků hodin mezi výrobci událostí, hodinovým zkosením mezi oddíly nebo latencí sítě.
Například hodiny zařízení pro *TollID* 2 jsou pět sekund za *TollID* 1 a hodiny zařízení pro *TollID* 3 jsou deset sekund po hodnotě *TollID* 1. Výpočet může být nezávisle u každého placená linka a zvažuje pouze vlastní data o hodinách jako časové razítko.

**Vstup**:

| LicensePlate | Značka | Čas | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015 – 07 – 27T00:00:01.0000000 Z | 1 |
| YHN 6970 |Make2 |2015 – 07 – 27T00:00:05.0000000 Z | 1 |
| QYF 9358 |Make1 |2015 – 07 – 27T00:00:01.0000000 Z | 2 |
| GXF 9462 |Make3 |2015 – 07 – 27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Make2 |2015 – 07 – 27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Make1 |2015 – 07 – 27T00:00:03.0000000 Z | 3 |
| MDR 6128 |Make3 |2015 – 07 – 27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Make4 |2015 – 07 – 27T00:00:07.0000000 Z | 3 |

**Výstup**:

| TollID | Počet |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Dotaz**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**Časová osa přes** klauzuli vyhledává každou časovou osu zařízení nezávisle na podproudech. Výstupní událost pro každý *TollID* je vygenerována při jejich výpočtu, což znamená, že události jsou v souladu s jednotlivými *TollIDy* namísto přeřazení, jako kdyby byla všechna zařízení ve stejný čas.

Další informace najdete v tématu [časové razítko pomocí](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering)služby.

## <a name="session-windows"></a>Okna relace

Okno relace je okno, které udržuje rozbalení jako události a je zavřené pro výpočet, pokud se po určité době neobdrží žádná událost ani když okno dosáhne maximální doby trvání.
Toto okno je zvláště užitečné při výpočtu dat interakce uživatele. Okno se spustí, když uživatel spustí interakci se systémem a zavře, když se nepozorují žádné další události, což znamená, že uživatel zastavil interakci.
Uživatel například komunikuje s webovou stránkou, kde je zaznamenán počet kliknutí, okno relace lze použít k zjištění, jak dlouho uživatel pracuje s webem.

**Vstup**:

| User_id | Čas | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000 Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000 Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000 Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000 Z | "www.example.com/e.html" |

**Výstup**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | 2017-01-26T00:01:10.0000000 Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000 Z | 2017-01-26T00:01:15.0000000 Z | 20 |

**Dotaz**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

**Výběr** projektuje data relevantní pro interakci s uživatelem, a to spolu s dobou trvání interakce. Seskupení dat podle uživatele a **SessionWindow** , které se zavřou, pokud žádná interakce neproběhne během 1 minuty, s maximální velikostí okna 60 minut.

Další informace o **SessionWindow** najdete v [okně relace](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Jazyková rozšíření s uživatelsky definovanou funkcí v jazycích JavaScript a C #

Dotazovací jazyk Azure Stream Analytics lze rozšířit pomocí vlastních funkcí napsaných v jazyce JavaScript nebo C#. Uživatelsky definované funkce (UDF) jsou vlastní nebo komplexní výpočty, které nelze snadno vyjádřit pomocí jazyka **SQL** . Tyto UDF se dají v rámci dotazu definovat jednou a používat víckrát. Například UDF lze použít k převodu hexadecimální hodnoty *nvarchar (max)* na hodnotu *bigint* .

**Vstup**:

| Device_id | HexValue |
| --- | --- |
| 1 | B4 |
| 2 | "11B" |
| 3 | "121" |

**Výstup**:

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

Funkce definovaná uživatelem vypočítá hodnotu *bigint* z HexValue na všech spotřebovaných událostech.

Další informace najdete v tématu [JavaScript](./stream-analytics-javascript-user-defined-functions.md) a [C#](./stream-analytics-edge-csharp-udf.md).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Rozšířené porovnávání vzorů s MATCH_RECOGNIZE

**MATCH_RECOGNIZE** je pokročilý mechanismus porovnávání vzorů, který lze použít k porovnání sekvence událostí s dobře definovaným vzorem regulárního výrazu.
Například v reálném čase je sledování sítě ATM v reálném čase k selháním, když se při provozu služby ATM zobrazí dvě po sobě jdoucí zprávy upozorňující na to, že správce musí být upozorněni.

**Vstup**:

| ATM_id | Operation_id | Return_Code | Čas |
| --- | --- | --- | --- |
| 1 | "Zadávání kódu PIN" | Nástup | 2017-01-26T00:10:00.0000000 Z |
| 2 | "Otevření měnové přihrádky" | Nástup | 2017-01-26T00:10:07.0000000 Z |
| 2 | "Uzavírací peníze – slot" | Nástup | 2017-01-26T00:10:11.0000000 Z |
| 1 | "Vstup do množství pro stažení" | Nástup | 2017-01-26T00:10:08.0000000 Z |
| 1 | "Otevření měnové přihrádky" | Upozornění | 2017-01-26T00:10:14.0000000 Z |
| 1 | "Vytištění bankovního zůstatku" | Upozornění | 2017-01-26T00:10:19.0000000 Z |

**Výstup**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Otevření měnové přihrádky" | 2017-01-26T00:10:14.0000000 Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Tento dotaz se shoduje s nejméně dvěma po sobě jdoucími událostmi selhání a při splnění podmínek vygeneruje alarm.
**Vzor** definuje regulární výraz, který se má použít u porovnávání, v tomto případě libovolný počet úspěšných operací následovaný nejméně dvěma po sobě jdoucími selháními.
Úspěch a neúspěch jsou definovány pomocí Return_Code hodnoty a jakmile je podmínka splněna, **míry** se procházejí *ATM_id*, první operací upozornění a prvním časem upozornění.

Další informace najdete v tématu [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Geografická a geoprostorové dotazy

Azure Stream Analytics poskytuje integrované geoprostorové funkce, které se dají použít k implementaci scénářů, jako je Správa loďstva, nasdílení po evidenci, připojená auta a sledování prostředků.
Geoprostorové údaje lze ingestovat v Well formátech nebo v rámci datového proudu událostí nebo referenčních dat.
Například společnost, která je specializovaná na výrobní počítače pro tisk passportů, zapůjčí své počítače vládám a konzulárním úřadům. Umístění těchto počítačů je intenzivně řízeno, aby nedocházelo k chybám při umísťování a možnému použití při padělání cestovních pasů. Každý počítač je vybaven sledovacím nástrojem GPS, tyto informace jsou předávány zpět do Azure Stream Analytics úlohy.
Výroba by mohla sledovat umístění těchto počítačů a zobrazovat výstrahy, pokud některý z nich opustí oprávněnou oblast, a to tak, jak můžou vzdáleně zakázat, upozorňovat autority a načítat zařízení.

**Vstup**:

| Equipment_id | Equipment_current_location | Čas |
| --- | --- | --- |
| 1 | "POINT (-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000 Z |
| 1 | "POINT (-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000 Z |
| 1 | "POINT (-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000 Z |
| 1 | "POINT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

**Referenční datové vstupy**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "MNOHOÚHELNÍK ((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Výstup**:

| Equipment_id | Equipment_alert_location | Čas |
| --- | --- | --- |
| 1 | "POINT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

Dotaz umožňuje výrobci automaticky monitorovat umístění počítačů a získávat výstrahy, když počítač opustí povolenou geografickou oblast. Integrovaná geoprostorové funkce umožňuje uživatelům používat data GPS v rámci dotazu bez knihoven třetích stran.

Další informace najdete v článku [scénáře použití geografických zón a geoprostorové agregace s Azure Stream Analytics](geospatial-scenarios.md) článkem.

## <a name="get-help"></a>Získání pomoci

Pokud chcete získat další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)