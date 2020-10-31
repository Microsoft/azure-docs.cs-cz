---
title: Poddotazy SQL pro Azure Cosmos DB
description: Přečtěte si o poddotazech SQL a jejich běžných případech použití a různých typech poddotazů v Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 16be1b91d007ca2dbc88405cfc55ff519f51ee41
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081532"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Příklady poddotazů SQL pro Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Poddotaz je dotaz vnořený do jiného dotazu. Poddotaz se také nazývá vnitřní dotaz nebo vnitřní výběr. Příkaz, který obsahuje poddotaz, se obvykle nazývá vnější dotaz.

Tento článek popisuje poddotazy SQL a jejich běžné případy použití v Azure Cosmos DB. Všechny Ukázkové dotazy v tomto dokumentu lze spustit na nutriční datové sadě, která je předem načtena v [Azure Cosmos DB testovací prostředí dotazů](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Typy poddotazů

Existují dva hlavní typy poddotazů:

* **Korelace** : poddotaz, který odkazuje na hodnoty z vnějšího dotazu. Poddotaz se vyhodnocuje jednou pro každý řádek, který zpracovává vnější dotaz.
* **Bez korelace** : poddotaz, který je nezávislý na vnějším dotazu. Dá se spustit samostatně, aniž by se musel spoléhat na vnější dotaz.

> [!NOTE]
> Azure Cosmos DB podporuje pouze korelační poddotazy.

Poddotazy lze dále klasifikovat na základě počtu řádků a sloupců, které vrátí. Existují tři typy:
* **Table** : vrátí více řádků a více sloupců.
* **Multi-Value** : vrátí více řádků a jeden sloupec.
* **Skalární** : vrátí jeden řádek a jeden sloupec.

Dotazy SQL v Azure Cosmos DB vždycky vracejí jeden sloupec (buď jednoduchou hodnotu, nebo složitý dokument). Proto se v Azure Cosmos DB použijí pouze vícehodnotové a skalární poddotazy. Poddotaz s více hodnotami lze použít pouze v klauzuli FROM jako relační výraz. Skalární poddotaz lze použít jako skalární výraz v klauzuli SELECT nebo WHERE nebo jako relační výraz v klauzuli FROM.

## <a name="multi-value-subqueries"></a>Poddotazy s více hodnotami

Poddotazy s více hodnotami vrací sadu dokumentů a jsou vždy použity v rámci klauzule FROM. Používají se pro:

* Optimalizují se výrazy JOIN. 
* Vyhodnocování drahých výrazů jednou a odkazování vícekrát.

## <a name="optimize-join-expressions"></a>Optimalizovat výrazy JOIN

Podhodnoty poddotazů mohou optimalizovat výrazy JOIN vložením predikátů za každým výrazem Select-many, nikoli po všech křížových spojeních v klauzuli WHERE.

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

Pro tento dotaz bude index odpovídat jakémukoli dokumentu, který má značku s názvem "počáteční vzorec". Jedná se o živinovou položku s hodnotou mezi 0 a 10 a obsluhující položku s množstvím větším než 1. Výraz JOIN tady provede více produktů všech položek značek, živin a zachová pole pro každý shodný dokument před použitím jakéhokoli filtru. 

Klauzule WHERE pak použije predikát filtru u každé <c, t, n, s> řazené kolekce členů. Pokud má například odpovídající dokument 10 položek v každém ze tří polí, rozšíří se na 1 x 10 x 10 x 10 (tj. 1 000) řazené kolekce členů. Použití poddotazů tady může pomoci při filtrování propojených položek pole před připojením k dalšímu výrazu.

Tento dotaz je ekvivalentní předchozímu, ale používá poddotazy:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Předpokládejme, že filtr odpovídá pouze jedné položce v poli značek a existuje pět položek pro živiny i pole. Výrazy JOIN se pak rozšíří na 1 x 1 x 5 × 5 = 25 položek, a to na rozdíl od 1 000 položek v prvním dotazu.

## <a name="evaluate-once-and-reference-many-times"></a>Vyhodnotit jednou a odkazovat mnohokrát

Poddotazy můžou přispět k optimalizaci dotazů s nákladnými výrazy, jako jsou uživatelsky definované funkce (UDF), komplexní řetězce nebo aritmetické výrazy. Můžete použít poddotaz spolu s výrazem JOIN k vyhodnocení výrazu, ale na něj bude odkazovat mnohokrát.

Následující dotaz používá formát UDF `GetMaxNutritionValue` dvakrát:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Tady je ekvivalentní dotaz, který používá formát UDF jenom jednou:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Mějte na paměti, že se ve výrazech spojení nejedná o chování mezi produkty. Pokud se výraz UDF může vyhodnotit jako nedefinovaný, měli byste zajistit, aby výraz JOIN vždy vytvořil jeden řádek vrácením objektu z dílčího dotazu a nikoli hodnoty přímo.
>

Zde je podobný příklad, který vrací objekt namísto hodnoty:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Přístup není omezený na UDF. Platí pro jakýkoliv potenciálně nákladný výraz. Můžete například využít stejný přístup k matematické funkci `avg` :

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Napodobení spojení s externími referenčními daty

Často je třeba odkazovat na statická data, která se zřídka mění, například jednotky měření nebo kód země. Je lepší, aby taková data pro každý dokument nebyla duplicitní. Když se tato duplicita vyhnete, uloží se do úložiště a vylepšit výkon při zápisu, protože velikost dokumentu zůstane menší. Můžete použít poddotaz k napodobování sémantiky vnitřního spojení s kolekcí referenčních dat.

Zvažte například tuto sadu referenčních dat:

| **Jednotka** | **Název**            | **Koeficient** | **Základní jednotka** |
| -------- | ------------------- | -------------- | ------------- |
| plyn       | Nanogram            | 1,00 e-09       | Gram          |
| μg       | Microgram           | 1,00 e-06       | Gram          |
| mg/Nm3       | Mg           | 1,00 e-03       | Gram          |
| g        | Gram                | 1,00 e + 00       | Gram          |
| kg       | Kilogram            | 1,00 e + 03       | Gram          |
| Mg/Nm3       | Megagram            | 1,00 e + 06       | Gram          |
| GG       | Gigagram            | 1,00 e + 09       | Gram          |
| Newarku       | Nanojoule           | 1,00 e-09       | Joule         |
| µJ       | Microjoule          | 1,00 e-06       | Joule         |
| mJ       | Millijoule          | 1,00 e-03       | Joule         |
| J        | Joule               | 1,00 e + 00       | Joule         |
| Systému kJ       | Kilojoule           | 1,00 e + 03       | Joule         |
| MJ       | Megajoule           | 1,00 e + 06       | Joule         |
| GJ       | Gigajoule           | 1,00 e + 09       | Joule         |
| klientských      | Calorie             | 1,00 e + 00       | calorie       |
| kcal     | Calorie             | 1,00 e + 03       | calorie       |
| IU       | Mezinárodní jednotky |                |               |


Následující dotaz napodobá spojení s těmito daty, takže přidáte název jednotky do výstupu:

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

Skalární výraz poddotazu je poddotaz, který je vyhodnocen jako jediná hodnota. Hodnota výrazu skalárního poddotazu je hodnota projekce (VÝBĚRová klauzule) poddotazu.  Můžete použít výraz skalárního poddotazu na mnoha místech, kde je skalární výraz platný. Například můžete použít skalární poddotaz v libovolném výrazu v klauzulích SELECT a WHERE.

Použití skalárního poddotazu neumožňuje vždy optimalizovat, ale. Například předání skalárního poddotazu jako argumentu systém nebo uživatelsky definované funkce neposkytuje žádnou výhodu v spotřebě nebo latenci prostředků jednotky (RU).

Skalární poddotazy lze dále klasifikovat jako:
* Skalární poddotazy jednoduchého výrazu
* Agregace skalárních poddotazů

## <a name="simple-expression-scalar-subqueries"></a>Skalární poddotazy jednoduchého výrazu

Skalární poddotaz v jednoduchém výrazu je korelační poddotaz, který má klauzuli SELECT, která neobsahuje žádné agregační výrazy. Tyto poddotazy neposkytují žádné výhody optimalizace, protože kompilátor je převede na jeden větší jednoduchý výraz. Mezi vnitřními a vnějšími dotazy neexistuje žádný korelační kontext.

Tady je několik příkladů:

**Příklad 1**

```sql
SELECT 1 AS a, 2 AS b
```

Tento dotaz můžete přepsat pomocí jednoduchého skalárního poddotazu jednoduchého výrazu na:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Oba dotazy vytváří tento výstup:

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

Tento dotaz můžete přepsat pomocí jednoduchého skalárního poddotazu jednoduchého výrazu na:

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

Tento dotaz můžete přepsat pomocí jednoduchého skalárního poddotazu jednoduchého výrazu na:

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

### <a name="aggregate-scalar-subqueries"></a>Agregace skalárních poddotazů

Agregační skalární poddotaz je poddotaz, který obsahuje agregační funkci ve své projekci nebo filtru, který je vyhodnocen jako jediná hodnota.

**Příklad 1:**

Tady je poddotaz s jedním agregačním výrazem funkce ve své projekci:

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

Tady je poddotaz s vícenásobnými agregačními výrazy funkcí:

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

Tady je dotaz s agregačním poddotazem v projekci i ve filtru:

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

Efektivnější způsob, jak tento dotaz zapsat, je připojit se k poddotazu a odkazovat na alias poddotazu v klauzulích SELECT a WHERE. Tento dotaz je efektivnější, protože musíte spustit poddotaz jenom v rámci příkazu JOIN, a ne jak v projekci, tak ve filtru.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>Výraz EXISTS

Azure Cosmos DB podporuje výrazy EXISTS. Toto je agregovaný skalární poddotaz integrovaný do rozhraní API služby Azure Cosmos DB SQL. EXISTUJE logický výraz, který přebírá výraz poddotazu a vrací hodnotu true, pokud poddotaz vrátí všechny řádky. V opačném případě vrátí hodnotu false.

Protože rozhraní API Azure Cosmos DB SQL nerozlišuje mezi logickými výrazy a žádnými jinými skalárními výrazy, můžete použít v klauzulích SELECT a WHERE. To je na rozdíl od T-SQL, kde logický výraz (například existuje, mezi a v) je omezen na filtr.

Pokud poddotaz EXISTS vrátí jednu nedefinovanou hodnotu, vyhodnotí se hodnota EXISTS na false. Zvažte například následující dotaz, který se vyhodnotí jako false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Pokud je klíčové slovo VALUE v předchozím poddotazu vynecháno, dotaz se vyhodnotí jako true:
```sql
SELECT EXISTS (SELECT undefined) 
```

Poddotaz vloží seznam hodnot do vybraného seznamu v objektu. Pokud vybraný seznam neobsahuje žádné hodnoty, bude poddotaz vracet jedinou hodnotu {} ' '. Tato hodnota je definována, takže EXISTS je vyhodnocena jako true.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Příklad: přepis ARRAY_CONTAINS a JOIN jako existující

Běžným případem použití ARRAY_CONTAINS je filtrování dokumentu podle existence položky v poli. V tomto případě kontrolujeme, zda pole značek obsahuje položku s názvem "oranžová".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Můžete přepsat stejný dotaz, který se má použít:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Kromě toho ARRAY_CONTAINS možné zaškrtnout pouze v případě, že je hodnota rovna libovolnému prvku v poli. Pokud k vlastnostem pole potřebujete komplexnější filtry, použijte příkaz JOIN.

Vezměte v úvahu následující dotaz, který filtruje na základě jednotek a `nutritionValue` vlastností v poli: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Pro každý z dokumentů v kolekci je mezi produktem proveden prvek pole. Tato operace JOIN umožňuje filtrovat vlastnosti v rámci pole. Tato spotřeba z tohoto dotazu bude ale významná. Pokud třeba 1 000 dokumenty obsahovaly 100 položek v každém poli, rozšíří se na 1 000 a × 100 (tj. 100 000) řazené kolekce členů.

Použití existující aplikace může pomoci vyhnout se tomuto nákladnému více produktům:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

V tomto případě filtrujete prvky pole v rámci poddotazu EXISTS. Pokud prvek pole odpovídá filtru, potom jej provedete a existuje vyhodnocen jako true.

Můžete také alias existovat a odkazovat na něj v projekci:

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

## <a name="array-expression"></a>Výraz pole

Výraz ARRAY můžete použít k projekci výsledků dotazu jako pole. Tento výraz můžete použít pouze v klauzuli SELECT dotazu.

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

Stejně jako u ostatních poddotazů je možné filtry s výrazem pole.

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

Výrazy Array mohou také pocházet za klauzulí FROM v poddotazech.

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

- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelování dat dokumentů](modeling-data.md)
