---
title: Systémové funkce v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o integrovaných a uživatelsky definovaných funkcích systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 36ebe80671c77cc83ddba770e3259f6542472e58
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526835"
---
# <a name="system-functions-azure-cosmos-db"></a>Systémové funkce (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Cosmos DB poskytuje mnoho vestavěných funkcí SQL. Kategorie integrovaných funkcí jsou uvedené níže.  
  
|Skupina funkcí|Description|Operace|  
|--------------|-----------------|-----------------| 
|[Funkce pro práci s poli](sql-query-array-functions.md)|Funkce pole provádí operaci u vstupní hodnoty pole a vrací číselnou, logickou nebo hodnotu pole. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md) [ARRAY_SLICE](sql-query-array-slice.md) |
|[Funkce pro datum a čas](sql-query-date-time-functions.md)|Funkce data a času vám umožní získat aktuální datum a čas UTC ve dvou formách. číselné časové razítko, jehož hodnota je epocha systému UNIX v milisekundách nebo jako řetězec, který odpovídá formátu ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md), [GetCurrentTicks](sql-query-getcurrentticks.md) |
|[Matematické funkce](sql-query-mathematical-functions.md)|Matematické funkce každý provádí výpočet, obvykle na základě vstupních hodnot, které jsou k dispozici jako argumenty a vracejí číselnou hodnotu. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [asin](sql-query-asin.md), [Atan](sql-query-atan.md), [ATN2](sql-query-atn2.md), [strop](sql-query-ceiling.md), [cos](sql-query-cos.md), [COT](sql-query-cot.md), [stupně](sql-query-degrees.md), [exp](sql-query-exp.md), [Floor](sql-query-floor.md), [log](sql-query-log.md), [log10 –](sql-query-log10.md), [Pi](sql-query-pi.md), [Power](sql-query-power.md), [radiáns](sql-query-radians.md), [Rand](sql-query-rand.md), [Round](sql-query-round.md), [Sign](sql-query-sign.md), [Sin](sql-query-sin.md), [Sqrt](sql-query-sqrt.md), [Square](sql-query-square.md), [tan](sql-query-tan.md), [TRUNC –](sql-query-trunc.md) |
|[Prostorové funkce](sql-query-spatial-functions.md)|Prostorové funkce provádějí operaci na vstupní hodnotě prostorového objektu a vracejí numerickou nebo logickou hodnotu. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md) [ST_WITHIN](sql-query-st-within.md) |
|[Funkce řetězců](sql-query-string-functions.md)|Řetězcové funkce provádějí operaci na vstupní hodnotě řetězce a vracejí řetězec, číselnou nebo logickou hodnotu. | [Concat](sql-query-concat.md), [Contains](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [Left](sql-query-left.md), [Length](sql-query-length.md), [Lower](sql-query-lower.md), [LTrim](sql-query-ltrim.md), [REGEXMATCH](sql-query-regexmatch.md)[Replace](sql-query-replace.md), [Replica](sql-query-replicate.md), [Reverse](sql-query-reverse.md), [Right](sql-query-right.md), [RTrim](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [podřetězec](sql-query-substring.md), [ToString](sql-query-tostring.md), [Trim](sql-query-trim.md), [Upper](sql-query-upper.md) |
|[Funkce kontroly typů](sql-query-type-checking-functions.md)|Funkce kontroly typů umožňují kontrolovat typ výrazu v rámci dotazů SQL. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Předdefinované funkce versus uživatelsky definované funkce (UDF)

Pokud aktuálně používáte uživatelsky definovanou funkci (UDF), pro kterou je teď k dispozici integrovaná funkce, bude mít odpovídající integrovaná funkce rychlejší spuštění a efektivnější.

## <a name="built-in-versus-ansi-sql-functions"></a>Integrované funkce oproti ANSI funkcí SQL

Hlavním rozdílem mezi funkcemi Cosmos DB a ANSI funkcí SQL je, že funkce Cosmos DB jsou navržené tak, aby dobře fungovaly s daty schématu a se smíšenými schématy. Například pokud vlastnost chybí nebo má jinou než číselnou hodnotu `undefined` , položka je vynechána místo vrácení chyby.

## <a name="next-steps"></a>Další kroky

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Funkce pro práci s poli](sql-query-array-functions.md)
- [Funkce data a času](sql-query-date-time-functions.md)
- [Matematické funkce](sql-query-mathematical-functions.md)
- [Prostorové funkce](sql-query-spatial-functions.md)
- [Funkce řetězců](sql-query-string-functions.md)
- [Funkce kontroly typů](sql-query-type-checking-functions.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)
- [Agregace](sql-query-aggregate-functions.md)
