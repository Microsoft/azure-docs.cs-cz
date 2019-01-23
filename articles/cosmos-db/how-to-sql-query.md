---
title: Dotazy SQL pro službu Azure Cosmos DB
description: Další informace o syntaxi jazyka SQL, databázových koncepcí a dotazů SQL pro službu Azure Cosmos DB. SQL můžete použít jako dotazovací jazyk typu JSON ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/15/2018
ms.author: mjbrown
ms.openlocfilehash: d101931fb2a4184ff9ffffc0aed4fc90fee2cbd5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452951"
---
# <a name="sql-query-examples-to-query-data-from-azure-cosmos-db"></a>Příklady dotazů SQL k dotazování dat ze služby Azure Cosmos DB

Azure Cosmos DB podporuje dotazování položek pomocí SQL (Structured Query Language) jako dotazovací jazyk typu JSON na účty rozhraní SQL API. Při navrhování dotazovací jazyk pro službu Azure Cosmos DB, jsou považovány za tyto dva cíle:

* Místo inventing nový dotazovací jazyk, provedli jsme služby Azure Cosmos DB pro podporu SQL, jeden z nejčastěji známé a Oblíbené dotazovací jazyky. Azure Cosmos DB SQL poskytuje formální programovací model pro bohaté dotazy nad položkami formátu JSON.  

* Azure Cosmos DB používá model programování v jazyce JavaScript jako základ pro dotazovací jazyk. Rozhraní SQL API je integrován do systému typů v jazyce JavaScript, vyhodnocení výrazu a volání funkce. Tato naopak poskytuje přirozený programovací model pro relační projekce, hierarchická navigace v rámci položky formátu JSON, spojení, prostorových dotazů a volání uživatelem definované funkce (UDF) vytvořené zcela v JavaScriptu, kromě jiných funkcí.

Tento článek vás provede Příklady dotazů SQL pomocí jednoduchého položek JSON. Další informace o syntaxi jazyka SQL služby Azure Cosmos DB najdete v tématu [referenční příručka syntaxe SQL](sql-api-query-reference.md) článku.

## <a id="GettingStarted"></a>Začínáme s příkazy jazyka SQL

Vytvoříme dvě jednoduché položky formátu JSON a dotazovat data. Vezměte v úvahu dvě položky JSON o rodiny, vložte tyto položky JSON do kontejneru a následně dotazovat data. Tady máme jednoduchý JSON položky pro rodinu a Wakefieldů rodiny, rodiče, podřízené položky (a jejich mazlíčků), adresu a informace o registraci. Položka má řetězců, čísel, logické hodnoty, pole a vnořené vlastnosti.

**Item1 –**

```JSON
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
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Tady je druhá položka s jeden malý rozdíl – `givenName` a `familyName` se používají místo `firstName` a `lastName`.

**Položka 2**

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
        "gender": "female", "grade": 1,
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

Teď si vyzkoušíme několik dotazů na tato data pochopit některé z klíčových aspektů dotazovací jazyk SQL služby Azure Cosmos DB.

**Query1**: Například následující dotaz vrátí položky, jejichž pole id odpovídá `AndersenFamily`. Protože se jedná `SELECT *`výstup dotazu je úplná položka formátu JSON, další informace o syntaxi najdete v tématu [příkaz SELECT](sql-api-query-reference.md#select-query):

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Results**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

**Dotaz2:** Nyní vezměte si situaci, kdy budeme potřebovat opakovaně formátovat výstup JSON v odlišném tvaru. Tento dotaz projekty nový objekt JSON s dvěma vybraná pole jméno a Město, když na adresu město má stejný název jako stav. V takovém případě odpovídá "NY, USA".

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

**Results**

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

**Query3**: Tento dotaz vrátí křestní jména všech dětí v rodině, jejíž id odpovídá `WakefieldFamily` seřazené podle města, kde bydlíte.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

**Results**

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

Několik aspektů dotazovací jazyk služby Cosmos DB provede příklady, které jste zatím viděli jsou následující:  

* Protože rozhraní SQL API pracuje na hodnoty JSON, zabývá strom entit místo řádků a sloupců ve tvaru. Proto jazyk umožňuje odkazovat na uzly stromu v jakékoli libovolné hloubky, jako je třeba `Node1.Node2.Node3…..Nodem`, podobně jako relační databáze SQL odkazující na odkaz na dvě části `<table>.<column>`.

* Jazyk SQL pracuje s daty bez schématu. Systém typů, třeba navázat dynamicky. Stejný výraz může přinést různé typy na různé položky. Výsledek dotazu je platnou hodnotu JSON, ale není zaručeno, že bude pevné schéma.  

* Azure Cosmos DB podporuje pouze striktní položky formátu JSON. To znamená, že do systému typů a výrazy jsou omezené na řešil pouze typy JSON. Odkazovat [JSON specifikace](http://www.json.org/) další podrobnosti.  

* Kontejneru Cosmos DB je kolekce položek JSON bez schématu. Vztahy v datových entit v různých položek v kontejneru i jsou implicitně zachycena členství ve skupině a ne primární klíč a cizí klíče relace. To je důležitý aspekt zmínku nedávným spojení uvnitř položky popsané dále v tomto článku.

## <a id="SelectClause"></a>Select – klauzule

Každý dotaz se skládá z klauzule SELECT a volitelné a klauzulí WHERE za standardy ANSI SQL. Pro každý dotaz, obvykle je vypočten zdroji v klauzuli FROM. Pak je ve zdroji pro načtení podmnožiny JSON položek použitý filtr v klauzuli WHERE. Nakonec se používá klauzuli SELECT do projektu požadované hodnoty JSON v seznamu select. Další informace o syntaxi najdete v tématu [vyberte syntaxe](sql-api-query-reference.md#bk_select_query).

Následující příklad ukazuje typické zpracování dotazu SELECT.

**Dotaz**

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }
    }]
```

### <a name="nested-properties"></a>Vnořené vlastnosti

V následujícím příkladu jsme se projekci dvě vnořené vlastnosti `f.address.state` a `f.address.city`.

**Dotaz**

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "state": "WA",
      "city": "seattle"
    }]
```

Projekce také podporuje výrazy JSON, jak je znázorněno v následujícím příkladu:

**Dotaz**

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "$1": {
        "state": "WA",
        "city": "seattle",
        "name": "AndersenFamily"
      }
    }]
```

Podívejme se na roli `$1` tady. `SELECT` Klauzule potřebuje k vytvoření objektu JSON a protože je k dispozici žádný klíč, můžeme použít implicitní argument proměnné s názvem začínajícím `$1`. Například tento dotaz vrátí dvě implicitní argument proměnné s názvem `$1` a `$2`.

**Dotaz**

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "$1": {
        "state": "WA",
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>FROM – klauzule

Z < from_specification > klauzule je nepovinný, pokud je zdroj filtrovaná nebo předpokládané později v dotazu. Další informace o syntaxi najdete v tématu [ze syntaxe](sql-api-query-reference.md#bk_from_clause). Dotaz, jako jsou `SELECT * FROM Families` znamená, že celé řady kontejneru je zdroj nad tím, které chcete získat výčet. Speciální identifikátor KOŘENOVÉ slouží k reprezentaci kontejneru místo názvu kontejneru.
Následující seznam obsahuje pravidla, která vynucují každý dotaz:

* Kontejner může být například vytvořen alias, `SELECT f.id FROM Families AS f` nebo jednoduše `SELECT f.id FROM Families f`. Tady `f` je ekvivalentem `Families`. `AS` optional – klíčové slovo na alias je identifikátor.  

* Jednou alias nemůže být vázán na původní zdroj. Například `SELECT Families.id FROM Families f` je syntakticky neplatný, protože již nelze přeložit identifikátor "Rodiny".  

* Všechny vlastnosti, které je potřeba na něj odkazovat musí být plně kvalifikovaný. Chybí splňuje striktní schéma tato velikost je vyžadována, aby všechny vazby, které nejednoznačný. Proto `SELECT id FROM Families f` je syntakticky neplatný, protože vlastnost `id` není vázán.

### <a name="get-subitems-using-from-clause"></a>Získat podřízené položky pomocí klauzule FROM

Zdroj může být také omezené na menší podmnožinu. Například k vytvoření výčtu pouze podstrom v každé položce, subroot může pak můžou stát zdroji, jak je znázorněno v následujícím příkladu:

**Dotaz**

```sql
    SELECT *
    FROM Families.children
```

**Results**

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

Při výše uvedeném příkladu používá pole jako zdroj, objekt může také sloužit jako zdroj, který je, jak je znázorněno v následujícím příkladu: Pro zařazení do výsledků dotazu se považuje za jakékoli platnou hodnotu JSON (nedefinované), který najdete ve zdroji. Pokud nemáte některé rodiny `address.state` hodnotu, jsou vyloučeny ve výsledku dotazu.

**Dotaz**

```sql
    SELECT *
    FROM Families.address.state
```

**Results**

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>Klauzule WHERE

Klauzule WHERE (**`WHERE <filter_condition>`**) je volitelný. Určuje, že-li být zahrnuty jako součást výsledku musí splňovat tyto podmínky, která JSON položky k dispozici zdroj. Všechny položky formátu JSON musí být vyhodnocen zadané podmínky na "true", která se má zohlednit výsledek. Klauzule WHERE index vrstvy používají k určení absolutní nejmenší podmnožinu zdrojové položky, které můžou být součástí výsledku. Další informace o syntaxi najdete v tématu [syntaxe WHERE](sql-api-query-reference.md#bk_where_clause).

Následující dotaz požaduje položek, které obsahují vlastnost name, jehož hodnota je `AndersenFamily`. Jakoukoli jinou položku, která nemá vlastnost name, nebo pokud hodnota se neshoduje s `AndersenFamily` je vyloučený.

**Dotaz**

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }
    }]
```

Předchozí příklad ukázal dotaz rovnosti jednoduché. Rozhraní SQL API také podporuje širokou škálu skalární výrazy. Nejčastěji používané jsou binární soubor a unární výrazy. Odkazy na vlastnosti z objektu JSON zdroje jsou také výrazy platný.

Následující binární operátory jsou aktuálně podporovány a je možné v dotazech, jak je znázorněno v následujícím příkladu:  

|**Typ operátoru**  | **Hodnoty** |
|---------|---------|
|Aritmetické operace | +,-,*,/,% |
|bitové operace    | \|, &, ^, <<>>,, >>> (výplně nulové posunutí doprava) |
|Logické    | A, NEBO NE      |
|Porovnání | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Řetězec     |  \|\| (zřetězení) |

Pojďme se podívat na některé dotazy pomocí binární operátory.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5
```

Unární operátory +,-, ~ a ne jsou také podporovány a může být použit uvnitř dotazů, jak je znázorněno v následujícím příkladu:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Kromě binární soubor a unární operátory jsou také povoleny odkazy na vlastnosti. Například `SELECT * FROM Families f WHERE f.isRegistered` vrátí položku JSON obsahující vlastnost `isRegistered` kde hodnota této vlastnosti je rovna hodnotě JSON `true` hodnotu. Všechny ostatní hodnoty (false, null, nedefinovaný, `<number>`, `<string>`, `<object>`, `<array>`atd) vede k vyloučení z výsledku Zdrojová položka. 

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

Pro ostatní operátory porovnání, jako například >, > =,! =, <, a < =, následující pravidla platí:

* Výsledkem porovnání typů Nedefinováno.  
* Porovnání mezi dvěma objekty nebo dvě pole za následek Nedefinováno.

Pokud je výsledkem výrazu skalární ve filtru Undefined, odpovídající položka není zahrnuta do výsledku, protože není typu Undefined logicky odpovídá "true".

## <a name="between-keyword"></a>MEZI klíčové slovo
Můžete také pomocí klíčového slova BETWEEN můžete dotazy na rozsah hodnot jako ANSI SQL express. MEZI dá se použít pro řetězce nebo čísla.

Například tento dotaz vrátí všechny rodiny položky, ve kterých je prvním podřízeným objektem na podnikové úrovni mezi 1-5 (obojí včetně).

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Na rozdíl od v ANSI SQL, můžete také v klauzuli BETWEEN v klauzuli FROM stejně jako v následujícím příkladu.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Hlavní rozdíl mezi použitím BETWEEN rozhraní SQL API i ANSI SQL je, že můžete vyjádřit rozsahu dotazy na vlastnosti smíšené typy – může mít například "třída" se jednat o číslo (5) v některých položek a řetězce v jiných ("grade4"). V těchto případech stejně jako v jazyce JavaScript, porovnání mezi dvěma různými typy výsledků v "undefined" a položka bude vynechána.

> [!NOTE]
> Pro rychlejší dobou provedení dotazu nezapomeňte vytvořit zásady indexování, který používá typ index rozsahu pro všechny číselné vlastnosti/cesty, které jsou filtrovány v klauzuli BETWEEN.

### <a name="logical-and-or-and-not-operators"></a>Logický (AND, OR a NOT) operátory
Logické operátory pracují s logickými hodnotami. Logické tabulky pravdivých informací pro tyto operátory jsou uvedeny v následujících tabulkách.

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

## <a name="in-keyword"></a>IN – klíčové slovo

Po klíčovém slovu IN slouží ke kontrole, zda zadaná hodnota odpovídá libovolné hodnotě v seznamu. Například tento dotaz vrátí všechny rodiny položky, kde id je jedním z "WakefieldFamily" nebo "AndersenFamily".

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

V tomto příkladu vrátí všechny položky. Pokud stav není žádný ze zadaných hodnot.

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="ternary--and-coalesce--operators"></a>Ternary (?) a operátory Coalesce (?)

Ternary a Coalesce operátory lze používat k vytváření podmíněné výrazy, podobně jako oblíbené programovací jazyky, jako je C# a JavaScript. Ternary (?) – operátor může být užitečná při vytváření nových vlastností JSON v reálném čase. Například teď můžete psát dotazy ke klasifikaci úrovně třídy v podobě čitelné člověkem jako Začátečník nebo zprostředkující/Upřesnit, jak je znázorněno níže.

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Lze také vnořit volání operátoru jako v dotazu níže.

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel
    FROM Families.children[0] c
```

Jako s dalšími operátory dotazu, pokud chybí odkazovaný vlastnosti na podmíněný výraz v libovolné položky nebo typy, který se porovnává se liší, pak tyto položky jsou vyloučeny ve výsledcích dotazu.

Coalesce (?) – operátor umožňuje efektivně vyhledávat přítomnost vlastnosti v položce. Tento operátor je užitečná při dotazování na částečně strukturovaná nebo data smíšené typy. Tento dotaz například vrátí "lastName", pokud jsou k dispozici, nebo "Příjmení", pokud není k dispozici.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="EscapingReservedKeywords"></a>Přistupující objekt vlastnosti v uvozovkách
Můžete také přistupovat k vlastnosti pomocí operátoru v uvozovkách vlastnost `[]`. Například `SELECT c.grade` a `SELECT c["grade"]` jsou ekvivalentní. Tato syntaxe je užitečné, když budete chtít řídicí vlastnost, která obsahuje mezery, speciální znaky, nebo se stane, chcete-li sdílet stejný název jako klíčové slovo SQL nebo vyhrazené slovo.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="aliasing"></a>Vyhlazení

Teď můžeme rozšířit v příkladu výše s aliasy explicitní hodnoty. Je klíčové slovo používané pro aliasy. Zadání je volitelné, jak je znázorněno při projekci druhá hodnota jako `NameInfo`.

V případě, že dotaz má dvě vlastnosti se stejným názvem, aliasy musíte použít k přejmenujte jednu nebo obě vlastnosti tak, aby jejich jsou jednoznačně rozlišit předpokládané výsledku.

**Dotaz**

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="scalar-expressions"></a>Skalární výrazy

Kromě odkazy na vlastnosti klauzuli SELECT podporuje také skalární výrazy, jako jsou konstanty, výrazy aritmetické, logické výrazy atd. Například tady je jednoduchý dotaz "Hello World".

**Dotaz**

```sql
    SELECT "Hello World"
```

**Results**

```json
    [{
      "$1": "Hello World"
    }]
```

Zde je složitější příklad, který používá skalární výraz.

**Dotaz**

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

**Results**

```json
    [{
      "$1": 1.33333
    }]
```

V následujícím příkladu výsledkem skalární výraz, který je logická hodnota.

**Dotaz**

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

**Results**

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

Další klíčovou funkcí rozhraní SQL API je vytvoření pole nebo objektu. V předchozím příkladu jste vytvořili nový objekt JSON. Podobně jedna můžete také sestavit pole jak je znázorněno v následujícím příkladu:

**Dotaz**

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

**Results**

```json
    [
      {
        "CityState": [
          "seattle",
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

## <a id="ValueKeyword"></a>VALUE – klíčové slovo

**Hodnotu** – klíčové slovo poskytuje způsob, jak vrátit hodnotu JSON. Například níže dotaz vrátí skalárních `"Hello World"` místo `{$1: "Hello World"}`.

**Dotaz**

```sql
    SELECT VALUE "Hello World"
```

**Results**

```json
    [
      "Hello World"
    ]
```

Následující dotaz vrátí hodnotu JSON bez `"address"` popisek ve výsledcích.

**Dotaz**

```sql
    SELECT VALUE f.address
    FROM Families f
```

**Results**

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

Následující příklad rozšiřuje ukazují, jak vrátit primitivní hodnoty JSON (listový úroveň stromu JSON).

**Dotaz**

```sql
    SELECT VALUE f.address.state
    FROM Families f
```

**Results**

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="-operator"></a>* – Operátor
Speciální operátor (*) je podporován pro projektové položky jako-je. Při použití, musí být pouze očekávané pole. Zatímco dotaz podobný tomuto: `SELECT * FROM Families f` je platný, `SELECT VALUE * FROM Families f` a `SELECT *, f.id FROM Families f` nejsou platné.

**Dotaz**

```sql
    SELECT * 
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Results**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="TopKeyword"></a>Operátor TOP

HORNÍ – klíčové slovo je možné omezit počet hodnot z dotazu. Při horní se používá ve spojení s klauzulí ORDER BY, není omezen na první číslo N hodnot seřazených; sada výsledků dotazu v opačném případě vrátí prvních N počet výsledků v nedefinované pořadí. Jako osvědčený postup v příkazu SELECT, vždy pomocí klauzule ORDER BY klauzuli TOP. TCombining těchto dvou klauzulí je jediný způsob, jak předvídatelným způsobem označit řádky, které jsou ovlivněny nahoru. 

**Dotaz**

```sql
    SELECT TOP 1 *
    FROM Families f
```

**Results**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

NAHORU je možné s konstantní hodnotou (jak jsme ukázali výše) nebo s hodnotou proměnné použití parametrizovaných dotazů. Další podrobnosti najdete v tématu parametrizované dotazy níže.

## <a id="Aggregates"></a>Agregační funkce

Můžete také provádět agregace v `SELECT` klauzuli. Agregační funkce provádí výpočet na sadu hodnot a vrátí jednu hodnotu. Například následující dotaz vrátí počet řady položek v rámci kontejneru.

**Dotaz**

```sql
    SELECT COUNT(1)
    FROM Families f
```

**Results**

```json
    [{
        "$1": 2
    }]
```

Můžete také vrátit skalární hodnotu agregace pomocí `VALUE` – klíčové slovo. Například následující dotaz vrátí počet hodnot jako jedno číslo:

**Dotaz**

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

**Results**

```json
    [ 2 ]
```

Můžete také provést agregace v kombinaci s filtry. Například následující dotaz vrátí počet položek s adresou ve státě Washington.

**Dotaz**

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

**Results**

```json
    [ 1 ]
```

Následující tabulka uvádí seznam podporovaných agregačních funkcí v rozhraní SQL API. `SUM` a `AVG` se provádí přes číselných hodnot, zatímco `COUNT`, `MIN`, a `MAX` lze provést přes čísla, řetězce, logické hodnoty a hodnoty Null.

| Využití | Popis |
|-------|-------------|
| POČET | Vrátí počet položek ve výrazu. |
| SUM   | Vrátí součet všech hodnot ve výrazu. |
| MIN   | Vrátí minimální hodnotu ve výrazu. |
| MAX   | Vrátí maximální hodnotu ve výrazu. |
| PRŮMĚR   | Vrátí průměr hodnot ve výrazu. |

Agregace lze také provést přes výsledky iterace pole. Další informace najdete v tématu [pole iterace v dotazech](#Iteration).

> [!NOTE]
> Při použití Průzkumníku dat na webu Azure portal, mějte na paměti, že agregace dotazů může vracet částečně agregované výsledky na stránce dotazů. Sady SDK vytvořit jednu kumulativní hodnotu na všech stránkách.
>
> Aby bylo možné provádět dotazy agregace pomocí kódu, je nutné, sady .NET SDK 1.12.0, sady SDK .NET Core 1.1.0 nebo sady Java SDK 1.9.5 nebo vyšší.
>

## <a id="OrderByClause"></a>ORDER BY – klauzule

Stejně jako v ANSI SQL, můžete zahrnout volitelné klauzuli Order By při dotazování. V klauzuli může obsahovat nepovinný argument ASC/DESC pro určení pořadí, ve kterém musí načíst výsledky.

Tady je příklad dotaz, který načte skupin v pořadí podle názvu rezidenční město.

**Dotaz**

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

**Results**

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

A tady je dotaz, který načte skupin v pořadí datum vytvoření, který je uložený jako číslo představující epochy čas, tj, uplynulý čas od 1 ledna 1970 v řádu sekund.

**Dotaz**

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

**Results**

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

## <a id="Advanced"></a>Pokročilé databázových koncepcí a dotazy SQL

### <a id="Iteration"></a>Iterace

Novou konstrukci bylo přidáno prostřednictvím operace **v** – klíčové slovo v rozhraní SQL API poskytuje podporu pro iterace přes pole JSON. Zdroj FROM poskytuje podporu pro iteraci. Začneme v následujícím příkladu:

**Dotaz**

```sql
    SELECT *
    FROM Families.children
```

**Results**

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

Nyní Pojďme se podívat na jiný dotaz, který provádí iteraci podřízené prvky v kontejneru. Všimněte si rozdílů v poli výstup. V tomto příkladu rozdělí `children` a výsledky se sloučí do jediného pole.  

**Dotaz**

```sql
    SELECT *
    FROM c IN Families.children
```

**Results**

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

To dále slouží k filtrování na každou položku pole, jak je znázorněno v následujícím příkladu:

**Dotaz**

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

**Results**

```json
    [{
      "givenName": "Lisa"
    }]
```

Můžete také provést agregaci přes výsledek pole iterace. Například následující dotaz vrátí počet podřízených mezi všechny rodiny.

**Dotaz**

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

**Results**

```json
    [
      {
        "$1": 3
      }
    ]
```

### <a id="Joins"></a>Spojení

Nutnost připojení u tabulek v relační databázi, je důležité. Je logický důsledkem do navrhování schémat normalizovaná. Naproti tomu rozhraní SQL API pracuje s modelem Nenormalizovaná data bez schémat položek, což je logický ekvivalent a "spojení sama na sebe".

Syntaxe, která podporuje jazyk je `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Celkově tento dotaz vrátí sadu **N**- řazených kolekcí členů (řazené kolekce členů s **N** hodnoty). Všechny řazené kolekce členů obsahuje hodnoty vytvořené ve všechny aliasy kontejneru iterování celého jejich příslušných sad. Jinými slovy tento dotaz provede úplnou smíšený produkt sad účastní spojení.

Následující příklady ukazují, jak funguje klauzule JOIN. V následujícím příkladu výsledkem je prázdný od smíšený produkt každou položku ze zdroje a Prázdná množina je prázdný.

**Dotaz**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

**Results**

```json
    [{
    }]
```

V následujícím příkladu je spojení mezi kořenové položky a `children` subroot. Je smíšený produkt mezi dvěma objekty JSON. Skutečnost, že je podřízené prvky pole není platné ve spojení protože jsme pracovali s jeden kořenový, který je podřízené prvky pole. Proto výsledek obsahuje pouze dva výsledky, protože právě pouze jednu položku vrací smíšený produkt každou položku pole.

**Dotaz**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

**Results**

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

**Dotaz**

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

**Results**

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

První věc, kterou si je, že `from_source` z **připojení** klauzule je iterátor. Ano tok v tomto případě je následujícím způsobem:  

* Rozbalte každý podřízený prvek **c** v poli.
* Použít smíšený produkt s kořenem položky **f** s každou podřízeným elementem **c** , který byl zjednodušen v prvním kroku.
* Nakonec projektu kořenový objekt **f** name – vlastnost samostatně.

První položka (`AndersenFamily`) obsahuje pouze jeden podřízený element, tak sada výsledků obsahuje pouze jeden objekt odpovídající této položky. Druhá položka (`WakefieldFamily`) obsahuje dva podřízené položky. Ano smíšený produkt vytváří samostatný objekt pro každý podřízený prvek, což by vedlo k dva objekty, jeden pro každý podřízený prvek odpovídající této položky. Kořenové pole v obou těchto položek jsou stejné, stejně jako byste očekávali v smíšený produkt.

Nástroj skutečné spojení je formulář řazených kolekcí členů z mezi produkty ve tvaru, který je v opačném případě obtížně projektu. Kromě toho, jak uvidíme v následujícím příkladu můžete vyfiltrovat na kombinaci řazené kolekce členů, že umožňuje uživatel vybral podmínku celkové splněno řazené kolekce členů.

**Dotaz**

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

**Results**

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

Tento příklad v předchozím příkladu představuje přirozené rozšíření a provádí double spojení. Smíšený produkt tak, lze zobrazit jako pseudoelement následovně:

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

`AndersenFamily` má jeden podřízený prvek, který má jednoho nebo více mazlíčků. Ano, vrací smíšený produkt jeden řádek (1\*1\*1) z této řady. WakefieldFamily má ale dvěma dětmi, ale pouze jeden podřízený prvek "Jesse" má mazlíčků. Jesse i když má dvě mazlíčků. Proto vrací smíšený produkt 1\*1\*2 = 2 řádky z této řady.

V následujícím příkladu je další filtr na `pet`, která nezahrnuje všechny záznamy, kde není "Stínu" jméno mazlíčka. Všimněte si, že jsme schopní řazenými kolekcemi členů z polí, filtr na některý z prvků řazené kolekce členů, ale taky popustit libovolnou kombinaci prvků projektu.

**Dotaz**

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

**Results**

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="JavaScriptIntegration"></a>Integrace jazyka JavaScript

Azure Cosmos DB poskytuje programovací model pro spouštění logiky aplikací JavaScript na základě přímo na kontejnerech z hlediska uložené procedury a triggery, tato metoda umožňuje podporuje:

* Možnost pro vysoce výkonné transakční operace CRUD a dotazů na položky v kontejneru tím, že tato těsná integrace modulu runtime jazyka JavaScript přímo uvnitř databázového stroje.
* Přirozené modelování toku řízení, proměnné rozsahu a přiřazení a integrace primitivních elementů s transakcemi databáze zpracování výjimek. Podrobné informace o podpoře služby Azure Cosmos DB pro integrace v jazyce JavaScript naleznete v dokumentaci JavaScript programování na straně serveru.

### <a id="UserDefinedFunctions"></a>Uživatelem definované funkce (UDF)

Spolu s typy již definované v tomto článku rozhraní SQL API poskytuje podporu pro uživateli definované funkce (UDF). Skalární funkce UDF jsou podporovány zejména, kde mohou vývojáři předat argumenty nula nebo více a vrácení zpět výsledku jeden argument. Každá z těchto argumentů se kontroluje u právě platné hodnoty JSON.  

Syntaxe jazyka SQL je rozšířit o podporu vlastní logiky aplikace pomocí těchto uživatelsky definovaných funkcí. Uživatelem definovanými funkcemi lze registrovat pomocí rozhraní SQL API a klikněte na něho odkazovat jako součást příkazu jazyka SQL. Ve skutečnosti UDF jsou exquisitely navržené k volání z dotazů. Jako důsledkem této volby funkcí UDF nebudou mít přístup k objektu context, které mají jiné typy jazyka JavaScript (uložených procedur a aktivačních událostí). Protože provádějí se dotazy jen pro čtení, můžou běžet na primárním nebo na sekundárních replikách. Proto se funkce UDF jsou navrženy ke spouštění na sekundárních replikách na rozdíl od jiných typů jazyka JavaScript.

Níže je příklad, jak lze registrovat systému souborů UDF v databázi Cosmos DB, konkrétně v rámci kontejner položek.

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

V předchozím příkladu se vytvoří UDF, jehož název je `REGEX_MATCH`. Přijímá dva řetězcové hodnoty JSON `input` a `pattern` a zkontroluje, jestli první odpovídá vzoru zadané v druhý použitím string.match() funkce v jazyce JavaScript.

Tento systém souborů UDF jsme teď můžete použít v dotazu v projekci. UDF musí být kvalifikován s předponu malá a velká písmena "udf." Při volání z v rámci dotazů.

> [!NOTE]
> Před 3/17/2015 Cosmos DB nepodporuje volání UDF bez "udf." Předpona, jako jsou REGEX_MATCH() vyberte. Tento model volání je zastaralá.  
>

**Dotaz**

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

**Results**

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

UDF můžete použít také uvnitř filtr, jak je znázorněno v následujícím příkladu také kvalifikován s "udf." Předpona:

**Dotaz**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

**Results**

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

V podstatě UDF jsou platná skalární výrazy a je možné v projekce a filtry.

Rozšíření power uživatelem definovanými funkcemi, Podívejme se na další příklad s podmíněnou logiku:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
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

Tady je příklad, která zpracovává UDF.

**Dotaz**

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

**Results**

```json
     [
      {
        "city": "seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Jako v předchozích příkladech prezentovat, integrace UDF sílu jazyka JavaScript pomocí rozhraní SQL API poskytuje také bohaté rozhraní příkazů programovatelný provádět komplexní procesní, podmíněné logiky pomocí integrovaných možností modulu runtime jazyka JavaScript.

Rozhraní SQL API poskytuje argumenty UDF pro každou položku ve zdroji v aktuální fázi (klauzuli WHERE nebo klauzuli SELECT) zpracování UDF. Výsledkem je součástí celkového kanálu provádění bez problémů. Pokud uvedené vlastnosti podle UDF nejsou dostupné v hodnotě JSON, se považuje za parametr undefined a proto je při vyvolání UDF zcela přeskočen. Podobně pokud výsledek UDF není definován, není součástí výsledku.

Stručně řečeno funkce UDF představují vynikající nástroj provádět komplexní obchodní logiku jako součást dotazu.

### <a name="operator-evaluation"></a>Vyhodnocení – operátor

Cosmos DB, důsledku toho, že databáze JSON, nakreslí parallels s operátory jazyka JavaScript a jeho vyhodnocení sémantiku. Zatímco Cosmos DB se snaží zachovat sémantiku jazyka JavaScript z hlediska podpory JSON, v některých případech liší operace hodnocení.

V rozhraní SQL API na rozdíl od v tradiční SQL, typy hodnot jsou často není známé dokud hodnoty se načítají z databáze. Aby bylo možné efektivní provádění dotazů, většina operátory mají požadavky na striktní typy.

Rozhraní SQL API neprovádí implicitních převodů, na rozdíl od jazyka JavaScript. Například dotaz jako `SELECT * FROM Person p WHERE p.Age = 21` odpovídá položek, které obsahují některou vlastnost stáří, jehož hodnota je 21. Další položky, jejichž stáří vlastnosti odpovídají může být nekonečné varianty řetězců "21" nebo jiné, například "021", "21.0", "0021", "00021", nebude odpovídat atd. Toto je oproti jazyka JavaScript, kde jsou implicitně převedena na čísla řetězcové hodnoty (podle operátoru: ==). Tato volba je zásadní pro efektivní indexu odpovídající v rozhraní SQL API.

## <a name="parameterized-sql-queries"></a>Parametrizované dotazy SQL

Cosmos DB podporuje dotazy s parametry vyjádřit pomocí známé \@ zápis. Parametrizovaný dotaz SQL poskytuje robustní zpracování a uvozovací znaky z uživatelský vstup, brání náhodnou expozici dat prostřednictvím útoku prostřednictvím injektáže SQL.

Můžete například napsat dotaz, který přijímá jako parametry příjmení a stav adresy a proveďte jej pro různé hodnoty poslední název a stav adresy na základě uživatelského zadání.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Tento požadavek můžete pak odešlou do služby Cosmos DB jako parametrický dotaz JSON, jako je vidíte níže.

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Argument TOP lze nastavit pomocí parametrizovaných dotazů, jako vidíte níže.

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Hodnoty parametru může být libovolný platný kód JSON (řetězce, čísla, logické hodnoty null, dokonce i pole nebo vnořené JSON). Také Cosmos DB je bez schématu, parametry nejsou ověřovat na libovolného typu.

## <a id="BuiltinFunctions"></a>Předdefinované funkce

Cosmos DB podporuje také řadu integrovaných funkcí pro běžné operace, které lze použít uvnitř dotazů jako uživatelem definované funkce (UDF).

| Skupina – funkce | Operace |
|---------|----------|
| Matematické funkce | ABS, HORNÍ MEZ, EXP, FLOOR, PROTOKOLU, LOG10, POWER, KRUHOVÉ, PŘIHLÁŠENÍ, SQRT, ČTVEREC, TRUNC –, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN TAN |
| Kontrola funkce typu | IS_ARRAY – IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT –, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funkce řetězců | CONCAT, OBSAHUJE, ENDSWITH, INDEX_OF, VLEVO, DÉLKA, DOLNÍ, LTRIM, NAHRAĎTE, REPLIKACI, OBRÁTIT, VPRAVO, RTRIM, STARTSWITH, PODŘETĚZEC, HORNÍ |
| Funkce pole | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH a ARRAY_SLICE |
| Prostorové funkce | ST_DISTANCE ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Pokud aktuálně používáte uživatelem definované funkce (UDF) pro kterou předdefinované funkce je teď k dispozici, když se rychleji na spuštění by měl použít odpovídající předdefinované funkce a efektivněji.

### <a name="mathematical-functions"></a>Matematické funkce

Matematické funkce provádí výpočet, založené na vstupní hodnoty, které jsou k dispozici jako argumenty a vrátit číselnou hodnotu. Tady je tabulka podporovaných předdefinovaných matematických funkcí.

| Využití | Popis |
|----------|--------|
| [[ABS (num_expr)](#bk_abs) | Vrátí absolutní hodnotu (pozitivní) zadaný číselný výraz. |
| [Horní MEZ (num_expr)](#bk_ceiling) | Vrátí nejmenší hodnotu celé číslo větší než nebo rovna zadané číselný výraz. |
| [Dolní MEZ (num_expr)](#bk_floor) | Vrátí největší celé číslo menší nebo rovna zadané číselný výraz. |
| [EXP (num_expr)](#bk_exp) | Vrátí exponent zadaný číselný výraz. |
| [LOG (num_expr [,base])](#bk_log) | Vrátí přirozený logaritmus zadaného číselný výraz nebo pomocí zadaném základu logaritmu |
| [LOG10 (num_expr)](#bk_log10) | Vrátí hodnotu logaritmu základu 10 zadaný číselný výraz. |
| [ROUND (num_expr)](#bk_round) | Vrátí číselnou hodnotu, zaokrouhlený na nejbližší celočíselnou hodnotu. |
| [TRUNC (num_expr)](#bk_trunc) | Vrátí číselnou hodnotu, zkrácen na nejbližší celočíselnou hodnotu. |
| [SQRT (num_expr)](#bk_sqrt) | Vrátí druhou odmocninu určeného číselného výrazu. |
| [HRANATÉ (num_expr)](#bk_square) | Vrátí druhou mocninu zadaný číselný výraz. |
| [NAPÁJENÍ (num_expr, num_expr)](#bk_power) | Vrátí sílu zadaný číselný výraz na zadanou hodnotu. |
| [SIGN (num_expr)](#bk_sign) | Vrátí hodnotu znaménko (-1, 0, 1) zadaný číselný výraz. |
| [ACOS (num_expr)](#bk_acos) | Vrací úhel v radiánech, jehož kosinus je zadaný číselný výraz. Zkratka Arkus kosinus. |
| [ASIN (num_expr)](#bk_asin) | Vrací úhel v radiánech, jehož sinus je zadaný číselný výraz. Arkus sinus je také voláním této funkce. |
| [ATAN (num_expr)](#bk_atan) | Vrací úhel v radiánech, jehož tangens odpovídá zadané číselný výraz. Označuje se také Arkus tangens. |
| [ATN2 (num_expr)](#bk_atn2) | Vrací úhel v radiánech mezi kladnou osy x a ray ze zdroje do bodu (y, x), kde x a y jsou hodnoty ze dvou výrazů zadaného typu float. |
| [COS (num_expr)](#bk_cos) | Vrátí trigonometrických kosinus úhlu určeného v radiánech v zadaným výrazem. |
| [COT (num_expr)](#bk_cot) | Vrátí trigonometrických kotangens úhlu určeného v radiánech v zadané číselný výraz. |
| [DEGREES (num_expr)](#bk_degrees) | Vrátí odpovídající úhel ve stupních, kterým je úhel zadaný v radiánech. |
| [PI ()](#bk_pi) | Vrátí konstantní hodnotu čísla PÍ. |
| [RADIANS (num_expr)](#bk_radians) | Radians vrátí, pokud je zadán číselný výraz ve stupních. |
| [SIN (num_expr)](#bk_sin) | Vrátí trigonometrických sinus úhlu určeného v radiánech v zadaným výrazem. |
| [TAN (num_expr)](#bk_tan) | Vrátí tangens vstupní výraz zadaným výrazem. |

Například nyní můžete spustit dotazy jak je znázorněno v následujícím příkladu:

**Dotaz**

```sql
    SELECT VALUE ABS(-4)
```

**Results**

```json
    [4]
```

Hlavní rozdíl mezi funkcemi služby Cosmos DB ve srovnání s ANSI SQL je, že jsou navrženy pro práci i s daty bez schématu a smíšené schéma. Například pokud položky, kde chybí vlastnost velikosti, nebo má jiné než číselné hodnoty jako "Neznámý", pak položky se přeskočil, místo vrácení chyby.

### <a name="type-checking-functions"></a>Kontrola funkce typu

Funkce pro kontrolu typů umožňují zkontrolujte typ výrazu v rámci dotazů SQL. Funkce pro kontrolu typů umožňuje určit typ vlastnosti v rámci položky v reálném čase, kdy je neznámý nebo proměnné. Tady je tabulka znázorňující podporované předdefinovaný typ kontroly funkce.

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

Použití těchto funkcí, teď můžete spustit dotazy jak je znázorněno v následujícím příkladu:

**Dotaz**

```sql
    SELECT VALUE IS_NUMBER(-4)
```

**Results**

```json
    [true]
```

### <a name="string-functions"></a>Funkce řetězců

Následující skalární funkce provádění operací na vstupní hodnotu řetězce a vrátí řetězec, číslo nebo logickou hodnotu. Tady je tabulka funkcí integrovaných řetězec:

| Využití | Popis |
| --- | --- |
| [Délka (str_expr)](sql-api-query-reference.md#bk_length) | Vrátí počet znaků ze zadaného řetězcového výrazu |
| [CONCAT (str_expr str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcových hodnot. |
| [Dílčí řetězec (str_expr, num_expr num_expr.)](sql-api-query-reference.md#bk_substring) | Vrátí část řetězcového výrazu. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce začíná druhé |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce končí na druhý |
| [OBSAHUJE (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Vrátí hodnotu typu Boolean označující, zda řetězec prvního výrazu obsahuje druhý. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | Vrátí počáteční pozici prvního výskytu druhý řetězec výrazu v rámci prvního zadaného řetězcového výrazu nebo -1, pokud není nalezen řetězec. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | Vrátí levou část řetězec zadaný počet znaků. |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | Vrátí část reprezentující správný řetězec zadaný počet znaků. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | Vrátí řetězcový výraz po odebere úvodní mezery. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | Vrátí řetězcový výraz po zkracování všechny koncové mezery. |
| [MALÁ (str_expr)](sql-api-query-reference.md#bk_lower) | Vrátí řetězcový výraz po převedení dat velkým písmenem na malá písmena. |
| [VELKÁ (str_expr)](sql-api-query-reference.md#bk_upper) | Vrátí řetězcový výraz po převedení data znaků na malá písmena na velká písmena. |
| [NAHRAĎTE (str_expr, str_expr str_expr.)](sql-api-query-reference.md#bk_replace) | Nahradí všechny výskyty zadaná řetězcová hodnota s jinou hodnotou řetězce. |
| [REPLIKACE (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) | Opakuje hodnotu řetězce zadaného počtu opakování. |
| [REVERZNÍ (str_expr)](sql-api-query-reference.md#bk_reverse) | Vrátí hodnotu řetězce obráceném pořadí. |

Použití těchto funkcí, můžete nyní spouštět dotazy takto. Například se můžete vrátit název rodiny na velká písmena následujícím způsobem:

**Dotaz**

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

**Results**

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

Nebo zřetězení řetězců jako v tomto příkladu:

**Dotaz**

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]
```

Funkce řetězce lze použít také v klauzuli WHERE pro filtrování výsledků, stejně jako v následujícím příkladu:

**Dotaz**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Funkce pole

Následující skalární funkce provádění operací na hodnotu vstupního pole a vrácené číselné, logickou hodnotu nebo hodnotu pole. Tady je tabulka funkcí integrovaných pole:

| Využití | Popis |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |Vrátí počet prvků výrazu určeném poli. |
| [ARRAY_CONCAT (arr_expr arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Vrátí pole, která je výsledkem zřetězení dvou nebo více hodnot pole. |
| [ARRAY_CONTAINS (arr_expr, výraz [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Vrátí logickou hodnotu označující, zda pole obsahuje zadanou hodnotu. Můžete zadat, pokud shoda není celé nebo jeho část. |
| [ARRAY_SLICE (arr_expr num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Vrátí část reprezentující výraz pole. |

Pole funkcí lze použít k manipulaci s poli ve formátu JSON. Tady je příklad dotaz, který vrátí všechny položky, kde jeden z rodičů je "Robin Wakefieldů". 

**Dotaz**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Můžete zadat částečná fragment pro porovnání prvků v poli. Následující dotaz najde všechny nadřazené položky s `givenName` z `Robin`.

**Dotaz**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Tady je další příklad, který používá ARRAY_LENGTH zobrazíte počet podřízených objektů, za řady.

**Dotaz**

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

**Results**

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

Cosmos DB podporuje následující předdefinované funkce Otevřít geoprostorové W3c (OGC) pro geoprostorové dotazování. 

| Využití | Popis |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | Vrací vzdálenost mezi dvěma GeoJSON bodu mnohoúhelníku či LineString výrazy. |
| T_WITHIN (point_expr, polygon_expr) | Vrací výraz Boolean určující, zda je první objekt GeoJSON (bodu, mnohoúhelník nebo LineString) v rámci druhého objektu GeoJSON (bodu, mnohoúhelník nebo LineString). |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Vrátí hodnotu určující, zda dvě zadané GeoJSON objekty (bodu, mnohoúhelník nebo LineString) intersect logický výraz. |
| ST_ISVALID | Vrátí logickou hodnotu označující, zda je zadaný výraz GeoJSON bodu mnohoúhelníku či LineString platný. |
| ST_ISVALIDDETAILED | Vrátí hodnotu JSON obsahující logická hodnota, pokud zadaný výraz GeoJSON bodu mnohoúhelníku či LineString je platný a pokud není platný, kromě důvod jako hodnotu řetězce. |

Prostorové funkce lze použít k provádění dotazů blízkosti prostorová data. Například tady je dotaz, který vrátí všechny rodiny položky, které jsou v rámci 30 km pomocí integrované funkce ST_DISTANCE zadaného umístění.

**Dotaz**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Další informace o podpoře geoprostorových ve službě Cosmos DB najdete v tématu [práce s Geoprostorová data ve službě Azure Cosmos DB](geospatial.md). Tím končí naše shrnutí prostorové funkce a syntaxe SQL pro službu Cosmos DB. Nyní Pojďme se podívat, jak funguje a jak komunikuje se službou syntaxe dotazu LINQ zaznamenali jsme dosud.

## <a id="Linq"></a>Technologie LINQ to SQL API

LINQ je programovací model rozhraní .NET, který vyjadřuje výpočet jako dotazy na datové proudy objektů. Cosmos DB poskytuje knihovnu na straně klienta pro rozhraní s dotazy LINQ usnadněním převod mezi objekty JSON a .NET a mapování z určité podmnožiny dotazů LINQ dotazy Cosmos DB.

Následující obrázek ukazuje architekturu podporovat LINQ dotazů pomocí služby Cosmos DB.  Pomocí klienta služby Cosmos DB, vývojáři mohou vytvářet **IQueryable** objekt, který se dotazuje přímo poskytovatele dotazů Cosmos DB, který pak převádí dotazu LINQ na dotaz Cosmos DB. Dotaz je pak předán serveru služby Cosmos DB k načtení sady výsledků ve formátu JSON. Vrácené výsledky jsou deserializovat do datového proudu objekty .NET na straně klienta.

![Architektura podporuje dotazů LINQ pomocí rozhraní SQL API - syntaxi SQL, JSON dotazovací jazyk, databázových koncepcí a dotazy SQL][1]

### <a name="net-and-json-mapping"></a>.NET a mapování JSON

Mapování mezi objekty .NET a položkami JSON je přirozeně – každé datové pole člena je mapována k objektu JSON, kde název pole je namapovaná na "klíče" součástí objektu a části "value" je rekurzivně namapované na část hodnoty objektu. Vezměte v úvahu v následujícím příkladu: Řada objekt vytvořený je namapována na položky JSON, jak je znázorněno níže. A naopak je namapovat položky JSON zpět na objekt .NET.

**Třída jazyka C#**

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

**JSON**

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

Cosmos DB poskytovatele dotazů provede nejlepší úsilí mapování z dotazu LINQ do dotazu Cosmos DB SQL. V následující popis předpokládáme, že čtečky má základní znalosti o LINQ.

Nejprve pro systém typů, podporujeme všechny JSON primitivní typy – číselné typy, logická hodnota, řetězec a hodnotu null. Podporovány jsou pouze tyto typy JSON. Jsou podporovány následující skalární výrazy.

* Konstantní hodnoty – patří mezi ně konstantní hodnoty primitivních datových typů v době, kdy je vyhodnocen dotaz.
* Vlastnost nebo pole indexu výrazy – tyto výrazy odkazovat na vlastnost objektu nebo k elementu pole.
  
     řady. ID;    Family.Children[0].familyName;    Family.Children[0].Grade;    Family.Children[n].Grade; n je proměnná typu int
* Aritmetických výrazech – patří mezi ně běžné aritmetické výrazy u číselných a logických hodnot. Úplný seznam naleznete ve specifikaci SQL.
  
     2 * family.children[0].grade;    x + y;
* Řetězcového výrazu porovnání – patří mezi ně porovnání řetězcovou hodnotu na hodnotu konstanty typu řetězec.  
  
     mother.familyName == "Novák";    child.givenName == s. Řetězcová proměnná je s
* Objekt nebo pole výraz vytvoření – tyto návratové výrazy objekt složené hodnotový typ nebo anonymního typu nebo polem takových objektů. Tyto hodnoty mohou být vnořené.
  
     Nový nadřazený {familyName = "Macek", givenName = "Jan"}; nové {první = 1, druhý = 2}; anonymní typ s dvěma poli              
     New [] int {3, child.grade, 5};

### <a id="SupportedLinqOperators"></a>Seznam podporovaných operátory LINQ

Tady je seznam podporovaných operátory LINQ v zprostředkovatele LINQ, který je součástí SQL SDK pro .NET.

* **Vyberte**: Projekce přeložit a vyberte SQL, včetně vytváření objektu.
* **Kde**: Převést na SQL kde filtry a podporovat překlad mezi & &, || a! pro operátory SQL
* **Operátor SelectMany**: Umožňuje uvolnění polí do klauzule SQL JOIN. Je možné řetězec/vnoření výrazy k filtrování prvky pole
* **OrderBy a OrderByDescending**: Přeloží na klauzule ORDER BY ascending/descending
* **Počet**, **součet**, **Min**, **maximální**, a **průměrné** operátory pro agregaci a jejich ekvivalenty asynchronní **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, a **AverageAsync**.
* **CompareTo**: Přeloží na porovnání rozsahu. Běžně se používají pro řetězce, protože nejsou porovnatelné v rozhraní .NET
* **Využijte**: Přeloží do horní části SQL pro omezení výsledků z dotazu
* **Matematické funkce**: Podporuje překlad z. NET pro funkce Abs, funkce Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, protokolu, Log10, Pow, kruhové, přihlášení, Sin, Sqrt, Tan, Truncate na ekvivalentní funkce integrované v SQL.
* **Řetězec funkce**: Podporuje překlad z. EndsWith Concat, obsahuje, od sítě, IndexOf, počet, ToLower, TrimStart, nahradit, zpětné, TrimEnd, StartsWith, podřetězec, ToUpper na ekvivalentní integrované funkce SQL.
* **Array – funkce**: Podporuje překlad z. NET pro funkce Concat, obsahuje a počet, který má ekvivalentní funkce integrované v SQL.
* **Rozšíření geoprostorové funkce**: Podporuje překlad z metody zástupných procedur vzdálenost v IsValid a IsValidDetailed na ekvivalentní funkce integrované v SQL.
* **Uživatelem definované funkce rozšíření funkce**: Podporuje překlad z zástupná metoda UserDefinedFunctionProvider.Invoke odpovídající uživatelem definované funkce.
* **Různé**: Podporuje překlad coalesce a podmíněných operátorů. Jsou dobře převeditelné obsahuje řetězec obsahuje, ARRAY_CONTAINS nebo v SQL v závislosti na kontextu.

### <a name="sql-query-operators"></a>Operátory dotazů SQL

Tady je několik příkladů, které demonstrují, jak některé standardní operátory dotazu LINQ jsou přeloženy do služby Cosmos DB dotazy.

#### <a name="select-operator"></a>Vyberte operátor

Syntaxe je `input.Select(x => f(x))`, kde `f` je skalární výraz.

**Lambda výraz LINQ**

```csharp
    input.Select(family => family.parents[0].familyName);
```

**SQL** 

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
```

**Lambda výraz LINQ**

```csharp
    input.Select(family => family.children[0].grade + c); // c is an int variable
```

**SQL**

```sql
    SELECT VALUE f.children[0].grade + c
    FROM Families f
```

**Lambda výraz LINQ**

```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
```

**SQL** 

```sql
    SELECT VALUE {"name":f.children[0].familyName,
                  "grade": f.children[0].grade + 3 }
    FROM Families f
```


#### <a name="selectmany-operator"></a>Operátor SelectMany – operátor

Syntaxe je `input.SelectMany(x => f(x))`, kde `f` je skalární výraz, který vrátí typ kontejneru.

**Lambda výraz LINQ**

```csharp
    input.SelectMany(family => family.children);
```

**SQL**

```sql
    SELECT VALUE child
    FROM child IN Families.children
```

#### <a name="where-operator"></a>Kde – operátor

Syntaxe je `input.Where(x => f(x))`, kde `f` je skalární výraz, který vrací logickou hodnotu.

**Lambda výraz LINQ**

```csharp
    input.Where(family=> family.parents[0].familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**Lambda výraz LINQ**

```csharp
    input.Where(
        family => family.parents[0].familyName == "Smith" &&
        family.children[0].grade < 3);
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3
```

### <a name="composite-sql-queries"></a>Složený dotazy SQL

Výše uvedené operátory mohou být složené tvoří výkonnějších dotazů. Protože Cosmos DB podporuje vnořené kontejnery, složení můžete být zřetězené nebo vnořené.

#### <a name="concatenation"></a>Zřetězení

Syntaxe je `input(.|.SelectMany())(.Select()|.Where())*`. Zřetězených dotazů můžete začít s volitelným `SelectMany` dotaz a potom pomocí několika `Select` nebo `Where` operátory.

**Lambda výraz LINQ**

```csharp
    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**Lambda výraz LINQ**

```csharp
    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);
```

**SQL**

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3
```

**Lambda výraz LINQ**

```csharp
    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)
```

**Lambda výraz LINQ**

```csharp
    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"
```

#### <a name="nesting"></a>Vnoření

Syntaxe je `input.SelectMany(x=>x.Q())` kde Q je `Select`, `SelectMany`, nebo `Where` operátor.

Do vnořeného dotazu se použije vnitřní dotaz na každý prvek kontejneru vnější. Jednou z důležitou funkcí je, že vnitřní dotaz může odkazovat na pole prvků v kontejneru vnější jako spojení.

**Lambda výraz LINQ**

```csharp
    input.SelectMany(family=>
        family.parents.Select(p => p.familyName));
```

**SQL**

```sql
    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents
```

**Lambda výraz LINQ**

```csharp
    input.SelectMany(family =>
        family.children.Where(child => child.familyName == "Jeff"));
```

**SQL**

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"
```

**Lambda výraz LINQ**

```csharp
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));
```

**SQL**

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName
```

## <a id="ExecutingSqlQueries"></a>Zpracování dotazů SQL

Cosmos DB zveřejňuje prostředky přes rozhraní REST API, které je možné vyvolat v jakémkoli jazyce schopném zasílat požadavky HTTP/HTTPS. Cosmos DB dále nabízí programovací knihovny pro několik oblíbených jazyků, jako je .NET, Node.js, JavaScript a Python. Rozhraní REST API různé knihovny podporují a dotazování pomocí SQL. Sady .NET SDK podporuje kromě SQL dotazu LINQ.

Následující příklady ukazují, jak vytvořit dotaz a odeslat ji proti účtu databáze Cosmos DB.

### <a id="RestAPI"></a>ROZHRANÍ REST API

Cosmos DB nabízí otevřete programovací model RESTful přes HTTP. Účty databází lze zřizovat pomocí předplatného Azure. Model prostředků služby Cosmos DB obsahuje sadu prostředků v rámci účtu databáze, z nichž každý je adresovatelné logické a stabilní identifikátor URI. Sada prostředků se označuje jako kanál v této položce. Databázový účet se skládá ze sady databází, každá obsahuje několik kontejnerů, každý z které naopak obsahuje položky, funkcí UDF a další typy prostředků.

Základní interakce s těmito prostředky je model pomocí příkazů HTTP GET, PUT, POST a DELETE s jejich standardní interpretaci. Operace POST se používá pro vytvoření nového prostředku, pro provedení uložené procedury nebo pro zadání dotazu Cosmos DB. Dotazy jsou vždy jen pro čtení operací s žádným vedlejším účinkům.

Následující příklady ukazují příspěvek pro dotaz rozhraní SQL API provedená oproti kontejner obsahující dvě ukázkové položky, že jsme si zatím. Dotaz obsahuje jednoduchý filtr na název vlastnosti JSON. Všimněte si, `x-ms-documentdb-isquery` a Content-Type: `application/query+json` hlavičky k označení, že operace je dotaz.

**Požadavek**
```
    POST https://<REST URI>/docs HTTP/1.1
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

**Results**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

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
                "city":"seattle"
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

Druhý příklad ukazuje komplexnější dotaz, který vrátí více výsledků z spojení.

**Požadavek**
```
    POST https://<REST URI>/docs HTTP/1.1
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

**Results**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

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

Pokud výsledky dotazu se nemůže vejít na jedné stránce výsledky a potom vrátí token pro pokračování prostřednictvím rozhraní REST API `x-ms-continuation-token` hlavičky odpovědi. Klienti můžou stránkovat výsledky včetně záhlaví v dalších výsledků. Počet výsledků na stránku je možné řídit také prostřednictvím `x-ms-max-item-count` číslo hlavičky. Pokud zadaný dotaz obsahuje agregační funkci jako `COUNT`, pak na stránce dotazů může vracet částečně agregovaná hodnota na stránku s výsledky. Klienti musí provést druhé úrovně agregace přes tyto výsledky poslední výsledky, například, součet přes počty vrácené v jednotlivých stránek vrátit celkový počet.

Chcete-li spravovat zásady konzistence dat pro dotazy, použijte `x-ms-consistency-level` záhlaví stejně jako všechny požadavky rozhraní REST API. Pro zajištění konzistence relace, je potřeba také echo nejnovější `x-ms-session-token` hlavička Cookie v dotazu žádosti. Zásady indexování dotazované kontejner může také ovlivnit konzistence výsledky dotazu. S výchozí nastavení zásady indexování pro kontejnery indexu je vždy s obsahem položky aktuální a výsledky dotazu odpovídat konzistence, které jste zvolili pro data. Pokud k opožděné je mírnější zásady indexování, dotazy mohou vracet zastaralé výsledky. Další informace najdete v tématu [Azure Cosmos DB úrovním][consistency-levels].

Pokud nakonfigurované zásady indexování v kontejneru nepodporuje zadaný dotaz, server služby Azure Cosmos DB vrátí 400 "Chybný požadavek". Tato chybová zpráva se vrací pro dotazy na rozsah proti cesty, které jsou nakonfigurované pro vyhledávání hodnoty hash (rovnost) a explicitně vyloučené z indexování cesty. `x-ms-documentdb-query-enable-scan` Může být zadáno záhlaví umožňující dotazu má provést kontrola při indexu není k dispozici.

Můžete získat podrobné metriky spouštění dotazů nastavením `x-ms-documentdb-populatequerymetrics` záhlaví `True`. Další informace najdete v tématu [metriky dotaz SQL pro službu Azure Cosmos DB](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>SADY SDK JAZYKA C# (.NET)

Sady .NET SDK podporuje LINQ a SQL dotazování. Následující příklad ukazuje, jak provést dotaz filtru zavedené dříve v této položce.
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

Tento příklad porovná dvě vlastnosti rovnost v rámci každé položky a používá anonymní projekce.

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

Další příklad ukazuje spojení vyjádřen prostřednictvím operátor SelectMany LINQ.

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

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET client automaticky Iteruje přes všechny stránky výsledků dotazu v blocích foreach, jak je znázorněno výše. Možnosti dotazu představíme v části rozhraní REST API jsou dostupné v pomocí sady .NET SDK `FeedOptions` a `FeedResponse` třídy v metodě CreateDocumentQuery. Počet stránek se dá řídit pomocí `MaxItemCount` nastavení.

Můžete také explicitně kontrolovat stránkování tak, že vytvoříte `IDocumentQueryable` pomocí `IQueryable` objekt, potom načtením` ResponseContinuationToken` hodnoty a jejich předávání zpátky jako `RequestContinuationToken` v `FeedOptions`. `EnableScanInQuery` je možné nastavit pro povolení vyhledávání, když dotaz nemůže být podporována nakonfigurované zásady indexování. Pro dělené kontejnerů, můžete použít `PartitionKey` do jednoho oddílu (i když Azure Cosmos DB můžete automaticky extrahovat to z textu dotazu), spusťte dotaz a `EnableCrossPartitionQuery` ke spouštění dotazů, které může být nutné spustit proti více oddílů.

Odkazovat na [ukázek Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet) pro další ukázky obsahující dotazy.

### <a id="JavaScriptServerSideApi"></a>Rozhraní API pro JavaScript na straně serveru

Cosmos DB poskytuje programovací model pro spouštění logiky aplikací JavaScript na základě přímo na kontejnery pomocí uložené procedury a triggery. Logiky JavaScript zaregistrovaných na úrovni kontejneru pak můžou provádět databázové operace na operace u položek daného kontejneru. Tyto operace jsou zabaleny v okolí transakce ACID.

Následující příklad ukazuje způsob použití queryDocuments v rozhraní API jazyka JavaScript serveru dotazy z vnitřního uložených procedur a aktivačních událostí.

```javascript
    function businessLogic(name, author) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="References"></a>Odkazy

1. [Úvod do služby Azure Cosmos DB][introduction]
2. [Azure Cosmos DB SQL specifikace](https://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
4. [Úrovně konzistence služby Azure Cosmos DB][consistency-levels]
5. ANSI SQL 2011 [https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [https://json.org/](https://json.org/)
7. Specifikace jazyka JavaScript [https://www.ecma-international.org/publications/standards/Ecma-262.htm](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [https://msdn.microsoft.com/library/bb308959.aspx](https://msdn.microsoft.com/library/bb308959.aspx) 
9. Techniky hodnocení dotazování u velkých databází [https://dl.acm.org/citation.cfm?id=152611](https://dl.acm.org/citation.cfm?id=152611)
10. Zpracování v paralelní relačními databázovými systémy. počítač IEEE Press společnosti, 1994 dotazů
11. Logická jednotka, Ooi, Tan, zpracování v paralelní relačními databázovými systémy. počítač IEEE Press společnosti, 1994 dotazů.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: Není tak cizího jazyka pro zpracování dat, SIGMOD 2008.
13. G. Graefe. Cascades architektura pro optimalizaci dotazu. Eng. IEEE dat Bull., 18(3): 1995.

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
