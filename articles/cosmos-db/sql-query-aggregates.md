---
title: Agregační funkce ve službě Azure Cosmos DB
description: Další informace o syntaxi agregační funkci SQL pro službu Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: a6937e9e811ea8e44eda6f2bcb5d2c7d78db4934
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342879"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Agregační funkce ve službě Azure Cosmos DB

Agregační funkce provádí výpočet na sadu hodnot v klauzuli SELECT a vrátí jednu hodnotu. Například následující dotaz vrátí počet položek v rámci `Families` kontejneru:

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

Můžete také vrátit pouze skalární hodnotu agregace pomocí VALUE – klíčové slovo. Například následující dotaz vrátí počet hodnot jako jedno číslo:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Výsledky jsou:

```json
    [ 2 ]
```

Můžete také kombinovat agregace s filtry. Například následující dotaz vrátí počet položek se stavem adresu `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Výsledky jsou:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Typy agregační funkce

Rozhraní SQL API podporuje následující agregační funkce. SOUČET a průměr pracovat s číselnými hodnotami a COUNT, MIN a MAX pracovat na čísla, řetězce, logické hodnoty a hodnoty Null.

| Funkce | Popis |
|-------|-------------|
| POČET | Vrátí počet položek ve výrazu. |
| SUM   | Vrátí součet všech hodnot ve výrazu. |
| MIN   | Vrátí minimální hodnotu ve výrazu. |
| MAX   | Vrátí maximální hodnotu ve výrazu. |
| PRŮMĚR   | Vrátí průměr hodnot ve výrazu. |

Můžete také agregovat přes výsledky iterace pole.

> [!NOTE]
> V Průzkumníku dat na portálu Azure může agregovat agregace dotazy částečných výsledků přes stránku pouze jeden dotaz. Sada SDK vytváří jednu kumulativní hodnotu na všech stránkách. K provádění dotazů agregace pomocí kódu, je nutné, sady .NET SDK 1.12.0, sady SDK .NET Core 1.1.0 nebo sady Java SDK 1.9.5 nebo vyšší.

## <a name="next-steps"></a>Další postup

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Funkce systému](sql-query-system-functions.md)
- [Uživatelem definované funkce](sql-query-udfs.md)