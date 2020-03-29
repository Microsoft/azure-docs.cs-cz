---
title: Systémové funkce v dotazovacím jazyce Azure Cosmos DB
description: Další informace o integrovaných a uživatelem definovaných funkcích systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870526"
---
# <a name="system-functions-azure-cosmos-db"></a>Systémové funkce (Azure Cosmos DB)

 Cosmos DB poskytuje mnoho vestavěných funkcí SQL. Kategorie vestavěných funkcí jsou uvedeny níže.  
  
|Skupina funkcí|Popis|Provoz|  
|--------------|-----------------|-----------------| 
|[Funkce pole](sql-query-array-functions.md)|Funkce pole provádějí operaci se vstupní hodnotou pole a vracejí číselnou, logickou nebo maticovou hodnotu. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md) [ARRAY_SLICE](sql-query-array-slice.md) |
|[Funkce data a času](sql-query-date-time-functions.md)|Funkce data a času umožňují získat aktuální datum a čas utc ve dvou formách; číselné časové razítko, jehož hodnota je unixová epocha v milisekundách nebo jako řetězec, který odpovídá formátu ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimerazítk](sql-query-getcurrenttimestamp.md) |
|[Matematické funkce](sql-query-mathematical-functions.md)|Z nich jsou matematické funkce, z nichž každý provádí výpočet, obvykle na základě vstupních hodnot, které jsou k dispozici jako argumenty, a vrátí číselnou hodnotu. | [ABS,](sql-query-abs.md) [ACOS,](sql-query-acos.md) [ASIN,](sql-query-asin.md) [ATAN,](sql-query-atan.md) [ATN2,](sql-query-atn2.md) [CEILING,](sql-query-ceiling.md) [COS,](sql-query-cos.md) [COT,](sql-query-cot.md) [Degrees,](sql-query-degrees.md) [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10,](sql-query-log10.md) [PI,](sql-query-pi.md) [POWER,](sql-query-power.md) [RADIANS](sql-query-radians.md), [RAND](sql-query-rand.md), [ROUND,](sql-query-round.md) [SIGN,](sql-query-sign.md) [SIN](sql-query-sin.md), [SQRT,](sql-query-sqrt.md) [SQUARE](sql-query-square.md), [TAN,](sql-query-tan.md) [TRUNC](sql-query-trunc.md) |
|[Prostorové funkce](sql-query-spatial-functions.md)|Prostorové funkce provádějí operaci se vstupní hodnotou prostorového objektu a vracejí číselnou nebo logickou hodnotu. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_WITHIN](sql-query-st-within.md) [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md) |
|[Funkce řetězců](sql-query-string-functions.md)|Funkce řetězce provádějí operaci se vstupní hodnotou řetězce a vracejí řetězcovou, číselnou nebo logickou hodnotu. | [Concat](sql-query-concat.md), [contains,](sql-query-contains.md) [endswith](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [left,](sql-query-left.md) [length,](sql-query-length.md) [lower,](sql-query-lower.md) [LTRIM](sql-query-ltrim.md), [REPLACE](sql-query-replace.md), [REPLICATE](sql-query-replicate.md), [REVERSE](sql-query-reverse.md), [RIGHT,](sql-query-right.md) [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToNo,](sql-query-stringtoarray.md) [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [Substring](sql-query-substring.md), [Tostring](sql-query-tostring.md), [TRIM](sql-query-trim.md), [UPPER](sql-query-upper.md) |
|[Funkce kontroly typů](sql-query-type-checking-functions.md)|Funkce kontroly typu umožňují zkontrolovat typ výrazu v dotazech SQL. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL IS_DEFINED](sql-query-is-bool.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md) [,](sql-query-is-string.md) [IS_PRIMITIVE IS_STRING](sql-query-is-primitive.md) [IS_DEFINED](sql-query-is-defined.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Vestavěné versus uživatelem definované funkce (UPROTI)

Pokud aktuálně používáte uživatelem definovanou funkci (UDF), pro kterou je nyní k dispozici vestavěná funkce, bude odpovídající vestavěná funkce rychlejší a efektivnější.

## <a name="built-in-versus-ansi-sql-functions"></a>Vestavěné funkce ANSI SQL

Hlavní rozdíl mezi funkcemi Cosmos DB a FUNKCEMI ANSI SQL spočívá v tom, že funkce Cosmos DB jsou navrženy tak, aby dobře fungovaly s daty bez schématu a smíšených schémat. Například pokud vlastnost chybí nebo má nečíselnou `unknown`hodnotu, jako je , položka je přeskočena namísto vrácení chyby.

## <a name="next-steps"></a>Další kroky

- [Úvod do Azure Cosmos DB](introduction.md)
- [Funkce pole](sql-query-array-functions.md)
- [Funkce data a času](sql-query-date-time-functions.md)
- [Matematické funkce](sql-query-mathematical-functions.md)
- [Prostorové funkce](sql-query-spatial-functions.md)
- [Funkce řetězců](sql-query-string-functions.md)
- [Funkce kontroly typů](sql-query-type-checking-functions.md)
- [Uživatelem definované funkce](sql-query-udfs.md)
- [Agregace](sql-query-aggregates.md)
