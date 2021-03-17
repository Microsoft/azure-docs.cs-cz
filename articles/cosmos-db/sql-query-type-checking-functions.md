---
title: Funkce pro kontrolu typu v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o kontrole typů funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2becc9216d847dfe26d8fd3a433993112fff7980
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546344"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funkce pro kontrolu typů (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Funkce pro kontrolu typů umožňují kontrolovat typ výrazu v rámci dotazu SQL. Můžete použít funkce pro ověřování typu k určení typů vlastností v rámci položek za běhu, když jsou proměnné nebo neznámé. 

## <a name="functions"></a>Funkce

Tady je tabulka podporovaných integrovaných funkcí pro kontrolu typů:

Následující funkce podporují kontrolu typu proti vstupním hodnotám a každý vrací logickou hodnotu.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Další kroky

- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)
- [Agregace](sql-query-aggregate-functions.md)
