---
title: Operátory dotazů SQL pro službu Azure Cosmos DB
description: Další informace o operátorech SQL pro službu Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: eecc1522f8c260286c7dd7fc4c2e58d5d8caa8fb
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342686"
---
# <a name="operators-in-azure-cosmos-db"></a>Operátory ve službě Azure Cosmos DB

Tento článek podrobně popisuje různé operátory jsou podporované službou Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Rovnost a porovnávací operátory

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

Pro operátory porovnání, jako například `>`, `>=`, `!=`, `<`, a `<=`, porovnání typů nebo mezi dvěma objekty nebo pole vytvoří `Undefined`.  

Pokud je výsledkem skalární výraz, který `Undefined`, položka není zahrnut ve výsledku, protože `Undefined` není roven `true`.

## <a name="logical-and-or-and-not-operators"></a>Logický (AND, OR a NOT) operátory

Logické operátory pracují s logickými hodnotami. Následující tabulky popisují logické tabulky pravdivých informací pro tyto operátory:

**NEBO – operátor**

| NEBO | True | False | Nedefinováno |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Nedefinováno |
| Nedefinováno |True |Nedefinováno |Nedefinováno |

**AND – operátor**

| A | True | False | Nedefinováno |
| --- | --- | --- | --- |
| True |True |False |Nedefinováno |
| False |False |False |False |
| Nedefinováno |Nedefinováno |False |Nedefinováno |

**NOT – operátor**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Nedefinováno |Nedefinováno |


## <a name="-operator"></a>* – operátor

Speciální operátor * projekty celé položky, jako je. Při použití, musí být pouze očekávané pole. Dotaz, jako jsou `SELECT * FROM Families f` je platný, ale `SELECT VALUE * FROM Families f` a `SELECT *, f.id FROM Families f` nejsou platné.

## <a name="-and--operators"></a>? a?? Operátory

Můžete použít Ternary (?) a operátory (?) podmíněné výrazy, stejně jako v programovacích jazycích, jako je sloučení C# a JavaScript. 

Můžete použít? operátorem k vytvoření nové vlastnosti JSON v reálném čase. Například následující dotaz rozdělí na podnikové úrovni úrovně do `elementary` nebo `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Lze také vnořit volání? operátor, stejně jako v následující dotaz: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Stejně jako u jiných operátorů dotazu? operátor vyloučí položky, pokud nejsou zadané vlastnosti odkazované nebo typy, který se porovnává se liší.

Použití?? operátor efektivně vyhledávat vlastnosti v položce při dotazování na smíšený typ nebo částečně strukturovaná data. Například následující dotaz vrátí `lastName` Pokud jsou k dispozici, nebo `surname` Pokud `lastName` není k dispozici.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Další postup

- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [klíčová slova](sql-query-keywords.md)
- [Klauzule SELECT](sql-query-select.md)
