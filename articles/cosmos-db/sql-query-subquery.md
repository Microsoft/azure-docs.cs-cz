---
title: Poddotazy SQL pro Azure Cosmos DB
description: Informace o poddotazech SQL a jejich běžných případech použití a různých typech poddotazů v Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870560"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Příklady poddotazů SQL pro Azure Cosmos DB

Poddotaz je dotaz vnořený do jiného dotazu. Poddotaz se také nazývá vnitřní dotaz nebo vnitřní výběr. Příkaz, který obsahuje poddotaz se obvykle nazývá vnější dotaz.

Tento článek popisuje poddotazy SQL a jejich běžné případy použití v Azure Cosmos DB. Všechny ukázkové dotazy v tomto dokumentu lze spustit proti datové sady výživy, která je předinstalována na [hřišti dotazu Azure Cosmos DB](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Typy poddotazů

Existují dva hlavní typy poddotazů:

* **Korelační**: Poddotaz, který odkazuje na hodnoty z vnějšího dotazu. Poddotaz je vyhodnocen jednou pro každý řádek, který zpracovává vnější dotaz.
* **Nekorektní**: Poddotaz, který je nezávislý na vnějším dotazu. Lze spustit samostatně bez spoléhání se na vnější dotaz.

> [!NOTE]
> Azure Cosmos DB podporuje pouze korelované poddotazy.

Poddotazy lze dále klasifikovat na základě počtu řádků a sloupců, které vrátí. Existují tři typy:
* **Tabulka**: Vrátí více řádků a více sloupců.
* **Vícehodnot :** Vrátí více řádků a jeden sloupec.
* **Skalar**: Vrátí jeden řádek a jeden sloupec.

Dotazy SQL v Db Azure Cosmos vždy vrátí jeden sloupec (jednoduchou hodnotu nebo složitý dokument). Proto v Azure Cosmos DB jsou použitelné jenom poddotazy s více hodnotami a skalárními dotazy. Poddotaz s více hodnotami můžete použít pouze v klauzuli FROM jako relační výraz. Skalární poddotaz můžete použít jako skalární výraz v klauzuli SELECT nebo WHERE nebo jako relační výraz v klauzuli FROM.

## <a name="multi-value-subqueries"></a>Vícehodnotové poddotazy

Vícehodnotové poddotazy vrátí sadu dokumentů a jsou vždy používány v rámci from klauzule. Používají se pro:

* Optimalizace výrazů JOIN. 
* Vyhodnocení nákladné výrazy jednou a odkazování vícekrát.

## <a name="optimize-join-expressions"></a>Optimalizovat výrazy JOIN

Vícehodnotové poddotazy můžete optimalizovat join výrazy tím, že tlačí predikáty za každý výraz select-many, nikoli po všech křížových spojeních v klauzuli WHERE.

Zamyslete se nad následujícím dotazem:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Pro tento dotaz bude index odpovídat všem dokumentům, které mají značku, s názvem "počáteční kojenecká výživa". Je to živina položka s hodnotou mezi 0 a 10 a podávací položka s množstvím větší než 1. Výraz JOIN zde provede křížový součin všech položek značek, živin a porcí polí pro každý odpovídající dokument před použitím libovolného filtru. 

Klauzule WHERE pak použije predikát filtru na každý <c, t, n, s> n-t. Například pokud odpovídající dokument měl 10 položek v každé ze tří polí, rozšíří se na 1 x 10 x 10 x 10 (to znamená 1 000) řazené kolekce členů. Použití poddotazů zde může pomoci při filtrování spojených položek pole před spojením s dalším výrazem.

Tento dotaz je ekvivalentní předchozímu dotazu, ale používá poddotazy:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Předpokládejme, že pouze jedna položka v poli značky odpovídá filtru a existuje pět položek pro živiny a porce pole. Výrazy JOIN se pak rozbalí na 1 x 1 x 5 x 5 = 25 položek, na rozdíl od 1 000 položek v prvním dotazu.

## <a name="evaluate-once-and-reference-many-times"></a>Vyhodnoťte jednou a odkazujte mnohokrát

Poddotazy mohou pomoci optimalizovat dotazy s drahými výrazy, jako jsou uživatelem definované funkce (UD), složité řetězce nebo aritmetické výrazy. Můžete použít poddotaz spolu s výrazem JOIN k vyhodnocení výrazu jednou, ale odkazovat na něj mnohokrát.

Následující dotaz spustí UDF `GetMaxNutritionValue` dvakrát:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Zde je ekvivalentní dotaz, který spouští UDF pouze jednou:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Mějte na paměti chování mezi produkty výrazů JOIN. Pokud výraz UDF lze vyhodnotit na undefined, měli byste zajistit, že výraz JOIN vždy vytvoří jeden řádek vrácením objektu z poddotazu, nikoli přímo hodnoty.
>

Zde je podobný příklad, který vrací objekt spíše než hodnotu:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Tento přístup se neomezuje pouze na UDF. Platí pro všechny potenciálně nákladné výraz. Můžete například zaujmout stejný přístup s `avg`matematickou funkcí :

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Napodobit spojení s externími referenčními daty

Často může být nutné odkazovat na statická data, která se mění jen zřídka, například jednotky měření nebo kódy zemí. Je lepší tato data pro každý dokument duplikovat. Vyhnete-li se této duplicitě, ušetříte na úložišti a zlepšíte výkon zápisu tím, že budete mít menší velikost dokumentu. Poddotaz můžete použít k napodobení sémantiky vnitřního spojení s kolekcí referenčních dat.

Zvažte například tuto sadu referenčních dat:

| **Jednotka** | **Název**            | **Multiplikátor** | **Základní jednotka** |
| -------- | ------------------- | -------------- | ------------- |
| Ng       | Nanogram            | 1.00E-09       | Gram          |
| Μg       | Mikrogramů           | 1.00E-06       | Gram          |
| Mg       | Miligram           | 1.00E-03       | Gram          |
| g        | Gram                | 1.00E+00       | Gram          |
| Kg       | Kilogram            | 1.00E+03       | Gram          |
| Mg       | Megagram            | 1.00E+06       | Gram          |
| Gg       | Gigagram            | 1.00E+09       | Gram          |
| Nj       | Nanojoule           | 1.00E-09       | Joule         |
| μJ       | Mikrojoule          | 1.00E-06       | Joule         |
| Mj       | Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E+00       | Joule         |
| Kj       | Kilojoule           | 1.00E+03       | Joule         |
| Mj       | Megajoule           | 1.00E+06       | Joule         |
| Gj       | Gigajoule (Gigajoule)           | 1.00E+09       | Joule         |
| Cal      | Kalorií             | 1.00E+00       | Kalorií       |
| Kcal     | Kalorií             | 1.00E+03       | Kalorií       |
| Iu       | Mezinárodní jednotky |                |               |


Následující dotaz napodobuje spojení s těmito daty tak, že přidáte název jednotky do výstupu:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Skalární poddotazy

Skalární výraz poddotazu je poddotaz, který se vyhodnocuje na jednu hodnotu. Hodnota výrazu skalárního poddotazu je hodnota projekce (klauzule SELECT) poddotazu.  Výraz skalárního poddotazu můžete použít na mnoha místech, kde je skalární výraz platný. Například můžete použít skalární poddotaz v libovolném výrazu v klauzulích SELECT a WHERE.

Použití skalárního poddotazu však ne vždy pomůže optimalizovat. Například předání skalárního poddotazu jako argumentu systémovým nebo uživatelem definovaným funkcím neposkytuje žádnou výhodu ve spotřebě jednotky prostředků (RU) nebo latenci.

Skalární poddotazy lze dále klasifikovat jako:
* Skalární poddotazy s jednoduchým výrazem
* Agregovat skalární poddotazy

## <a name="simple-expression-scalar-subqueries"></a>Skalární poddotazy s jednoduchým výrazem

Skalární poddotaz s jednoduchým výrazem je korelační poddotaz, který má klauzuli SELECT, která neobsahuje žádné agregační výrazy. Tyto poddotazy poskytují žádné výhody optimalizace, protože kompilátor převádí je do jednoho většího jednoduchého výrazu. Neexistuje žádný korelační kontext mezi vnitřní a vnější dotazy.

Zde je několik příkladů:

**Příklad 1**

```sql
SELECT 1 AS a, 2 AS b
```

Tento dotaz můžete přepsat pomocí skalárního poddotazu s jednoduchým výrazem takto:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Oba dotazy produkují tento výstup:

```json
[
  { "a": 1, "b": 2 }
]
```

**Příklad 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Tento dotaz můžete přepsat pomocí skalárního poddotazu s jednoduchým výrazem takto:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Výstup dotazu:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Příklad 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Tento dotaz můžete přepsat pomocí skalárního poddotazu s jednoduchým výrazem takto:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Výstup dotazu:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Agregovat skalární poddotazy

Agregovaný skalární poddotaz je poddotaz, který má agregační funkci ve své projekci nebo filtru, který je vyhodnocen na jednu hodnotu.

**Příklad 1:**

Zde je poddotaz s jedním agregačním výrazem funkce v jeho projekci:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Výstup dotazu:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Příklad 2**

Tady je poddotaz s více agregačními výrazy funkcí:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Výstup dotazu:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Příklad 3**

Zde je dotaz s agregačním poddotazem v projekci i filtru:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Výstup dotazu:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Více optimální způsob, jak napsat tento dotaz je připojit se k poddotazu a odkazovat na alias poddotazu v select a where klauzule. Tento dotaz je efektivnější, protože je třeba spustit poddotaz pouze v rámci join prohlášení a nikoli v projekci a filtru.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>Exists výraz

Azure Cosmos DB podporuje výrazy EXISTS. Toto je agregační skalární poddotaz integrovaný do rozhraní SQL API Azure Cosmos DB. EXISTS je logický výraz, který přebírá výraz poddotazu a vrátí hodnotu true, pokud poddotaz vrátí všechny řádky. V opačném případě vrátí false.

Vzhledem k tomu, že rozhraní SQL API Azure Cosmos DB nerozlišuje mezi logickými výrazy a jinými skalárními výrazy, můžete použít exists v klauzulích SELECT i WHERE. To je na rozdíl od T-SQL, kde logický výraz (například EXISTS, BETWEEN a IN) je omezen na filtr.

Pokud poddotaz EXISTS vrátí jednu hodnotu, která není definována, exists vyhodnotí na false. Zvažte například následující dotaz, který je vyhodnocen jako false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Pokud je klíčové slovo VALUE v předchozím poddotazu vynecháno, dotaz se vyhodnotí na hodnotu true:
```sql
SELECT EXISTS (SELECT undefined) 
```

Poddotaz uzavře seznam hodnot ve vybraném seznamu v objektu. Pokud vybraný seznam neobsahuje žádné hodnoty, vrátí poddotaz jednu hodnotu .{} Tato hodnota je definována, takže EXISTS vyhodnotí na true.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Příklad: Přepsání ARRAY_CONTAINS a SPOJENÍ jako EXISTS

Běžným případem použití ARRAY_CONTAINS je filtrování dokumentu podle existence položky v poli. V tomto případě kontrolujeme, zda pole značek obsahuje položku s názvem "oranžová".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Můžete přepsat stejný dotaz, který chcete použít exists:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Kromě toho ARRAY_CONTAINS můžete pouze zkontrolovat, pokud hodnota se rovná libovolný prvek v rámci pole. Pokud potřebujete složitější filtry na vlastnosti pole, použijte JOIN.

Vezměte v úvahu následující dotaz, `nutritionValue` který filtruje na základě jednotek a vlastností v poli: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Pro každý z dokumentů v kolekci se provádí meziprodukt s prvky pole. Tato operace JOIN umožňuje filtrovat vlastnosti v rámci pole. Spotřeba ru tohoto dotazu však bude významná. Například pokud 1 000 dokumentů mělo 100 položek v každém poli, rozšíří se na 1 000 x 100 (tj. 100 000) řazených kolekcí členů.

Použití EXISTS může pomoci vyhnout se této drahé cross-product:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

V takovém případě můžete filtrovat prvky pole v poddotazu EXISTS. Pokud prvek pole odpovídá filtru, pak jej promítnete a EXISTS vyhodnotí na hodnotu true.

Můžete také alias EXISTS a odkazovat na něj v projekci:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Výstup dotazu:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>Pole výraz

Výraz ARRAY můžete použít k promítání výsledků dotazu jako pole. Tento výraz lze použít pouze v rámci klauzule SELECT dotazu.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Výstup dotazu:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Stejně jako u jiných poddotazů jsou možné filtry s výrazem ARRAY.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Výstup dotazu:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Pole výrazy mohou také přijít po FROM klauzule v poddotazech.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Výstup dotazu:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Další kroky

- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelování dat dokumentů](modeling-data.md)
