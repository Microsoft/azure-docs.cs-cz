---
title: Funkce pro kontrolu typu v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o kontrole typů funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "71349081"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funkce pro kontrolu typů (Azure Cosmos DB)

Funkce pro kontrolu typů umožňují kontrolovat typ výrazu v rámci dotazu SQL. Můžete použít funkce pro ověřování typu k určení typů vlastností v rámci položek za běhu, když jsou proměnné nebo neznámé. 

## <a name="functions"></a>Functions

Tady je tabulka podporovaných integrovaných funkcí pro kontrolu typů:

Následující funkce podporují kontrolu typu proti vstupním hodnotám a každý vrací logickou hodnotu.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Další kroky

- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)
- [Agregace](sql-query-aggregates.md)
