---
title: Operátory dotazů SQL pro Azure Cosmos DB
description: Přečtěte si o operátorech SQL pro Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 899355ad7331a3df8cd5d647a573dc15e3a0bb14
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003383"
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

Pro operátory `>`porovnání, jako `<` `!=`například `>=`,,,, `<=`a, porovnání mezi typy nebo mezi dvěma objekty nebo poli `Undefined`generuje.  

Pokud je `Undefined`výsledek skalárního výrazu, položka není obsažena ve výsledku, protože `Undefined` se nerovná `true`.

## <a name="logical-and-or-and-not-operators"></a>Logický (AND, OR a NOT) operátory

Logické operátory pracují s logickými hodnotami. V následujících tabulkách jsou uvedeny logické tabulky pravdy pro tyto operátory:

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

Speciální operátor * projektuje celou položku tak, jak je. Při použití, musí být pouze očekávané pole. Dotaz, jako `SELECT * FROM Families f` je platný, `SELECT *, f.id FROM Families f` ale `SELECT VALUE * FROM Families f` je neplatný.

## <a name="-and--operators"></a>? a?? Logické

Můžete použít operátory Ternární (?) a COALESCE (??) k sestavení podmíněných výrazů, jako v programovacích jazycích C# , jako je například JavaScript. 

Můžete použít? operátor pro vytvoření nových vlastností JSON za běhu. Například následující dotaz klasifikuje úrovně stupňů do `elementary` nebo: `other`

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

Chcete použít? operátor pro efektivní kontrolu vlastnosti v položce při dotazování na částečně strukturovaných nebo smíšených dat typu. Například následující dotaz vrátí `lastName` , pokud je k dispozici, nebo `lastName` `surname` Pokud není k dispozici.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Další postup

- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [klíčová slova](sql-query-keywords.md)
- [Klauzule SELECT](sql-query-select.md)
