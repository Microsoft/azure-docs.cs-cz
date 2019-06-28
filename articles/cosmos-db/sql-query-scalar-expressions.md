---
title: Skalární výrazy v dotazech SQL služby Azure Cosmos DB
description: Další informace o syntaxi SQL skalární výraz pro službu Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 4464c39a45c47c680a13f3ebc34841b47ee0d7c6
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342851"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Skalární výrazy v dotazech SQL služby Azure Cosmos DB

[Klauzule SELECT](sql-query-select.md) podporuje skalární výrazy. Skalární výraz, který je kombinací symbolů a operátorů, které lze vyhodnotit na získání jedné hodnoty. Příklady skalární výrazy: konstanty, odkazy na vlastnosti, odkazy na prvky pole, odkazy na alias nebo volání funkce. Skalární výrazy je možné kombinovat do složité výrazy pomocí operátorů.

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
  
   Reprezentuje hodnotu vlastnosti objektu. Pokud neexistuje vlastnost nebo vlastnost se odkazuje na hodnotu, která není objekt, pak je výraz vyhodnocen **nedefinované** hodnotu.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Reprezentuje hodnotu vlastnosti s názvem `property_name` nebo element pole s indexem `array_index` pole. Pokud vlastnost nebo pole index neexistuje nebo index vlastnost nebo pole se odkazuje na hodnotu, která není objekt nebo pole a pak je výraz vyhodnocen nedefinovaná hodnota.  
  
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
  
  Při volání skalární funkce vestavěný nebo uživatelem definované, musí být definovány všechny argumenty. Pokud některý z argumentů není definován, nebude volána funkce a bude výsledek nedefinovaný.  
  
  Při vytvoření objektu, bude přeskočen a není součástí vytvořený objekt jakákoli vlastnost, která je přiřazena hodnota undefined.  
  
  Při vytváření pole, libovolný element, který je přiřazen **nedefinované** hodnota bude přeskočen a není součástí vytvořený objekt. To způsobí, že další definovaný prvek k provedení jeho tak, že vytvořené pole nebude přeskočena indexy.  

## <a name="examples"></a>Příklady

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Výsledky jsou:

```json
    [{
      "$1": 1.33333
    }]
```

V následujícím dotazem výsledkem skalární výraz, který je logická hodnota:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Výsledky jsou:

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

## <a name="next-steps"></a>Další postup

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Poddotazy](sql-query-subquery.md)