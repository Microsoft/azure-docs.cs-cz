---
title: Funkce kontroly typů v dotazovacím jazyce Azure Cosmos DB
description: Další informace o kontrole typů funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349081"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funkce kontroly typů (Azure Cosmos DB)

Funkce kontroly typů umožňují zkontrolovat typ výrazu v rámci dotazu SQL. Funkce kontroly typů můžete použít k určení typů vlastností v rámci položek za běhu, pokud jsou variabilní nebo neznámé. 

## <a name="functions"></a>Funkce

Tady je tabulka podporovaných vestavěných funkcí kontroly typu:

Následující funkce podporují kontrolu typu proti vstupním hodnotám a každá vrátí logickou hodnotu.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Další kroky

- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
- [Uživatelem definované funkce](sql-query-udfs.md)
- [Agregace](sql-query-aggregates.md)
