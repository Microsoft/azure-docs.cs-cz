---
title: Optimalizace Azure Stream Analytics úloh pomocí změny rozdělení na oddíly
description: Tento článek popisuje, jak pomocí změny rozdělení na oddíly optimalizovat Azure Stream Analytics úlohy, které se nedají paralelně rozdělit.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75732378"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Použití změny rozdělení na oddíly pro optimalizaci zpracování pomocí Azure Stream Analytics

V tomto článku se dozvíte, jak použít změnu rozdělení na oddíly pro škálování Azure Stream Analytics dotazů na scénáře, které se nedají plně [paralelismuovat](stream-analytics-scale-jobs.md).

Je možné, že nebudete moci použít [paralelní](stream-analytics-parallelization.md) postup v těchto případech:

* Neovládáte klíč oddílu pro vstupní datový proud.
* Váš zdroj "se zastříká" ve více oddílech, které jsou později potřeba sloučit.

Při zpracování dat v datovém proudu, který není horizontálně dělené podle přirozeného vstupního schématu, jako je například **PartitionID** pro Event Hubs, se vyžaduje přerozdělení na oddíly nebo přerozdělování. Když změníte oddíly, jednotlivé horizontálních oddílů se dají zpracovat nezávisle, což vám umožní lineárně škálovat kanál streamování.

## <a name="how-to-repartition"></a>Postup změny rozdělení

Chcete-li změnit rozdělení na oddíly, použijte klíčové slovo **do** výrazu po příkazu **partition by** v dotazu. Následující příklad rozdělí data podle typu **DeviceID** na počet oddílů na hodnotu 10. Pomocí algoritmu hash typu **DeviceID** se určí, který oddíl bude akceptovat daný podproud. Data se vyprázdní nezávisle pro každý datový proud, za předpokladu, že výstup podporuje dělené zápisy a má 10 oddílů.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

Následující příklad dotazu spojuje dva proudy přerozdělených dat. Při spojování dvou datových proudů předělených dat musí mít proudy stejný klíč oddílu a počet. Výsledkem je datový proud, který má stejné schéma oddílu.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Výstupní schéma by se mělo shodovat s klíčem a počtem streamového schématu, aby bylo možné jednotlivé podproudy vyprázdnit nezávisle. Datový proud je také možné sloučit a znovu rozdělit na jiné schéma před vyprázdněním, ale je třeba se tomuto způsobu vyhnout, protože přičítá k obecné latenci zpracování a zvyšuje využití prostředků.

## <a name="streaming-units-for-repartitions"></a>Jednotky streamování pro změny oddílů

Experimentujte a sledujte využití prostředků vaší úlohy a určete přesný počet oddílů, které potřebujete. Počet [jednotek streamování (SU)](stream-analytics-streaming-unit-consumption.md) se musí upravit podle fyzických prostředků potřebných pro každý oddíl. Obecně platí, že pro každý oddíl je potřeba šest SUs. Pokud se k úloze přiřadí nedostatečné prostředky, systém použije přerozdělení jenom v případě, že má úlohu přednost.

## <a name="repartitions-for-sql-output"></a>Změny oddílů pro výstup SQL

Pokud vaše úloha používá pro výstup SQL Database, použijte explicitní přerozdělení na oddíly, aby se maximalizovala propustnost. Vzhledem k tomu, že SQL funguje nejlépe s osmi moduly pro zápis, je možné tento tok změnit na osm, než se vyprázdní, nebo někam do dalšího nadřazeného, může to přinést výkon úloh. 

Pokud je k dispozici více než 8 vstupních oddílů, nemusí být dědění vstupního schématu dělení vhodné. Zvažte možnost [použít v](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) dotazu k explicitnímu zadání počtu modulů pro zápis výstupu. 

Následující příklad přečte ze vstupu bez ohledu na to, že je přirozeně rozdělený, a přerozdělení streamu desetinásobné podle dimenze DeviceID a vyprázdní data na výstup. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Další informace najdete v tématu [Azure Stream Analytics výstup do Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Stream Analytics](stream-analytics-introduction.md)
* [Využití paralelismu dotazů v Azure Stream Analytics](stream-analytics-parallelization.md)
