---
title: Operátory dotazů SQL pro Azure Cosmos DB
description: Přečtěte si o operátorech SQL, jako jsou rovnost, porovnání a logické operátory podporované Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870934"
---
# <a name="operators-in-azure-cosmos-db"></a>Operátory v Azure Cosmos DB

Tento článek podrobně popisuje různé operátory podporované nástrojem Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operátory rovnosti a porovnání

V následující tabulce jsou uvedeny výsledek porovnání rovnosti v rozhraní SQL API mezi jakékoli dva typy JSON.

| **OP** | **Nedefinovaný** | **Hodnotu Null** | **Datový typ Boolean** | **Číslo** | **řetězec** | **objekt** | **Pole** |
|---|---|---|---|---|---|---|---|
| **Nedefinovaný** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno |
| **Hodnotu Null** | Nedefinováno | **Ok** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno |
| **Datový typ Boolean** | Nedefinováno | Nedefinováno | **Ok** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno |
| **Číslo** | Nedefinováno | Nedefinováno | Nedefinováno | **Ok** | Nedefinováno | Nedefinováno | Nedefinováno |
| **řetězec** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | **Ok** | Nedefinováno | Nedefinováno |
| **objekt** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | **Ok** | Nedefinováno |
| **Pole** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | **Ok** |

Pro operátory porovnání, například `>`, `>=`, `!=`, `<`a `<=`, porovnávání mezi typy nebo mezi dvěma objekty nebo poli vytváří `Undefined`.  

Pokud je výsledek skalárního výrazu `Undefined`, položka není obsažena ve výsledku, protože `Undefined` se nerovná `true`.

## <a name="logical-and-or-and-not-operators"></a>Logický (AND, OR a NOT) operátory

Logické operátory pracují s logickými hodnotami. V následujících tabulkách jsou uvedeny logické tabulky pravdy pro tyto operátory:

**NEBO – operátor**

| NEBO | Pravda | Nepravda | Nedefinováno |
| --- | --- | --- | --- |
| Pravda |Pravda |Pravda |Pravda |
| Nepravda |Pravda |Nepravda |Nedefinováno |
| Nedefinováno |Pravda |Nedefinováno |Nedefinováno |

**AND – operátor**

| A | Pravda | Nepravda | Nedefinováno |
| --- | --- | --- | --- |
| Pravda |Pravda |Nepravda |Nedefinováno |
| Nepravda |Nepravda |Nepravda |Nepravda |
| Nedefinováno |Nedefinováno |Nepravda |Nedefinováno |

**NOT – operátor**

| NOT |  |
| --- | --- |
| Pravda |Nepravda |
| Nepravda |Pravda |
| Nedefinováno |Nedefinováno |


## <a name="-operator"></a>* – operátor

Speciální operátor * projektuje celou položku tak, jak je. Při použití, musí být pouze očekávané pole. Dotaz, jako je `SELECT * FROM Families f`, je platný, ale `SELECT VALUE * FROM Families f` a `SELECT *, f.id FROM Families f` nejsou platné.

## <a name="-and--operators"></a>? a?? operátory

Můžete použít operátory Ternární (?) a COALESCE (??) k sestavení podmíněných výrazů, jako v programovacích jazycích C# , jako je například JavaScript. 

Můžete použít? operátor pro vytvoření nových vlastností JSON za běhu. Následující dotaz například klasifikuje úrovně stupňů do `elementary` nebo `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Můžete také vnořit volání do? – operátor, jak je uvedeno v následujícím dotazu: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Stejně jako u jiných operátorů dotazů? operátor vyloučí položky, pokud nebyly nalezeny odkazované vlastnosti nebo se porovnávané typy liší.

Chcete použít? operátor pro efektivní kontrolu vlastnosti v položce při dotazování na částečně strukturovaných nebo smíšených dat typu. Například následující dotaz vrátí `lastName`, pokud je k dispozici, nebo `surname`, pokud `lastName` není k dispozici.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Další kroky

- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [klíčová slova](sql-query-keywords.md)
- [Klauzule SELECT](sql-query-select.md)
