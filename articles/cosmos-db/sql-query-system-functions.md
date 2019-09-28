---
title: Systémové funkce v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkcích systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 98a087f02035545b828cfcf11c58d03d02a5e76a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349175"
---
# <a name="system-functions-azure-cosmos-db"></a>Systémové funkce (Azure Cosmos DB)

 Cosmos DB poskytuje mnoho předdefinovaných funkcí SQL. Kategorie předdefinované funkce jsou uvedeny níže.  
  
|Skupina – funkce|Popis|Operace|  
|--------------|-----------------|-----------------| 
|[Funkce pole](sql-query-array-functions.md)|Funkce pole provádění operací na hodnotu vstupního pole a vrácené číselné, logickou hodnotu nebo hodnotu pole. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Funkce pro datum a čas](sql-query-date-time-functions.md)|Funkce data a času vám umožní získat aktuální datum a čas UTC ve dvou formách. číselné časové razítko, jehož hodnota je epocha systému UNIX v milisekundách nebo jako řetězec, který odpovídá formátu ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Matematické funkce](sql-query-mathematical-functions.md)|Matematické funkce provádí výpočet, obvykle podle vstupní hodnoty, které jsou k dispozici jako argumenty a vrátit číselnou hodnotu. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [STROP](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [STUPNĚ](sql-query-degrees.md), [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10 –](sql-query-log10.md), [PI](sql-query-pi.md), [POWER](sql-query-power.md), [RADIÁNY](sql-query-radians.md), [RAND](sql-query-rand.md), [ROUND](sql-query-round.md), [SIGN](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [SQUARE](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNC –](sql-query-trunc.md) |
|[Prostorové funkce](sql-query-spatial-functions.md)|Prostorové funkce provádění operací na prostorový vstupní hodnotu a vrátí číslo nebo logickou hodnotu. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Funkce řetězců](sql-query-string-functions.md)|Řetězcové funkce provádění operací na vstupní hodnotu řetězce a vrátí řetězec, číslo nebo logickou hodnotu. | [Concat](sql-query-concat.md), [Contains](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [Left](sql-query-left.md), [Length](sql-query-length.md), [Lower](sql-query-lower.md), [LTrim](sql-query-ltrim.md), [Replace](sql-query-replace.md), [Replica](sql-query-replicate.md), [revers](sql-query-reverse.md), [Right](sql-query-right.md), [RTrim](sql-query-rtrim.md), [ STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [substring](sql-query-substring.md), [ToString](sql-query-tostring.md), [Trim](sql-query-trim.md), [Upper](sql-query-upper.md) |
|[Kontrola funkce typu](sql-query-type-checking-functions.md)|Funkce pro kontrolu typů umožňují zkontrolujte typ výrazu v rámci dotazů SQL. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Předdefinované funkce versus uživatelsky definované funkce (UDF)

Pokud aktuálně používáte uživatelsky definovanou funkci (UDF), pro kterou je teď k dispozici integrovaná funkce, bude mít odpovídající integrovaná funkce rychlejší spuštění a efektivnější.

## <a name="built-in-versus-ansi-sql-functions"></a>Integrované funkce oproti ANSI funkcí SQL

Hlavním rozdílem mezi funkcemi Cosmos DB a ANSI funkcí SQL je, že funkce Cosmos DB jsou navržené tak, aby dobře fungovaly s daty schématu a se smíšenými schématy. Například pokud vlastnost chybí nebo má jinou než číselnou hodnotu jako `unknown`, položka se přeskočí místo vrácení chyby.

## <a name="next-steps"></a>Další kroky

- [Úvod do Azure Cosmos DB](introduction.md)
- [Funkce pole](sql-query-array-functions.md)
- [Funkce pro datum a čas](sql-query-date-time-functions.md)
- [Matematické funkce](sql-query-mathematical-functions.md)
- [Prostorové funkce](sql-query-spatial-functions.md)
- [Funkce řetězců](sql-query-string-functions.md)
- [Kontrola funkce typu](sql-query-type-checking-functions.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)
- [Agregace](sql-query-aggregates.md)
