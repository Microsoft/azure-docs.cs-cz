---
title: Dotazy SQL JOIN pro Azure Cosmos DB
description: Naučte se, jak propojit více tabulek v Azure Cosmos DB k dotazování na data.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871138"
---
# <a name="joins-in-azure-cosmos-db"></a>Spojení v Azure Cosmos DB

V relační databázi jsou spojení mezi tabulkami logickým Corollary návrhu normalizovaných schémat. Naproti tomu rozhraní SQL API používá denormalizovaný datový model položek bez schématu, což je logický ekvivalent samostatného *spojení*.

Vnitřní spojení za následek úplnou smíšený produkt sad účastní spojení. Výsledek spojení N-způsob, jak je sada N prvek řazené kolekce členů, kde každá hodnota řazené kolekce členů je přidružen alias nastavit účast ve spojení a je přístupný pomocí odkazu na tento alias v jiných klauzule.

## <a name="syntax"></a>Syntaxe

Jazyk podporuje `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`syntaxe. Tento dotaz vrací sadu řazených kolekcí členů s hodnotami `N`. Všechny řazené kolekce členů obsahuje hodnoty vytvořené ve všechny aliasy kontejneru iterování celého jejich příslušných sad. 

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

Následující příklady ukazují, jak funguje klauzule JOIN. Před spuštěním těchto příkladů nahrajte ukázková [data rodiny](sql-query-getting-started.md#upload-sample-data). V následujícím příkladu je výsledek prázdný, protože mezi produktem každé položky ze zdroje a prázdnou sadou je prázdný:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Výsledek je následující:

```json
    [{
    }]
```

V následujícím příkladu je spojení meziproduktem mezi dvěma objekty JSON, kořenovým adresářem položky `id` a podkořenem `children`. Skutečnost, že `children` pole není ve spojení platná, protože se zabývá jediným kořenem, který je polem `children`. Výsledek obsahuje jenom dva výsledky, protože u různých produktů každé položky s polem je přesně jenom jedna položka.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Výsledky jsou následující:

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

Výsledky jsou následující:

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

Zdrojem od klauzule JOIN je iterátor. Proto tok v předchozím příkladu:  

1. Rozbalíte všechny `c` podřízených elementů v poli.
2. Použijte u každého podřízeného prvku mezi produktem kořen položky `f` a `c`, že první krok je plochý.
3. Nakonec naprojektujte kořenový objekt `f` samotné vlastnosti `id`.

První položka, `AndersenFamily`, obsahuje pouze jeden prvek `children`, takže sada výsledků obsahuje pouze jeden objekt. Druhá položka, `WakefieldFamily`, obsahuje dva `children`, takže mezi produkty vznikne dva objekty, jeden pro každý prvek `children`. Kořenové pole v obou těchto položek jsou stejné, stejně jako byste očekávali v smíšený produkt.

Skutečným nástrojem klauzule JOIN je vytvořit řazené kolekce členů z různých produktů v obrazci, který je jinak obtížné projektovat. Níže uvedený příklad filtruje kombinaci řazené kolekce členů, která umožňuje uživateli zvolit stav splněný celkovými řazenými kolekcemi členů.

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

Výsledky jsou následující:

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

Následující přípona v předchozím příkladu provádí dvojité spojení. Mezi produkty můžete zobrazit následující pseudo kód:

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

`AndersenFamily` má jednu podřízenou položku, která má jednu PET, takže mezi produkty patří jeden řádek (1\*1\*1) z této rodiny. `WakefieldFamily` má dvě podřízené položky, jenom jeden z nich má domácí položku, ale má dvě domácí. Mezi produktem této řady patří 1\*1\*2 = 2 řádky.

V následujícím příkladu je k dispozici další filtr na `pet`, který vylučuje všechny řazené kolekce členů, kde název PET není `Shadow`. Můžete sestavovat řazené kolekce členů z polí, filtrovat na libovolný prvek řazené kolekce členů a projektovat libovolnou kombinaci prvků.

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

Výsledky jsou následující:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Poddotazy](sql-query-subquery.md)