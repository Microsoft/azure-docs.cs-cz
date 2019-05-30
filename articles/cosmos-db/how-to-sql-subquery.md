---
title: Poddotazy SQL pro službu Azure Cosmos DB
description: Další informace o SQL poddotazy a jejich běžné případy použití ve službě Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: tisande
ms.openlocfilehash: 68465f4ca195930ce08bb579e68d0227e9c18dd6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242846"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Příklady poddotaz SQL pro službu Azure Cosmos DB

Poddotaz je dotaz vnořené jiný dotaz. Poddotazu se také nazývá vnitřní dotaz nebo vnitřní vyberte. Příkaz, který obsahuje poddotaz je obvykle volána vnější dotaz.

Tento článek popisuje SQL poddotazy a jejich běžné případy použití ve službě Azure Cosmos DB.

## <a name="types-of-subqueries"></a>Typy poddotazů

Existují dva hlavní typy poddotazy:

* **Korelační**: Poddotaz, který odkazuje na hodnoty z vnější dotaz. Poddotazu se vyhodnotí jednou pro každý řádek, který zpracovává vnější dotaz.
* **Korelační**: Poddotaz, která je nezávislá vnější dotaz. Může běžet na svůj vlastní bez nutnosti spoléhat se na vnější dotaz.

> [!NOTE]
> Azure Cosmos DB podporuje pouze korelační poddotazy.

Poddotazy lze dále rozdělit na základě počtu řádků a sloupců, které vracejí. Existují tři typy:
* **Tabulka**: Vrátí více řádků a více sloupců.
* **Vícehodnotový**: Vrátí více řádků a jeden sloupec.
* **Skalární**: Vrátí jeden řádek a jeden sloupec.

Dotazy SQL ve službě Azure Cosmos DB je vždycky vrátí jeden sloupec (hodnotu jednoduché nebo komplexní dokumentu). Proto pouze s více hodnotami a skalární poddotazy se dají použít ve službě Azure Cosmos DB. Vícehodnotový poddotaz můžete použít jenom v klauzuli FROM jako relační výraz. Skalární poddotaz můžete použít jako skalární výraz, který v POLOŽKU nebo klauzuli WHERE, nebo jako relační výraz v klauzuli FROM.


## <a name="multi-value-subqueries"></a>Poddotazy s více hodnotami

Vícehodnotový poddotazy vrátit sadu dokumentů a používají se vždycky v rámci klauzule FROM. Slouží pro:

* Optimalizace spojení výrazů. 
* Po vyhodnocení výrazů nákladné a odkazuje na více než jednou.

### <a name="optimize-join-expressions"></a>Optimalizace spojení výrazů

Poddotazy s více hodnotami lze optimalizovat spojení výrazů formou predikáty po každý výraz select: n, nikoli po všechny křížové spojení v klauzuli WHERE.

Vezměte v úvahu následující dotaz:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Pro tento dotaz bude odpovídat indexu všechny dokumenty, které má značku s názvem "počáteční vzorec." Je obsah položky s hodnotou 0 až 10 a poskytování obsahu položky dobu větší než 1. Tady výrazu JOIN provede mezi produkty všechny položky značky, živin a porcí pole pro každý odpovídající dokument před použitím filtru. 

Klauzule WHERE potom použije filtr predikát pro každou < c, t, n, s > řazené kolekce členů. Například pokud odpovídající dokumentu 10 položek v každém ze tří polí, ji bude rozbalen do 1 × 10 x 10 x 10 (1000) řazené kolekce členů. Poddotazy Tady můžete pomoci v odfiltrováním položek pole připojené k doméně před spojením s další výraz.

Tento dotaz je ekvivalentní s předchozím histogramem ale používá poddotazy:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Předpokládají, že pouze jedna položka v poli značky odpovídá filtru, a je pět položek pro živin a porcí pole. SPOJENÍ výrazů se pak rozšířit na 1 × 1 × 5 × 5 = 25 položky, na rozdíl od 1 000 položek v prvním dotazu.

### <a name="evaluate-once-and-reference-many-times"></a>Hodnocení jednou a odkaz v mnoha případech

Poddotazy může pomoct optimalizovat dotazy s nákladné výrazy, jako je například uživatelem definované funkce (UDF), komplexní řetězce nebo aritmetických výrazech. Vyhodnocení výrazu jednou, ale na něj odkazovat v mnoha případech můžete použít poddotaz spolu s výrazu JOIN.

Spuštění následujícího dotazu UDF `GetMaxNutritionValue` dvakrát:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Tady je ekvivalentní dotazu, který spouští UDF pouze jednou:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Mějte na paměti mezi produkty chování spojení výrazů. Pokud UDF výraz lze vyhodnotit na nedefinovaný, měli byste zajistit, že výraz spojení vždy vytvoří jeden řádek tak, že vrací objekt z poddotazu, nikoli hodnotu přímo.
>

Tady je podobný příklad, který vrací objekt, nikoli hodnotu:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Tento přístup není omezena pouze na funkce UDF. Platí pro libovolný výraz potenciálně nákladné. Například můžete provést stejný přístup s matematické funkce `avg`:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>Napodobení spojení s daty externího odkazu

Potřebujete často odkazují na statická data, která se mění jen zřídka, jako jsou jednotky měření nebo kódy zemí. Je lepší nepoužívat duplicitní taková data pro každý dokument. Zabránění tato duplikace ušetříte na úložišti a zvýšit výkon zápisu udržováním menší velikost dokumentu. Můžete tak, aby napodoboval sémantiky vnitřního spojení s kolekcí referenčních dat poddotaz.

Zvažte například tuto sadu referenční data:

| **Jednotka** | **Název**            | **Násobitel** | **Základní jednotka** |
| -------- | ------------------- | -------------- | ------------- |
| NG       | Nanogram            | 1.00E-09       | Gramatika          |
| µg       | Microgram           | 1.00E-06       | Gramatika          |
| mg       | Milligram           | 1.00E-03       | Gramatika          |
| G        | Gramatika                | 1.00E + 00       | Gramatika          |
| kg       | Kilogram            | 1.00E + 03       | Gramatika          |
| mg       | Megagram            | 1.00E + 06       | Gramatika          |
| Gg       | Gigagram            | 1.00E + 09       | Gramatika          |
| nJ       | Nanojoule           | 1.00E-09       | Joule – Měrná         |
| µJ       | Microjoule          | 1.00E-06       | Joule – Měrná         |
| mJ       | Millijoule          | 1.00E-03       | Joule – Měrná         |
| J        | Joule – Měrná               | 1.00E + 00       | Joule – Měrná         |
| kJ       | Kilojoule           | 1.00E + 03       | Joule – Měrná         |
| MJ       | Megajoule           | 1.00E + 06       | Joule – Měrná         |
| GJ       | Gigajoule           | 1.00E + 09       | Joule – Měrná         |
| licence CAL      | Kalorie             | 1.00E + 00       | Kalorie       |
| kcal     | Kalorie             | 1.00E + 03       | Kalorie       |
| IU       | Mezinárodní jednotky |                |               |


Následující dotaz napodobuje propojení s těmito daty tak, že přidáte název jednotky do výstupu:

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

## <a name="scalar-subqueries"></a>Skalární poddotazů

Poddotaz skalární výraz, který je poddotaz, který se vyhodnotí na hodnotu single. Hodnota výrazu poddotaz skalární hodnotu projekce (klauzule SELECT) poddotazu.  Poddotaz skalární výraz, který můžete použít na mnoha místech, kde skalární výraz, který je platný. Například můžete použít skalární poddotaz v jakékoli výraz v obou POLOŽKU a klauzule WHERE.

Skalární poddotazu není vždy pomoci optimalizovat, i když. Například předávání skalární poddotaz jako argument systémem nebo uživatelem definované funkce nepřináší žádné výhody v spotřeby prostředků jednotky (RU) nebo latence.

Skalární poddotazy můžete dále dělí jako:
* Skalární poddotazy jednoduchý výraz
* Agregační skalární poddotazů

### <a name="simple-expression-scalar-subqueries"></a>Skalární poddotazy jednoduchý výraz

Skalární poddotaz jednoduchý výraz je poddotaz korelační klauzule SELECT, který neobsahuje žádné agregační výrazy. Tyto poddotazy poskytuje výhody žádné optimalizace, protože kompilátor převede je do jedné větší jednoduchý výraz. Neexistuje žádný korelační kontext mezi vnitřní a vnější dotazů.

Tady je několik příkladů:

**Příklad 1**

```sql
SELECT 1 AS a, 2 AS b
```

Tento dotaz, můžete přepsat pomocí skalární poddotaz jednoduchý výraz:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Oba dotazy vytvoří tento výstup:

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

Tento dotaz, můžete přepsat pomocí skalární poddotaz jednoduchý výraz:

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

Tento dotaz, můžete přepsat pomocí skalární poddotaz jednoduchý výraz:

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

### <a name="aggregate-scalar-subqueries"></a>Agregační skalární poddotazů

Agregační skalární poddotaz je poddotazu obsahuje agregační funkci v projekci nebo filtr, který se vyhodnotí jako jedinou hodnotu.

**Příklad 1:**

Tady je poddotaz s výrazem jeden agregační funkci v jeho projekce:

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

Tady je poddotaz s více výrazy agregační funkce:

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

Tady je dotaz s agregační poddotaz v projekci a filtr:

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

Více ideální způsob, jak napsat tento dotaz je spojit poddotazu a odkazovat na alias v obou SELECT poddotazu a klauzule WHERE. Tento dotaz je mnohem efektivnější, protože je nutné provést poddotazu, pouze v rámci příkazu join a ne v projekci i filtru.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

#### <a name="exists-expression"></a>Výraz EXISTS

Azure Cosmos DB podporuje výrazy EXISTS. Toto je agregované skalární poddotaz integrované do SQL API služby Azure Cosmos DB. EXISTS je logický výraz, který přijímá poddotaz výrazy a vrátí hodnotu true, pokud poddotaz vrátí všechny řádky. V opačném případě vrátí hodnotu false.

Protože SQL API služby Azure Cosmos DB nerozlišují varianty logické výrazy a další skalární výrazy, můžete použít existuje v obou POLOŽKU a klauzule WHERE. To je rozdíl oproti T-SQL, kde je omezen na filtr logický výraz (například EXISTS, mezi a).

Pokud existuje poddotaz vrátí jednu hodnotu, která má nedefinované, existuje vyhodnotí na hodnotu false. Zvažte například následující dotaz, který se vyhodnotí na hodnotu false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Pokud je vynechán VALUE – klíčové slovo v předchozím poddotazu, dotaz bude vyhodnocen na hodnotu true:
```sql
SELECT EXISTS (SELECT undefined) 
```

Poddotaz bude zahrnovat seznam hodnot ve vybraném seznamu v objektu. Pokud vybraný seznam neobsahuje žádné hodnoty, poddotazu vrátí jednu hodnotu "{}". Tato hodnota je definován, takže EXISTS vyhodnotí jako true.

#### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>Příklad: Přepis adres ARRAY_CONTAINS a spojení jako EXISTS

Běžným případem použití z ARRAY_CONTAINS je vyfiltrovat dokumentu existenci položku v poli. V tomto případě jsme už kontroluje se, zda pole značky obsahuje položku s názvem "oranžovou."

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Stejný dotaz, který bude použit EXISTS je možné přepsat:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Kromě toho ARRAY_CONTAINS může vrátit pouze pokud je hodnota rovna libovolný prvek v poli. Pokud potřebujete složitější filtry u pole vlastností, pomocí spojení.

Vezměte v úvahu následující dotaz, který filtruje na základě jednotek a `nutritionValue` vlastnosti pole: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Pro jednotlivé dokumenty v kolekci probíhá mezi produkty s jeho prvky pole. Tato operace JOIN umožňuje můžete filtrovat podle vlastností v rámci pole. Tento dotaz spotřebu RU však bude významný. Například pokud 1 000 dokumentů 100 položek v každé pole, se bude rozbalen do 1 000 × 100 (to znamená 100 000) řazené kolekce členů.

Pomocí EXISTS může pomoct vyhnout této nákladné mezi produkty:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

V takovém případě můžete filtrovat podle pole elementů v rámci existuje poddotaz. Pokud k elementu pole odpovídá filtru, potom projektových a EXISTS vyhodnotí jako true.

Můžete také alias EXISTS odkazovat v projekci:

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

#### <a name="array-expression"></a>Výraz pole

Chcete-li promítnout výsledky dotazu jako pole můžete použít výraz pole. Můžete použít tento výraz jenom v klauzuli SELECT dotazu.

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

Stejně jako u jiných poddotazy, je možné filtry se výraz pole.

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

Pole výrazy mohou pocházet také po klauzuli FROM v poddotazy.

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

## <a name="next-steps"></a>Další postup

- [Příklady dotazů SQL](how-to-sql-query.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modelování dat dokumentů](modeling-data.md)
