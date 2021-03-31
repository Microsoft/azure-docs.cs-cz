---
title: Skalární výrazy v Azure Cosmos DBch dotazech SQL
description: Seznamte se s syntaxí skalárního výrazu SQL pro Azure Cosmos DB. Tento článek také popisuje, jak kombinovat skalární výrazy do složitých výrazů pomocí operátorů.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 34df2d2f4a2209cca93c3f7ac12dcd203bf4a089
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339627"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Skalární výrazy v Azure Cosmos DBch dotazech SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[Klauzule SELECT](sql-query-select.md) podporuje skalární výrazy. Skalární výraz je kombinací symbolů a operátorů, které lze vyhodnotit, aby získala jedinou hodnotu. Příklady skalárních výrazů: konstanty, odkazy na vlastnosti, odkazy na prvky pole, odkazy na aliasy nebo volání funkcí. Skalární výrazy lze kombinovat do složitých výrazů pomocí operátorů.

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
  
   Představuje konstantní hodnotu. Podrobnosti najdete v části [konstanty](sql-query-constants.md) .  
  
- `input_alias`  
  
   Představuje hodnotu definovanou `input_alias` klauzulí představenou v `FROM` klauzuli.  
  Tato hodnota se garantuje jako **nedefinovaná** –**nedefinované** hodnoty ve vstupu se přeskočí.  
  
- `<scalar_expression>.property_name`  
  
   Představuje hodnotu vlastnosti objektu. Pokud vlastnost neexistuje nebo se na vlastnost odkazuje na hodnotu, která není objekt, je výraz vyhodnocen jako **nedefinovaná** hodnota.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Představuje hodnotu vlastnosti s názvem `property_name` nebo prvkem pole s indexem `array_index` pole. Pokud index Property nebo Array neexistuje nebo se na něj odkazuje index Property nebo Array na hodnotě, která není objektem nebo polem, je výraz vyhodnocen jako Nedefinovaná hodnota.  
  
- `unary_operator <scalar_expression>`  
  
   Představuje operátor, který se použije na jednu hodnotu. Podrobnosti najdete v části [Operators](sql-query-operators.md) .  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Představuje operátor, který se použije na dvě hodnoty. Podrobnosti najdete v části [Operators](sql-query-operators.md) .  
  
- `<scalar_function_expression>`  
  
   Představuje hodnotu definovanou výsledkem volání funkce.  
  
- `udf_scalar_function`  
  
   Název uživatelsky definované skalární funkce.  
  
- `builtin_scalar_function`  
  
   Název předdefinované skalární funkce.  
  
- `<create_object_expression>`  
  
   Představuje hodnotu získanou vytvořením nového objektu se zadanými vlastnostmi a jejich hodnotami.  
  
- `<create_array_expression>`  
  
   Představuje hodnotu získanou vytvořením nového pole se zadanými hodnotami jako elementy.  
  
- `parameter_name`  
  
   Představuje hodnotu zadaného názvu parametru. Názvy parametrů musí mít \@ jako první znak jedinou hodnotu.  
  
## <a name="remarks"></a>Poznámky
  
  Při volání předdefinované nebo uživatelsky definované skalární funkce musí být definovány všechny argumenty. Pokud některý z argumentů není definován, funkce nebude volána a výsledek nebude definován.  
  
  Při vytváření objektu se všechny vlastnosti, které jsou přiřazeny nedefinované hodnoty, přeskočí a nebudou zahrnuty do vytvořeného objektu.  
  
  Při vytváření pole se všechny hodnoty prvků, které mají přiřazenou **nedefinovanou** hodnotu, přeskočí a nebudou zahrnuty do vytvořeného objektu. To způsobí, že následující definovaný prvek bude převzít místo v takovém případě, že vytvořené pole nebude mít přeskočené indexy.  

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

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Poddotazy](sql-query-subquery.md)