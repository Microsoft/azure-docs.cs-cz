---
title: Připojte se k SQL dotazy pro Azure Cosmos DB
description: Další informace o syntaxi JOIN SQL pro službu Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 408ee11b318143b3128833a741e04dd68f3816ed
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342431"
---
# <a name="joins-in-azure-cosmos-db"></a>Připojí ve službě Azure Cosmos DB

Spojení mezi tabulkami v relační databázi, jsou logické důsledkem do navrhování schémat normalizovaná. Naproti tomu rozhraní SQL API pomocí modelu Nenormalizovaná data bez schémat položek, který je logický ekvivalent *spojení sama na sebe*.

Vnitřní spojení za následek úplnou smíšený produkt sad účastní spojení. Výsledek spojení N-způsob, jak je sada N prvek řazené kolekce členů, kde každá hodnota řazené kolekce členů je přidružen alias nastavit účast ve spojení a je přístupný pomocí odkazu na tento alias v jiných klauzule.

## <a name="syntax"></a>Syntaxe

Jazyk podporuje syntaxi `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Tento dotaz vrací sadu řazených kolekcí členů s `N` hodnoty. Všechny řazené kolekce členů obsahuje hodnoty vytvořené ve všechny aliasy kontejneru iterování celého jejich příslušných sad. 

Podívejme se na následující klauzule FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Umožnit každému zdroji definovat `input_alias1, input_alias2, …, input_aliasN`. Tato klauzule FROM vrací sadu N-řazených kolekcí členů (řazené kolekce členů s N hodnot). Všechny řazené kolekce členů obsahuje hodnoty vytvořené ve všechny aliasy kontejneru iterování celého jejich příslušných sad.  
  
**Příklad 1** -2 zdroje  
  
- Umožní `<from_source1>` být kontejner rozsahem a reprezentaci sady {A, B, C}.  
  
- Umožní `<from_source2>` být dokument s rozsahem odkazující na input_alias1 a představují sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- Klauzule FROM `<from_source1> JOIN <from_source2>` bude mít za následek následující řazených kolekcí členů:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Příklad 2** -3 zdrojů  
  
- Umožní `<from_source1>` být kontejner rozsahem a reprezentaci sady {A, B, C}.  
  
- Umožní `<from_source2>` být odkazující na dokument s rozsahem `input_alias1` a představují sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- Umožní `<from_source3>` být odkazující na dokument s rozsahem `input_alias2` a představují sady:  
  
    {100, 200} pro `input_alias2 = 1,`  
  
    {300} pro `input_alias2 = 3,`  
  
- Klauzule FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` bude mít za následek následující řazených kolekcí členů:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Chybějící řazených kolekcí členů pro ostatní hodnoty `input_alias1`, `input_alias2`, pro kterou `<from_source3>` nevrátil žádné hodnoty.  
  
**Příklad 3** -3 zdrojů  
  
- Umožní < from_source1 > se s rozsahem kontejneru a reprezentaci sady {A, B, C}.  
  
- Umožní `<from_source1>` být kontejner rozsahem a reprezentaci sady {A, B, C}.  
  
- Umožní < from_source2 > se s rozsahem dokumentu odkazující input_alias1 a představují sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- Umožní `<from_source3>` omezené na `input_alias1` a představují sady:  
  
    {100, 200} pro `input_alias2 = A,`  
  
    {300} pro `input_alias2 = C,`  
  
- Klauzule FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` bude mít za následek následující řazených kolekcí členů:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), C, 4, 300, (C, 5, 300)  
  
  > [!NOTE]
  > Výsledkem byl smíšený produkt mezi `<from_source2>` a `<from_source3>` protože obě jsou omezená na stejný `<from_source1>`.  Výsledkem byl 4 (2 x 2) řazené kolekce členů s hodnotou A 0 řazené kolekce členů s hodnotou B (1 x 0) a 2 (2 × 1) řazené kolekce členů s hodnotou C.  
  
## <a name="examples"></a>Příklady

Následující příklady ukazují, jak funguje klauzule JOIN. V následujícím příkladu výsledkem je prázdný, protože smíšený produkt každou položku ze zdroje a Prázdná množina je prázdný:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Výsledkem je:

```json
    [{
    }]
```

V následujícím příkladu je spojení mezi produkt mezi dvěma objekty JSON, kořenové položky `id` a `children` subroot. Fakt, který `children` je pole není platné ve spojení, protože se zabývá jeden kořenový, který je `children` pole. Výsledek obsahuje pouze dva výsledky, protože právě pouze jednu položku vrací smíšený produkt každou položku pole.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Výsledky jsou:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Následující příklad ukazuje konvenčnější spojení:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Výsledky jsou:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

JOIN – klauzule FROM zdroj je iterátor. Ano je tok v předchozím příkladu:  

1. Rozbalte každý podřízený prvek `c` v poli.
2. Použít smíšený produkt s kořenem položky `f` s každou podřízeným elementem `c` , která sloučí první krok.
3. Nakonec projektu kořenový objekt `f` `id` vlastnost samostatně.

První položka `AndersenFamily`, obsahuje pouze jednu `children` elementu, tak sada výsledků obsahuje pouze jeden objekt. Druhá položka `WakefieldFamily`, obsahuje dva `children`, takže smíšený produkt vytvoří dva objekty, jeden pro každou `children` elementu. Kořenové pole v obou těchto položek jsou stejné, stejně jako byste očekávali v smíšený produkt.

Skutečné nástroj klauzuli JOIN je formulář řazených kolekcí členů z smíšený produkt v obrazci, který je v opačném případě obtížně projektu. V příkladu níže filtry na kombinaci řazené kolekce členů, která umožňuje uživatelům vybrat podmínku spokojeni podle celkové řazené kolekce členů.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Výsledky jsou:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Následující rozšíření v předchozím příkladu provede double spojení. Smíšený produkt může zobrazit jako pseudoelement následovně:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` má jeden podřízený prvek, který má jednoho nebo více mazlíčků, takže jeden řádek vrací smíšený produkt (1\*1\*1) z této řady. `WakefieldFamily` má dvě děti, má pouze jeden z těchto mazlíčků, ale má tento podřízený dvě mazlíčků. Vrací smíšený produkt této řady 1\*1\*2 = 2 řádky.

V následujícím příkladu je další filtr na `pet`, která nezahrnuje všechny záznamy, kde jméno mazlíčka není `Shadow`. Můžete vytvářet řazenými kolekcemi členů z polí, filtr na některý z prvků řazené kolekce členů a libovolnou kombinaci prvků projektu.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Výsledky jsou:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Další postup

- [Začínáme](sql-query-getting-started.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Poddotazy](sql-query-subquery.md)