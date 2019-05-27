---
title: Dotazy SQL pro službu Azure Cosmos DB
description: Další informace o syntaxi jazyka SQL, databázových koncepcí a dotazů SQL pro službu Azure Cosmos DB. Použití SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: bbca0239053b8f3164055a07b376abc597b0348f
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954128"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Příklady dotazů SQL pro službu Azure Cosmos DB

Účty služby Azure SQL API služby Cosmos DB podporuje dotazování položek pomocí jazyka SQL (Structured Query) jako dotazovací jazyk typu JSON. Cíle návrhu dotazovací jazyk služby Azure Cosmos DB je:

* Podporují SQL, jeden z nejčastěji známé a Oblíbené dotazovací jazyky, namísto inventing nový dotazovací jazyk. SQL přes JSON položky poskytuje formální programovací model pro bohaté dotazy.  

* Model programování v jazyce JavaScript můžete použijte jako základ pro dotazovací jazyk. Systém typů, vyhodnocení výrazu a volání funkce v jazyce JavaScript jsou kořeny rozhraní SQL API. Tyto kořeny poskytují přirozené programovací model pro funkce, jako relační projekce, hierarchická navigace mezi položkami formátu JSON, spojení, prostorových dotazů a volání uživatelem definované funkce (UDF) vytvořené zcela v JavaScriptu.

Tento článek vás provede Příklady dotazů SQL na jednoduché položky formátu JSON. Další informace o syntaxi jazyka SQL služby Azure Cosmos DB najdete v tématu [referenční příručka syntaxe SQL](sql-api-query-reference.md).

## <a id="GettingStarted"></a>Začínáme s dotazy SQL

Ve vašem účtu SQL rozhraní API Cosmos DB, vytvoření kontejneru s názvem `Families`. Vytvořte dvě jednoduché položky JSON v kontejneru a spouštějte je několik jednoduchých dotazů.

### <a name="create-json-items"></a>Vytváření položek JSON

Následující kód vytvoří dvě jednoduché JSON položky o rodiny. Jednoduché položky JSON rodin Andersen a Wakefieldů zahrnují nadřazené položky, podřízené položky a jejich mazlíčků, adresu a informace o registraci. První položka obsahuje řetězce, čísla, logické hodnoty, pole a vnořené vlastnosti.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Druhá položka používá `givenName` a `familyName` místo `firstName` a `lastName`.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female", 
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Dotaz JSON položek

Zkuste několik dotazů na data JSON pochopit některé z klíčových aspektů dotazovací jazyk SQL služby Azure Cosmos DB.

Následující dotaz vrátí položek kde `id` pole odpovídá `AndersenFamily`. Protože se jedná `SELECT *` dotaz, výstup dotazu je úplná položka formátu JSON. Další informace o vyberte syntaxe, naleznete v tématu [příkaz SELECT](sql-api-query-reference.md#select-query). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky dotazu jsou: 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Následující dotaz přeformátuje výstup JSON v odlišném tvaru. Dotaz projekty nové JSON `Family` objekt s dvěma vybraná pole `Name` a `City`, pokud adresa město je stejné jako stav. Tento případ se shoduje s "NY, USA".

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Výsledky dotazu jsou:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Následující dotaz vrátí křestní jména všech dětí v rodině jehož `id` odpovídá `WakefieldFamily`, seřazený podle města.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Výsledky jsou:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

Předchozí příklady ukazují několik aspektů dotazovací jazyk služby Cosmos DB:  

* Protože rozhraní SQL API pracuje na hodnoty JSON, zabývá entity ve tvaru stromu místo řádků a sloupců. Můžete se podívat do uzlů stromu na libovolnou hloubku libovolného jako `Node1.Node2.Node3…..Nodem`, podobně jako odkaz na dvě části `<table>.<column>` v ANSI SQL.

* Protože dotazovací jazyk pracuje s daty bez schématu, systém typů musí být vázán dynamicky. Stejný výraz může přinést různé typy na různé položky. Výsledek dotazu je platnou hodnotu JSON, ale není zaručeno, že bude pevné schéma.  

* Azure Cosmos DB podporuje pouze striktní položky formátu JSON. Systém typů a výrazy jsou omezeny na řešil pouze typy JSON. Další informace najdete v tématu [JSON specifikace](https://www.json.org/).  

* Kontejneru Cosmos DB je kolekce položek JSON bez schématu. Vztahy v různých položek kontejneru i jsou implicitně zachycena členství ve skupině, není ve primárního klíče a vztahy cizího klíče. Tato funkce je důležitá pro spojení uvnitř položky popsané dále v tomto článku.

## <a id="SelectClause"></a>Klauzule SELECT

Každý dotaz se skládá z klauzule SELECT a volitelné a klauzulí WHERE za standardy ANSI SQL. Obvykle je vypočten zdroji v klauzuli FROM a klauzuli WHERE aplikuje filtr na zdroj k načtení podmnožiny položky formátu JSON. Klauzule SELECT a projekty požadované hodnoty JSON v seznamu select. Další informace o syntaxi najdete v tématu [příkaz SELECT](sql-api-query-reference.md#select-query).

Vyberte následující dotaz vrací hodnotu příklad `address` z `Families` jehož `id` odpovídá `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a id="EscapingReservedKeywords"></a>Přistupující objekt vlastnosti v uvozovkách
Můžete přistupovat k vlastnosti pomocí vlastností v uvozovkách operator []. Například `SELECT c.grade` a `SELECT c["grade"]` jsou ekvivalentní. Tato syntaxe je užitečné k uvození vlastnost, která obsahuje mezery, speciální znaky, nebo má stejný název jako klíčové slovo SQL nebo vyhrazené slovo.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>Vnořené vlastnosti

Následující příklad projekty dvě vnořené vlastnosti `f.address.state` a `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="json-expressions"></a>Výrazy JSON

Projekce také podporuje výrazy JSON, jak je znázorněno v následujícím příkladu:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

V předchozím příkladu klauzule SELECT potřebuje k vytvoření objektu JSON, a protože ukázka poskytuje žádný klíč, v klauzuli používá název proměnné implicitní argument `$1`. Následující dotaz vrátí dvě proměnné implicitní argument: `$1` a `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="ValueKeyword"></a>VALUE – klíčové slovo

VALUE – klíčové slovo poskytuje způsob, jak vrátit hodnotu JSON samostatně. Například níže dotaz vrátí skalární výraz `"Hello World"` místo `{$1: "Hello World"}`:

```sql
    SELECT VALUE "Hello World"
```

Následující dotaz vrátí hodnoty JSON bez `address` popisku:

```sql
    SELECT VALUE f.address
    FROM Families f
```

Výsledky jsou:

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

Následující příklad ukazuje, jak vrátit primitivní hodnoty JSON (listový úroveň stromu JSON):


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

Výsledky jsou:

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="DistinctKeyword"></a>Klíčové slovo DISTINCT

Klíčové slovo DISTINCT odstraňuje duplicity v projekci dotazu.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

V tomto příkladu dotaz projekty hodnoty pro každý příjmení.

Výsledky jsou:

```json
[
    "Andersen"
]
```

Můžete také promítnout tento počet jedinečných objektů. V tomto případě pole lastName neexistuje v jednom ze dvou dokumentů, takže dotaz vrátí prázdný objekt.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Výsledky jsou:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT je také možné v projekci v poddotazu:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Tento dotaz projekty obsahující jednotlivých podřízených givenName s odebranými pole. Toto pole je alias jako ChildNames a vykreslují ve vnější dotaz.

Výsledky jsou:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="aliasing"></a>Vyhlazení

Můžete explicitně alias hodnoty v dotazech. Pokud dotaz má dvě vlastnosti se stejným názvem, přejmenujte jednu nebo obě vlastnosti tak, aby se jednoznačně rozlišit ve výsledku předpokládané pomocí aliasů.

As – klíčové slovo pro aliasy je volitelné, jak je znázorněno v následujícím příkladu, při projekci druhá hodnota jako `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>FROM – klauzule

OD (`FROM <from_specification>`) klauzule je volitelné, pokud je zdroj filtrovaná nebo předpokládané později v dotazu. Další informace o syntaxi najdete v tématu [ze syntaxe](sql-api-query-reference.md#bk_from_clause). Dotaz, jako jsou `SELECT * FROM Families` vytvoří výčet na celém `Families` kontejneru. Speciální identifikátor KOŘENOVÉ můžete použít také pro kontejner místo názvu kontejneru.

Klauzule FROM vynucuje každý dotaz následující pravidla:

* Kontejner může být například vytvořen alias, `SELECT f.id FROM Families AS f` nebo jednoduše `SELECT f.id FROM Families f`. Tady `f` je alias pro `Families`. Optional – klíčové slovo na alias je identifikátor.  

* Jednou alias nemůže být vázán na původní název zdroje. Například `SELECT Families.id FROM Families f` je syntakticky neplatný protože identifikátor `Families` byl vytvořen alias a nelze ho přeložit zobrazovat.  

* Všechny odkazované vlastnosti musí být plně kvalifikovaný, aby se zabránilo všechny nejednoznačný vazby, které chybí splňuje striktní schéma. Například `SELECT id FROM Families f` je syntakticky neplatný protože vlastnost `id` není vázán.

### <a name="get-subitems-by-using-the-from-clause"></a>Získat podřízené položky pomocí klauzule FROM

Klauzule FROM může snížit zdroji na menší podmnožinu. Pokud chcete získat výčet jenom podstrom v každé položce, můžete subroot stane zdroj, jak je znázorněno v následujícím příkladu:

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

Předchozí dotaz používá pole jako zdroj, ale můžete také použít objekt jako zdroj. Dotaz bude považovat za libovolnou hodnotu JSON platný, definovaný ve zdroji pro zařazení ve výsledku. V následujícím příkladu by vyloučit `Families` nemají `address.state` hodnotu.

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

## <a id="WhereClause"></a>Klauzule WHERE

Volitelná klauzule WHERE (`WHERE <filter_condition>`) určuje podmínky, že zdrojové položky formátu JSON musí splňovat pro dotaz můžete zahrnout do výsledků. Položka formátu JSON musí být vyhodnocen zadané podmínky, které mají `true` považovat pro výsledek. Index vrstvy používá klauzuli WHERE určit nejmenší podmnožinu zdrojové položky, které můžou být součástí výsledku. Další informace o syntaxi najdete v tématu [syntaxe WHERE](sql-api-query-reference.md#bk_where_clause).

Následující dotaz položky požadavků, které obsahují `id` vlastnost, jejíž hodnota je `AndersenFamily`. Vyloučí všechny položky, které nemá `id` vlastnost nebo jejichž hodnota neodpovídá `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Skalární výrazy v klauzuli WHERE

Předchozí příklad ukázal dotaz rovnosti jednoduché. Rozhraní SQL API také podporuje různé [skalární výrazy](#scalar-expressions). Nejčastěji používané jsou binární soubor a unární výrazy. Odkazy na vlastnosti z objektu JSON zdroje jsou také výrazy platný.

Můžete použít tyto podporované binární operátory:  

|**Typ operátoru**  | **Hodnoty** |
|---------|---------|
|Aritmetické operace | +,-,*,/,% |
|bitové operace    | \|, &, ^, <<>>,, >>> (výplně nulové posunutí doprava) |
|Logické    | A, NEBO NE      |
|Porovnání | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Řetězec     |  \|\| (zřetězení) |

Binární operátory použít následující dotazy:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Můžete také použít unární operátory +,-, ~ a ne v dotazech, jak je znázorněno v následujícím příkladu:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Odkazy na vlastnosti můžete použít taky v dotazech. Například `SELECT * FROM Families f WHERE f.isRegistered` vrátí položku JSON obsahující vlastnost `isRegistered` s hodnotou rovná `true`. Jakékoli jiné hodnoty, například `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, nebo `<array>`, vyloučí položky z výsledku. 

### <a name="equality-and-comparison-operators"></a>Operátory rovnosti a porovnání

V následující tabulce jsou uvedeny výsledek porovnání rovnosti v rozhraní SQL API mezi jakékoli dva typy JSON.

| **OP** | **Nedefinovaný** | **Hodnotu Null** | **Datový typ Boolean** | **Číslo** | **řetězec** | **objekt** | **Pole** |
|---|---|---|---|---|---|---|---|
| **Nedefinovaný** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno |
| **Hodnotu Null** | Nedefinováno | **Ok** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno |
| **Datový typ Boolean** | Nedefinováno | Nedefinováno | **Ok** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno |
| **Číslo** | Nedefinováno | Nedefinováno | Nedefinováno | **Ok** | Nedefinováno | Nedefinováno | Nedefinováno |
| **řetězec** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | **Ok** | Nedefinováno | Nedefinováno |
| **objekt** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | **Ok** | Nedefinováno |
| **Pole** | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | Nedefinováno | **Ok** |

Pro operátory porovnání, jako například `>`, `>=`, `!=`, `<`, a `<=`, porovnání typů nebo mezi dvěma objekty nebo pole vytvoří `Undefined`.  

Pokud je výsledkem skalární výraz, který `Undefined`, položka není zahrnut ve výsledku, protože `Undefined` není roven `true`.

### <a name="logical-and-or-and-not-operators"></a>Logický (AND, OR a NOT) operátory

Logické operátory pracují s logickými hodnotami. Následující tabulky popisují logické tabulky pravdivých informací pro tyto operátory:

**NEBO – operátor**

| NEBO | True | False | Nedefinováno |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Nedefinováno |
| Nedefinováno |True |Nedefinováno |Nedefinováno |

**AND – operátor**

| A | True | False | Nedefinováno |
| --- | --- | --- | --- |
| True |True |False |Nedefinováno |
| False |False |False |False |
| Nedefinováno |Nedefinováno |False |Nedefinováno |

**NOT – operátor**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Nedefinováno |Nedefinováno |

## <a name="between-keyword"></a>MEZI klíčové slovo

Jako ANSI SQL můžete použít klíčové slovo BETWEEN vyjádřit dotazy na rozsah řetězec nebo číselné hodnoty. Například následující dotaz vrátí všechny položky, ve kterých je prvním podřízeným objektem na podnikové úrovni 1-5, včetně.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Na rozdíl od v ANSI SQL, můžete také v klauzuli BETWEEN v klauzuli FROM, jako v následujícím příkladu.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

V rozhraní SQL API, na rozdíl od ANSI SQL můžete vyjádřit rozsahu dotazy na vlastnosti smíšené typy. Například `grade` může být počet lajk `5` v některé položky a řetězec jako `grade4` v jiných. V těchto případech, jako v jazyce JavaScript, výsledkem porovnání mezi dvěma různými typy `Undefined`, takže položka se přeskočí.

> [!TIP]
> Pro kratší doby provádění dotazu vytvoření zásady indexování, který používá index typu rozsah proti číselné vlastnosti nebo cesty, které BETWEEN klauzule filtry.

## <a name="in-keyword"></a>IN – klíčové slovo

Pomocí klíčového slova v můžete zkontrolovat, zda zadaná hodnota odpovídá libovolné hodnotě v seznamu. Například následující dotaz vrátí všechny rodiny položek kde `id` je `WakefieldFamily` nebo `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Následující příklad vrátí všechny položky. Pokud stav není žádný ze zadaných hodnot:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* – operátor

Speciální operátor * projekty celé položky, jako je. Při použití, musí být pouze očekávané pole. Dotaz, jako jsou `SELECT * FROM Families f` je platný, ale `SELECT VALUE * FROM Families f` a `SELECT *, f.id FROM Families f` nejsou platné. [Nejprve dotazy v tomto článku](#query-the-json-items) použít * – operátor. 

## <a name="-and--operators"></a>? a?? Operátory

Můžete použít Ternary (?) a operátory (?) podmíněné výrazy, stejně jako v programovacích jazycích, jako je sloučení C# a JavaScript. 

Můžete použít? operátorem k vytvoření nové vlastnosti JSON v reálném čase. Například následující dotaz rozdělí na podnikové úrovni úrovně do `elementary` nebo `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Lze také vnořit volání? operátor, stejně jako v následující dotaz: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Stejně jako u jiných operátorů dotazu? operátor vyloučí položky, pokud nejsou zadané vlastnosti odkazované nebo typy, který se porovnává se liší.

Použití?? operátor efektivně vyhledávat vlastnosti v položce při dotazování na smíšený typ nebo částečně strukturovaná data. Například následující dotaz vrátí `lastName` Pokud jsou k dispozici, nebo `surname` Pokud `lastName` není k dispozici.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>Operátor TOP

HORNÍ – klíčové slovo vrátí první `N` počet výsledků dotazu v nedefinované pořadí. Jako osvědčený postup, použijte horní s klauzulí ORDER BY omezit rozsah výsledků na první `N` počet seřazené hodnoty. Kombinace těchto dvou klauzulí je jediný způsob, jak předvídatelně označení, které řádky nejvyšší ovlivňuje.

Horní části můžete použít s konstantní hodnotou, stejně jako v následujícím příkladu nebo s hodnotou proměnné použití parametrizovaných dotazů. Další informace najdete v tématu [parametrizované dotazy](#parameterized-queries) oddílu.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Výsledky jsou:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="OrderByClause"></a>ORDER BY – klauzule

Stejně jako v ANSI SQL můžete zahrnout volitelné klauzuli ORDER BY v dotazech. Volitelný argument ASC nebo DESC určuje, jestli se má načíst výsledky ve vzestupném nebo sestupném pořadí. Výchozím nastavením je ASC.

Například tady je dotaz, který načte rodiny ve vzestupném pořadí název města rezidenční:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Výsledky jsou:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Následující dotaz načte řady `id`s v pořadí podle jejich datum vytvoření položky. Položka `creationDate` je představující číslo *unixovém čase*, nebo uplynulý čas od dne do 1, 1970 v řádu sekund.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Výsledky jsou:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Kromě toho můžete uspořádat podle několika vlastností. Dotaz, který seřadí podle několika vlastností vyžaduje [složeném indexu](index-policy.md#composite-indexes). Vezměte v úvahu následující dotaz:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Tento dotaz načte rodině `id` ve vzestupném pořadí název města. Pokud více položek mají stejný název města, bude order dotaz `creationDate` v sestupném pořadí.

## <a id="OffsetLimitClause"></a>OFFSET LIMIT clause

Posun omezení je volitelná klauzule přeskočit pak provést některé hodnoty z dotazu. Počet POSUNUTÍ a počtu LIMIT se vyžadují v klauzuli LIMIT posun.

Při posunu omezení se používá ve spojení s klauzulí ORDER BY, sadu výsledků dotazu je vytvořen tímto způsobem přeskočit a provádět s nimi seřazené hodnoty. Pokud se používá bez klauzule ORDER by, způsobí deterministické pořadí hodnot.

Tady je příklad dotaz, který přeskočí první hodnotu a vrátí hodnotu druhého (v pořadí podle název rezidenční města):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Výsledky jsou:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Tady je dotaz, který přeskočí první hodnotu a vrátí hodnotu druhého (bez řazení):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Výsledky jsou:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```




## <a name="scalar-expressions"></a>Skalární výrazy

Klauzule SELECT podporuje skalární výrazy jako konstanty aritmetických výrazů a logických výrazů. Následující dotaz používá skalární výraz, který:


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

## <a name="object-and-array-creation"></a>Vytvoření objektu a pole

Klíčovou funkcí služby rozhraní SQL API je vytvoření pole a objektu. Předchozí příklad vytvoří nový objekt JSON, `AreFromSameCityState`. Můžete také vytvořit pole, jak je znázorněno v následujícím příkladu:


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Výsledky jsou:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

Následující dotaz SQL je další příklad použití pole v rámci v poddotazy. Tento dotaz získá všechny různé křestní jména všech dětí v poli.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```


## <a id="Iteration"></a>Iterace

Rozhraní SQL API poskytuje podporu pro iterace přes pole JSON, s novou konstrukci přidány prostřednictvím po klíčovém slovu IN ve zdroji FROM. V následujícím příkladu:

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
          "pets": [{ "givenName": "Fluffy"}]
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

Další dotaz provádí iterace nad `children` v `Families` kontejneru. Pole výstup se liší od předchozího dotazu. V tomto příkladu rozdělí `children`a výsledky se sloučí do jediného pole:  

```sql
    SELECT *
    FROM c IN Families.children
```

Výsledky jsou:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
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
```

Můžete filtrovat, dále na každou položku pole, jak je znázorněno v následujícím příkladu:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Výsledky jsou:

```json
    [{
      "givenName": "Lisa"
    }]
```

Můžete také agregovat přes výsledek iteraci pole. Například následující dotaz vrátí počet podřízených mezi všechny řady:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Výsledky jsou:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>Spojení

Spojení mezi tabulkami v relační databázi, jsou logické důsledkem do navrhování schémat normalizovaná. Naproti tomu rozhraní SQL API pomocí modelu Nenormalizovaná data bez schémat položek, který je logický ekvivalent *spojení sama na sebe*.

Jazyk podporuje syntaxi `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Tento dotaz vrací sadu řazených kolekcí členů s `N` hodnoty. Všechny řazené kolekce členů obsahuje hodnoty vytvořené ve všechny aliasy kontejneru iterování celého jejich příslušných sad. Jinými slovy tento dotaz provede úplnou smíšený produkt sad účastní spojení.

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

## <a id="UserDefinedFunctions"></a>Uživatelem definované funkce (UDF)

Rozhraní SQL API poskytuje podporu pro uživatelem definované funkce (UDF). Skalární funkce UDF můžete předat argumenty nula nebo více a vrácení výsledku jeden argument. Rozhraní API zkontroluje každý argument se platné hodnoty JSON.  

Rozhraní API rozšiřuje syntaxe jazyka SQL pro podporu vlastní logiky aplikace pomocí funkcí UDF. Může vytvářet UDF s rozhraním SQL API a referenční v dotazech SQL. Ve skutečnosti UDF jsou exquisitely navržené k volání z dotazů. Jako důsledkem funkcí UDF nebudou mít přístup k objektu kontextu stejně jako jiné typy jazyka JavaScript, jako jsou uložené procedury a triggery. Dotazy jsou jen pro čtení a lze spustit buď na primární nebo sekundární repliky. Na rozdíl od jiných typů JavaScript, funkcí UDF jsou navrženy ke spouštění na sekundárních replikách.

Následující příklad registruje UDF pod kontejner položek v databázi Cosmos DB. V příkladu se vytvoří UDF, jehož název je `REGEX_MATCH`. Přijímá dva řetězcové hodnoty JSON `input` a `pattern`, a zkontroluje, jestli první odpovídá vzoru zadané v druhý pomocí jazyka JavaScript `string.match()` funkce.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Teď pomocí tohoto systému souborů UDF v projekci dotazu. Malá a velká písmena předponou musí kvalifikovat UDF `udf.` při volání z v rámci dotazů.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Výsledky jsou:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Můžete použít UDF kvalifikován s `udf.` předpona rámci filtru, stejně jako v následujícím příkladu:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Výsledky jsou:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

V podstatě UDF jsou platná skalární výrazy, které můžete použít v projekce a filtry.

Rozšíření power uživatelem definovanými funkcemi, podívejte se na další příklad s podmíněnou logiku:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

V následujícím příkladu vykonává UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Výsledky jsou:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Pokud uvedené vlastnosti podle UDF nejsou k dispozici v hodnotě JSON, parametry, se považuje za parametr undefined a vyvolání UDF se přeskočí. Podobně pokud výsledek UDF není definován, není součástí výsledku.

Jako v předchozích příkladech, integrace UDF sílu jazyka JavaScript pomocí rozhraní SQL API. UDF poskytuje také bohaté rozhraní příkazů programovatelný provádět komplexní procesní, podmíněné logiky pomocí integrované funkce modulu runtime jazyka JavaScript. Rozhraní SQL API poskytuje argumenty UDF pro každou položku zdroje v aktuální WHERE nebo klauzuli SELECT fáze zpracování. Výsledek je bezproblémově součástí celkového spuštění kanálu. Stručně řečeno funkce UDF představují vynikající nástroj provádět komplexní obchodní logiku jako součást dotazy.

## <a id="Aggregates"></a>Agregační funkce

Agregační funkce provádí výpočet na sadu hodnot v klauzuli SELECT a vrátí jednu hodnotu. Například následující dotaz vrátí počet položek v rámci `Families` kontejneru:

```sql
    SELECT COUNT(1)
    FROM Families f
```

Výsledky jsou:

```json
    [{
        "$1": 2
    }]
```

Můžete také vrátit pouze skalární hodnotu agregace pomocí VALUE – klíčové slovo. Například následující dotaz vrátí počet hodnot jako jedno číslo:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Výsledky jsou:

```json
    [ 2 ]
```

Můžete také kombinovat agregace s filtry. Například následující dotaz vrátí počet položek se stavem adresu `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Výsledky jsou:

```json
    [ 1 ]
```

Rozhraní SQL API podporuje následující agregační funkce. SOUČET a průměr pracovat s číselnými hodnotami a COUNT, MIN a MAX pracovat na čísla, řetězce, logické hodnoty a hodnoty Null.

| Funkce | Popis |
|-------|-------------|
| POČET | Vrátí počet položek ve výrazu. |
| SUM   | Vrátí součet všech hodnot ve výrazu. |
| MIN   | Vrátí minimální hodnotu ve výrazu. |
| MAX   | Vrátí maximální hodnotu ve výrazu. |
| PRŮMĚR   | Vrátí průměr hodnot ve výrazu. |

Můžete také agregovat přes výsledky iterace pole. Další informace najdete v tématu [iterace](#Iteration) oddílu.

> [!NOTE]
> V Průzkumníku dat na portálu Azure může agregovat agregace dotazy částečných výsledků přes stránku pouze jeden dotaz. Sada SDK vytváří jednu kumulativní hodnotu na všech stránkách. K provádění dotazů agregace pomocí kódu, je nutné, sady .NET SDK 1.12.0, sady SDK .NET Core 1.1.0 nebo sady Java SDK 1.9.5 nebo vyšší.
>

## <a id="BuiltinFunctions"></a>Předdefinované funkce

Cosmos DB podporuje také řadu integrovaných funkcí pro běžné operace, které můžete použít uvnitř dotazů jako uživatelem definované funkce (UDF).

| Skupina – funkce | Operace |
|---------|----------|
| Matematické funkce | ABS, HORNÍ MEZ, EXP, FLOOR, PROTOKOLU, LOG10, POWER, KRUHOVÉ, PŘIHLÁŠENÍ, SQRT, ČTVEREC, TRUNC –, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN TAN |
| Kontrola typu funkce | IS_ARRAY – IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT –, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funkce řetězců | CONCAT, OBSAHUJE, ENDSWITH, INDEX_OF, VLEVO, DÉLKA, DOLNÍ, LTRIM, NAHRAĎTE, REPLIKACI, OBRÁTIT, VPRAVO, RTRIM, STARTSWITH, PODŘETĚZEC, HORNÍ |
| Funkce pole | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH a ARRAY_SLICE |
| Prostorové funkce | ST_DISTANCE ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Pokud aktuálně používáte uživatelem definované funkce (UDF) pro kterou předdefinované funkce je teď k dispozici, bude odpovídající integrované funkce pro spuštění rychlejší a efektivnější.

Hlavní rozdíl mezi funkcí ANSI SQL a Cosmos DB je, že funkce Cosmos DB jsou navrženy pro práci i s daty bez schématu a smíšené schématu. Například, pokud vlastnost chybí nebo má jiné než číselné hodnoty, jako jsou `unknown`, položky se přeskočila místo vrácení chyby.

### <a name="mathematical-functions"></a>Matematické funkce

Matematické funkce provádí výpočet, založené na vstupní hodnoty, které jsou k dispozici jako argumenty a vrátit číselnou hodnotu. Tady je tabulka podporovaných předdefinovaných matematických funkcí.

| Využití | Popis |
|----------|--------|
| ABS (num_expr) | Vrátí absolutní hodnotu (pozitivní) zadaný číselný výraz. |
| Horní MEZ (num_expr) | Vrátí nejmenší hodnotu celé číslo větší než nebo rovna zadané číselný výraz. |
| Dolní MEZ (num_expr) | Vrátí největší celé číslo menší nebo rovna zadané číselný výraz. |
| EXP (num_expr) | Vrátí exponent zadaný číselný výraz. |
| PROTOKOL (num_expr, base) | Vrátí přirozený logaritmus zadaného číselný výraz nebo pomocí určený základ logaritmu. |
| LOG10 (num_expr) | Vrátí hodnotu logaritmu základu 10 zadaný číselný výraz. |
| ROUND (num_expr) | Vrátí číselnou hodnotu, zaokrouhlený na nejbližší celočíselnou hodnotu. |
| TRUNC (num_expr) | Vrátí číselnou hodnotu, zkrácen na nejbližší celočíselnou hodnotu. |
| SQRT (num_expr) | Vrátí druhou odmocninu určeného číselného výrazu. |
| HRANATÉ (num_expr) | Vrátí druhou mocninu zadaný číselný výraz. |
| NAPÁJENÍ (num_expr, num_expr) | Vrátí sílu zadaný číselný výraz na zadanou hodnotu. |
| SIGN (num_expr) | Vrátí hodnotu znaménko (-1, 0, 1) zadaný číselný výraz. |
| ACOS (num_expr) | Vrací úhel v radiánech, jehož kosinus je zadaný číselný výraz. Zkratka Arkus kosinus. |
| ASIN (num_expr) | Vrací úhel v radiánech, jehož sinus je zadaný číselný výraz. Arkus sinus je také voláním této funkce. |
| ATAN (num_expr) | Vrací úhel v radiánech, jehož tangens odpovídá zadané číselný výraz. Arkus tangens je také voláním této funkce. |
| ATN2 (num_expr) | Vrací úhel v radiánech mezi kladnou osy x a ray ze zdroje do bodu (y, x), kde x a y jsou hodnoty ze dvou výrazů zadaného typu float. |
| COS (num_expr) | Vrátí trigonometrických kosinus úhlu určeného v radiánech v zadaným výrazem. |
| COT (num_expr) | Vrátí trigonometrických kotangens úhlu určeného v radiánech v zadané číselný výraz. |
| DEGREES (num_expr) | Vrátí odpovídající úhel ve stupních, kterým je úhel zadaný v radiánech. |
| PI () | Vrátí konstantní hodnotu čísla PÍ. |
| RADIANS (num_expr) | Radians vrátí, pokud je zadán číselný výraz ve stupních. |
| SIN (num_expr) | Vrátí trigonometrických sinus úhlu určeného v radiánech v zadaným výrazem. |
| TAN (num_expr) | Vrátí tangens vstupní výraz zadaným výrazem. |

Můžete spouštět dotazy jako v následujícím příkladu:

```sql
    SELECT VALUE ABS(-4)
```

Výsledkem je:

```json
    [4]
```

### <a name="type-checking-functions"></a>Kontrola typu funkce

Funkce kontroly typu umožňují zkontrolujte typ výrazu v rámci dotazu SQL. Kontrola typu funkce můžete použít k určení typů vlastností v rámci položky v reálném čase, když jsou proměnné nebo neznámý. Tady je tabulka funkcí podporovaných předdefinovaných kontroly typů:

| **Použití** | **Popis** |
|-----------|------------|
| [Is_array – (výraz)](sql-api-query-reference.md#bk_is_array) | Vrátí logickou hodnotu označující, zda je typ hodnoty pole. |
| [IS_BOOL (výraz)](sql-api-query-reference.md#bk_is_bool) | Vrátí logickou hodnotu označující, pokud je typ hodnoty logická hodnota. |
| [IS_NULL (výraz)](sql-api-query-reference.md#bk_is_null) | Vrátí logickou hodnotu označující, zda je typ hodnoty null. |
| [IS_NUMBER (výraz)](sql-api-query-reference.md#bk_is_number) | Vrátí logickou hodnotu označující, pokud je typ hodnoty čísla. |
| [Is_object – (výraz)](sql-api-query-reference.md#bk_is_object) | Vrátí logickou hodnotu označující, pokud je typ hodnoty objektu JSON. |
| [IS_STRING (výraz)](sql-api-query-reference.md#bk_is_string) | Vrátí logickou hodnotu označující, pokud je typ hodnoty řetězec. |
| [IS_DEFINED (výraz)](sql-api-query-reference.md#bk_is_defined) | Vrátí logickou hodnotu označující, pokud vlastnost byla přiřazena hodnota. |
| [IS_PRIMITIVE (výraz)](sql-api-query-reference.md#bk_is_primitive) | Vrátí logickou hodnotu označující, pokud je typ hodnoty řetězec, číslo, logickou hodnotu nebo hodnotu null. |

Použití těchto funkcí, můžete spouštět dotazy jako v následujícím příkladu:

```sql
    SELECT VALUE IS_NUMBER(-4)
```

Výsledkem je:

```json
    [true]
```

### <a name="string-functions"></a>Řetězcové funkce

Následující skalární funkce provádění operací na vstupní hodnotu řetězce a vrátí řetězec, číslo nebo logickou hodnotu. Tady je tabulka funkcí integrovaných řetězec:

| Využití | Popis |
| --- | --- |
| [Délka (str_expr)](sql-api-query-reference.md#bk_length) | Vrátí počet znaků ze zadaného řetězcového výrazu. |
| [CONCAT (str_expr str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcových hodnot. |
| [Dílčí řetězec (str_expr, num_expr num_expr.)](sql-api-query-reference.md#bk_substring) | Vrátí část řetězcového výrazu. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce začíná druhé. |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce končí na druhý. |
| [OBSAHUJE (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Vrátí hodnotu typu Boolean označující, zda řetězec prvního výrazu obsahuje druhý. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | Vrátí počáteční pozici prvního výskytu druhý řetězec výrazu v rámci prvního zadaného řetězcového výrazu nebo -1, pokud není nalezen řetězec. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | Vrátí levou část řetězec zadaný počet znaků. |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | Vrátí část reprezentující správný řetězec zadaný počet znaků. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | Vrátí řetězcový výraz po odebere úvodní mezery. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | Vrátí řetězcový výraz po zkracování všechny koncové mezery. |
| [MALÁ (str_expr)](sql-api-query-reference.md#bk_lower) | Vrátí řetězcový výraz po převedení dat velkým písmenem na malá písmena. |
| [VELKÁ (str_expr)](sql-api-query-reference.md#bk_upper) | Vrátí řetězcový výraz po převedení data znaků na malá písmena na velká písmena. |
| [NAHRAĎTE (str_expr, str_expr str_expr.)](sql-api-query-reference.md#bk_replace) | Nahradí všechny výskyty zadaná řetězcová hodnota s jinou hodnotou řetězce. |
| [REPLIKACE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | Opakuje hodnotu řetězce zadaného počtu opakování. |
| [REVERZNÍ (str_expr)](sql-api-query-reference.md#bk_reverse) | Vrátí hodnotu řetězce obráceném pořadí. |

Použití těchto funkcí, můžete spouštět dotazy následujícím postupem, který vrací rodině `id` na velká písmena:

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

Výsledky jsou:

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

Nebo řetězení řetězců, jako je v tomto příkladu:

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

Výsledky jsou:

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

Můžete použít také funkce řetězce v klauzuli WHERE filtrovat výsledky, jako v následujícím příkladu:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

Výsledky jsou:

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Funkce pole

Následující skalární funkce provádění operací na vstupní hodnotu pole a vrátí číselnou, logickou hodnotu nebo hodnotu pole. Tady je tabulka funkcí integrovaných pole:

| Využití | Popis |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |Vrátí počet prvků výrazu určeném poli. |
| [ARRAY_CONCAT (arr_expr arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Vrátí pole, která je výsledkem zřetězení dvou nebo více hodnot pole. |
| [ARRAY_CONTAINS (arr_expr, výraz [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Vrátí logickou hodnotu označující, zda pole obsahuje zadanou hodnotu. Můžete zadat, pokud shoda není celé nebo jeho část. |
| [ARRAY_SLICE (arr_expr num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Vrátí část reprezentující výraz pole. |

Pomocí pole funkcí pro manipulaci s poli ve formátu JSON. Například tady je dotaz, který vrátí všechny položky `id`s tam, kde jeden z `parents` je `Robin Wakefield`: 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

Výsledkem je:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Můžete zadat částečná fragment pro porovnání prvků v poli. Následující dotaz hledá všechny položky `id`, které mají `parents` s `givenName` z `Robin`:

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

Výsledkem je:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Tady je další příklad, který používá ARRAY_LENGTH zobrazíte počet `children` za řady:

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

Výsledky jsou:

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>Prostorové funkce

Cosmos DB podporuje následující předdefinované funkce Otevřít geoprostorové W3c (OGC) pro geoprostorové dotazování: 

| Využití | Popis |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | Vrací vzdálenost mezi dvěma GeoJSON `Point`, `Polygon`, nebo `LineString` výrazy. |
| T_WITHIN (point_expr, polygon_expr) | Vrátí výrazu logické hodnoty označující, zda první objektu GeoJSON (`Point`, `Polygon`, nebo `LineString`) je v rámci druhého objektu GeoJSON (`Point`, `Polygon`, nebo `LineString`). |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Vrátí hodnotu určující, zda dvě zadané objekty GeoJSON logický výraz (`Point`, `Polygon`, nebo `LineString`) intersect. |
| ST_ISVALID | Vrátí logickou hodnotu označující, zda zadaný GeoJSON `Point`, `Polygon`, nebo `LineString` výraz je neplatný. |
| ST_ISVALIDDETAILED | Vrací hodnotu JSON obsahující logickou hodnotu, pokud zadaný GeoJSON `Point`, `Polygon`, nebo `LineString` výrazu je platný a pokud není platný, důvod jako hodnotu řetězce. |

Prostorové funkce můžete použít k provádění dotazů blízkosti prostorová data. Tady je příklad dotaz, který vrátí všechny rodiny položky, které jsou v rámci 30 km zadané umístění pomocí integrované funkce ST_DISTANCE:

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

Výsledkem je:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Další informace o podpoře geoprostorových ve službě Cosmos DB najdete v tématu [práce s Geoprostorová data ve službě Azure Cosmos DB](geospatial.md). 

## <a name="parameterized-queries"></a>Parametrizované dotazy

Cosmos DB podporuje dotazy s parametry vyjádřena pomocí známé @ zápis. Parametrizované dotazy SQL poskytuje robustní zpracování a uvození vstupu uživatele a zabraňuje náhodnou expozici dat prostřednictvím útoku prostřednictvím injektáže SQL.

Můžete například napsat dotaz, který přebírá `lastName` a `address.state` jako parametry a proveďte jej pro různé hodnoty `lastName` a `address.state` na základě uživatelského zadání.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Můžete pak odeslat tuto žádost do služby Cosmos DB jako parametrický dotaz JSON podobný tomuto:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Následující příklad nastaví argument TOP s parametrizovaného dotazu: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Hodnoty parametru může být libovolný platný kód JSON: řetězce, čísla, logické hodnoty null, dokonce i pole nebo vnořené JSON. Cosmos DB je bez schématu, parametry nejsou ověřovat na libovolného typu.

## <a id="JavaScriptIntegration"></a>Integrace jazyka JavaScript

Azure Cosmos DB poskytuje programovací model pro spouštění logiky aplikace založené na jazyce JavaScript přímo v kontejnerech, pomocí uložené procedury a triggery. Tento model podporuje:

* Vysoce výkonné transakční operace CRUD a dotazů na položky v kontejneru, tím, že tato těsná integrace modulu runtime jazyka JavaScript v databázovém stroji.
* Přirozené modelování toku řízení, proměnné rozsahu a přiřazení a integrace primitiv zpracování výjimek s transakcemi databáze. 

Další informace o integraci Azure Cosmos DB JavaScript, najdete v článku [rozhraní API pro JavaScript na straně serveru](#JavaScriptServerSideApi) oddílu.

### <a name="operator-evaluation"></a>Vyhodnocení – operátor

Cosmos DB, tím, že se databáze JSON, nakreslí parallels s operátory jazyka JavaScript a sémantika hodnocení. Cosmos DB se snaží zachovat sémantiku jazyka JavaScript z hlediska podpory JSON, ale v některých případech liší operace hodnocení.

V rozhraní SQL API na rozdíl od v tradiční SQL, typy hodnot jsou často není známé dokud rozhraní API načte hodnoty z databáze. Aby bylo možné efektivní provádění dotazů, většina operátory mají požadavky na striktní typy.

Na rozdíl od jazyka JavaScript neprovede rozhraní SQL API implicitní převody. Například dotaz jako `SELECT * FROM Person p WHERE p.Age = 21` odpovídá položek, které obsahují `Age` vlastnost, jejíž hodnota je `21`. Neodpovídá žádné další položky, jejichž `Age` vlastnosti odpovídají může být nekonečné variace jako `twenty-one`, `021`, nebo `21.0`. Tím se liší od jazyka JavaScript, kde jsou hodnoty řetězců implicitně přetypován na čísla podle operátorů, například: `==`. Toto chování rozhraní SQL API je zásadní pro odpovídající efektivní indexu.

## <a id="ExecutingSqlQueries"></a>Spuštění dotazu SQL

Jakémkoli jazyce schopném zasílat požadavky HTTP/HTTPS můžete volat rozhraní API REST služby Cosmos DB. Cosmos DB dále nabízí programovací knihovny pro .NET, Node.js, JavaScript a Python programovacích jazyků. Dotazování prostřednictvím SQL podporují rozhraní API REST a knihovny, a sady .NET SDK podporuje také [dotazování LINQ na](#Linq).

Následující příklady ukazují, jak vytvořit dotaz a odeslat ji proti účtu databáze Cosmos DB.

### <a id="RestAPI"></a>ROZHRANÍ REST API

Cosmos DB nabízí otevřete programovací model RESTful přes HTTP. Modelu prostředků, který se skládá ze sady prostředků v rámci databázový účet, který ustanovení předplatnému Azure. Databázový účet se skládá ze sady *databází*, z nichž každá může obsahovat více *kontejnery*, které pak obsahovat *položky*, funkcí UDF a další typy prostředků. Každý prostředek Cosmos DB je adresovatelné logické a stabilní identifikátor URI. Je volána sadu prostředků *kanálu*. 

Pomocí příkazů HTTP je model základní interakce s těmito prostředky `GET`, `PUT`, `POST`, a `DELETE`, s jejich standardní interpretace. Použití `POST` Pokud chcete vytvořit nový prostředek, spustit uloženou proceduru nebo vydat dotaz Cosmos DB. Dotazy jsou vždy jen pro čtení operací s žádným vedlejším účinkům.

Následující příklady ukazují `POST` pro rozhraní SQL API dotazu na ukázkové položky. Dotaz musí jednoduchý filtr na JSON `name` vlastnost. `x-ms-documentdb-isquery` A Content-Type: `application/query+json` záhlaví označují, že operace je dotaz. Nahraďte `mysqlapicosmosdb.documents.azure.com:443` s identifikátorem URI pro váš účet služby Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Výsledky jsou:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

Dál se složitější dotaz vrátí více výsledků ze spojení:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

Výsledky jsou: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Pokud výsledky dotazu se nemůže vejít na jedné stránce, rozhraní REST API vrátí token pro pokračování prostřednictvím `x-ms-continuation-token` hlavičky odpovědi. Klienti můžou stránkovat výsledky včetně záhlaví v dalších výsledků. Můžete také řídit počet výsledků na stránku prostřednictvím `x-ms-max-item-count` číslo hlavičky. 

Pokud dotaz obsahuje agregační funkci, jako je počet, na stránce dotaz může vracet částečně agregovanou hodnotu pouze jednotlivé stránky výsledků. Klienti musí provést agregace druhé úrovně přes tyto výsledky poslední výsledky. Například součet přes počty vrácené v jednotlivých stránek vrátit celkový počet.

Chcete-li spravovat zásady konzistence dat pro dotazy, použijte `x-ms-consistency-level` záhlaví jako všechny požadavky rozhraní REST API. Konzistence typu relace vyžaduje také nejnovější přečtou `x-ms-session-token` hlavička cookie v dotazu žádosti. Zásady indexování dotazované kontejner může také ovlivnit konzistence výsledky dotazu. S výchozí nastavení zásad pro kontejnery indexování, index je vždy s obsahem položky aktuální a výsledky dotazu odpovídat konzistence, které jste zvolili pro data. Další informace najdete v tématu [úrovně konzistence služby Azure Cosmos DB][consistency-levels].

Pokud nakonfigurované zásady indexování v kontejneru nepodporuje zadaný dotaz, server služby Azure Cosmos DB vrátí 400 "Chybný požadavek". Tato chybová zpráva se vrací pro dotazy s cestami, které jsou explicitně vyloučené z indexování. Můžete zadat `x-ms-documentdb-query-enable-scan` záhlaví umožňující dotazu má provést kontrola indexu není k dispozici.

Můžete získat podrobné metriky spouštění dotazů nastavením `x-ms-documentdb-populatequerymetrics` záhlaví `true`. Další informace najdete v tématu [metriky dotaz SQL pro službu Azure Cosmos DB](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>C#(SADU .NET SDK)

Sady .NET SDK podporuje LINQ a SQL dotazování. Následující příklad ukazuje, jak provést předchozí dotaz filtru s .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Následující příklad porovná dvě vlastnosti rovnost v rámci každé položky a používá anonymní projekce.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Další příklad ukazuje, spojení, vyjádřené pomocí LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET client automaticky Iteruje přes všechny stránky výsledků dotazu v `foreach` blokuje, jak je znázorněno v předchozím příkladu. Počínaje možnosti dotazu [rozhraní REST API](#RestAPI) části jsou také k dispozici v sadě .NET SDK pomocí `FeedOptions` a `FeedResponse` tříd v `CreateDocumentQuery` metoda. Počet stránek, můžete řídit pomocí `MaxItemCount` nastavení.

Můžete také explicitně kontrolovat stránkování tak, že vytvoříte `IDocumentQueryable` pomocí `IQueryable` objekt, potom načtením `ResponseContinuationToken` hodnoty a jejich předávání zpátky jako `RequestContinuationToken` v `FeedOptions`. Můžete nastavit `EnableScanInQuery` povolení vyhledávání, když dotaz nepodporuje nakonfigurované zásady indexování. Pro dělené kontejnerů, můžete použít `PartitionKey` ke spuštění dotazu jeden oddíl, i když službu Azure Cosmos DB můžete automaticky extrahovat to z text dotazu. Můžete použít `EnableCrossPartitionQuery` ke spouštění dotazů na několik oddílů.

Další ukázky .NET pomocí dotazů, najdete v článku [ukázek Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet) v Githubu.

### <a id="JavaScriptServerSideApi"></a>Rozhraní API pro JavaScript na straně serveru

Cosmos DB poskytuje programovací model pro spouštění logiky aplikací JavaScript na základě přímo v kontejnerech pomocí uložené procedury a triggery. Logiky JavaScript zaregistrovaných na úrovni kontejneru pak můžou provádět databázové operace na položky daného kontejneru zabalené v okolí transakce ACID.

Následující příklad ukazuje, jak používat `queryDocuments` serveru JavaScript API posílat dotazy z vnitřního uložených procedur a aktivačních událostí:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>Technologie LINQ to SQL API

LINQ je programovací model rozhraní .NET, který vyjadřuje výpočet jako dotazy na objekt datové proudy. Cosmos DB poskytuje knihovnu na straně klienta pro rozhraní s dotazy LINQ usnadněním převod mezi objekty JSON a .NET a mapování z určité podmnožiny dotazů LINQ dotazy Cosmos DB.

Následující diagram znázorňuje architekturu podporovat LINQ dotazů pomocí služby Cosmos DB. Pomocí klienta služby Cosmos DB, můžete vytvořit `IQueryable` objekt, který přímo dotazuje zprostředkovateli dotazu Cosmos DB a překládá do dotazu Cosmos DB dotaz LINQ. Potom předejte dotaz na server služby Cosmos DB, který načte sadu výsledků ve formátu JSON. Deserializátor JSON převádí výsledky do datového proudu objekty .NET na straně klienta.

![Architektura podporuje dotazů LINQ pomocí rozhraní SQL API - syntaxi SQL, JSON dotazovací jazyk, databázových koncepcí a dotazy SQL][1]

### <a name="net-and-json-mapping"></a>.NET a mapování JSON

Mapování mezi objekty .NET a položkami JSON je přirozený. Každé datové pole člena se mapuje na objekt JSON, ve kterém mapuje na název pole *klíč* součástí objektu a hodnota rekurzivně mapuje *hodnotu* součástí objektu. Následující map kódu `Family` třídy s položkou formátu JSON a pak vytvoří `Family` objektu:

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

Předchozí příklad vytváří následující položky JSON:

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female",
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller",
              "givenName": "Lisa",
              "gender": "female",
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```

### <a name="linq-to-sql-translation"></a>Technologie LINQ to SQL překladu

Cosmos DB poskytovatele dotazů provede nejlepší úsilí mapování z dotazu LINQ do dotazu Cosmos DB SQL. Následující popis předpokládá základní znalost LINQ.

Systém typů poskytovatele dotazů podporuje pouze primitivní typy JSON: číselná nebo logická hodnota, řetězec a s hodnotou null. 

Poskytovatele dotazů podporuje následující skalární výrazy:

- Konstantní hodnoty, včetně konstantní hodnoty primitivních datových typů v době vyhodnocení dotazu.
  
- Vlastnost nebo pole indexové výrazy, které odkazují na vlastnosti objektu nebo k elementu pole. Příklad:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetických výrazů, včetně aritmetických výrazů běžné u číselných a logických hodnot. Úplný seznam najdete v článku [SQL služby Azure Cosmos DB specifikace](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Výrazy porovnání řetězce, mezi které patří porovnání řetězcovou hodnotu na hodnotu konstanty typu řetězec.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Objekt nebo pole vytváření výrazů, které vracejí objekt složené hodnotový typ nebo anonymního typu nebo polem tyto objekty. Tyto hodnoty můžete vnořit.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>Podporované operátory LINQ

Poskytovatel LINQ součástí SQL SDK pro .NET podporuje následující operátory:

- **Vyberte**: Projekce přeložit a vyberte SQL, včetně konstrukce objektu.
- **Kde**: Filtry přeloží do WHERE příkazu SQL a podporovat překlad mezi `&&`, `||`, a `!` SQL operátorů
- **Operátor SelectMany**: Umožňuje uvolnění polí do klauzule SQL JOIN. Pomocí řetězení nebo vnořené výrazy k filtrování prvků pole.
- **Řadit podle** a **OrderByDescending**: Převede uzel do ORDER BY s ASC nebo DESC.
- **Počet**, **součet**, **Min**, **maximální**, a **průměrné** operátory pro agregaci a jejich ekvivalenty asynchronní **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, a **AverageAsync**.
- **CompareTo**: Přeloží na porovnání rozsahu. Běžně používá pro řetězce, protože nejsou porovnatelné v rozhraní .NET.
- **Využijte**: Přeloží nahoru SQL pro omezení výsledků z dotazu.
- **Matematické funkce**: Podporuje překlad z .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, a `Truncate` na ekvivalentní funkce integrované v SQL.
- **Řetězec funkce**: Podporuje překlad z .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, a `TrimStart` na ekvivalentní funkce integrované v SQL.
- **Array – funkce**: Podporuje překlad z .NET `Concat`, `Contains`, a `Count` na ekvivalentní funkce integrované v SQL.
- **Rozšíření geoprostorové funkce**: Podporuje překlad z metody zástupných procedur `Distance`, `IsValid`, `IsValidDetailed`, a `Within` na ekvivalentní funkce integrované v SQL.
- **Uživatelem definované funkce rozšíření funkce**: Podporuje překlad z zástupná metoda `UserDefinedFunctionProvider.Invoke` odpovídající uživatelem definované funkce.
- **Různé**: Podporuje překlad `Coalesce` a podmíněných operátorů. Jsou dobře převeditelné `Contains` řetězec obsahuje, ARRAY_CONTAINS nebo v SQL, v závislosti na kontextu.

### <a name="sql-query-operators"></a>Operátory dotazů SQL

Následující příklady znázorňují, jak některé standardní operátory dotazu LINQ přeložit do služby Cosmos DB dotazů.

#### <a name="select-operator"></a>Vyberte operátor

Syntaxe je `input.Select(x => f(x))`, kde `f` je skalární výraz.

**Vyberte operátor, příklad 1:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Vyberte operátor, příklad 2:** 

- **Lambda výraz LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Vyberte operátor, příklad 3:**

- **Lambda výraz LINQ**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

#### <a name="selectmany-operator"></a>Operátor SelectMany – operátor

Syntaxe je `input.SelectMany(x => f(x))`, kde `f` je skalární výraz, který vrátí typ kontejneru.

- **Lambda výraz LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

#### <a name="where-operator"></a>Kde – operátor

Syntaxe je `input.Where(x => f(x))`, kde `f` je skalární výraz, který vrací logickou hodnotu.

**Pokud operátor, příklad 1:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Pokud operátor, příklad 2:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

### <a name="composite-sql-queries"></a>Složený dotazy SQL

Můžete vytvořit předchozí operátorům tvoří výkonnějších dotazů. Protože Cosmos DB podporuje vnořené kontejnery, lze zřetězit nebo vnořené složení.

#### <a name="concatenation"></a>Zřetězení

Syntaxe je `input(.|.SelectMany())(.Select()|.Where())*`. Zřetězených dotazů můžete začít s volitelným `SelectMany` dotazu, za nímž následuje více `Select` nebo `Where` operátory.

**Zřetězení, příklad 1:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Zřetězení, příklad 2:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Zřetězení, příklad 3:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Zřetězení, příklad 4:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

#### <a name="nesting"></a>Vnoření

Syntaxe je `input.SelectMany(x=>x.Q())` kde `Q` je `Select`, `SelectMany`, nebo `Where` operátor.

Vnořený dotaz použije vnitřní dotaz na každý prvek vnější kontejneru. Jednu důležitou funkci je, že vnitřní dotaz mohou odkazovat na pole prvků v vnější kontejneru, jako jsou spojení sama.

**Vnoření, příklad 1:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Vnoření, příklad 2:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Vnoření, příklad 3:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a id="References"></a>Odkazy

- [Azure Cosmos DB SQL specifikace](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [Specifikace jazyka JavaScript](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz. [Dotazování techniky hodnocení za u velkých databází](https://dl.acm.org/citation.cfm?id=152611). *ACM computingu zjišťování* 25, žádné. 2 (1993).
- Graefe, např. "Cascades architektura pro optimalizaci dotazu." *Eng. IEEE dat Bull.* 18, žádné. 3 (1995).
- Logická jednotka, Ooi, Tan. "Zpracování dotazů v paralelní relačními databázovými systémy." *Stisknutím klávesy společnosti IEEE počítače* (1994).
- Olston Christopher, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar a Andrew Tomkins. "Pig Latin: Není tak cizího jazyka pro zpracování dat." *SIGMOD* (2008).

## <a name="next-steps"></a>Další postup

- [Úvod do služby Azure Cosmos DB][introduction]
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB úrovně konzistence][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
