---
title: Skalární výrazy v Azure Cosmos DBch dotazech SQL
description: Seznamte se s syntaxí skalárního výrazu SQL pro Azure Cosmos DB. Tento článek také popisuje, jak kombinovat skalární výrazy do složitých výrazů pomocí operátorů.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: f8c98915ad3b682af00492acc7bc51672ec874a8
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870730"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Skalární výrazy v Azure Cosmos DBch dotazech SQL

[Klauzule SELECT](sql-query-select.md) podporuje skalární výrazy. Skalární výraz, který je kombinací symbolů a operátorů, které lze vyhodnotit na získání jedné hodnoty. Příklady skalárních výrazů: konstanty, odkazy na vlastnosti, odkazy na prvky pole, odkazy na aliasy nebo volání funkcí. Skalární výrazy lze kombinovat do složitých výrazů pomocí operátorů.

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
  
   Reprezentuje hodnotu konstanty. Zobrazit [konstanty](sql-query-constants.md) podrobné informace.  
  
- `input_alias`  
  
   Představuje hodnoty definované `input_alias` zavedený `FROM` klauzuli.  
  Tato hodnota je zaručeno, že nebudou **nedefinované** –**nedefinované** hodnoty ve vstupu se přeskočí.  
  
- `<scalar_expression>.property_name`  
  
   Reprezentuje hodnotu vlastnosti objektu. Pokud vlastnost neexistuje nebo se na vlastnost odkazuje na hodnotu, která není objekt, je výraz vyhodnocen jako **nedefinovaná** hodnota.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Představuje hodnotu vlastnosti s názvem `property_name` nebo element pole s indexovým `array_index` pole. Pokud vlastnost nebo pole index neexistuje nebo index vlastnost nebo pole se odkazuje na hodnotu, která není objekt nebo pole a pak je výraz vyhodnocen nedefinovaná hodnota.  
  
- `unary_operator <scalar_expression>`  
  
   Představuje operátor, který se použije na jednu hodnotu. Zobrazit [operátory](sql-query-operators.md) podrobné informace.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Představuje operátor, který se použije na dvě hodnoty. Zobrazit [operátory](sql-query-operators.md) podrobné informace.  
  
- `<scalar_function_expression>`  
  
   Představuje hodnou definovanou ve výsledku volání funkce.  
  
- `udf_scalar_function`  
  
   Název definovaný uživatelem skalární funkci.  
  
- `builtin_scalar_function`  
  
   Název integrovanou skalární funkci.  
  
- `<create_object_expression>`  
  
   Představuje hodnotu získat tak, že vytvoříte nový objekt v zadaných vlastností a jejich hodnoty.  
  
- `<create_array_expression>`  
  
   Reprezentuje hodnotu získala při vytváření nové pole pomocí zadaných hodnot jako prvky  
  
- `parameter_name`  
  
   Reprezentuje hodnotu zadaný název parametru. Názvy parametrů musí mít jeden \@ jako první znak.  
  
## <a name="remarks"></a>Poznámky
  
  Při volání předdefinované nebo uživatelsky definované skalární funkce musí být definovány všechny argumenty. Pokud některý z argumentů není definován, nebude volána funkce a bude výsledek nedefinovaný.  
  
  Při vytvoření objektu, bude přeskočen a není součástí vytvořený objekt jakákoli vlastnost, která je přiřazena hodnota undefined.  
  
  Při vytváření pole, libovolný element, který je přiřazen **nedefinované** hodnota bude přeskočen a není součástí vytvořený objekt. To způsobí, že další definovaný prvek k provedení jeho tak, že vytvořené pole nebude přeskočena indexy.  

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
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Poddotazy](sql-query-subquery.md)