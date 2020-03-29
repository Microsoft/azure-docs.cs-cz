---
title: Operátory dotazů SQL pro Azure Cosmos DB
description: Další informace o operátorech SQL, jako je rovnost, porovnání a logické operátory podporované Službou Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063562"
---
# <a name="operators-in-azure-cosmos-db"></a>Operátory v Azure Cosmos DB

Tento článek podrobně popisuje různé operátory podporované Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operátory rovnosti a porovnání

Následující tabulka ukazuje výsledek porovnání rovnosti v rozhraní SQL API mezi libovolnými dvěma typy JSON.

| **Op** | **Nedefinované** | **Null** | **Logická hodnota** | **Číslo** | **Řetězec** | **Objekt** | **Pole** |
|---|---|---|---|---|---|---|---|
| **Nedefinované** | Nedefinované | Nedefinované | Nedefinované | Nedefinované | Nedefinované | Nedefinované | Nedefinované |
| **Null** | Nedefinované | **Ok** | Nedefinované | Nedefinované | Nedefinované | Nedefinované | Nedefinované |
| **Logická hodnota** | Nedefinované | Nedefinované | **Ok** | Nedefinované | Nedefinované | Nedefinované | Nedefinované |
| **Číslo** | Nedefinované | Nedefinované | Nedefinované | **Ok** | Nedefinované | Nedefinované | Nedefinované |
| **Řetězec** | Nedefinované | Nedefinované | Nedefinované | Nedefinované | **Ok** | Nedefinované | Nedefinované |
| **Objekt** | Nedefinované | Nedefinované | Nedefinované | Nedefinované | Nedefinované | **Ok** | Nedefinované |
| **Pole** | Nedefinované | Nedefinované | Nedefinované | Nedefinované | Nedefinované | Nedefinované | **Ok** |

Pro operátory `>`porovnání, `!=` `<`jako `<=`je například , `>=`, , a `Undefined`, porovnání mezi typy nebo mezi dvěma objekty nebo pole vytváří .  

Pokud je `Undefined`výsledkem skalárního výrazu , položka není zahrnuta ve výsledku, protože `Undefined` se nerovná `true`.

## <a name="logical-and-or-and-not-operators"></a>Logické (AND, OR a NOT) operátory

Logické operátory pracují s logickými hodnotami. V následujících tabulkách jsou uvedeny tabulky logické pravdy pro tyto operátory:

**Operátor OR**

Vrátí, `true` pokud je `true`některá z podmínek .

|  | **True** | **False** | **Nedefinované** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |Nedefinované |
| **Nedefinované** |True |Nedefinované |Nedefinované |

**Operátor A**

Vrátí, `true` pokud jsou `true`oba výrazy .

|  | **True** | **False** | **Nedefinované** |
| --- | --- | --- | --- |
| **True** |True |False |Nedefinované |
| **False** |False |False |False |
| **Nedefinované** |Nedefinované |False |Nedefinované |

**NOT operátor**

Obrátí hodnotu libovolného logického výrazu.

|  | **Ne** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Nedefinované** |Nedefinované |

**Priorita operátorů**

Logické `OR`operátory `AND`, `NOT` a mají níže uvedenou úroveň priority:

| **Operátor** | **Priorita** |
| --- | --- |
| **Ne** |1 |
| **A** |2 |
| **Nebo** |3 |

## <a name="-operator"></a>* – operátor

Speciální operátor * promítá celou položku tak, jak je. Při použití musí být jediným promítatým polem. Dotaz jako `SELECT * FROM Families f` je platný, ale `SELECT VALUE * FROM Families f` `SELECT *, f.id FROM Families f` nejsou platné.

## <a name="-and--operators"></a>? A?? operátory

Můžete použít Ternary (?) a Coalesce (??) operátory k vytvoření podmíněných výrazů, jako v programovacích jazycích, jako je C# a JavaScript.

Můžete použít ? operátor a vytvořit nové vlastnosti JSON za běhu. Například následující dotaz klasifikuje `elementary` `other`úrovně hodnocení do nebo :

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Můžete také hnízdit volání do ? operátor, jako v následujícím dotazu: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Stejně jako u jiných operátorů dotazů, ? operátor vyloučí položky, pokud chybí odkazované vlastnosti nebo porovnávané typy se liší.

Použijte ?? operátor efektivně kontrolovat vlastnost v položce při dotazování proti částečně strukturované nebo smíšené typu dat. Například následující dotaz `lastName` vrátí, pokud `surname` `lastName` je k dispozici, nebo pokud není k dispozici.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Další kroky

- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Klíčová slova](sql-query-keywords.md)
- [Klauzule SELECT](sql-query-select.md)
