---
title: Agregační funkce v Azure Cosmos DB
description: Přečtěte si o syntaxi agregační funkce SQL, typech agregačních funkcí, které podporuje Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: tisande
ms.openlocfilehash: f04590e78b5f1ea9d5e00c9f3d42c2fc32bebc5f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "96001775"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Agregační funkce v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Agregační funkce provádějí výpočet pro sadu hodnot v `SELECT` klauzuli a vrací jedinou hodnotu. Například následující dotaz vrátí počet položek v rámci `Families` kontejneru:

## <a name="examples"></a>Příklady

Při použití nástroje `COUNT()` můžete jako vstup použít libovolný platný skalární výraz, jako `1` je například.

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

## <a name="types-of-aggregate-functions"></a>Typy agregačních funkcí

Rozhraní SQL API podporuje následující agregační funkce. `SUM` a `AVG` pracují s numerickými hodnotami, a `COUNT` , `MIN` a `MAX` pracují na číslech, řetězcích, logických hodnotách a hodnotách null.

| Funkce | Description |
|-------|-------------|
| POČET | Vrátí počet položek ve výrazu. |
| SUM   | Vrátí součet všech hodnot ve výrazu. |
| MIN   | Vrátí minimální hodnotu ve výrazu. |
| MAX   | Vrátí maximální hodnotu ve výrazu. |
| PRŮMĚR   | Vrátí průměr hodnot ve výrazu. |

Můžete také agregovat výsledky iterace pole.

> [!NOTE]
> Dotazy agregace v Průzkumník dat Azure Portal můžou agregovat částečné výsledky jenom na jednu stránku dotazu. Sada SDK vytvoří v rámci všech stránek jednu kumulativní hodnotu. Chcete-li provádět agregační dotazy pomocí kódu, potřebujete sadu .NET SDK 1.12.0, .NET Core SDK 1.1.0 nebo Java SDK 1.9.5 nebo vyšší.

## <a name="remarks"></a>Poznámky

Tyto agregované systémové funkce budou využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy). Pokud očekáváte `COUNT` ,,,, `SUM` `MIN` `MAX` nebo `AVG` na vlastnost, měli byste [Zahrnout relevantní cestu do zásad indexování](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Systémové funkce](sql-query-system-functions.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)