---
title: Agregační funkce v Azure Cosmos DB
description: Přečtěte si o syntaxi agregační funkce SQL, typech agregačních funkcí, které podporuje Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: tisande
ms.openlocfilehash: df9700dd51c8915ff28c34cf0a29c2f5e48baa44
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897832"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Agregační funkce v Azure Cosmos DB

Agregační funkce provádějí výpočet pro sadu hodnot v klauzuli SELECT a vracejí jedinou hodnotu. Například následující dotaz vrátí počet položek v kontejneru `Families`:

## <a name="examples"></a>Příklady

```sql
    SELECT COUNT(1)
    FROM Families f
```

Výsledky jsou:

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

Výsledky jsou:

```json
    [ 2 ]
```

Agregace můžete kombinovat i s filtry. Například následující dotaz vrátí počet položek se stavem adresy `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Výsledky jsou:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Typy agregačních funkcí

Rozhraní SQL API podporuje následující agregační funkce. SUM a AVG pracují na numerických hodnotách a počet, minimální a maximální práce na číslech, řetězcích, logických hodnotách a hodnotách null.

| Funkce | Popis |
|-------|-------------|
| POČET | Vrátí počet položek ve výrazu. |
| SOUČET   | Vrátí součet všech hodnot ve výrazu. |
| MIN   | Vrátí minimální hodnotu ve výrazu. |
| MAX.   | Vrátí maximální hodnotu ve výrazu. |
| PRŮMĚR   | Vrátí průměr hodnot ve výrazu. |

Můžete také agregovat výsledky iterace pole.

> [!NOTE]
> Dotazy agregace v Průzkumník dat Azure Portal můžou agregovat částečné výsledky jenom na jednu stránku dotazu. Sada SDK vytvoří v rámci všech stránek jednu kumulativní hodnotu. Chcete-li provádět agregační dotazy pomocí kódu, potřebujete sadu .NET SDK 1.12.0, .NET Core SDK 1.1.0 nebo Java SDK 1.9.5 nebo vyšší.

## <a name="next-steps"></a>Další kroky

- [Úvod do Azure Cosmos DB](introduction.md)
- [Systémové funkce](sql-query-system-functions.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)