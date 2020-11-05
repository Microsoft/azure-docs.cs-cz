---
title: Mezery při vyplňování a imputace chybějících hodnot – Azure SQL Edge
description: Další informace o mezerách při vyplňování a imputaci chybějících hodnot ve službě Azure SQL Edge
keywords: SQL Edge, časové řady
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: eb1b3534481c55723bd5d13760259b18e0b5cce2
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392040"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>Mezery při vyplňování a imputace chybějících hodnot 

Při práci s daty časových řad je často možné, že data časové řady chybějící hodnoty pro atributy. Je také možné, že vzhledem k povaze dat nebo v důsledku přerušení sběru dat v datové sadě existují časové *mezery* .

Například při shromažďování statistik využívání energie pro inteligentní zařízení, kdykoli zařízení nefunguje, budou v statistikách využití mezery. Podobně ve scénáři shromažďování dat telemetrie počítačů je možné, že různé senzory jsou nakonfigurované tak, aby emitují data v různých intervalech, což pro senzory vyplývají z chybějících hodnot. Například pokud existují dva senzory, napětí a tlak, nakonfigurované v 100 Hz a frekvence 10 Hz, senzor napětí vygeneruje data každé každé setiny sekundy, zatímco snímač tlaku bude vysílat data pouze každou desetinu sekundy.

Následující tabulka popisuje datovou sadu telemetrie počítačů, která byla shromážděna v intervalu 1 sekund. 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

Existují dva důležité charakteristiky předchozí datové sady. 

- Datová sada neobsahuje žádné datové body související s několika časovými razítky `2020-09-07 06:14:47.000` , `2020-09-07 06:14:48.000` ,, `2020-09-07 06:14:50.000` `2020-09-07 06:14:53.000` a `2020-09-07 06:14:55.000` . Tato časová razítka jsou v datové sadě *mezerami* .  
- K dispozici jsou hodnoty, které jsou reprezentovány jako hodnota `null` pro hodnoty napětí a zatížení. 

## <a name="gap-filling"></a>Vyplňování mezer 

Vyplňování mezer je technika, která pomáhá vytvořit souvislou a seřazenou sadu časových razítek pro usnadnění analýzy dat časových řad. Nejjednodušší způsob, jak vyplnit mezery v datové sadě Azure SQL Edge, je definovat dočasnou tabulku s požadovanou časovou distribucí a pak provést `LEFT OUTER JOIN` `RIGHT OUTER JOIN` operaci nebo v tabulce DataSet. 

Při přebírání `MachineTelemetry` dat uvedených výše jako příklad lze použít následující dotaz k vygenerování souvislé seřazené sady časových razítek pro účely analýzy. 

> [!NOTE]
> Následující dotaz vygeneruje chybějící řádky s hodnotami a hodnotami časového razítka `null` pro atributy. 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
Výše uvedený dotaz vytvoří následující výstup obsahující všechna časová razítka s *jedním druhým* v zadaném rozsahu.

Toto je sada výsledků dotazu.

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>Zadávání chybějících hodnot

Předchozí dotaz vygeneroval chybějící časová razítka pro analýzu dat, ale nenahradila žádné chybějící hodnoty (reprezentované jako null) pro `voltage` a `pressure` čtení. Ve službě Azure SQL Edge byl do jazyka T-SQL a do funkcí přidaných Nová syntaxe `LAST_VALUE()` `FIRST_VALUE()` , která poskytuje mechanismy pro imputace chybějících hodnot na základě předchozích nebo následujících hodnot v datové sadě. 

Nová syntaxe přidá `IGNORE NULLS` klauzule and `RESPECT NULLS` k `LAST_VALUE()` `FIRST_VALUE()` funkcím a. Následující dotaz na `MachineTelemetry` datovou sadu vypočítá chybějící hodnoty pomocí funkce last_value, kde chybějící hodnoty jsou nahrazené poslední zjištěnou hodnotou v datové sadě.

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
Toto je sada výsledků dotazu.

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

Následující dotaz přijedná chybějící hodnoty pomocí `LAST_VALUE()` `FIRST_VALUE` funkce a. Pro sloupec výstup `ImputedVoltage` chybějící hodnoty jsou nahrazeny poslední zjištěnou hodnotou, zatímco pro výstupní sloupec `ImputedPressure` jsou chybějící hodnoty nahrazeny další zjištěnou hodnotou v datové sadě. 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
Toto je sada výsledků dotazu.

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> Výše uvedený dotaz používá `FIRST_VALUE()` funkci k nahrazení chybějících hodnot další zjištěnou hodnotou. Stejný výsledek lze dosáhnout pomocí `LAST_VALUE()` funkce s `ORDER BY <ordering_column> DESC` klauzulí.

## <a name="next-steps"></a>Další kroky 

- [FIRST_VALUE (Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%252fazure%252fazure-sql-edge%252ftoc.json)
- [LAST_VALUE (Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%252fazure%252fazure-sql-edge%252ftoc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [Agregační funkce (Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)