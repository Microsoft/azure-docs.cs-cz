---
title: Skalární výrazy v dotazech SQL Azure Cosmos DB
description: Přečtěte si o syntaxi skalárního výrazu SQL pro Azure Cosmos DB. Tento článek také popisuje, jak kombinovat skalární výrazy do složitých výrazů pomocí operátorů.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: f8c98915ad3b682af00492acc7bc51672ec874a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870730"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Skalární výrazy v dotazech SQL Azure Cosmos DB

Klauzule [SELECT](sql-query-select.md) podporuje skalární výrazy. Skalární výraz je kombinace symbolů a operátorů, které lze vyhodnotit získat jednu hodnotu. Příklady skalárních výrazů: konstanty, odkazy na vlastnosti, odkazy na prvky pole, odkazy na alias nebo volání funkcí. Skalární výrazy lze kombinovat do složitých výrazů pomocí operátorů.

## <a name="syntax"></a>Syntaxe
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argumenty
  
- `<constant>`  
  
   Představuje konstantní hodnotu. Podrobnosti naleznete v části [Konstanty.](sql-query-constants.md)  
  
- `input_alias`  
  
   Představuje hodnotu definovanou `input_alias` zavedenou `FROM` v klauzuli.  
  Tato hodnota je zaručeno, že není **definována** –**nedefinované** hodnoty ve vstupu jsou přeskočeny.  
  
- `<scalar_expression>.property_name`  
  
   Představuje hodnotu vlastnosti objektu. Pokud vlastnost neexistuje nebo vlastnost je odkazována na hodnotu, která není objektem, pak výraz vyhodnotí **na nedefinovanou** hodnotu.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Představuje hodnotu vlastnosti `property_name` s name nebo `array_index` element pole s indexem pole. Pokud index vlastnosti/pole neexistuje nebo je index vlastnosti/pole odkazován na hodnotu, která není objektem nebo polem, vyhodnotí se výraz na nedefinovanou hodnotu.  
  
- `unary_operator <scalar_expression>`  
  
   Představuje operátor, který je použit pro jednu hodnotu. Podrobnosti najdete v části [Operátoři.](sql-query-operators.md)  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Představuje operátor, který je použit na dvě hodnoty. Podrobnosti najdete v části [Operátoři.](sql-query-operators.md)  
  
- `<scalar_function_expression>`  
  
   Představuje hodnotu definovanou výsledkem volání funkce.  
  
- `udf_scalar_function`  
  
   Název uživatelem definované skalární funkce.  
  
- `builtin_scalar_function`  
  
   Název vestavěné skalární funkce.  
  
- `<create_object_expression>`  
  
   Představuje hodnotu získanou vytvořením nového objektu se zadanými vlastnostmi a jejich hodnotami.  
  
- `<create_array_expression>`  
  
   Představuje hodnotu získanou vytvořením nového pole se zadanými hodnotami jako prvky.  
  
- `parameter_name`  
  
   Představuje hodnotu zadaného názvu parametru. Názvy parametrů musí \@ mít jeden jako první znak.  
  
## <a name="remarks"></a>Poznámky
  
  Při volání vestavěné nebo uživatelem definované skalární funkce musí být definovány všechny argumenty. Pokud některý z argumentů není definován, funkce nebude volána a výsledek nebude definován.  
  
  Při vytváření objektu bude jakákoli vlastnost, které je přiřazena nedefinovaná hodnota, přeskočena a nebude zahrnuta do vytvořeného objektu.  
  
  Při vytváření pole bude jakákoli hodnota prvku, které je **přiřazena nedefinovaná** hodnota, přeskočena a nebude zahrnuta do vytvořeného objektu. To způsobí, že další definovaný prvek zaujmout své místo takovým způsobem, že vytvořené pole nebude mít přeskočené indexy.  

## <a name="examples"></a>Příklady

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Výsledky jsou následující:

```json
    [{
      "$1": 1.33333
    }]
```

V následujícím dotazu je výsledkem skalárního výrazu logická hodnota:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Výsledky jsou následující:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Další kroky

- [Úvod do Azure Cosmos DB](introduction.md)
- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Poddotazů](sql-query-subquery.md)