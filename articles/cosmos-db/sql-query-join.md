---
title: Dotazy NA PŘIPOJENÍ SQL pro Azure Cosmos DB
description: Zjistěte, jak spojit více tabulek v Azure Cosmos DB a dotazovat se na data
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871138"
---
# <a name="joins-in-azure-cosmos-db"></a>Spojení v Azure Cosmos DB

V relační databázi jsou spojení mezi tabulkami logickým důsledkem návrhu normalizovaných schémat. Naproti tomu rozhraní SQL API používá nenormalizovaný datový model položek bez schématu, což je logický ekvivalent *vlastního spojení*.

Výsledkem vnitřního spojení je kompletní křížový součin sad, které se spojení účastní. Výsledkem spojení N-way je sada n-element řazené kolekce členů, kde každá hodnota v řazené kolekce členů je spojena s aliased set účastní spojení a lze přistupovat odkazem na tento alias v jiných klauzulí.

## <a name="syntax"></a>Syntaxe

Jazyk podporuje syntaxi `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Tento dotaz vrátí sadu řazených kolekcí členů s `N` hodnotami. Každá řazená kolekce členů má hodnoty vytvořené iteracem všech aliasů kontejneru nad jejich příslušnými sadami. 

Podívejme se na následující from klauzule:`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Nechte každý `input_alias1, input_alias2, …, input_aliasN`zdroj definovat . Tato klauzule FROM vrátí sadu N-n-tice (řazené kolekce členů s n hodnoty). Každá řazená kolekce členů má hodnoty vytvořené iteracem všech aliasů kontejneru nad jejich příslušnými sadami.  
  
**Příklad 1** - 2 zdrojů  
  
- Nechť `<from_source1>` je kontejner obor a představují sadu {A, B, C}.  
  
- Nechť `<from_source2>` jsou odkazování s rozsahem dokumentu input_alias1 a představují sady:  
  
    {1, 2} pro`input_alias1 = A,`  
  
    {3}Pro`input_alias1 = B,`  
  
    {4, 5} pro`input_alias1 = C,`  
  
- From klauzule `<from_source1> JOIN <from_source2>` bude mít za následek následující řazené kolekce členů:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Příklad 2** - 3 zdrojů  
  
- Nechť `<from_source1>` je kontejner obor a představují sadu {A, B, C}.  
  
- Nechť `<from_source2>` jsou odkazování `input_alias1` s rozsahem dokumentu a představují sady:  
  
    {1, 2} pro`input_alias1 = A,`  
  
    {3}Pro`input_alias1 = B,`  
  
    {4, 5} pro`input_alias1 = C,`  
  
- Nechť `<from_source3>` jsou odkazování `input_alias2` s rozsahem dokumentu a představují sady:  
  
    {100, 200} pro`input_alias2 = 1,`  
  
    {300}Pro`input_alias2 = 3,`  
  
- From klauzule `<from_source1> JOIN <from_source2> JOIN <from_source3>` bude mít za následek následující řazené kolekce členů:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Nedostatek řazené kolekce `input_alias1` `input_alias2`členů pro `<from_source3>` jiné hodnoty , , pro které nevrátil žádné hodnoty.  
  
**Příklad 3** - 3 zdrojů  
  
- Nechte <from_source1> být kontejnerem a představují sadu {A, B, C}.  
  
- Nechť `<from_source1>` je kontejner obor a představují sadu {A, B, C}.  
  
- Nechť <from_source2> odkazování na input_alias1 s rozsahem dokumentu a představují sady:  
  
    {1, 2} pro`input_alias1 = A,`  
  
    {3}Pro`input_alias1 = B,`  
  
    {4, 5} pro`input_alias1 = C,`  
  
- Nechť `<from_source3>` je `input_alias1` vymezen rozsah a představují sady:  
  
    {100, 200} pro`input_alias2 = A,`  
  
    {300}Pro`input_alias2 = C,`  
  
- From klauzule `<from_source1> JOIN <from_source2> JOIN <from_source3>` bude mít za následek následující řazené kolekce členů:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300) , (C, 5, 300)  
  
  > [!NOTE]
  > Výsledkem je křížový `<from_source2>` `<from_source3>` produkt mezi a protože `<from_source1>`oba jsou vymezeny na stejné .  Výsledkem bylo, že 4 (2x2) řazené kolekce členů mají hodnotu A, 0 řazených kolekcí členů s hodnotou B (1x0) a 2 (2x1) n-tic s hodnotou C.  
  
## <a name="examples"></a>Příklady

Následující příklady ukazují, jak klauzule JOIN funguje. Před spuštěním těchto příkladů nahrajte ukázková [rodinná data](sql-query-getting-started.md#upload-sample-data). V následujícím příkladu je výsledek prázdný, protože křížový sousto každé položky ze zdroje a prázdná sada je prázdná:

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

V následujícím příkladu je spojení křížovým produktem mezi dvěma `id` objekty `children` JSON, kořenem položky a podkořenem. Skutečnost, `children` že je pole není efektivní v spojení, protože se zabývá `children` jeden kořen, který je pole. Výsledek obsahuje pouze dva výsledky, protože křížový součin každé položky s polem dává přesně pouze jednu položku.

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

From source join klauzule je iterátor. Tok v předchozím příkladu je tedy:  

1. Rozbalte každý `c` podřízený prvek v poli.
2. Použijte křížový produkt s kořenem `f` položky `c` s každým podřízeným prvkem, který první krok zploštěl.
3. Nakonec promítnout `f` `id` vlastnost kořenového objektu samostatně.

První položka `AndersenFamily`, obsahuje `children` pouze jeden prvek, takže sada výsledků obsahuje pouze jeden objekt. Druhá položka `WakefieldFamily`, obsahuje `children`dva , takže křížový produkt vytvoří `children` dva objekty, jeden pro každý prvek. Kořenová pole v obou těchto položkách jsou stejná, stejně jako byste očekávali v křížovém produktu.

Skutečným nástrojem klauzule JOIN je vytvoření řazených kolekcí členů z křížového produktu ve tvaru, který je jinak obtížné promítnout. Následující příklad filtruje kombinaci řazené kolekce členů, která umožňuje uživateli zvolit podmínku, kterou celkově splňuje řazená kolekce členů.

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

Následující rozšíření předchozího příkladu provádí dvojité spojení. Křížový produkt můžete zobrazit jako následující pseudokód:

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

`AndersenFamily`má jedno dítě, které má jedno domácí zvíře,\*takže\*křížový produkt dává jednu řadu (1 1 1) z této rodiny. `WakefieldFamily`má dvě děti, z nichž pouze jedno má domácí zvířata, ale toto dítě má dvě domácí zvířata. Křížový produkt pro tuto\*rodinu\*dává 1 1 2 = 2 řádky.

V dalším příkladu je další `pet`filtr na , který vylučuje všechny řazené kolekce členů, kde není název domácího mazlíčka `Shadow`. Můžete sestavit řazené kolekce členů z polí, filtrovat na některý z prvků řazené kolekce členů a promítnout libovolnou kombinaci prvků.

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
- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Poddotazů](sql-query-subquery.md)