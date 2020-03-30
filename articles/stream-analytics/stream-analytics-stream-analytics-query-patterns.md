---
title: Běžné vzorce dotazů ve Službě Azure Stream Analytics
description: Tento článek popisuje několik běžných vzorů dotazů a návrhů, které jsou užitečné v azure stream analytics úlohy.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982318"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Běžné vzorce dotazů ve Službě Azure Stream Analytics

Dotazy ve službě Azure Stream Analytics se vyjadřují v dotazovacím jazyce podobném SQL. Konstrukce jazyka jsou zdokumentovány v referenční příručce [pro dotazovací jazyk Stream Analytics.](/stream-analytics-query/stream-analytics-query-language-reference) 

Návrh dotazu může vyjádřit jednoduchou logiku předání pro přesunutí dat událostí z jednoho vstupního datového proudu do výstupního úložiště dat nebo může provést bohatou porovnávání vzorů a časovou analýzu pro výpočet agregací v různých časových oknech jako v [řešení Sestavení IoT pomocí průvodce Stream Analytics.](stream-analytics-build-an-iot-solution-using-stream-analytics.md) Můžete spojit data z více vstupů kombinovat události streamování a můžete provést vyhledávání proti statickým referenčním datům a obohatit hodnoty událostí. Můžete také zapisovat data do více výstupů.

Tento článek popisuje řešení několika běžných vzorů dotazů založených na reálných scénářích.

## <a name="supported-data-formats"></a>Podporované formáty dat

Azure Stream Analytics podporuje zpracování událostí ve formátech dat CSV, JSON a Avro.

JSON a Avro mohou obsahovat složité typy, jako jsou vnořené objekty (záznamy) nebo pole. Další informace o práci s těmito složitými datovými typy naleznete v článku [analýzy json a avro dat.](stream-analytics-parsing-json.md)

## <a name="simple-pass-through-query"></a>Jednoduchý předávací dotaz

Jednoduchý předávací dotaz lze zkopírovat vstupní data datového proudu do výstupu. Například pokud proud dat obsahující informace o vozidle v reálném čase je třeba uložit v databázi SQL pro analýzu dopisů, bude to provést jednoduchý předávací dotaz.

**Vstup**:

| Značka | Time | Hmotnost |
| --- | --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Ovčák1 |2015-01-01T00:00:02.0000000z |"2000" |

**Výstup**:

| Značka | Time | Hmotnost |
| --- | --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Ovčák1 |2015-01-01T00:00:02.0000000z |"2000" |

**Dotaz**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

A **SELECT** * dotaz projekty všechna pole příchozí události a odešle je do výstupu. Stejným způsobem **select** lze také použít pouze projekt oválná požadovaná pole ze vstupu. V tomto příkladu, *Time* pokud *jsou* pouze povinná pole vozidla, která mají být uložena, lze tato pole zadat v příkazu **SELECT.**

**Vstup**:

| Značka | Time | Hmotnost |
| --- | --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |1000 |
| Ovčák1 |2015-01-01T00:00:02.0000000z |2000 |
| Vytvořit2 |2015-01-01T00:00:04.0000000z |1 500 |

**Výstup**:

| Značka | Time |
| --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |
| Ovčák1 |2015-01-01T00:00:02.0000000z |
| Vytvořit2 |2015-01-01T00:00:04.0000000z |

**Dotaz**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>Agregace dat v průběhu času

Chcete-li vypočítat informace v časovém okně, data lze agregovat společně. V tomto příkladu se počítá počet za posledních 10 minut času pro každou konkrétní automobilovou řadu.

**Vstup**:

| Značka | Time | Hmotnost |
| --- | --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |1000 |
| Ovčák1 |2015-01-01T00:00:02.0000000z |2000 |
| Vytvořit2 |2015-01-01T00:00:04.0000000z |1 500 |

**Výstup**:

| Značka | Počet |
| --- | --- |
| Ovčák1 | 2 |
| Vytvořit2 | 1 |

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

Tato agregace seskupuje auta podle *make* a počítá je každých 10 sekund. Výstup má *Make* a *Count* automobilů, které prošly mýtného.

TumblingWindow je funkce okna slouží ke seskupení událostí dohromady. Agregace lze použít přes všechny seskupené události. Další informace naleznete v [tématu funkce okna](stream-analytics-window-functions.md).

Další informace o agregaci naleznete v [agregačních funkcích](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="data-conversion"></a>Převod dat

Data mohou být přetypována v reálném čase pomocí metody **CAST.** Například hmotnost auta může být převedena z typu **nvarchar(max)** na typ **bigint** a může být použita na číselném výpočtu.

**Vstup**:

| Značka | Time | Hmotnost |
| --- | --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Ovčák1 |2015-01-01T00:00:02.0000000z |"2000" |

**Výstup**:

| Značka | Hmotnost |
| --- | --- |
| Ovčák1 |3000 |

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

K určení jeho datového typu použijte příkaz **CAST.** Podívejte se na seznam podporovaných datových typů na [datových typech (Azure Stream Analytics).](/stream-analytics-query/data-types-azure-stream-analytics)

Další informace o [funkcích převodu dat](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="string-matching-with-like-and-not-like"></a>Porovnávání řetězců s like a ne jako

**LIKE** a **NOT LIKE** lze použít k ověření, zda pole odpovídá určitému vzoru. Například filtr může být vytvořen vrátit pouze registrační značky, které začínají písmenem 'A' a končí číslem 9.

**Vstup**:

| Značka | License_plate | Time |
| --- | --- | --- |
| Ovčák1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Vytvořit2 |AAA-999 |2015-01-01T00:00:02.0000000z |
| Osekat3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Výstup**:

| Značka | License_plate | Time |
| --- | --- | --- |
| Vytvořit2 |AAA-999 |2015-01-01T00:00:02.0000000z |
| Osekat3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Dotaz**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Ke kontrole hodnoty **pole License_plate** použijte příkaz **LIKE.** Měl by začínat písmenem "A", pak mít libovolný řetězec nula nebo více znaků, končící číslem 9.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Zadejte logiku pro různé případy/hodnoty (příkazy CASE)

**Case** příkazy mohou poskytovat různé výpočty pro různá pole, na základě konkrétního kritéria. Například přiřaďte jízdní pruh "A" vozům *Make1* a pruhu "B" jakékoli jiné výrobě.

**Vstup**:

| Značka | Time |
| --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |
| Vytvořit2 |2015-01-01T00:00:02.0000000z |
| Vytvořit2 |2015-01-01T00:00:03.0000000Z |

**Výstup**:

| Značka |Dispatch_to_lane | Time |
| --- | --- | --- |
| Ovčák1 |"A" |2015-01-01T00:00:01.0000000Z |
| Vytvořit2 |"B" |2015-01-01T00:00:02.0000000z |

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

Výraz **CASE** porovnává výraz se sadou jednoduchých výrazů k určení jeho výsledku. V tomto příkladu jsou vozidla *Make1* odeslána do jízdního pruhu "A", zatímco vozidlům jiné hospo-

Další informace naleznete v [písmenu výraz](/stream-analytics-query/case-azure-stream-analytics).

## <a name="send-data-to-multiple-outputs"></a>Odesílání dat do více výstupů

Více **příkazů SELECT** lze použít k výstupu dat do různých výstupních propadů. Například jeden **SELECT** můžete výstup výstrahy založené na prahové hodnotě, zatímco jiný může výstup událostí do úložiště objektů blob.

**Vstup**:

| Značka | Time |
| --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |
| Ovčák1 |2015-01-01T00:00:02.0000000z |
| Vytvořit2 |2015-01-01T00:00:01.0000000Z |
| Vytvořit2 |2015-01-01T00:00:02.0000000z |
| Vytvořit2 |2015-01-01T00:00:03.0000000Z |

**Výstup ArchiveOutputOutput**:

| Značka | Time |
| --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |
| Ovčák1 |2015-01-01T00:00:02.0000000z |
| Vytvořit2 |2015-01-01T00:00:01.0000000Z |
| Vytvořit2 |2015-01-01T00:00:02.0000000z |
| Vytvořit2 |2015-01-01T00:00:03.0000000Z |

**Výstupní výstrahaVýstup**:

| Značka | Time | Počet |
| --- | --- | --- |
| Vytvořit2 |2015-01-01T00:00:10.0000000Z |3 |

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

Klauzule **INTO** říká Stream Analytics, které výstupy pro zápis dat. První **SELECT** definuje předávací dotaz, který přijímá data ze vstupu a odesílá je do výstupu s názvem **ArchiveOutput**. Druhý dotaz provádí některé jednoduché agregace a filtrování před odesláním výsledků na výstup systému následnévýstrahy s názvem **AlertOutput**.

Všimněte si, že **WITH** klauzule lze definovat více bloků dílčí dotaz. Tato možnost má tu výhodu, že otevírá méně čteček vstupnímu zdroji.

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

Další informace naleznete v [klauzuli **WITH** ](/stream-analytics-query/with-azure-stream-analytics).

## <a name="count-unique-values"></a>Počítat jedinečné hodnoty

**COUNT** a **DISTINCT** lze spočítat počet jedinečných hodnot polí, které se zobrazí v datovém proudu v časovém okně. Můžete vytvořit dotaz pro výpočet počtu jedinečných *osazení* vozů projetých mýtnou kabinou v okně 2 sekundy.

**Vstup**:

| Značka | Time |
| --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |
| Ovčák1 |2015-01-01T00:00:02.0000000z |
| Vytvořit2 |2015-01-01T00:00:01.0000000Z |
| Vytvořit2 |2015-01-01T00:00:02.0000000z |
| Vytvořit2 |2015-01-01T00:00:03.0000000Z |

**Výstup:**

| Count_make | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Dotazu:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT(DISTINCT Make)** vrátí počet odlišných hodnot ve sloupci **Vytvořit** v časovém okně.
Další informace naleznete v souhrnné funkci [ **COUNT** ](/stream-analytics-query/count-azure-stream-analytics).

## <a name="calculation-over-past-events"></a>Výpočet oproti minulým událostem

Funkci **MAS** lze použít k prohlédnutí minulých událostí v časovém okně a jejich porovnání s aktuální událostí. Například, aktuální auto make může být výstup, pokud se liší od posledního vozu, který prošel mýtného.

**Vstup**:

| Značka | Time |
| --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |
| Vytvořit2 |2015-01-01T00:00:02.0000000z |

**Výstup**:

| Značka | Time |
| --- | --- |
| Vytvořit2 |2015-01-01T00:00:02.0000000z |

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

Pomocí **MAS** můžete nahlédnout do vstupního datového proudu o jednu událost zpět, načíst hodnotu *Make* a porovnat ji s hodnotou *Make* aktuální události a výstupem události.

Další informace naleznete v [**masce MAS**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Načtení první události v okně

**IsFirst** lze načíst první událost v časovém okně. Například v každém intervalu 10 minut předávací první informace o voze.

**Vstup**:

| License_plate | Značka | Time |
| --- | --- | --- |
| DXE 5291 |Ovčák1 |2015-07-27T00:00:05.0000000z |
| YZK 5704 |Osekat3 |2015-07-27T00:02:17.0000000z |
| RMV 8282 |Ovčák1 |2015-07-27T00:05:01.0000000z |
| YHN 6970 |Vytvořit2 |2015-07-27T00:06:00.0000000z |
| VFE 1616 |Vytvořit2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Ovčák1 |2015-07-27T00:12:02.0000000z |
| MDR 6128 |Ovčák4 |2015-07-27T00:13:45.0000000z |

**Výstup**:

| License_plate | Značka | Time |
| --- | --- | --- |
| DXE 5291 |Ovčák1 |2015-07-27T00:00:05.0000000z |
| QYF 9358 |Ovčák1 |2015-07-27T00:12:02.0000000z |

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

**IsFirst** může také rozdělit data a vypočítat první událost pro každé konkrétní auto *Make* nalezené v každém intervalu 10 minut.

**Výstup**:

| License_plate | Značka | Time |
| --- | --- | --- |
| DXE 5291 |Ovčák1 |2015-07-27T00:00:05.0000000z |
| YZK 5704 |Osekat3 |2015-07-27T00:02:17.0000000z |
| YHN 6970 |Vytvořit2 |2015-07-27T00:06:00.0000000z |
| QYF 9358 |Ovčák1 |2015-07-27T00:12:02.0000000z |
| MDR 6128 |Ovčák4 |2015-07-27T00:13:45.0000000z |

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

Další informace naleznete v [**isfirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Vrátit poslední událost v okně

Jako události jsou spotřebovány systémem v reálném čase, neexistuje žádná funkce, která může určit, zda událost bude poslední, která dorazí pro toto časové období. K dosažení tohoto cíle vstupní datový proud musí být spojen s jiným, kde čas události je maximální čas pro všechny události v tomto okně.

**Vstup**:

| License_plate | Značka | Time |
| --- | --- | --- |
| DXE 5291 |Ovčák1 |2015-07-27T00:00:05.0000000z |
| YZK 5704 |Osekat3 |2015-07-27T00:02:17.0000000z |
| RMV 8282 |Ovčák1 |2015-07-27T00:05:01.0000000z |
| YHN 6970 |Vytvořit2 |2015-07-27T00:06:00.0000000z |
| VFE 1616 |Vytvořit2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Ovčák1 |2015-07-27T00:12:02.0000000z |
| MDR 6128 |Ovčák4 |2015-07-27T00:13:45.0000000z |

**Výstup**:

| License_plate | Značka | Time |
| --- | --- | --- |
| VFE 1616 |Vytvořit2 |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |Ovčák4 |2015-07-27T00:13:45.0000000z |

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

První krok v dotazu najde maximální časové razítko v 10minutových oknech, což je časové razítko poslední události pro toto okno. Druhý krok spojuje výsledky prvního dotazu s původním datovým proudem a vyhledá událost, která odpovídá posledním časovým razítkům v každém okně. 

**DATEDIFF** je funkce specifická pro datum, která porovnává a vrací časový rozdíl mezi dvěma poli DateTime, další informace naleznete v [date function](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Další informace o spojování datových proudů naleznete v odkazech [**JOIN**](/stream-analytics-query/join-azure-stream-analytics).


## <a name="correlate-events-in-a-stream"></a>Korelace událostí v datovém proudu

Korelační události ve stejném datovém proudu lze provést při pohledu na minulé události pomocí funkce **MAS.** Například výstup může být generován pokaždé, když dvě po sobě jdoucí auta ze *stejnémake* projít mýtnéza posledních 90 sekund.

**Vstup**:

| Značka | License_plate | Time |
| --- | --- | --- |
| Ovčák1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Ovčák1 |AAA-999 |2015-01-01T00:00:02.0000000z |
| Vytvořit2 |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Ovčák1 |GHI-345 |2015-01-01T00:00:04.0000000z |

**Výstup**:

| Značka | Time | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Ovčák1 |2015-01-01T00:00:02.0000000z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

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

Funkce **MAS** můžete nahlédnout do vstupního datového proudu jednu událost zpět a načíst *Make* hodnotu, porovnáním, že s *Make* hodnotu aktuální události.  Jakmile je podmínka splněna, data z předchozí události lze promítnout pomocí **MAS** v příkazu **SELECT.**

Další informace naleznete v [masce MAS](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Zjištění doby mezi událostmi

Dobu trvání události lze vypočítat pohledem na poslední událost Start po přijetí události End. Tento dotaz může být užitečný k určení času, který uživatel stráví na stránce nebo funkci.

**Vstup**:  

| Uživatel | Funkce | Událost | Time |
| --- | --- | --- | --- |
| user@location.com |Pravá nabídka |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |Pravá nabídka |End |2015-01-01T00:00:08.0000000z |

**Výstup**:  

| Uživatel | Funkce | Doba trvání |
| --- | --- | --- |
| user@location.com |Pravá nabídka |7 |

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

**Funkci LAST** lze použít k načtení poslední události v rámci určité podmínky. V tomto příkladu je podmínka událost typu Start, která rozdělí hledání podle uživatele a funkce **PARTITION BY.** Tímto způsobem každý uživatel a funkce je zacházeno nezávisle při hledání Start události. **LIMIT DURATION** omezuje hledání zpět v čase na 1 hodinu mezi události Konec a Zahájení.

## <a name="detect-the-duration-of-a-condition"></a>Zjištění doby trvání podmínky

Pro podmínky, které se rozprostírají přes více událostí, lze funkci **MAS** použít k identifikaci doby trvání této podmínky. Předpokládejme například, že chyba za následek všechny vozy s nesprávnou hmotností (nad 20 000 liber) a doba trvání této chyby musí být vypočítána.

**Vstup**:

| Značka | Time | Hmotnost |
| --- | --- | --- |
| Ovčák1 |2015-01-01T00:00:01.0000000Z |2000 |
| Vytvořit2 |2015-01-01T00:00:02.0000000z |250 000 |
| Ovčák1 |2015-01-01T00:00:03.0000000Z |26000 |
| Vytvořit2 |2015-01-01T00:00:04.0000000z |250 000 |
| Ovčák1 |2015-01-01T00:00:05.0000000z |26000 |
| Vytvořit2 |2015-01-01T00:00:06.0000000z |250 000 |
| Ovčák1 |2015-01-01T00:00:07.0000000z |26000 |
| Vytvořit2 |2015-01-01T00:00:08.0000000z |2000 |

**Výstup**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

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
První příkaz **SELECT** koreluje aktuální měření hmotnosti s předchozím měřením a promítá jej společně s aktuálním měřením. Druhý **SELECT** se ohlíží zpět na poslední akci, kde *je previous_weight* menší než 20000, kde je aktuální hmotnost menší než 20000 a *previous_weight* aktuální události byla větší než 20000.

End_fault je aktuální nevadla, kdy předchozí událost byla vadná a Start_fault je poslední nevadla předtím.

## <a name="periodically-output-values"></a>Pravidelně výstupní hodnoty

V případě nepravidelných nebo chybějících událostí lze z řídkějšího datového vstupu vygenerovat výstup pravidelného intervalu. Například generovat událost každých 5 sekund, která hlásí naposledy viděný datový bod.

**Vstup**:

| Time | Hodnota |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Výstup (prvních 10 řádků):**

| Window_end | Last_event. Čas | Last_event. Hodnotu |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

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

Tento dotaz generuje události každých 5 sekund a výstupy poslední událost, která byla přijata dříve. **HoppingWINDOW** doba trvání určuje, jak daleko zpět dotaz vypadá najít nejnovější událost.

Další informace naleznete v [okně Hopping](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="process-events-with-independent-time-substreams"></a>Zpracovat události s nezávislým časem (Substreams)

Události mohou být doručeny pozdě nebo mimo pořadí z důvodu zkosení hodin mezi výrobci událostí, zkosením hodin mezi oddíly nebo latencí sítě.
Například hodiny zařízení pro *TollID* 2 je pět sekund za *TollID* 1 a hodiny zařízení pro *TollID* 3 je deset sekund za *TollID* 1. Výpočtmůže dojít nezávisle pro každé mýtné, s ohledem pouze na vlastní hodiny data jako časové razítko.

**Vstup**:

| Registrační značka | Značka | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Ovčák1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Vytvořit2 |2015-07-27T00:00:05.0000000z | 1 |
| QYF 9358 |Ovčák1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |Osekat3 |2015-07-27T00:00:04.0000000z | 2 |
| VFE 1616 |Vytvořit2 |2015-07-27T00:00:10.0000000z | 1 |
| RMV 8282 |Ovčák1 |2015-07-27T00:00:03.0000000z | 3 |
| MDR 6128 |Osekat3 |2015-07-27T00:00:11.0000000z | 2 |
| YZK 5704 |Ovčák4 |2015-07-27T00:00:07.0000000z | 3 |

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

Klauzule **TIMESTAMP OVER BY** se dívá na časovou osu každého zařízení nezávisle pomocí substreams. Výstupní událost pro každý *TollID* je generována, jak jsou vypočítány, což znamená, že události jsou v pořadí s ohledem na každý *TollID* namísto přeuspořádané, jako kdyby všechna zařízení byla na stejné hodiny.

Další informace naleznete [v timestamp by over](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="remove-duplicate-events-in-a-window"></a>Odebrání duplicitních událostí v okně

Při provádění operace, jako je výpočet průměrů nad událostmi v daném časovém okně, by měly být filtrovány duplicitní události. V následujícím příkladu druhá událost je duplikát první.

**Vstup**:  

| DeviceId | Time | Atribut | Hodnota |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Teplota |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Teplota |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Teplota |40 |
| 1 |2018-07-27T00:00:05.0000000z |Teplota |60 |
| 2 |2018-07-27T00:00:05.0000000z |Teplota |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Teplota |100 |

**Výstup**:  

| Průměrná hodnota | DeviceId |
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

**funkce COUNT(DISTINCT Time)** vrátí počet odlišných hodnot ve sloupci Čas v časovém okně. Výstup prvního kroku pak lze použít k výpočtu průměru na zařízení, zahozením duplikátů.

Další informace naleznete [v count(distinct time)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="session-windows"></a>Relace Windows

Okno relace je okno, které se neustále rozpíná, když dochází k událostem, a zavře se pro výpočty, pokud není přijata žádná událost po určité době nebo pokud okno dosáhne maximální doby trvání.
Toto okno je užitečné zejména při výpočtu dat interakce uživatele. Okno se spustí, když uživatel začne pracovat se systémem a zavře se, když nejsou pozorovány žádné další události, což znamená, že uživatel přestal komunikovat.
Uživatel například interaguje s webovou stránkou, kde je zaznamenán počet kliknutí, okno relace lze použít k zjištění, jak dlouho uživatel s webem komunikoval.

**Vstup**:

| User_id | Time | zprostředkovatele identity |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000z | "www.example.com/e.html" |

**Výstup**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000z | 2017-01-26T00:01:15.0000000z | 20 |

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

**SELECT** promítá data relevantní pro interakci s uživatelem spolu s dobou trvání interakce. Seskupení dat podle uživatele a **SessionWindow,** který zavře, pokud žádná interakce dojde do 1 minuty, s maximální velikost okna 60 minut.

Další informace o **SessionWindow**naleznete v [okně relace](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Rozšiřitelnost jazyka s uživatelem definovanou funkcí v JavaScriptu a C #

Dotazovací jazyk Azure Stream Analytics lze rozšířit o vlastní funkce napsané v jazyce JavaScript nebo C#. Uživatelem definované funkce (UDF) jsou vlastní/složité výpočty, které nelze snadno vyjádřit pomocí jazyka **SQL.** Tyto UDFs lze definovat jednou a použít vícekrát v rámci dotazu. Například UDF lze použít k převodu šestnáctkové *nvarchar(max)* hodnotu *bigint* hodnotu.

**Vstup**:

| Device_id | Hodnota HexValue |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**Výstup**:

| Device_id | Desetinné číslo |
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

Uživatelem definovaná funkce vypočítá hodnotu *bigint* z Hodnoty HexValue při každé spotřebované události.

Další informace naleznete v [jazycích JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) a [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Pokročilé porovnávání vzorů s MATCH_RECOGNIZE

**MATCH_RECOGNIZE** je pokročilý mechanismus porovnávání vzorů, který lze použít k porovnání posloupnosti událostí s dobře definovaným vzorem regulárního výrazu.
Například atm je sledován v reálném čase pro selhání, během provozu ATM, pokud existují dvě po sobě jdoucí varovné zprávy správce musí být upozorněni.

**Vstup**:

| ATM_id | Operation_id | Return_Code | Time |
| --- | --- | --- | --- |
| 1 | "Zadání pinu" | "Úspěch" | 2017-01-26T00:10:00.0000000Z |
| 2 | "Otevření money slot" | "Úspěch" | 2017-01-26T00:10:07.0000000z |
| 2 | "Uzavření money slot" | "Úspěch" | 2017-01-26T00:10:11.0000000z |
| 1 | "Zadání vybrat množství" | "Úspěch" | 2017-01-26T00:10:08.0000000Z |
| 1 | "Otevření money slot" | "Varování" | 2017-01-26T00:10:14.0000000z |
| 1 | "Tisk bankovního zůstatku" | "Varování" | 2017-01-26T00:10:19.0000000z |

**Výstup**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Otevření money slot" | 2017-01-26T00:10:14.0000000z |

```SQL
SELECT *
FROM intput TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    PARTITON BY ATM_id
    LIMIT DURATION(minute, 1)
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

Tento dotaz odpovídá alespoň dvěma po sobě jdoucím událostem selhání a při splnění podmínek vygeneruje alarm.
**VZOR** definuje regulární výraz, který má být použit při porovnávání, v tomto případě libovolný počet úspěšných operací následovaný choulenými alespoň dvěma po sobě jdoucími selháními.
Úspěch a neúspěch jsou definovány pomocí Return_Code hodnoty a po splnění podmínky jsou **funkce MEASURES** promítány s *ATM_id*, první výstražnou operací a prvním časem upozornění.

Další informace naleznete [v MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Geofencing a geoprostorové dotazy
Azure Stream Analytics poskytuje integrované geoprostorové funkce, které lze použít k implementaci scénářů, jako je správa vozového parku, sdílení jízd, propojená auta a sledování majetku.
Geoprostorová data mohou být ingestována ve formátech GeoJSON nebo WKT jako součást datového proudu událostí nebo referenčních dat.
Například společnost, která se specializuje na výrobu strojů pro tisk pasů, pronajímá své stroje vládám a konzulátům. Umístění těchto strojů je silně kontrolováno, aby se zabránilo nesprávnému umístění a možnému použití pro padělání cestovních pasů. Každý počítač je vybaven gps trackerem, který se předává zpět do úlohy Azure Stream Analytics.
Výrobce by chtěl sledovat umístění těchto strojů a být upozorněni, pokud jeden z nich opustí povolenou oblast, tímto způsobem mohou vzdáleně vypnout, upozornit orgány a získat zařízení.

**Vstup**:

| Equipment_id | Equipment_current_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.1328797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000Z |
| 1 | "POINT(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000z |
| 1 | "POINT(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000z |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000z |

**Vstup referenčních dat**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON(-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.1326165554346 21 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794)" |

**Výstup**:

| Equipment_id | Equipment_alert_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000z |

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

Dotaz umožňuje výrobci sledovat umístění počítačů automaticky, získávání výstrah, když počítač opustí povolenou geografickou zónu. Vestavěná geoprostorová funkce umožňuje uživatelům používat data GPS v rámci dotazu bez knihoven třetích stran.

Další informace najdete v článku [Geofencing a geospatial agregace s Azure Stream Analytics.](geospatial-scenarios.md)

## <a name="get-help"></a>Podpora

Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
