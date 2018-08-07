---
title: Běžné vzory dotazů ve službě Azure Stream Analytics
description: Tento článek popisuje některé běžné vzory dotazů a návrhy, které jsou užitečné pro úlohy Azure Stream Analytics.
services: stream-analytics
author: jseb225
manager: kfile
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 7f171fa1eb8c91b55119d0308b57fe3d3e70261b
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578887"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Příklady pro běžné vzory využití Stream Analytics dotazů

## <a name="introduction"></a>Úvod
Dotazy ve službě Azure Stream Analytics jsou vyjádřeny v jazyce podobném SQL dotazu. Jazykové konstrukce jsou dokumentovány v článku [Stream Analytics query referenční informace k jazyku](https://msdn.microsoft.com/library/azure/dn834998.aspx) průvodce. 

Návrh dotazu můžete vyjádřit jednoduché průchozí logiku pro přesun dat události z jednoho vstupního datového proudu do jiného úložiště dat výstup. Nebo můžete provést bohaté vzor odpovídající a dočasné analýzy pro výpočet agregace v různých časových oken viz ukázka TollApp. Můžete připojit data z více vstupů kombinovat, streamování událostí, a proveďte vyhledávání proti statická referenční data rozšiřuje hodnoty událostí. Také můžete zapisovat data do více výstupů.

Tento článek popisuje, jak řešení několik běžné vzory dotazů založené na scénářích ze skutečného světa. Je ve vývoji a nadále se nová schémata průběžně aktualizovat.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Práce s komplexními datovými typy ve formátu JSON a AVRO 
Azure Stream Analytics podporuje zpracování událostí v CSV, JSON a Avro datových formátů.
Komplexní typy, jako jsou vnořené objekty (záznamy) nebo pole může obsahovat JSON a Avro. Chcete-li pracovat s těmito komplexní datové typy, najdete [Parsování formátu JSON a AVRO data](stream-analytics-parsing-json.md) článku.


## <a name="query-example-convert-data-types"></a>Příklad dotazu: Převést datové typy
**Popis**: definování typů vlastností vstupního datového proudu.
Například váha Auto se chystá vstupního datového proudu jako řetězce a musí být převeden na **INT** provádět **součet** jej.

**Vstup**:

| Ujistěte se | Čas | Hmotnost |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Výstup**:

| Ujistěte se | Hmotnost |
| --- | --- |
| Honda |3000 |

**Řešení**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Vysvětlení**: použití **PŘETYPOVÁNÍ** příkaz v **váha** pro zadání jeho datového typu pole. Seznam podporované datové typy v [datové typy (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Příklad dotazu: použití je nebo není chtěli vzorovou shodu
**Popis**: Zkontrolujte, jestli hodnota pole na událost odpovídá vzoru pro určité.
Třeba zkontrolujte, že výsledek vrátí talířů licence, které začínají na A a na konci 9.

**Vstup**:

| Ujistěte se | LicensePlate | Čas |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Výstup**:

| Ujistěte se | LicensePlate | Čas |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Řešení**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Vysvětlení**: použijte **jako** příkaz **LicensePlate** hodnota pole. By měl začínat obsahujícím A potom mít libovolný řetězec nula nebo více znaků a potom končit 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Příklad dotazu: Zadejte logiku pro různé případy/hodnoty (příkazy CASE)
**Popis**: poskytují různé výpočtu pro pole, na základě konkrétní kritéria.
Například poskytují řetězec popis Ujistěte se, kolik auta stejného proběhl úspěšně, s zvláštní případ 1.

**Vstup**:

| Ujistěte se | Čas |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Výstup**:

| CarsPassed | Čas |
| --- | --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Řešení**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Vysvětlení**: **případ** výraz porovnává sadu jednoduché výrazy k určení výsledků výrazu. V tomto příkladu je vozidla s počtem 1 vrátil popis jiný text než vozidla díky s počtem než 1. 

## <a name="query-example-send-data-to-multiple-outputs"></a>Příklad dotazu: odesílání dat do více výstupů
**Popis**: odesílání dat do více cílů výstup z jedné úlohy.
Například analyzovat data výstrahy založené na prahových hodnotách a archivovat všechny události do úložiště objektů blob.

**Vstup**:

| Ujistěte se | Čas |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Ujistěte se | Čas |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Ujistěte se | Čas | Počet |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Řešení**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
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

**Vysvětlení**: **INTO** klauzule říká Stream Analytics který výstupy zapsat data do tohoto příkazu.
První dotazu je průchozí dotaz, data přijatá do výstupu, který s názvem **ArchiveOutput**.
Druhý dotaz provádí některé jednoduché agregace a filtrování a to odešle výsledky na příjem dat systému výstrah.

Všimněte si, že můžete také znovu použít výsledky běžné tabulkové výrazy (Cte) (například **WITH** příkazy) ve více příkazech výstup. Tuto možnost má výhodu v podobě otevírání méně čtenářům vstupního zdroje.
Příklad: 

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

## <a name="query-example-count-unique-values"></a>Příklad dotazu: počet jedinečných hodnot
**Popis**: počet jedinečných hodnot pole, které se zobrazují v datovém proudu v rámci časové okno.
Například kolik jedinečný díky automobilů předává linka z mýtných bran v okně 2sekundové?

**Vstup**:

| Ujistěte se | Čas |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Výstup:**

| CountMake | Čas |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Řešení:**

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**Vysvětlení:**
**COUNT (DISTINCT zkontrolujte)** vrátí počet jedinečných hodnot v **zkontrolujte** sloupce v rámci časové okno.

## <a name="query-example-determine-if-a-value-has-changed"></a>Příklad dotazu: určení, pokud byla hodnota změněna
**Popis**: Podívejte se na předchozí hodnotu k určení, zda je jiná než aktuální hodnota.
Předchozí automobilu na cestách linka je například stejné značky jako aktuální car?

**Vstup**:

| Ujistěte se | Čas |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Výstup**:

| Ujistěte se | Čas |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Řešení**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Vysvětlení**: použití **PRODLEVA** náhled do jedné události vstupního datového proudu zpět a získat **zkontrolujte** hodnotu. Pak porovnat s **zkontrolujte** hodnoty na aktuální událost tak za výstupní události, pokud se liší.

## <a name="query-example-find-the-first-event-in-a-window"></a>Příklad dotazu: v okně Najít první událost
**Popis**: první auto hledání každých 10 minut.

**Vstup**:

| LicensePlate | Ujistěte se | Čas |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Výstup**:

| LicensePlate | Ujistěte se | Čas |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Řešení**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Teď můžeme změnit problém a najít první auto konkrétní značku v každé každých 10 minut.

| LicensePlate | Ujistěte se | Čas |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Řešení**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-the-last-event-in-a-window"></a>Příklad dotazu: v okně Najít poslední události
**Popis**: najít poslední car v každé každých 10 minut.

**Vstup**:

| LicensePlate | Ujistěte se | Čas |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Výstup**:

| LicensePlate | Ujistěte se | Čas |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Řešení**:

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

**Vysvětlení**: existují dva kroky v dotazu. První z nich najde poslední časové razítko v systému windows 10 minut. V druhém kroku spojí výsledky prvního dotazu s původního datového proudu událostí, které odpovídají poslední časová razítka v každé okno Najít. 

## <a name="query-example-detect-the-absence-of-events"></a>Příklad dotazu: zjištění neexistence událostí
**Popis**: Zkontrolujte, že datový proud nemá žádnou hodnotu, která odpovídá určité kritérium.
Například 2 po sobě jdoucích auta ze stejné značky zadali silniční linka během posledních 90 sekund?

**Vstup**:

| Ujistěte se | LicensePlate | Čas |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Výstup**:

| Ujistěte se | Čas | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Řešení**:

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

**Vysvětlení**: použití **PRODLEVA** náhled do jedné události vstupního datového proudu zpět a získat **zkontrolujte** hodnotu. Porovnat **provést** hodnotu aktuální události a události ve výstupu, pokud jsou stejné. Můžete také použít **PRODLEVA** k získání dat o předchozí automobilu.

## <a name="query-example-detect-the-duration-between-events"></a>Příklad dotazu: zjistit dobu trvání mezi událostmi
**Popis**: najít doba trvání dané události. Například s ohledem navštívených webových stránkách, určete čas strávený na funkce.

**Vstup**:  

| Uživatel | Funkce | Událost | Čas |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Konec |2015-01-01T00:00:08.0000000Z |

**Výstup**:  

| Uživatel | Funkce | Doba trvání |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Řešení**:

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Vysvětlení**: použijte **poslední** funkce k načtení poslední **čas** hodnotu, pokud byl typ události **Start**. **Poslední** funkce používá **PARTITION BY [user]** k označení, že výsledek je vypočítán na jedinečného uživatele. Dotaz musí maximální prahovou hodnotu 1 hodina časový rozdíl mezi **Start** a **Zastavit** události, ale je možné konfigurovat podle potřeby **(LIMIT DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Příklad dotazu: zjistit dobu trvání podmínku
**Popis**: zjištění jak dlouho podmínku došlo k chybě.
Předpokládejme například, že chyby výsledkem všech auta mají nesprávnou hmotnost (výše 20 000 librách) a musí být vypočítán doba trvání této chyby.

**Vstup**:

| Ujistěte se | Čas | Hmotnost |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Výstup**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Řešení**:

````
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
````

**Vysvětlení**: použití **PRODLEVA** zobrazení vstupního datového proudu po dobu 24 hodin a hledat instance where **StartFault** a **StopFault** jsou předané váha < 20000.

## <a name="query-example-fill-missing-values"></a>Příklad dotazu: vyplnit chybějící hodnoty
**Popis**: datový proud událostí, které chybí některé hodnoty a vytvořit datový proud událostí s pravidelných intervalech.
Například vygenerují událost každých 5 sekund, který bude hlásit nedávno zobrazené datového bodu.

**Vstup**:

| t | hodnota |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Výstup (prvních 10 řádků)**:

| windowend | lastevent.t | lastevent.Value |
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

**Řešení**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Vysvětlení**: Tento dotaz generuje události každých 5 sekund a vypíše poslední událost, která dříve byla přijata. [Hopping okno](https://msdn.microsoft.com/library/dn835041.aspx "Hopping okno – Azure Stream Analytics") doba určuje, jak daleko back dotaz vyhledá najít nejnovější události (v tomto příkladu je 300 sekund).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Příklad dotazu: porovnat dva typy událostí v rámci stejného datového proudu
**Popis**: někdy výstrahy musí být generovány na základě více typů událostí, ke kterým došlo v určitý čas rozsah.
Například ve scénáři IoT pro domácí trouby musí být vygenerována výstraha při teploty ventilátor je menší než 40 a maximální výkon během posledních 3 minut je menší než 10.

**Vstup**:

| time | deviceId | sensorName | hodnota |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | "power" |8 |

**Výstup**:

| čas události | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Zkrácené vytápění prvky" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Zkrácené vytápění prvky" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Zkrácené vytápění prvky" |15 |

**Řešení**:

````
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp AS windowTime,
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
````

**Vysvětlení**: první dotaz `max_power_during_last_3_mins`, používá [posuvné okno](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) k nalezení maximální hodnoty pro každé zařízení, snímače power během posledních 3 minut. Druhý dotaz je připojen k první dotaz, který vyhledá power hodnotu v okně nejnovější relevantní pro aktuální událost. A potom, pokud jsou splněny podmínky, vygeneruje se výstraha pro zařízení.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Příklad dotazu: zpracování událostí, které jsou nezávislé na zařízení hodin zkreslit (substreams)
**Popis**: můžete události dorazí pozdě nebo mimo pořadí kvůli časovým nepřesnostem mezi producentů událostí hodiny zkosí mezi oddíly nebo latence sítě. V následujícím příkladu je deset sekund za TollID 1 hodiny zařízení TollID 2 a zařízení účtovat poplatky za TollID 3 je pět sekund za TollID 1. 


**Vstup**:
| LicensePlate | Ujistěte se | Čas | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000Z | 3 |

**Výstup**:
| TollID | Počet |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Řešení**:

````
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId

````

**Vysvětlení**: [TIMESTAMP BY OVER](https://msdn.microsoft.com/azure/stream-analytics/reference/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) klauzule vypadá na každé zařízení časová osa samostatně pomocí substreams. Výstupní události pro každý TollID jsou generovány, jak se zpracovávají, což znamená, že tyto události jsou v pořadí s ohledem na každý TollID místo se přeuspořádány, jako kdyby byla všechna zařízení ve stejném formátu.


## <a name="get-help"></a>Podpora
Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

