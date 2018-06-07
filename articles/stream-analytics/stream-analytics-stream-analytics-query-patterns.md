---
title: Běžné typy dotazů v Azure Stream Analytics
description: Tento článek popisuje počet běžné typy dotazů a návrhy, které jsou užitečné v úlohy Azure Stream Analytics.
services: stream-analytics
author: jseb225
manager: kfile
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: f63ccd62136fe8d556a4cfb591e3294f3751dfb3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652242"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Dotaz příkladů běžných vzorů využití Stream Analytics

## <a name="introduction"></a>Úvod
Dotazy v Azure Stream Analytics jsou vyjádřeny v jazyce SQL jako dotaz. Jazykové konstrukty jsou dokumentovány v článku [Stream Analytics query referenční informace k jazyku](https://msdn.microsoft.com/library/azure/dn834998.aspx) průvodce. 

Návrhu dotazu lze vyjádřit jednoduché průchozí logiku pro přesun dat událostí z jeden vstupní datový proud do jiného úložiště dat výstup. Nebo ji můžete provést odpovídající a dočasné analysis bohaté vzor pro výpočet agregace v různých časových oken jako ukázka TollApp. Data z několika vstupů pro kombinovat streamování událostí a proveďte vyhledávání proti statická referenční data pro obohacení hodnoty události lze propojit. Také můžete zapsat data do více výstupů.

Tento článek popisuje řešení několik běžné typy dotazů, na základě reálného scénářů. Je pracuje a bude aktualizován s novou vzory průběžně.

## <a name="query-example-convert-data-types"></a>Příklad dotazu: Převést datové typy
**Popis**: definování typů vlastností v vstupního datového proudu.
Například váhy car pochází na vstupního datového proudu jako řetězce a je třeba má být převeden na **INT** k provedení **součet** ho nahoru.

**Vstup**:

| Ujistěte se | Čas | Váha |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Výstup**:

| Ujistěte se | Váha |
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

**Vysvětlení**: použití **PŘETYPOVÁNÍ** příkaz v **váhy** pro zadání jeho datového typu pole. Zobrazit seznam podporované datové typy v [datové typy (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Příklad dotazu: použijte jako nebo nebyla chtěli vzor odpovídající
**Popis**: Zkontrolujte, jestli hodnotu pole na události odpovídá určitého.
Například zkontrolujte, že výsledek vrátí desky licencí, které se začínat a končit 9.

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

**Vysvětlení**: použití **jako** příkazu ke kontrole **LicensePlate** pole hodnota. By měl začínat A potom mít libovolný řetězec nula nebo více znaků a pak končit 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Příklad dotazu: Zadejte logiku pro různé případech nebo hodnoty (CASE – příkazy)
**Popis**: Zadejte jiný výpočet pro pole, v závislosti na konkrétní kritérium.
Zadejte například, že řetězec popis Ujistěte se, kolik aut stejného byla dokončena s ve speciálním případě pro 1.

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

**Vysvětlení**: **případ** výraz porovná výraz, který se sada jednoduché výrazů, chcete-li zjistit výsledek. V tomto příkladu se vytváří vehicle s počtem 1 vrátilo popis jiný text než vehicle díky s počtem než 1. 

## <a name="query-example-send-data-to-multiple-outputs"></a>Příklad dotazu: odesílání dat do několik výstupů
**Popis**: posílat data do více cílů výstup z jediné úlohy.
Například analyzovat data pro upozornění na základě prahové hodnoty a archivaci všechny události do úložiště objektů blob.

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

**Vysvětlení**: **INTO** klauzule informuje Stream Analytics který výstupy zapsat data do tohoto příkazu.
První dotazu je průchozí dat přijatých výstupu, který s názvem **ArchiveOutput**.
Druhý dotaz nemá některé jednoduché agregace a filtrování a odesílá výsledky na příjem dat výstrah systém.

Všimněte si, že můžete také znovu použít výsledky běžných výrazech tabulky (odkazu Cte) (například **WITH** příkazy) ve více příkazů výstup. Tato možnost má výhodu v podobě otevírání méně uživatelům vstupního zdroje.
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

## <a name="query-example-count-unique-values"></a>Příklad dotazu: počet jedinečné hodnoty
**Popis**: počet jedinečné pole hodnoty, které se zobrazují v datovém proudu v rámci časové okno.
Například kolik jedinečný díky automobilů předána stánek projedou v okně sekundu 2?

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
**COUNT (odlišné ověřte)** vrátí počet jedinečných hodnot ve **zkontrolujte** sloupec v rámci časové okno.

## <a name="query-example-determine-if-a-value-has-changed"></a>Příklad dotazu: určení, pokud byla hodnota změněna
**Popis**: Podívejte se na předchozí hodnotu k určení, pokud se liší od aktuální hodnotu.
Předchozí car na cestách projedou je třeba vytvořit stejný jako aktuální Auto?

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

**Vysvětlení**: použití **PRODLEVA** prohlížet do jedné události vstupního datového proudu zpět a získat **zkontrolujte** hodnotu. Pak porovnávají ho do **zkontrolujte** hodnotu na aktuální událost a výstupní událost, pokud se liší.

## <a name="query-example-find-the-first-event-in-a-window"></a>Příklad dotazu: Najít první událost v okně
**Popis**: Najít první auto v intervalu každých 10 minut.

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

Teď umožňuje změnit problém a najít první auto konkrétní značky v intervalu každých 10 minut.

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

## <a name="query-example-find-the-last-event-in-a-window"></a>Příklad dotazu: najít poslední událost v okně
**Popis**: najít poslední car v intervalu každých 10 minut.

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

**Vysvětlení**: existují dva kroky v dotazu. První z nich vyhledá nejnovější časové razítko v systému windows 10 minut. Druhý krok spojí výsledky první dotaz s původní datového proudu a vyhledejte události, které odpovídají poslední časová razítka v každém okně. 

## <a name="query-example-detect-the-absence-of-events"></a>Příklad dotazu: zjištění absence událostí
**Popis**: Zkontrolujte, že datový proud má žádná hodnota, která odpovídá určité kritérium.
Například 2 po sobě jdoucích aut ze stejné zkontrolujte zadali silniční projedou v posledních 90 sekund?

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

**Vysvětlení**: použití **PRODLEVA** prohlížet do jedné události vstupního datového proudu zpět a získat **zkontrolujte** hodnotu. Porovnat s **ZKONTROLUJTE** hodnoty v aktuálním události a pak výstupní událost, pokud jsou stejné. Můžete také použít **PRODLEVA** k získání dat o předchozí Auto.

## <a name="query-example-detect-the-duration-between-events"></a>Příklad dotazu: zjistit dobu trvání mezi událostmi
**Popis**: najít trvání dané události. Například zadané webové clickstream, určete čas strávený na funkce.

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

**Vysvětlení**: použití **poslední** funkce načíst poslední **čas** hodnota typu události při **spustit**. **Poslední** využívá **PARTITION BY [user]** indikující, že výsledek se počítá na jedinečný uživatele. Dotaz musí maximální prahovou hodnotu 1 hodina časový rozdíl mezi **spustit** a **Zastavit** události, ale je možné konfigurovat podle potřeby **(LIMIT DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Příklad dotazu: zjistit dobu trvání podmínku
**Popis**: Najít se na jak dlouho podmínku došlo k chybě.
Předpokládejme například, že chyby výsledkem všechny aut, které mají nesprávné váhu (nad 20 000 libra) a musí být vypočteny dobu trvání této chyby.

**Vstup**:

| Ujistěte se | Čas | Váha |
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

**Vysvětlení**: použití **PRODLEVA** zobrazení vstupního datového proudu pro 24 hodin a vyhledejte instance kde **StartFault** a **StopFault** jsou předané váhu < 20000.

## <a name="query-example-fill-missing-values"></a>Příklad dotazu: vyplnění chybějící hodnoty
**Popis**: pro datový proud události, které mají chybějící hodnoty, vytvořit datový proud událostí s pravidelných intervalech.
Například generovat událost každých 5 sekund, která generuje sestavy naposledy zaznamenané datového bodu.

**Vstup**:

| T | hodnota |
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


**Vysvětlení**: Tento dotaz vygeneruje události každých 5 sekund a výstupy poslední událost, který jste získali dříve. [Hopping okno](https://msdn.microsoft.com/library/dn835041.aspx "Hopping okno – Azure Stream Analytics") doba trvání Určuje, jak daleko zpětný dotaz vypadá najít nejnovější událost (v tomto příkladu je 300 sekund).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Příklad dotazu: korelovat dva typy událostí v rámci stejného datového proudu
**Popis**: někdy výstrahy muset vygenerovat na základě více typů událostí, ke kterým došlo v určitý čas rozsah.
Například ve scénáři s IoT pro domácí trouby, musí být vygenerována výstraha když teplota ventilátor je menší než 40 a maximální výkon během posledních 3 minut je menší než 10.

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

| eventTime | deviceId | dočasné | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Krátké okruh vytápění elementy" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Krátké okruh vytápění elementy" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Krátké okruh vytápění elementy" |15 |

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

**Vysvětlení**: první dotaz `max_power_during_last_3_mins`, používá [posuvné okno](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) najít maximální hodnotu senzoru napájení pro každé zařízení během posledních 3 minut. Druhý dotazu je připojený k první dotaz, který vyhledá power hodnotu v okně nejnovější relevantní pro aktuální událost. A pak zadaný podmínky jsou splněny, je vygenerována výstraha pro zařízení.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Příklad dotazu: zpracování událostí, které jsou nezávislé na zařízení hodin zkreslit (substreams)
**Popis**: můžete události dorazí pozdní nebo mimo pořadí z důvodu zkosí hodin mezi událostí producenti hodiny zkosí mezi oddílů nebo latence sítě. V následujícím příkladu hodiny zařízení pro TollID 2 je deset sekund za TollID 1 a hodiny zařízení pro TollID 3 je pět sekund za TollID 1. 


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

**Vysvětlení**: [TIMESTAMP BY OVER](https://msdn.microsoft.com/en-us/azure/stream-analytics/reference/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) klauzule vypadá na každé zařízení časová osa samostatně pomocí substreams. Výstup události pro každý TollID jsou generovány, jak se vypočítávají, což znamená, že tyto události jsou v pořadí s ohledem na každý TollID místo pořadí měnit, jako kdyby byla všechna zařízení na stejném hodiny.


## <a name="get-help"></a>Podpora
Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

