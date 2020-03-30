---
title: KLAUZULE FROM v Azure Cosmos DB
description: Další informace o syntaxi SQL a příklad pro FROM klauzule pro Azure Cosmos DB. Tento článek také ukazuje příklady k oboru výsledky a získat dílčí položky pomocí FROM klauzule.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587681"
---
# <a name="from-clause-in-azure-cosmos-db"></a>KLAUZULE FROM v Azure Cosmos DB

From (`FROM <from_specification>`) Klauzule je volitelné, pokud zdroj je filtrován nebo promítán dále v dotazu. Dotaz jako `SELECT * FROM Families` zákuse je `Families` v celém kontejneru. Můžete také použít speciální identifikátor ROOT pro kontejner namísto použití názvu kontejneru.

From klauzule vynucuje následující pravidla na dotaz:

* Kontejner může být aliasován, například nebo `SELECT f.id FROM Families AS f` jednoduše `SELECT f.id FROM Families f`. Zde `f` je alias `Families`pro . AS je volitelné klíčové slovo pro [alias](sql-query-aliasing.md) identifikátor.  

* Po vyhlazení nemůže být původní název zdroje svázán. Například `SELECT Families.id FROM Families f` je syntakticky neplatný, protože identifikátor `Families` byl aliasován a již nemůže být vyřešen.  

* Všechny odkazované vlastnosti musí být plně kvalifikované, aby se zabránilo jakékoli nejednoznačné vazby v nepřítomnosti přísné schéma dodržování. Například `SELECT id FROM Families f` syntakticky neplatné, `id` protože vlastnost není vázána.

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
  
  Určuje zdroj dat s aliasem nebo bez něj. Pokud alias není zadán, bude odvozen z `<container_expression>` použití následujících pravidel:  
  
  -  Pokud je výraz container_name, bude container_name použit jako alias.  
  
  -  Pokud je `<container_expression>`výraz , pak property_name, pak property_name bude použit jako alias. Pokud je výraz container_name, bude container_name použit jako alias.  
  
- Jako`input_alias`  
  
  Určuje, že `input_alias` se jedná o sadu hodnot vrácených podkladovým výrazem kontejneru.  
 
- `input_alias`In  
  
  Určuje, že `input_alias` by měla představovat sadu hodnot získaných iterace přes všechny prvky pole každého pole vrácené základní výraz kontejneru. Jakákoli hodnota vrácená podkladovým výrazem kontejneru, který není polem, je ignorována.  
  
- `<container_expression>`  
  
  Určuje výraz kontejneru, který má být použit k načtení dokumentů.  
  
- `ROOT`  
  
  Určuje, že dokument by měl být načten z výchozího aktuálně připojeného kontejneru.  
  
- `container_name`  
  
  Určuje, že dokument by měl být načten z poskytnutého kontejneru. Název kontejneru se musí shodovat s názvem kontejneru, ke jehož stavu je aktuálně připojen.  
  
- `input_alias`  
  
  Určuje, že dokument by měl být načten z jiného zdroje definovaného poskytnutým aliasem.  
  
- `<container_expression> '.' property_name`  
  
  Určuje, že dokument by měl být `property_name` načten přístupem k vlastnosti.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Určuje, že dokument by měl být `property_name` načten přístupem k vlastnosti nebo array_index prvku pole pro všechny dokumenty načtené zadaným výrazem kontejneru.  
  
## <a name="remarks"></a>Poznámky
  
Všechny aliasy zadaný nebo odvozené v `<from_source>(`s) musí být jedinečné. Syntaxe `<container_expression>.`property_name je `<container_expression>' ['"property_name"']'`stejná jako . Druhá syntaxe však může být použita, pokud název vlastnosti obsahuje znak bez identifikátoru.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Zpracování chybějících vlastností, chybějících prvků pole a nedefinovaných hodnot
  
Pokud výraz kontejneru přistupuje k vlastnostem nebo prvkům pole a tato hodnota neexistuje, bude tato hodnota ignorována a nebude dále zpracována.  
  
### <a name="container-expression-context-scoping"></a>Vymezení kontextu výrazu kontejneru  
  
Výraz kontejneru může být s rozsahem kontejneru nebo s rozsahem dokumentu:  
  
-   Výraz je s rozsahem kontejneru, pokud je základním zdrojem výrazu kontejneru root nebo `container_name`. Takový výraz představuje sadu dokumentů načtených přímo z kontejneru a není závislý na zpracování jiných výrazů kontejneru.  
  
-   Výraz je vymezen rozsahem dokumentu, pokud je `input_alias` základní zdroj výrazu kontejneru zaveden dříve v dotazu. Takový výraz představuje sadu dokumentů získaných vyhodnocením výrazu kontejneru v oboru každého dokumentu, který patří k sadě přidružené k kontejneru aliasu.  Výsledná sada bude sjednocení sad získaných vyhodnocením výrazu kontejneru pro každý z dokumentů v podkladové sadě. 

## <a name="examples"></a>Příklady

### <a name="get-subitems-by-using-the-from-clause"></a>Získání dílčích položek pomocí klauzule FROM

From klauzule může snížit zdroj na menší podmnožinu. Chcete-li vytvořit výčet pouze podstrom v každé položce, podkořen se může stát zdrojem, jak je znázorněno v následujícím příkladu:

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

Předchozí dotaz použil pole jako zdroj, ale jako zdroj můžete také použít objekt. Dotaz bere v úvahu všechny platné, definované json hodnotu ve zdroji pro zahrnutí do výsledku. Následující příklad by `Families` vyloučit, které `address.state` nemají hodnotu.

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
- [KDE doložka](sql-query-where.md)
