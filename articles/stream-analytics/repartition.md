---
title: Použití repartitioningu k optimalizaci úloh Azure Stream Analytics
description: Tento článek popisuje, jak pomocí oddílů k optimalizaci úloh Azure Stream Analytics, které nelze paralelizovat.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732378"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Použití repartitioningu k optimalizaci zpracování pomocí Azure Stream Analytics

Tento článek ukazuje, jak pomocí oddílů škálovat váš dotaz Azure Stream Analytics pro scénáře, které nelze plně [paralelizovat](stream-analytics-scale-jobs.md).

[Paralelizaci](stream-analytics-parallelization.md) pravděpodobně nebudete moci použít, pokud:

* Klíč oddílu pro vstupní datový proud neřídíte.
* Zdroj "spreje" vstup přes více oddílů, které později je třeba sloučit.

Při zpracování dat v datovém proudu, který není rozdělený podle přirozeného vstupního schématu, jako je například **PartitionId** pro centra událostí, je vyžadováno přerozdělení oddílů nebo přemísťování. Při přerozdělení každý horizontální oddíl může být zpracovány nezávisle, což umožňuje lineárně horizontálního navýšení kapacity z vašeho kanálu streamování.

## <a name="how-to-repartition"></a>Jak rozdělit oddíl

Chcete-li znovu rozdělit, použijte klíčové slovo **INTO** po **příkazu PARTITION BY** v dotazu. Následující příklad rozdělí data podle **DeviceID** do počtu oddílů 10. Hash **DeviceID** se používá k určení, který oddíl musí přijmout který dílčí proud. Data jsou vyprázdněna nezávisle pro každý rozdělený datový proud za předpokladu, že výstup podporuje počet rozdělených zápisů a má 10 oddílů.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

Následující příklad dotazu spojuje dva datové proudy přerozdělená data. Při spojování dvou datových proudů předělaných dat musí mít datové proudy stejný klíč oddílu a počet. Výsledkem je datový proud, který má stejné schéma oddílu.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Výstupní schéma by mělo odpovídat klíči schématu datového proudu a počítat tak, aby každý dílčí datový proud mohl být vyprázdněn nezávisle. Datový proud může být také sloučena a znovu oddíly znovu jiné schéma před vyprázdnění, ale měli byste se vyhnout této metodě, protože přidá k obecné latenci zpracování a zvyšuje využití prostředků.

## <a name="streaming-units-for-repartitions"></a>Jednotky streamování pro předěly

Experimentujte a sledujte využití prostředků úlohy a určete přesný počet oddílů, které potřebujete. Počet [jednotek streamování (SU)](stream-analytics-streaming-unit-consumption.md) musí být upraven podle fyzických prostředků potřebných pro každý oddíl. Obecně platí, že pro každý oddíl je zapotřebí šest su. Pokud není k úloze přiřazendostatek prostředků, systém použije přerozdělení pouze v případě, že bude pro úlohu přínosem.

## <a name="repartitions-for-sql-output"></a>Přeoddíly pro výstup SQL

Pokud vaše úloha používá databázi SQL pro výstup, použijte explicitní dělení tak, aby odpovídalo optimálnímu počtu oddílů, abyste maximalizovali propustnost. Vzhledem k tomu, SQL funguje nejlépe s osmi zapisovače, rozdělení toku na osm před vyprázdnění nebo někde dále proti proudu, může mít prospěch výkon úlohy. 

Pokud existuje více než 8 vstupníoddíly, dědění vstupní schéma dělení nemusí být vhodnou volbou. Zvažte použití [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) v dotazu explicitně zadat počet zapisovačů výstupu. 

Následující příklad čte ze vstupu, bez ohledu na to, že je přirozeně rozdělena a přerozdělí datový proud desetkrát podle dimenze DeviceID a vyprázdní data do výstupu. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Další informace najdete v tématu [Výstup Azure Stream Analytics do Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Stream Analytics](stream-analytics-introduction.md)
* [Využití paralelizace dotazů ve službě Azure Stream Analytics](stream-analytics-parallelization.md)
