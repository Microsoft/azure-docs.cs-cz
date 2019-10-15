---
title: Klauzule FROM v Azure Cosmos DB
description: Seznamte se s klauzulí SQL FROM pro Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 79bb17277a041f71c095ed724737012f9501f16f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327000"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Klauzule FROM v Azure Cosmos DB

Klauzule FROM (`FROM <from_specification>`) je volitelná, pokud se zdroj nefiltruje nebo se v dotazu později nepoužívá. Dotaz, jako je `SELECT * FROM Families`, se zobrazí v celém kontejneru `Families`. Místo použití názvu kontejneru můžete použít také kořenový kořenový identifikátor pro kontejner.

Klauzule FROM vynutila následující pravidla na dotaz:

* Kontejner může obsahovat alias, například `SELECT f.id FROM Families AS f` nebo jednoduše `SELECT f.id FROM Families f`. Zde `f` je alias pro `Families`. Stejně jako volitelné klíčové slovo pro [alias](sql-query-aliasing.md) identifikátoru.  

* Po aliasu nelze původní název zdroje svázat. Například `SELECT Families.id FROM Families f` je syntakticky neplatný, protože identifikátor `Families` byl aliasem a nelze jej již vyřešit.  

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
  
  Určuje zdroj dat s aliasem nebo bez něj. Pokud není zadán alias, bude odvozen z `<container_expression>` pomocí následujících pravidel:  
  
  -  Pokud je výraz container_name, použije se jako alias container_name.  
  
  -  Pokud je výraz `<container_expression>` a pak se property_name, použije se property_name jako alias. Pokud je výraz container_name, použije se jako alias container_name.  
  
- JAKO `input_alias`  
  
  Určuje, že `input_alias` je množinou hodnot vrácených podkladovým výrazem kontejneru.  
 
- @no__t – 0 v  
  
  Určuje, že `input_alias` by měl představovat sadu hodnot, které byly získány pomocí iterace u všech prvků pole každého pole vráceného podkladovým výrazem kontejneru. Jakákoli hodnota vrácená podkladovým výrazem kontejneru, který není polem Array, je ignorována.  
  
- `<container_expression>`  
  
  Určuje výraz kontejneru, který se má použít k načtení dokumentů.  
  
- `ROOT`  
  
  Určuje, že by se měl dokument načíst z výchozího, aktuálně připojeného kontejneru.  
  
- `container_name`  
  
  Určuje, že se má dokument načíst ze zadaného kontejneru. Název kontejneru se musí shodovat s názvem kontejneru, ke kterému je aktuálně připojen.  
  
- `input_alias`  
  
  Určuje, že by se měl dokument načíst z jiného zdroje definovaného zadaným aliasem.  
  
- `<container_expression> '.' property_`  
  
  Určuje, že se má dokument načíst přístupem k vlastnosti `property_name` nebo k array_index elementu pole pro všechny dokumenty načtené pomocí zadaného výrazu kontejneru.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Určuje, že se má dokument načíst přístupem k vlastnosti `property_name` nebo k array_index elementu pole pro všechny dokumenty načtené pomocí zadaného výrazu kontejneru.  
  
## <a name="remarks"></a>Poznámky
  
Všechny aliasy zadané nebo odvozené v `<from_source>(`s) musí být jedinečné. Syntaxe `<container_expression>.`property_name je stejná jako `<container_expression>' ['"property_name"']'`. Nicméně druhá syntaxe může být použita, pokud název vlastnosti obsahuje znak, který není identifikátor.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Zpracování chybějících vlastností, chybějících prvků pole a nedefinovaných hodnot
  
Pokud výraz kontejneru přistupuje k vlastnostem nebo prvkům pole a tato hodnota neexistuje, bude tato hodnota ignorována a nebude zpracována dále.  
  
### <a name="container-expression-context-scoping"></a>Rozsah kontextu výrazu kontejneru  
  
Kontejnerový výraz může být v oboru kontejneru nebo v oboru dokumentu:  
  
-   Výraz je obor kontejneru, pokud je podkladový zdroj výrazu kontejneru buď ROOT, nebo `container_name`. Takový výraz představuje sadu dokumentů načtených z kontejneru přímo a není závislý na zpracování jiných výrazů kontejneru.  
  
-   Výraz je v oboru dokumentu, pokud je podkladový zdroj výrazu kontejneru `input_alias`, který jste dříve v dotazu zavedli. Takový výraz představuje sadu dokumentů získanou vyhodnocením výrazu kontejneru v oboru každého dokumentu, který patří do sady přidružené k kontejneru s aliasem.  Výsledná sada bude sjednocením sad získanou vyhodnocením výrazu kontejneru pro každý dokument v podkladové sadě. 

## <a name="examples"></a>Příklady

### <a name="get-subitems-by-using-the-from-clause"></a>Získat podpoložky pomocí klauzule FROM

Klauzule FROM může snížit zdroj na menší podmnožinu. Chcete-li vytvořit výčet pouze podstrom v každé položce, může se stát, že se dílčí kořen stane zdrojem, jak je znázorněno v následujícím příkladu:

```sql
    SELECT *
    FROM Families.children
```

Výsledky jsou:

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

Předchozí dotaz použil pole jako zdroj, ale můžete také použít objekt jako zdroj. Dotaz považuje při zahrnutí do výsledku platnou definovanou hodnotu JSON ve zdroji. Následující příklad vyloučí `Families`, které nemají hodnotu `address.state`.

```sql
    SELECT *
    FROM Families.address.state
```

Výsledky jsou:

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