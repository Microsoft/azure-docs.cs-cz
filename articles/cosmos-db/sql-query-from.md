---
title: Klauzule FROM v Azure Cosmos DB
description: Přečtěte si o syntaxi SQL a příklad pro klauzuli FROM pro Azure Cosmos DB. Tento článek také ukazuje příklady určení rozsahu výsledků a získání dílčích položek pomocí klauzule FROM.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 3844a628a8e3773821736324bfdaab3f64ff9da5
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336244"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Klauzule FROM v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Klauzule FROM ( `FROM <from_specification>` ) je volitelná, pokud se zdroj nefiltruje nebo se v dotazu později nepoužívá. Dotaz, jako `SELECT * FROM Families` je například výčet v rámci celého `Families` kontejneru. Místo použití názvu kontejneru můžete použít také kořenový kořenový identifikátor pro kontejner.

`FROM`Klauzule vynutila následující pravidla na dotaz:

* Kontejner může mít alias, například `SELECT f.id FROM Families AS f` nebo jednoduše `SELECT f.id FROM Families f` . Tady `f` je alias pro `Families` . Stejně jako volitelné klíčové slovo pro [alias](sql-query-working-with-json.md#aliasing) identifikátoru.  

* Po aliasu nelze původní název zdroje svázat. Například `SELECT Families.id FROM Families f` je syntakticky neplatný, protože identifikátor `Families` byl alias a již jej nelze přeložit.  

* Všechny odkazované vlastnosti musí být plně kvalifikované, aby nedocházelo k nejednoznačným vazbám při neexistenci striktního dodržování schématu. Například `SELECT id FROM Families f` je syntakticky neplatný, protože vlastnost `id` není vázaná.

## <a name="syntax"></a>Syntaxe
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=
        ROOT
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argumenty
  
- `<from_source>`  
  
  Určuje zdroj dat s aliasem nebo bez něj. Pokud alias není zadán, bude odvozen z `<container_expression>` pomocí následujících pravidel:  
  
-  Pokud je výraz container_name, container_name bude použit jako alias.  
  
-  Pokud je výraz `<container_expression>` , pak property_name a property_name bude použit jako alias. Pokud je výraz container_name, container_name bude použit jako alias.  
  
- FORMÁTU `input_alias`  
  
  Určuje, že `input_alias` je sada hodnot vrácená podkladovým výrazem kontejneru.  
 
- `input_alias` PRO  
  
  Určuje, že `input_alias` má představovat sadu hodnot, které byly získány pomocí iterace u všech prvků pole každého pole vráceného podkladovým výrazem kontejneru. Jakákoli hodnota vrácená podkladovým výrazem kontejneru, který není polem Array, je ignorována.  
  
- `<container_expression>`  
  
  Určuje výraz kontejneru, který se má použít k načtení dokumentů.  
  
- `ROOT`  
  
  Určuje, že by se měl dokument načíst z výchozího, aktuálně připojeného kontejneru.  
  
- `container_name`  
  
  Určuje, že se má dokument načíst ze zadaného kontejneru. Název kontejneru se musí shodovat s názvem kontejneru, ke kterému je aktuálně připojen.  
  
- `input_alias`  
  
  Určuje, že by se měl dokument načíst z jiného zdroje definovaného zadaným aliasem.  
  
- `<container_expression> '.' property_name`  
  
  Určuje, že se má dokument načíst přístupem k `property_name` Vlastnosti.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Určuje, že by měl být dokument načten přístupem k `property_name` vlastnosti nebo prvku array_index pole pro všechny dokumenty načtené pomocí zadaného výrazu kontejneru.  
  
## <a name="remarks"></a>Poznámky
  
Všechny aliasy, které jsou zadány nebo odvozeny v rámci, `<from_source>(` musí být jedinečné. Syntaxe `<container_expression>.` Property_Name je stejná jako `<container_expression>' ['"property_name"']'` . Nicméně druhá syntaxe může být použita, pokud název vlastnosti obsahuje znak, který není identifikátor.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Zpracování chybějících vlastností, chybějících prvků pole a nedefinovaných hodnot
  
Pokud výraz kontejneru přistupuje k vlastnostem nebo prvkům pole a tato hodnota neexistuje, bude tato hodnota ignorována a nebude zpracována dále.  
  
### <a name="container-expression-context-scoping"></a>Rozsah kontextu výrazu kontejneru  
  
Kontejnerový výraz může být v oboru kontejneru nebo v oboru dokumentu:  
  
- Výraz je obor kontejneru, pokud je podkladový zdroj výrazu kontejneru buď ROOT, nebo `container_name` . Takový výraz představuje sadu dokumentů načtených z kontejneru přímo a není závislý na zpracování jiných výrazů kontejneru.  
  
- Výraz je v oboru dokumentu, pokud je základní zdroj výrazu kontejneru `input_alias` představen dříve v dotazu. Takový výraz představuje sadu dokumentů získanou vyhodnocením výrazu kontejneru v oboru každého dokumentu, který patří do sady přidružené k kontejneru s aliasem. Výsledná sada bude sjednocením sad získanou vyhodnocením výrazu kontejneru pro každý dokument v podkladové sadě.

## <a name="examples"></a>Příklady

### <a name="get-subitems-by-using-the-from-clause"></a>Získat podpoložky pomocí klauzule FROM

Klauzule FROM může snížit zdroj na menší podmnožinu. Chcete-li vytvořit výčet pouze podstrom v každé položce, může se stát, že se dílčí kořen stane zdrojem, jak je znázorněno v následujícím příkladu:

```sql
    SELECT *
    FROM Families.children
```

Výsledky jsou následující:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Předchozí dotaz použil pole jako zdroj, ale můžete také použít objekt jako zdroj. Dotaz považuje při zahrnutí do výsledku platnou definovanou hodnotu JSON ve zdroji. Následující příklad by vyloučil `Families` , že nemá `address.state` hodnotu.

```sql
    SELECT *
    FROM Families.address.state
```

Výsledky jsou následující:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Klauzule SELECT](sql-query-select.md)
- [Klauzule WHERE](sql-query-where.md)
