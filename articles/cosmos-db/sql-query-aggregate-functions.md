---
title: Agregační funkce v Azure Cosmos DB
description: Přečtěte si o syntaxi agregační funkce SQL, typech agregačních funkcí, které podporuje Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553384"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Agregační funkce v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Agregační funkce provádějí výpočet pro sadu hodnot v `SELECT` klauzuli a vrací jedinou hodnotu. Například následující dotaz vrátí počet položek v rámci kontejneru:

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>Typy agregačních funkcí

Rozhraní SQL API podporuje následující agregační funkce. `SUM` a `AVG` pracují s numerickými hodnotami, a `COUNT` , `MIN` a `MAX` pracují na číslech, řetězcích, logických hodnotách a hodnotách null.

| Funkce | Popis |
|-------|-------------|
| [VOLÁNÍ](sql-query-aggregate-avg.md) | Vrátí průměr hodnot ve výrazu. |
| [COUNT](sql-query-aggregate-count.md) | Vrátí počet položek ve výrazu. |
| [MAX](sql-query-aggregate-max.md) | Vrátí maximální hodnotu ve výrazu. |
| [MIN](sql-query-aggregate-min.md) | Vrátí minimální hodnotu ve výrazu. |
| [SUM](sql-query-aggregate-sum.md) | Vrátí součet všech hodnot ve výrazu. |


Můžete také vrátit pouze skalární hodnotu agregace pomocí klíčového slova VALUE. Například následující dotaz vrátí počet hodnot jako jedno číslo:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Výsledky jsou následující:

```json
    [ 2 ]
```

Agregace můžete kombinovat i s filtry. Například následující dotaz vrátí počet položek se stavem adresa `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Výsledky jsou následující:

```json
    [ 1 ]
```

## <a name="remarks"></a>Poznámky

Tyto agregované systémové funkce budou využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy). Pokud očekáváte `AVG` , `COUNT` ,, `MAX` `MIN` nebo `SUM` na vlastnost, měli byste [Zahrnout relevantní cestu do zásad indexování](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Systémové funkce](sql-query-system-functions.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)