---
title: Agregační funkce v Azure Cosmos DB
description: Přečtěte si o syntaxi agregačních funkcí SQL, typech agregačních funkcí podporovaných službou Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464457"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Agregační funkce v Azure Cosmos DB

Agregační funkce provést výpočet na `SELECT` sadu hodnot v klauzuli a vrátit jednu hodnotu. Například následující dotaz vrátí počet položek `Families` v kontejneru:

## <a name="examples"></a>Příklady

```sql
    SELECT COUNT(1)
    FROM Families f
```

Výsledky jsou následující:

```json
    [{
        "$1": 2
    }]
```

Můžete také vrátit pouze skalární hodnotu agregace pomocí klíčového slova VALUE. Například následující dotaz vrátí počet hodnot jako jedno číslo:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Výsledky jsou následující:

```json
    [ 2 ]
```

Můžete také kombinovat agregace s filtry. Například následující dotaz vrátí počet položek se stavem adresy `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Výsledky jsou následující:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Typy agregačních funkcí

Rozhraní SQL API podporuje následující agregační funkce. `SUM`a `AVG` pracovat s číselnými hodnotami a `COUNT`, `MIN`a `MAX` pracovat s čísly, řetězci, logickými hodnotami a hodnotami null.

| Funkce | Popis |
|-------|-------------|
| POČET | Vrátí počet položek ve výrazu. |
| SUM   | Vrátí součet všech hodnot ve výrazu. |
| MIN   | Vrátí minimální hodnotu ve výrazu. |
| MAX   | Vrátí maximální hodnotu ve výrazu. |
| PRŮMĚR   | Vrátí průměr hodnot ve výrazu. |

Můžete také agregovat přes výsledky iterace pole.

> [!NOTE]
> V Průzkumníku dat na webu Azure Portal mohou dotazy agregace agregovat částečné výsledky pouze na jedné stránce dotazu. Sada SDK vytváří jednu kumulativní hodnotu na všech stránkách. Chcete-li provádět dotazy na agregaci pomocí kódu, potřebujete .NET SDK 1.12.0, .NET Core SDK 1.1.0 nebo Java SDK 1.9.5 nebo vyšší.

## <a name="remarks"></a>Poznámky

Tyto agregované systémové funkce budou mít prospěch z [indexu rozsahu](index-policy.md#includeexclude-strategy). Pokud očekáváte `COUNT`, `SUM`, `MIN` `MAX`, `AVG` nebo na vlastnost, měli byste [zahrnout příslušnou cestu do zásad indexování](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Úvod do Azure Cosmos DB](introduction.md)
- [Systémové funkce](sql-query-system-functions.md)
- [Uživatelem definované funkce](sql-query-udfs.md)