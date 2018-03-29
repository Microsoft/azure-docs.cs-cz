---
title: Dotazy SQL pro Azure Cosmos DB | Microsoft Docs
description: Další informace o syntaxi jazyka SQL, databáze koncepty a dotazy SQL pro Azure Cosmos DB. SQL lze použít jako dotazovací jazyk JSON v Azure Cosmos DB.
keywords: syntaxe SQL, dotaz sql, sql dotazy, json dotazovací jazyk, databázových koncepcí a sql, agregační funkce
services: cosmos-db
documentationcenter: ''
author: LalithaMV
manager: jhubbard
editor: monicar
ms.assetid: a73b4ab3-0786-42fd-b59b-555fce09db6e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: laviswa
ms.openlocfilehash: a79b1a97909a38b4bfba06186db875d0c0c25f03
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="sql-queries-for-azure-cosmos-db"></a>Dotazy SQL pro Azure Cosmos DB

Microsoft Azure Cosmos DB podporuje dotazování dokumentů pomocí jazyka SQL (Structured Query Language) jako dotazovací jazyk JSON na rozhraní API pro SQL účty. Azure Cosmos DB je skutečně bez schémat. Na základě jeho závazků do datového modelu JSON přímo v rámci databázový stroj poskytuje automatické indexování dokumentů JSON bez nutnosti explicitního schématu nebo vytváření sekundárních indexů.

Při navrhování dotazovacího jazyka pro Cosmos DB, jsme měli dva cíle v paměti:

* Místo inventing o nový jazyk dotazů JSON, jsme chtěli podporu SQL. SQL je jedním z nejvíce známé a oblíbených jazyků dotazu. SQL databáze cosmos umožňuje formální programovací model o bohaté dotazy prostřednictvím dokumentů JSON.
* Jako dokument databáze JSON může provést JavaScript přímo v databázovém stroji jsme chtěli použít model programování v jazyce JavaScript jako základ pro naše dotazovací jazyk. Rozhraní SQL API je integrován do systému typů JavaScript na vyhodnocení výrazu a volání funkce. Tato naopak poskytuje přirozené programovací model pro projekce relačních, hierarchických navigace mezi dokumenty JSON, vlastní spojení, prostorových dotazů a vyvolání uživatelem definovaných funkcí (UDF) vytvořené zcela v JavaScriptu mezi dalších funkcí. 

Věříme, že tyto funkce jsou klíčem k omezení tření mezi aplikací a databáze a jsou zásadní pro produktivita vývojářů.

Doporučujeme začít následujícím videem, kde Azure manažer programu DB Cosmos Andrew Liu ukazuje možnosti dotazování Azure Cosmos DB a předvádí online [Query Playground](http://www.documentdb.com/sql/demo), kde můžete vyzkoušet Azure Cosmos DB a spouštět dotazy SQL pro naší datové sadě, jak je předvedeno v videa.

> [!VIDEO https://www.youtube.com/embed/1LqUQRpHfFI]
>
>

Pokročilejší dotazování techniky je ukázán v této následné video:

> [!VIDEO https://www.youtube.com/embed/kASU9NOIR90]
>
>

Pak se vraťte k tomuto článku, kde Začneme s kurz dotaz SQL, který vás provede některé jednoduché dokumentů JSON a příkazy SQL.

## <a id="GettingStarted"></a>Začínáme s příkazy SQL v databázi systému Cosmos
SQL databáze Cosmos v práci najdete umožňuje začínat několik jednoduchých dokumentů JSON a provede několik jednoduchých dotazů u ní. Vezměte v úvahu tyto dva dokumenty JSON o dvou řad. S Cosmos DB jsme není potřeba explicitně vytvořit žádné schémata nebo sekundárních indexů. Jednoduše musíme vložit dokumenty JSON do kolekce Cosmos DB a následně dotazu. Tady bychom měli jednoduché JSON dokumentů pro rodinu, rodiče, děti (a jejich mazlíčků), adresu a informace o registraci. Má dokument řetězců, čísel, logické hodnoty, pole a vnořené vlastnosti. 

**Dokument**  

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

Tady je druhý dokument s jedním jemně rozdílem – `givenName` a `familyName` se používají místo `firstName` a `lastName`.

**Dokument**  

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

Nyní nyní si vyzkoušíte několik dotazů pro tato data pochopit některé z klíčových aspektů dotazovací jazyk SQL Azure Cosmos DB. Například následující dotaz vrátí dokumenty, kde v poli id odpovídá `AndersenFamily`. Vzhledem k tomu, že je `SELECT *`, výstup tohoto dotazu je kompletní dokumentu JSON:

**Dotaz**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

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


Teď se podíváme případu, které je třeba přeformátujte výstup JSON v různých obrazce. Tento dotaz projekty nový objekt JSON s dvě vybrané pole název a města, když na adresu města má stejný název jako stavu. V tomto případě "NY, NY" odpovídá.

**Dotaz**    

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Results**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


Další dotaz vrátí všechny názvy daným podřízených prvků v dané rodině, jehož id odpovídá `WakefieldFamily` seřazené podle města pobytu.

**Dotaz**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Results**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Rádi bychom se upozornit na několik pozoruhodné aspektů dotazovací jazyk Cosmos DB provede příklady, které jste viděli, pokud:  

* Vzhledem k tomu, že rozhraní SQL API funguje na hodnoty JSON, zabývá stromu ve tvaru entity místo řádků a sloupců. Proto jazyk umožňuje vztahují na všechny uzly stromu v jakékoli libovolný hloubku jako `Node1.Node2.Node3…..Nodem`, podobně jako relační SQL odkazující na odkaz na dvě části `<table>.<column>`.   
* Jazyk SQL pracuje s daty bez schématu. Systém typů proto musí být vázána dynamicky. Stejný výraz může přinést různých typů na různé dokumenty. Výsledek dotazu není platná hodnota JSON, ale není zaručena bezpečnost pro přístup z pevného schématu.  
* Cosmos databáze podporuje pouze striktní dokumentů JSON. To znamená, že systém typů a výrazy jsou omezeny na pracují jenom s typy JSON. Odkazovat [JSON specifikace](http://www.json.org/) další podrobnosti.  
* Cosmos DB kolekce je kontejner dokumentů JSON bez schémat. Vztahy v datových entit v rámci a na dokumentech v kolekci jsou implicitně zaznamenat členství ve skupině a ne primárního a cizího klíče relace. Toto je důležitým aspektem vhodné odkazující na základě spojení intra-document probírat později v tomto článku.

## <a id="Indexing"></a> Indexování cosmos DB
Než se nám získat do syntaxe SQL, je vhodné využít indexování návrhu v Azure Cosmos DB. 

Účelem indexy databáze je poskytovat dotazy v různých formách a tvarů s spotřeby minimální prostředků (např. využití procesoru a vstup/výstup) současně poskytují dobrý prostupnosti a nízké latence. Volba správného indexu pro dotazování databáze často vyžaduje mnohem plánování a experimentování. Tento přístup představuje výzvu pro bez schématu databáze, kde data neodpovídají striktní schéma a zpracovaní rychle. 

Proto když jsme navržený subsystém indexování Cosmos DB, nastaví sledovat tyto cíle:

* Indexování dokumentů bez nutnosti schématu: subsystém indexování nevyžaduje žádné informace o schématu ani vytvořit žádný odhad o schéma dokumentů. 
* Podpora pro efektivní, bohaté hierarchické a relační dotazy: index podporuje dotazovací jazyk Cosmos DB efektivně, včetně podpory pro hierarchické a relační projekce.
* Podpora pro konzistentní dotazy in face of svazek dlouhodobě zápisů: pro zápisu vysokou propustnost úlohy s konzistentní dotazy, aktualizace indexu postupně, efektivně a online při krátkodobém dlouhodobě svazku zápisů. Aktualizace konzistentní index je zásadní význam pro poskytovat dotazy na úrovni konzistence, ve kterém uživatel nakonfigurovali službu dokumentu.
* Podpora pro více klientů: zadána modelu založené na vyhrazené pro řízení prostředků mezi klienty v rámci rozpočtu systémových prostředků (procesoru, paměti a vstupně-výstupních operací za sekundu) přidělený na repliky jsou provedeny aktualizace indexu. 
* Efektivitu úložiště: pro finanční efektivita režijní náklady na úložiště na disku indexu je ohraničené a předvídatelné. To je velmi důležitý, protože Cosmos DB umožňuje vývojáři aby náklady na základě kompromisy mezi režijní náklady na indexů ve vztahu k dotazu výkon.  

Odkazovat [Azure Cosmos DB – ukázky](https://github.com/Azure/azure-documentdb-net) na webu MSDN ukázek znázorňující postup konfigurace zásady indexování pro kolekci. Nyní Pojďme na podrobné informace o syntaxi Azure Cosmos DB SQL.

## <a id="Basics"></a>Základní informace o příkazu jazyka Azure Cosmos DB SQL
Každý dotaz sestává z klauzule SELECT a volitelné FROM a klauzule WHERE za standardy ANSI SQL. Pro každý dotaz, obvykle je výčet zdroji v klauzuli FROM. Filtru v klauzuli WHERE se pak použije ve zdroji k načtení podmnožinu dokumentů JSON. Klauzule SELECT se nakonec slouží k plánování požadovaný JSON hodnot v seznamu select.

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a id="FromClause"></a>FROM – klauzule
`FROM <from_specification>` Klauzule je nepovinný, pokud je zdroj filtrovat nebo projekci později v dotazu. Účelem této klauzule je zadat zdroj dat, na kterém musí fungovat dotazu. Běžně celé kolekce je zdrojem, ale jeden místo toho zadat podmnožinu kolekce. 

Dotaz jako `SELECT * FROM Families` označuje, že je celou kolekci rodiny zdroji, za které se vytvořit výčet. Identifikátor speciální KOŘENOVÉ slouží k představují kolekci nepoužívejte název kolekce. Následující seznam obsahuje pravidla, které vynucuje na jeden dotaz:

* Kolekce je to možné, jako například `SELECT f.id FROM Families AS f` nebo jednoduše `SELECT f.id FROM Families f`. Zde `f` je ekvivalentem `Families`. `AS` optional – klíčové slovo alias je identifikátor.
* Jednou alias, nemůže být vázán původního zdroje. Například `SELECT Families.id FROM Families f` je syntakticky neplatný, protože už nelze přeložit identifikátor "Rodiny".
* Všechny vlastnosti, které je potřeba na něj odkazovat musí být plně kvalifikovaný. Chybí dodržování striktní schématu tato velikost je vyžadována předejdete žádné nejednoznačný vazby. Proto `SELECT id FROM Families f` je syntakticky neplatný, protože vlastnost `id` není vázán.

### <a name="subdocuments"></a>Vnořené dokumenty
Zdroj může být také omezené menší podmnožinu. Například k vytváření výčtu pouze podstrom v každém dokumentu, subroot může pak mohou stát zdroje, jak je znázorněno v následujícím příkladu:

**Dotaz**

    SELECT * 
    FROM Families.children

**Results**  

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

Při výše uvedeném příkladu pole jako zdroj, objekt může také použít jako zdroj, který je co je znázorněno v následujícím příkladu: žádné platná hodnota JSON (nedefinovaná), můžete najít ve zdroji je považován za pro zahrnutí do výsledků dotazu. Pokud nemáte některé rodiny `address.state` hodnotu, jsou vyloučeny ve výsledku dotazu.

**Dotaz**

    SELECT * 
    FROM Families.address.state

**Results**

    [
      "WA", 
      "NY"
    ]


## <a id="WhereClause"></a>Klauzule WHERE
Klauzule WHERE (**`WHERE <filter_condition>`**) je volitelný. Určuje, že podmínku (podmínky), které dokumenty JSON poskytuje zdroj musí splňovat, aby byla součástí výsledek. Dokumentu JSON se musí vyhodnotit na hodnotu true, aby byla považována za pro výsledek k zadaným podmínkám. Klauzule WHERE se používá vrstvou index aby bylo možné zjistit absolutní nejmenší podmnožinu dokumentů zdroje, které můžou být součástí výsledek. 

Následující dotaz požadavků dokumentů, které obsahují název vlastnosti, jehož hodnota je `AndersenFamily`. Jiného dokumentu, který nemá název vlastnosti, nebo kde hodnota neodpovídá `AndersenFamily` je vyloučen. 

**Dotaz**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


Předchozí příklad ukázal dotazu jednoduché rovnosti. Rozhraní SQL API také podporuje celou řadu skalární výrazy. Nejčastěji používané jsou výrazy binární a unární. Vlastnost odkazy z objektu JSON zdroje jsou také platné výrazy. 

Následující binární operátory jsou aktuálně podporovány a lze použít v dotazech, jak je znázorněno v následujících příkladech:  

<table>
<tr>
<td>Aritmetické operace</td>    
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Bitový</td>    
<td>|, &, ^, <<>>,, >>> (nula výplně posunutí doprava)</td>
</tr>
<tr>
<td>Logické</td>
<td>A, NEBO NE</td>
</tr>
<tr>
<td>Porovnání</td>    
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Řetězec</td>    
<td>|| (zřetězení)</td>
</tr>
</table>  


Podívejme se na některé dotazy pomocí binární operátory.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Unární operátory +,-, ~ není jsou podporovány také a dá se použít uvnitř dotazy, jak je znázorněno v následujícím příkladu:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Kromě binární a unární operátory mohou také vlastnost odkazy. Například `SELECT * FROM Families f WHERE f.isRegistered` vrátí dokumentu JSON obsahující vlastnost `isRegistered` kde hodnotu vlastnosti rovná JSON `true` hodnotu. Všechny ostatní hodnoty (false, hodnotu null a nedefinovaná, `<number>`, `<string>`, `<object>`, `<array>`atd) vede k zdrojový dokument k vyloučení z výsledku. 

### <a name="equality-and-comparison-operators"></a>Operátory rovnosti a porovnání
V následující tabulce jsou výsledkem porovnání rovnosti v rozhraní SQL API mezi všechny dva typy JSON.

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Nedefinovaná</strong>
         </td>
         <td valign="top">
            <strong>Hodnotu Null</strong>
         </td>
         <td valign="top">
            <strong>Logická hodnota</strong>
         </td>
         <td valign="top">
            <strong>Číslo</strong>
         </td>
         <td valign="top">
            <strong>Řetězec</strong>
         </td>
         <td valign="top">
            <strong>Object</strong>
         </td>
         <td valign="top">
            <strong>Pole</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Nedefinovaná<strong>
         </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Hodnotu Null<strong>
         </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Logická hodnota<strong>
         </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Číslo<strong>
         </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Řetězec<strong>
         </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Object<strong>
         </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Nedefinováno </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Pole<strong>
         </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
Nedefinováno </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

Pro jiné operátory porovnání jako >, > =,! =, < a < =, následující pravidla platí:   

* Výsledkem porovnání mezi typy Undefined.
* Porovnání mezi dvěma objekty nebo dvě maticových má za následek Undefined.   

Pokud je výsledek skalární výraz, který ve filtru není definována, odpovídající dokument není zahrnuta do výsledek, protože Undefined není logicky rovnat "true".

### <a name="between-keyword"></a>MEZI klíčové slovo
Můžete taky – klíčové slovo BETWEEN pro dotazy na rozsah hodnot jako v ANSI SQL express. MEZI můžete použít u řetězců nebo čísla.

Například tento dotaz vrací všechny rodiny dokumenty, ve kterých je prvním podřízeným objektem úrovni mezi 1-5 (obě včetně). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Na rozdíl od v ANSI SQL, můžete taky klauzuli BETWEEN v klauzuli FROM jako v následujícím příkladu.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Pro kratší časy spuštění dotazu mějte na paměti k vytvoření zásady indexování, která používá typ indexu rozsah proti jakékoli číselné vlastnosti nebo cesty, které jsou filtrovány v klauzuli BETWEEN. 

Hlavní rozdíl mezi použitím BETWEEN v rozhraní API pro SQL a ANSI SQL je, že můžete express rozsah dotazy na vlastnosti smíšený typů – například můžete mít "základní" být číslo (5) v některých dokumentů a řetězce v jiné ("grade4"). V těchto případech jako je v jazyce JavaScript, porovnání mezi dva různé typy výsledků v "undefined" a dokument bude přeskočen.

### <a name="logical-and-or-and-not-operators"></a>Logický (AND, OR a NOT) operátory
Logické operátory pracovat logické hodnoty. Logické tabulky pravdivosti pro tyto operátory jsou uvedené v následujících tabulkách.

| NEBO | True | False | Nedefinováno |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Nedefinováno |
| Nedefinováno |True |Nedefinováno |Nedefinováno |

| A | True | False | Nedefinováno |
| --- | --- | --- | --- |
| True |True |False |Nedefinováno |
| False |False |False |False |
| Nedefinováno |Nedefinováno |False |Nedefinováno |

| NENÍ |  |
| --- | --- |
| True |False |
| False |True |
| Nedefinováno |Nedefinováno |

### <a name="in-keyword"></a>IN – klíčové slovo
Klíčové slovo IN slouží ke kontrole, zda zadaná hodnota odpovídá žádnou hodnotu v seznamu. Například tento dotaz vrací všechny rodiny dokumenty, kde id je jedním z "WakefieldFamily" nebo "AndersenFamily". 

    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Tento příklad vrátí všechny dokumenty, kde je stav žádný ze zadaných hodnot.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Unární (?) a operátory Coalesce (?)
Operátory Unární a Coalesce lze použít k vytvoření podmíněné výrazy, podobně jako oblíbené programovacích jazyků, jako je C# a JavaScript. 

Operátor unární (?) může být velmi užitečné při vytváření nové vlastnosti JSON za chodu. Například teď můžete napsat dotazy ke klasifikaci třída úrovně do lidského čitelné podoby jako Začátečník nebo zprostředkující nebo Upřesnit, jak je uvedeno níže.

     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Můžete také vnořit volání operátor jako v dotazu níže.

    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Jako s dalšími operátory dotazu, pokud v dokumentu chybí odkazovaný vlastností v podmíněným výrazem, nebo pokud typy porovnávané se liší, pak tyto dokumenty nevylučují se ve výsledcích dotazu.

Operátor Coalesce (?) slouží k efektivní (také známa jako kontrolovat přítomnost vlastnost je definován) v dokumentu. To je užitečné při dotazování na částečně strukturovaných nebo data smíšený typů. Tento dotaz vrací například "lastName", pokud existuje, nebo "Přezdívka" Pokud není přítomen.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a id="EscapingReservedKeywords"></a>Vlastnost uvozovkách přístupového objektu
Můžete také přístup k vlastnostem pomocí operátoru vlastnost uvozovkách `[]`. Například `SELECT c.grade` a `SELECT c["grade"]` odpovídají. Tato syntaxe je užitečné, když potřebujete, abyste se vyhnuli vlastnost, která obsahuje mezery, speciální znaky, nebo se stane sdílet stejný název jako SQL – klíčové slovo nebo vyhrazené slovo.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a id="SelectClause"></a>Klauzule SELECT
Klauzule SELECT (**`SELECT <select_list>`**) je povinná a určuje, jaké hodnoty jsou načteny z dotazu, podobně jako v ANSI SQL. Podmnožina je filtrované nad dokumenty zdroje jsou předávány do fáze projekce, kde jsou načteny zadaných hodnot JSON a je vytvořený nový objekt JSON, pro každý vstupní předán na něj. 

Následující příklad ukazuje typické dotaz SELECT. 

**Dotaz**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Vnořené vlastnosti
V následujícím příkladu jsme jsou projekce dvě vnořené vlastnosti `f.address.state` a `f.address.city`.

**Dotaz**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Projekce také podporuje JSON výrazy, jak je znázorněno v následujícím příkladu:

**Dotaz**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Podívejme se na roli `$1` sem. `SELECT` Klauzule musí vytvořit objekt JSON a vzhledem k tomu, že žádný klíč je k dispozici, používáme názvy proměnných implicitní argument počínaje `$1`. Například tento dotaz vrací dvě implicitní argument proměnné s názvem bez přípony `$1` a `$2`.

**Dotaz**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Aliasy
Teď umožňuje rozšířit výše uvedeného příkladu s explicitní aliasy hodnot. Tak, jak jsou klíčové slovo používané pro aliasy. Zadání je volitelné, jak je znázorněno při promítnutí druhá hodnota jako `NameInfo`. 

V případě, že dotaz má dvě vlastnosti se stejným názvem, musí být aliasy používá k přejmenování jedno nebo obě vlastnosti tak, aby se jsou od sebe jednoznačně rozlišeny ve předpokládané výsledku.

**Dotaz**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Skalární výrazy
Kromě odkazů na vlastnost klauzule SELECT také podporuje skalární výrazy konstanty, aritmetických výrazech, logických výrazů, atd. Tady je příklad jednoduchého dotazu "Hello World".

**Dotaz**

    SELECT "Hello World"

**Results**

    [{
      "$1": "Hello World"
    }]


Zde je ukázka používající skalární výraz.

**Dotaz**

    SELECT ((2 + 11 % 7)-2)/3    

**Results**

    [{
      "$1": 1.33333
    }]


V následujícím příkladu je výsledek skalární výraz logická hodnota.

**Dotaz**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f    

**Results**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Vytvoření objektu a pole
Další klíčových funkcí rozhraní API SQL je vytvoření pole nebo objektu. V předchozím příkladu Všimněte si, že jsme vytvořili nový objekt JSON. Podobně jeden můžete také vytvořit pole podle následujících příkladů:

**Dotaz**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f    

**Results**  

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

### <a id="ValueKeyword"></a>VALUE – klíčové slovo
**Hodnotu** – klíčové slovo poskytuje způsob, jak vrátit hodnotu JSON. Například následující dotaz vrátí skalárních `"Hello World"` místo `{$1: "Hello World"}`.

**Dotaz**

    SELECT VALUE "Hello World"

**Results**

    [
      "Hello World"
    ]


Následující dotaz vrátí hodnotu JSON bez `"address"` popisek ve výsledcích.

**Dotaz**

    SELECT VALUE f.address
    FROM Families f    

**Results**  

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

Následující příklad rozšiřuje na ukazují, jak vrátit JSON primitivní hodnoty (úroveň listu stromu JSON). 

**Dotaz**

    SELECT VALUE f.address.state
    FROM Families f    

**Results**

    [
      "WA",
      "NY"
    ]


### <a name="-operator"></a>* – Operátor
Podporován je speciální operátor (*) do projektu dokumentu jako-je. Pokud se používá, musí být pouze předpokládané pole. Při dotazu jako `SELECT * FROM Families f` je platný, `SELECT VALUE * FROM Families f ` a `SELECT *, f.id FROM Families f ` nejsou platné.

**Dotaz**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

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

### <a id="TopKeyword"></a>Operátor TOP
TOP – klíčové slovo lze omezit počet hodnot z dotazu. Když horní se používá ve spojení s klauzulí ORDER BY, sadu výsledků dotazu je omezený na první číslo N seřazené hodnot. jinak vrátí první N počet výsledků v nedefinované pořadí. Jako osvědčený postup v příkazu SELECT, s vždy používejte klauzuli ORDER BY v klauzuli nejvyšší. Toto je jediný způsob, jak předvídatelné označují řádky, které jsou ovlivněné TOP. 

**Dotaz**

    SELECT TOP 1 * 
    FROM Families f 

**Results**

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

HORNÍ lze použít s konstantní hodnotou (jak jsme ukázali výše) nebo s hodnotou proměnné použití parametrických dotazů. Další podrobnosti najdete v tématu parametrizované dotazy níže.

### <a id="Aggregates"></a>Agregační funkce
Můžete také provést agregace v `SELECT` klauzule. Agregační funkce provádět výpočet sadu hodnot a vrátí jednu hodnotu. Například následující dotaz vrátí počet rodiny dokumentů v rámci kolekce.

**Dotaz**

    SELECT COUNT(1) 
    FROM Families f 

**Results**

    [{
        "$1": 2
    }]

Můžete se taky vrátit skalární hodnota agregace pomocí `VALUE` – klíčové slovo. Například následující dotaz vrátí počet hodnot jako jediné číslo:

**Dotaz**

    SELECT VALUE COUNT(1) 
    FROM Families f 

**Results**

    [ 2 ]

Můžete také provést agregace v kombinaci s filtry. Například následující dotaz vrátí počet dokumentů s adresou v státu Washington.

**Dotaz**

    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 

**Results**

    [ 1 ]

Následující tabulka uvádí seznam podporovaných agregační funkce v rozhraní SQL API. `SUM` a `AVG` se provádí přes číselných hodnot, zatímco `COUNT`, `MIN`, a `MAX` lze provést přes čísla, řetězce, logické hodnoty a hodnoty Null. 

| Využití | Popis |
|-------|-------------|
| POČET | Vrátí počet položek ve výrazu. |
| SOUČET   | Vrátí součet všech hodnot ve výrazu. |
| MIN.   | Vrátí minimální hodnotu ve výrazu. |
| MAX.   | Vrací maximální hodnotu ve výrazu. |
| PRŮMĚR   | Vrátí průměr hodnot ve výrazu. |

Agreguje lze také provést přes výsledky iterace pole. Další informace najdete v tématu [pole iterace v dotazech](#Iteration).

> [!NOTE]
> Při použití Průzkumníku dat portálu Azure, Všimněte si, že agregace dotazy může vracet částečně agregované výsledky dotazu stránky. Sady SDK vytvoří jednu kumulativní hodnotu na všech stránkách. 
> 
> Aby bylo možné provádět dotazy agregace pomocí kódu, je nutné .NET SDK 1.12.0, .NET Core SDK 1.1.0 nebo Java SDK 1.9.5 nebo vyšší.    
>

## <a id="OrderByClause"></a>Klauzuli ORDER by
Podobně jako v ANSI SQL, můžete zahrnout volitelné klauzule Order By při dotazování. V klauzuli může zahrnovat nepovinný argument ASC nebo DESC zadat pořadí, ve kterém musí načíst výsledky.

Tady je příklad dotaz, který načte rodiny v pořadí podle název trvalé města.

**Dotaz**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city

**Results**

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

A zde uvádíme dotaz, který načte rodiny v pořadí podle data vytvoření, který je uložený jako číslo představující epoch čas, tj, uplynulý čas od 1 ledna, pod hodnotou 1970 v sekundách.

**Dotaz**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC

**Results**

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

## <a id="Advanced"></a>Pokročilé databázových koncepcí a dotazy SQL

### <a id="Iteration"></a>Iterace
Byl přidán nový konstrukce prostřednictvím **IN** – klíčové slovo v rozhraní SQL API kvůli zajištění podpory pro iterování přes pole JSON. Zdroj FROM poskytuje podporu pro iterací. Začneme v následujícím příkladu:

**Dotaz**

    SELECT * 
    FROM Families.children

**Results**  

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

Nyní Podíváme se na další dotaz, který provádí iteraci přes podřízené položky v kolekci. Poznámka: rozdíl v poli výstup. Tento příklad rozdělí `children` a vyrovná výsledky do jednoho pole.  

**Dotaz**

    SELECT * 
    FROM c IN Families.children

**Results**  

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

To dále lze filtrovat na každou položku pole, jak je znázorněno v následujícím příkladu:

**Dotaz**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Results**  

    [{
      "givenName": "Lisa"
    }]

Můžete také provést agregace přes výsledek iterace pole. Například následující dotaz vrátí počet podřízených prvků mezi všechny řady.

**Dotaz**

    SELECT COUNT(child) 
    FROM child IN Families.children

**Results**  

    [
      { 
        "$1": 3
      }
    ]

### <a id="Joins"></a>Spojení
V relační databázi je důležité potřeba připojení u tabulky. Je logické důsledkem k navrhování normalizovaný schémat. Na rozdíl od toho se zabývá rozhraní SQL API nenormalizované datový model bez schémat dokumentů. Toto je logický ekvivalent a "spojení sama na sebe".

Syntaxe, které jazyk podporuje je < from_source1 > připojit < from_source2 > připojit... Připojte < from_sourceN >. Celkově platí, tento příkaz vrátí sadu **N**- n-tice (řazené kolekce členů s **N** hodnoty). Každá řazená kolekce členů má vyprodukované všechny aliasy kolekce iterování přes jejich příslušné sady hodnot. Jinými slovy Toto je úplná smíšený produkt sad účastní spojení.

Následující příklady ukazují, jak funguje klauzuli JOIN. V následujícím příkladu výsledkem je prázdný, od smíšený produkt každého dokumentu ze zdroje a prázdnou sadou je prázdný.

**Dotaz**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Results**  

    [{
    }]


V následujícím příkladu je spojení mezi kořen dokumentu a `children` subroot. Je smíšený produkt mezi dvěma objekty JSON. Skutečnost, že je podřízených prvků pole není platná ve spojení vzhledem k tomu, že jsme se zabývají na jednom kořenovou, která je pole podřízené objekty. Proto výsledek obsahuje pouze dva výsledky, protože smíšený produkt každý dokument s poli vypočítá přesně pouze jeden dokument.

**Dotaz**

    SELECT f.id
    FROM Families f
    JOIN f.children

**Results**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


Následující příklad ukazuje konvenční připojení:

**Dotaz**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Results**

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



Nejprve si všimněte si je, že `from_source` z **připojení** klauzule je iterátor. Ano tok v takovém případě je následující:  

* Rozbalte každý podřízený element **c** v poli.
* Použít smíšený produkt s kořene dokumentu **f** s každou podřízený element **c** , byl průmětu v prvním kroku.
* Nakonec projektu kořenový objekt **f** name – vlastnost samostatně. 

První dokument (`AndersenFamily`) obsahuje pouze jeden podřízený element, takže sadu výsledků dotazu obsahuje pouze jeden objekt odpovídající do tohoto dokumentu. Druhý dokumentu (`WakefieldFamily`) obsahuje dva podřízené položky. Ano smíšený produkt vytváří samostatný objekt pro všechny podřízené, což by vedlo k dva objekty, jeden pro každou podřízenou odpovídající do tohoto dokumentu. Kořenové pole v obou tyto dokumenty jsou stejné, stejně, jako byste očekávali v smíšený produkt.

Skutečné nástroje připojení k je formulář řazených kolekcí členů z smíšený produkt tvar, který je jinak obtížné projektu. Kromě toho, jak vidíte v následujícím příkladu můžete vyfiltrovat na kombinaci řazené kolekce členů, aby se umožňuje se uživatel rozhodl podmínku splňují celkové řazené kolekce členů.

**Dotaz**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets

**Results**

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



Tento příklad představuje přirozené rozšíření v předchozím příkladu a spojí double. Ano smíšený produkt lze zobrazit jako pseudo následující kód:

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

`AndersenFamily` má jednu podřízenou, který má jednoho nebo více mazlíčků. Ano, smíšený produkt vypočítá jeden řádek (1\*1\*1) z této rodiny. WakefieldFamily ale má dva podřízené, ale pouze jednu podřízenou "Jesse" má mazlíčků. Jesse, když má dva mazlíčků. Proto smíšený produkt vypočítá 1\*1\*řádků z této rodině, 2 = 2.

V následujícím příkladu je další filtr na `pet`. Nevztahuje se na všech záznamů, kde název pet není "Stínové". Všimněte si, že jsou jsme sestavení řazenými kolekcemi členů z pole filtru na všech elementů řazené kolekce členů a projektu libovolnou kombinaci prvků. 

**Dotaz**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Results**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a id="JavaScriptIntegration"></a>Integrace jazyka JavaScript
Azure Cosmos DB poskytuje programovací model pro spouštění logiky aplikace založené na jazyce JavaScript přímo na kolekcích z hlediska uložených procedur a aktivačních událostí. To umožňuje, aby obě:

* Možnost udělat transakční operace CRUD vysoce výkonné a dotazy na dokumenty v kolekci na základě těsná integrace běhu programu JavaScript přímo v rámci databázového stroje. 
* Fyzická modelování tok řízení, proměnné rozsahu a přiřazení a integrace výjimky zpracování primitiv s databázové transakce. Další podrobnosti o podpoře Azure Cosmos DB integrace jazyka JavaScript naleznete v dokumentaci serverové programovatelnosti JavaScript.

### <a id="UserDefinedFunctions"></a>Uživatelem definované funkce (UDF)
Společně s typy již definována v tomto článku rozhraní SQL API poskytuje podporu pro uživatele definované funkce (UDF). Skalární funkce UDF zejména, jsou podporovány, kde mohou vývojáři předejte v počtu nula či více argumentů a vrácení zpět výsledku jeden argument. Každý z těchto argumentů, se kontroluje na právě platné hodnoty na JSON.  

Syntaxe příkazu SQL je rozšířeno na podporu vlastní logiky aplikace pomocí tyto funkce definované uživatelem. Funkce UDF lze registrovat pomocí rozhraní API pro SQL a pak odkazuje v rámci dotazu SQL. Ve skutečnosti UDF jsou exquisitely navrženy pro vyvolat dotazy. Jako nezbytným důsledkem této volby UDF nemají přístup k objektu kontextu, které mají jiné JavaScript typy (uložených procedur a aktivačních událostí). Vzhledem k tomu, že dotazy se spustí jen pro čtení, mohou spouštět na primární nebo na sekundární repliky. Proto UDF jsou určená ke spuštění na sekundárních replikách na rozdíl od jiných typů jazyka JavaScript.

Níže je příklad, jak se dají registrovat UDF v databázi Cosmos DB, konkrétně v rámci kolekce dokumentů.

       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  

V předchozím příkladu se vytváří UDF, jehož název je `REGEX_MATCH`. Přijímá dvou řetězcových hodnot JSON `input` a `pattern` a ověří, zda je první odpovídá vzoru zadaný ve druhém pomocí funkce string.match() jazyce JavaScript.

Tato UDF jsme teď můžete použít v dotazu v projekci. Funkce UDF musí být kvalifikovaný pomocí malá a velká písmena předponu "udf." Když je volána v rámci dotazů. 

> [!NOTE]
> Před 3/17/2015 Cosmos DB podporované UDF volání bez "udf." Předpona jako vyberte REGEX_MATCH(). Tento vzor volání je zastaralá.  
> 
> 

**Dotaz**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Results**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

UDF můžete použít také uvnitř filtr, jak je znázorněno v následujícím příkladu také kvalifikovaný pomocí "udf." Předpona:

**Dotaz**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Results**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


V podstatě UDF jsou platné skalární výrazy a mohou být používány projekce a filtry. 

Chcete-li rozšířit na výkon UDF, podíváme se na další příklad s podmíněnou logiku:

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
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);


Níže je příklad, který vykonává UDF.

**Dotaz**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f    

**Results**

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


Jako v předchozích příkladech prezentují, funkce UDF sílu jazyka JavaScript integrovat rozhraní SQL API k poskytnutí bohaté programovatelný rozhraní udělat komplexní logiku procedurální, podmíněného pomocí integrované funkce JavaScript runtime.

Rozhraní SQL API poskytuje argumenty k UDF pro každý dokument ve zdroji na aktuální fázi (klauzuli WHERE nebo klauzuli SELECT) zpracování UDF. Výsledkem je obsažena v celkové spouštěcí kanál bezproblémově. Jestliže podle vlastnosti ve UDF parametry nejsou k dispozici v hodnotě JSON, parametr se považuje za není definována a proto je volání UDF zcela přeskočeno. Podobně pokud výsledek UDF, není součástí výsledek. 

V souhrnu funkce UDF jsou vynikající aplikace udělat komplexní obchodní logiky v rámci dotazu.

### <a name="operator-evaluation"></a>Vyhodnocení – operátor
Cosmos databáze, důsledku způsobená databáze JSON nevykresluje parallels s operátory jazyka JavaScript a jeho sémantiku vyhodnocení. Při Cosmos DB pokusí zachovat sémantiku JavaScript z hlediska podporu JSON, v některých případech odchylují vyhodnocení operaci.

V rozhraní SQL API, na rozdíl od v tradiční SQL, typy hodnot, jsou často není známý teprve po načtení hodnoty z databáze. Efektivní provádění dotazů, většina operátory má požadavky na typ strict. 

Rozhraní SQL API neprovede implicitní převody, na rozdíl od jazyka JavaScript. Například dotazu jako `SELECT * FROM Person p WHERE p.Age = 21` odpovídá dokumentů, které obsahují ve vlastnosti stáří, jehož hodnota je 21. Jiného dokumentu, jejichž stáří vlastnost odpovídá řetězec "21" nebo jiných může být nekonečné variace jako "021", "21.0", "0021", "00021", nebude odpovídat atd. Jde na rozdíl od jazyka JavaScript, kde jsou implicitně převedena na čísla řetězcové hodnoty (podle operátoru, například: ==). Tato volba je zásadní pro efektivní index porovnávání rozhraní SQL API. 

## <a name="parameterized-sql-queries"></a>Parametrizované dotazy SQL
Cosmos DB podporuje dotazy s parametry vyjádřené se známými @ zápis. Parametrizované SQL poskytuje robustní zpracování a uvozovací znaky vstup uživatele brání náhodnou expozici dat prostřednictvím Injektáž SQL. 

Můžete například napsat dotaz, který přebírá příjmení a stav adresy jako parametry a potom spusťte pro různé hodnoty příjmení a stav adresy založené na vstup uživatele.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Tento požadavek potom můžete odeslat do databáze Cosmos jako parametrizovaného dotazu JSON, jako vidíte níže.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

Argument TOP se dá nastavit pomocí parametrizované dotazy, jako vidíte níže.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Hodnoty parametru může být libovolný platný kód JSON (řetězce, čísla, logické hodnoty null, dokonce i pole nebo vnořený JSON). Také bez schématu totiž Cosmos DB parametry nejsou ověřovat na libovolného typu.

## <a id="BuiltinFunctions"></a>Integrované funkce
Cosmos DB podporuje také řadu integrovaných funkcí pro běžné operace, které lze použít uvnitř dotazy jako uživatelsky definované funkce (UDF).

| Funkce skupiny          | Operace                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Matematické funkce  | ABS mezní hodnoty, EXP, FLOOR, protokolu, LOG10, POWER, KRUHOVÉ, přihlášení, SQRT, HRANATÉ, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COP, STUPŇŮ, PI, RADIÁNECH, SIN a TAN |
| Typ kontroly funkce | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED a IS_PRIMITIVE                                                           |
| Řetězcové funkce        | CONCAT, obsahuje, ENDSWITH, INDEX_OF, vlevo, délka, nižší, LTRIM, NAHRAĎTE, REPLIKOVAT, zpětného, vpravo, RTRIM, STARTSWITH, SUBSTRING a horní       |
| Funkce pole         | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH a ARRAY_SLICE                                                                                         |
| Prostorové funkce       | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID a ST_ISVALIDDETAILED                                                                           | 

Pokud aktuálně používáte uživatelem definované funkce (UDF) pro kterou integrovaná funkce je nyní k dispozici, byste měli používat odpovídající integrované funkce, má být ke spuštění rychlejší a efektivnější. 

### <a name="mathematical-functions"></a>Matematické funkce
Matematické funkce provedení výpočtu, podle vstupní hodnoty, které jsou k dispozici jako argumenty a vrátí číselnou hodnotu. Zde je tabulku podporovaných předdefinovaných matematické funkce.


| Využití | Popis |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [[ABS (num_expr)](#bk_abs) | Vrátí absolutní hodnotu (kladné) zadaný číselný výraz. |
| [CEILING (num_expr)](#bk_ceiling) | Vrátí nejmenší hodnotu, celé číslo větší než nebo rovna hodnotě zadané číselný výraz. |
| [FLOOR (num_expr)](#bk_floor) | Vrátí největší celé číslo menší než nebo rovna zadané číselný výraz. |
| [EXP (num_expr)](#bk_exp) | Vrátí exponent zadaný číselný výraz. |
| [LOG (num_expr [,base])](#bk_log) | Vrátí přirozený logaritmus zadaný číselný výraz nebo pomocí o zadaném základu logaritmus |
| [LOG10 (num_expr)](#bk_log10) | Vrátí hodnotu logaritmické základu 10 zadaný číselný výraz. |
| [ZAOKROUHLÍ (num_expr)](#bk_round) | Vrátí číselnou hodnotu, zaokrouhlí na nejbližší celé číslo. |
| [TRUNC (num_expr)](#bk_trunc) | Vrátí číselnou hodnotu, zkrácen na nejbližší celé číslo. |
| [SQRT (num_expr)](#bk_sqrt) | Vrátí druhou odmocninu čísla zadaný číselný výraz. |
| [SQUARE (num_expr)](#bk_square) | Vrátí druhou mocninu zadaný číselný výraz. |
| [POWER (num_expr, num_expr)](#bk_power) | Hodnota zadaná vrátí sílu zadaný číselný výraz. |
| [SIGN (num_expr)](#bk_sign) | Vrátí hodnotu přihlašovací (-1, 0, 1) zadaný číselný výraz. |
| [ACOS (num_expr)](#bk_acos) | Vrací úhel, v radiánech, jehož kosinus je zadaný číselný výraz. Zkratka Arkus. |
| [ASIN (num_expr)](#bk_asin) | Vrací úhel, v radiánech, jehož sinus je zadaný číselný výraz. To je také označován Arkus sinus. |
| [ATAN (num_expr)](#bk_atan) | Vrací úhel, v radiánech, jehož tangens je zadaný číselný výraz. To je také označován Arkus. |
| [ATN2 (num_expr)](#bk_atn2) | Vrací úhel, v radiánech, mezi kladné osy x a paprsek z tohoto počátku do bodu (y, x), kde x a y jsou hodnoty dvou výrazů zadaný float. |
| [COS (num_expr)](#bk_cos) | Vrací trigonometrické kosinus určeného úhlu v radiánech v zadaným výrazem. |
| [COT (num_expr)](#bk_cot) | Vrací trigonometrické kotangens zadaný úhel v radiánech v zadaný číselný výraz. |
| [DEGREES (num_expr)](#bk_degrees) | Vrací odpovídající úhel ve stupních pro úhlu uvedeného v radiánech. |
| [PI ()](#bk_pi) | Vrátí konstantní hodnotu čísla PÍ. |
| [RADIANS (num_expr)](#bk_radians) | Vrátí radiánech při zadání číselného výrazu, ve stupních, se. |
| [SIN (num_expr)](#bk_sin) | Vrací trigonometrické sinus určeného úhlu v radiánech v zadaným výrazem. |
| [TAN (num_expr)](#bk_tan) | Vrátí tangens vstupní výraz zadaný výraz. |

Například můžete spustit nyní dotazy takto:

**Dotaz**

    SELECT VALUE ABS(-4)

**Results**

    [4]

Hlavní rozdíl mezi Cosmos DB funkce ve srovnání s ANSI SQL je, že jsou navrženy fungují dobře u dat bez schématu a smíšený schématu. Například pokud máte dokument, kdy je velikost vlastnost chybí, nebo má jiné než číselné hodnoty jako "Neznámý" a potom dokument se přeskočil, místo vrátila chybu.

### <a name="type-checking-functions"></a>Typ kontroly funkce
Kontrola, zda funkce typů umožňují zkontrolujte typ výrazu v rámci dotazů SQL. Kontrola, zda funkce typu slouží k určení typu vlastnosti v rámci dokumenty za chodu, když je neznámý nebo proměnné. Zde je tabulku kontroluje funkce podporované předdefinovaný typ.

<table>
<tr>
  <td><strong>Využití</strong></td>
  <td><strong>Popis</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (výraz)</a></td>
  <td>Vrátí logickou hodnotu, která určuje, jestli je typ hodnoty pole.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (výraz)</a></td>
  <td>Vrátí logickou hodnotu udávající, pokud typ hodnoty je logická hodnota.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Vrátí logickou hodnotu, která určuje, jestli je typ hodnoty null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (výraz)</a></td>
  <td>Vrátí logickou hodnotu udávající, zda je typ hodnoty číslo.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (výraz)</a></td>
  <td>Vrátí logickou hodnotu udávající, pokud typ hodnoty je objekt JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (výraz)</a></td>
  <td>Vrátí logickou hodnotu udávající, pokud je typ hodnoty string.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (výraz)</a></td>
  <td>Vrátí logickou hodnotu udávající, pokud byla vlastnost přiřazenou hodnotu.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Vrátí logickou hodnotu, která udává, pokud je typ hodnoty řetězce, číslo, logickou hodnotu nebo hodnotu null.</td>
</tr>

</table>

Pomocí těchto funkcí, teď můžete spouštět dotazy takto:

**Dotaz**

    SELECT VALUE IS_NUMBER(-4)

**Results**

    [true]

### <a name="string-functions"></a>Řetězcové funkce
Následující skalární funkce provést operaci s vstupní hodnotu řetězce a vrátí řetězec, číselnou nebo logická hodnota. Tady je tabulku funkce integrované řetězce:

| Využití | Popis |
| --- | --- |
| [LENGTH (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |Vrátí počet znaků ze zadaného řetězcového výrazu |
| [CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |Vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcové hodnoty. |
| [SUBSTRING (str_expr, num_expr num_expr.)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |Vrátí část řetězcového výrazu. |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |Vrátí logická hodnota, která určuje zda první řetězec výraz začíná druhý |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |Vrátí logická hodnota, která určuje zda první řetězec výraz končí druhý |
| [CONTAINS (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |Vrátí logická hodnota, která určuje zda první řetězec výraz obsahuje druhý. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |Vrátí počáteční pozici prvního výskytu druhý řetězec výrazu v rámci první zadaného řetězcového výrazu nebo -1, pokud není nalezen řetězec. |
| [LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |Vrátí levé části řetězec s zadaný počet znaků. |
| [RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |Vrátí pravou část řetězec s zadaný počet znaků. |
| [LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |Vrací výraz řetězce po ho odebere úvodní mezery. |
| [RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |Vrací výraz řetězce po zkracování všechny koncové mezery. |
| [LOWER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |Vrací výraz řetězce po převodu dat velké písmeno na malá písmena. |
| [HORNÍ (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |Vrací výraz řetězce po převodu dat malé písmeno na velká písmena. |
| [NAHRAĎTE (str_expr, str_expr str_expr.)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |Nahradí všechny výskyty zadaná řetězcová hodnota s jinou hodnotou řetězce. |
| [REPLIKOVAT (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) |Opakuje hodnotu řetězce zadaného počtu opakování. |
| [REVERSE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |Vrátí obráceném pořadí řetězcovou hodnotu. |

Pomocí těchto funkcí, můžete nyní spustit dotazy podobně jako tento. Například se můžete vrátit název rodiny na velká písmena následujícím způsobem:

**Dotaz**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Results**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Nebo zřetězení řetězců jako v tomto příkladu:

**Dotaz**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Results**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Funkce řetězce mohou sloužit také v klauzuli WHERE chcete filtrovat výsledky, jako v následujícím příkladu:

**Dotaz**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Results**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Funkce pole
Následující skalární funkce provedení operace hodnota vstupní pole a vrátí číselnou, logická hodnota nebo pole hodnota. Zde je také tabulka předdefinovaných pole funkcí:

| Využití | Popis |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |Vrátí počet prvků výrazu zadané pole. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Vrátí pole, které je výsledkem zřetězení dvě nebo více hodnot pole. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Vrátí logickou hodnotu udávající, zda pole obsahuje zadanou hodnotu. Můžete zadat, pokud je shoda celé nebo jeho část. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Vrátí část výraz pole. |

Funkce pole můžete použít k manipulaci s pole v rámci JSON. Tady je příklad dotaz, který vrátí všechny dokumenty, kde jeden z rodičů je "Každý s každým Wakefieldů". 

**Dotaz**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Můžete zadat částečné fragment pro párování elementů v rámci pole. Následující dotaz hledá všechny nadřazené objekty s `givenName` z `Robin`.

**Dotaz**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)

**Results**

    [{
      "id": "WakefieldFamily"
    }]


Zde je další příklad používající ARRAY_LENGTH získat počet podřízených za rodiny.

**Dotaz**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Results**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Prostorové funkce
Cosmos DB podporuje následující předdefinované funkce otevřete geoprostorové Consortium (OGC) pro geoprostorové dotazování. 

<table>
<tr>
  <td><strong>Využití</strong></td>
  <td><strong>Popis</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Vrací vzdálenost mezi dvěma GeoJSON bodu, mnohoúhelníku nebo LineString výrazy.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Vrací výraz logická hodnota určující, zda je první objekt GeoJSON (bod, mnohoúhelníku nebo LineString) je v rámci druhý objekt GeoJSON (bod, mnohoúhelníku nebo LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Vrátí hodnotu označující, zda dva zadané GeoJSON objekty (bod, mnohoúhelníku nebo LineString) intersect logický výraz.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Vrátí logickou hodnotu udávající, zda je zadaný výraz GeoJSON bodu, mnohoúhelníku nebo LineString platný.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Vrátí hodnotu hodnotu JSON obsahující logickou hodnotu, pokud zadaný výraz GeoJSON bodu, mnohoúhelníku nebo LineString je platný a pokud neplatný, dále z důvodu jako hodnotu řetězce.</td>
</tr>
</table>

Prostorové funkcí lze provádět dotazy blízkosti proti prostorová data. Tady je příklad dotaz, který vrátí všechny rodiny dokumenty, které jsou v rámci 30 km v zadaném umístění pomocí předdefinované funkci ST_DISTANCE. 

**Dotaz**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Další informace o podporovaných geoprostorové v Cosmos DB, najdete v tématu [práci s daty geoprostorové v Azure Cosmos DB](geospatial.md). Který zabalí prostorových funkce a syntaxe SQL pro Cosmos DB. Nyní Podívejme se na tom, jak LINQ dotazování funguje a jak komunikuje se syntaxí jsme viděli dosavadní.

## <a id="Linq"></a>Technologie LINQ to SQL API
LINQ je programovací model rozhraní .NET, která vyjadřuje výpočetní jako dotazy na datové proudy objektů. Cosmos DB poskytuje knihovnu klienta pro rozhraní s dotazy LINQ usnadněním převod mezi objekty JSON a rozhraní .NET a mapování z určité podmnožiny dotazů LINQ dotazy Cosmos DB. 

Následující obrázek ukazuje architekturu podporu dotazů LINQ pomocí Cosmos DB.  Pomocí klienta aplikace Cosmos DB vývojáři můžou vytvářet **IQueryable** objekt, který dotazuje přímo poskytovatele dotazu Cosmos DB, který pak překládá dotaz LINQ do dotazu Cosmos DB. Dotaz je předána na server Cosmos databáze k načtení sady výsledků ve formátu JSON. Do vrácených výsledků se deserializovat do datového proudu objektů .NET na straně klienta.

![Architektura podporu dotazů LINQ pomocí rozhraní SQL API - syntaxe SQL, JSON dotazovací jazyk, databázových koncepcí a dotazy SQL][1]

### <a name="net-and-json-mapping"></a>Rozhraní .NET a mapování JSON
Mapování mezi objekty .NET a dokumenty JSON přirozené – každé datové pole, člen je namapovaný na objekt JSON, kde název pole je namapovaná na část "klíč" objektu a části "value" je rekurzivní namapované na část hodnoty objektu. Podívejte se na následující příklad: rodiny objekt vytvořený je namapována na dokumentu JSON, jak je uvedeno níže. A naopak a dokumentu JSON je mapována na objekt .NET.

**C# – třída**

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


**JSON**  

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



### <a name="linq-to-sql-translation"></a>Technologie LINQ to SQL překlad
Zprostředkovatel dotazu Cosmos DB provede nejlepší úsilí mapování z dotazu LINQ do dotazu Cosmos DB SQL. V následující popis předpokládáme, že program pro čtení má základní znalosti o LINQ.

Nejprve pro typ systému, budeme podporovat všechny JSON primitivní typy – číselnými typy, logická hodnota, string a hodnotu null. Jsou podporovány pouze tyto typy JSON. Jsou podporovány následující skalární výrazy.

* Konstantní hodnoty – patří konstantní hodnoty primitivní datové typy v době, kdy je vyhodnocován dotaz.
* Vlastnost nebo pole indexu výrazy – tyto výrazy odkazovat na vlastnost objekt nebo pole elementu.
  
     rodiny. ID;    Family.Children[0].familyName;    Family.Children[0].Grade;    Family.Children[n].Grade; n je proměnná typu int.
* Aritmetických výrazech – jedná se o běžné aritmetických výrazech na číselné a logické hodnoty. Úplný seznam najdete v části specifikace SQL.
  
     2 * family.children[0].grade;    x a y;
* Výraz pro porovnání řetězce - patří porovnávání řetězcovou hodnotu na hodnotu konstantní řetězec.  
  
     mother.familyName == "Smith";    child.givenName == s; s je proměnná řetězce
* Objekt nebo pole vytvoření výrazu - tyto výrazy návratový typ složené hodnoty nebo anonymní typ objekt nebo pole tyto objekty. Tyto hodnoty mohou být použity.
  
     novou nadřazenou položku {familyName = "Smith", givenName = "Jan"}; nové {nejprve = 1, druhý = 2}; anonymní typ s dvě pole              
     New [] int {3, child.grade, 5};

### <a id="SupportedLinqOperators"></a>Seznam podporovaných operátory LINQ
Tady je seznam podporovaných LINQ operátory ve zprostředkovateli LINQ součástí SQL .NET SDK.

* **Vyberte**: projekce převede vyberte SQL, včetně vytváření objektů
* **Kde**: filtry nepřeloží na SQL kde a podporovat překlad mezi & &, || a! SQL operátorů
* **Označit více**: umožňuje unwinding polí pro klauzuli SQL JOIN. Je možné řetězec nebo vnoření výrazy k filtrování v rámci prvků pole
* **OrderBy a OrderByDescending**: přeloží na order pořadí
* **Počet**, **součet**, **Min**, **maximální**, a **průměrná** operátory pro agregaci a jejich ekvivalenty asynchronní **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, a **AverageAsync**.
* **CompareTo**: překládá do rozsahu porovnání. Běžně používané pro řetězce vzhledem k tomu, že nejste porovnatelný v rozhraní .NET
* **Trvat**: překládá do horní části SQL pro omezení výsledků dotazu
* **Matematické funkce**: podporuje překlad z. Asin Abs Acos, je NET, Atan, Ceiling Cos, Exp, Floor, protokolu, Log10, Pow, kruhové, přihlášení, Sin, Sqrt, Tan, Truncate na ekvivalentní integrované funkce SQL.
* **Funkce pro řetězce**: podporuje překlad z. NET na Concat, obsahuje, EndsWith, IndexOf, počet, ToLower, TrimStart –, nahraďte, zpětného, TrimEnd, StartsWith, SubString, ToUpper na ekvivalentní integrované funkce SQL.
* **Pole funkce**: podporuje překlad z. NET na Concat, obsahuje a počet, který má ekvivalentní integrované funkce SQL.
* **Funkce rozšíření geoprostorové**: podporuje překlad z metod se zakázaným inzerováním vzdálenost v rámci, IsValid a IsValidDetailed na ekvivalentní integrované funkce SQL.
* **Uživatelem definované funkce rozšíření funkce**: podporuje překlad z metody se zakázaným inzerováním UserDefinedFunctionProvider.Invoke odpovídající uživatelem definované funkce.
* **Různé**: podporuje překlad coalesce a podmíněné operátory. Může překládat obsahuje řetězec obsahuje, ARRAY_CONTAINS nebo v SQL v závislosti na kontextu.

### <a name="sql-query-operators"></a>Operátory dotazu SQL
Zde jsou některé příklady, které ilustrují, jak některé standardní operátory dotazu LINQ přeložit dolů Cosmos DB dotazy.

#### <a name="select-operator"></a>Select – operátor
Syntaxe je `input.Select(x => f(x))`, kde `f` je skalární výraz.

**Výraz lambda LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Výraz lambda LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Výraz lambda LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>Označit více – operátor
Syntaxe je `input.SelectMany(x => f(x))`, kde `f` se skalární výraz, který vrátí typ kolekce.

**Výraz lambda LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Kde – operátor
Syntaxe je `input.Where(x => f(x))`, kde `f` je skalární výraz, který vrací logickou hodnotu.

**Výraz lambda LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Výraz lambda LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Složené příkazy jazyka SQL
Výše uvedené operátory musí být komponovaná k vytvoření více výkonných dotazů. Vzhledem k tomu, že Cosmos DB podporuje vnořené kolekcí, složení můžete být zřetězen nebo vnořený.

#### <a name="concatenation"></a>Zřetězení
Syntaxe je `input(.|.SelectMany())(.Select()|.Where())*`. Zřetězené dotaz můžete spustit v volitelný `SelectMany` dotazu následuje více `Select` nebo `Where` operátory.

**Výraz lambda LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Výraz lambda LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Výraz lambda LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Výraz lambda LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Vnoření
Syntaxe je `input.SelectMany(x=>x.Q())` kde Q je `Select`, `SelectMany`, nebo `Where` operátor.

Pro každý prvek vnější kolekce se v vnořený dotaz, použít vnitřní dotaz. Jednou z důležitou součást je, že vnitřní dotaz mohou odkazovat na pole elementů v kolekci vnější jako spojení sama na sebe.

**Výraz lambda LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Výraz lambda LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Výraz lambda LINQ**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a id="ExecutingSqlQueries"></a>Provádění dotazů SQL
Cosmos DB zveřejňuje prostředky přes rozhraní REST API, kterou lze volat v jakémkoli jazyce schopném zasílat požadavky HTTP/HTTPS. Cosmos DB dále nabízí programovací knihovny pro několik oblíbených jazyků, jako je rozhraní .NET, Node.js, JavaScript a Python. Rozhraní REST API různých knihoven podporují a dotazování pomocí SQL. .NET SDK podporuje LINQ dotazování kromě SQL.

Následující příklady ukazují, jak vytvořit dotaz a odešlete ji proti Cosmos DB databázového účtu.

### <a id="RestAPI"></a>REST API
Cosmos DB nabízí otevřete RESTful programovací model přes protokol HTTP. Databáze účtů se dá zřídit pomocí předplatného Azure. Model prostředků Cosmos DB obsahuje sadu prostředků v rámci účtu databáze, z nichž každý je adresovatelné logické a stabilní identifikátoru URI. Sadu prostředků se označuje jako informačního kanálu v tomto dokumentu. Databázový účet se skládá ze sady databází, každá obsahuje několik kolekcí, každý z které naopak obsahovat dokumenty, funkce UDF a další typy prostředků.

Základní interakce model pomocí těchto prostředků je pomocí příkazů HTTP GET, PUT, POST a odstranit pomocí jejich standardní překladu. Příkaz POST se používá pro vytvoření nového prostředku, pro spuštění uložené procedury nebo pro zadání dotazu Cosmos DB. Dotazy jsou vždy jen pro čtení operací s žádné vedlejší účinky.

Následující příklady ukazují POST pro dotaz rozhraní SQL API směřovaný na kolekce obsahující dva ukázkové dokumenty, že jsme si přečetli dosavadní. Dotaz je jednoduchý filtr na název vlastnosti JSON. Všimněte si použití `x-ms-documentdb-isquery` a Content-Type: `application/query+json` hlavičky k označení, že operace je dotazu.

**Požadavek**

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


**Results**

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


Druhý příklad ukazuje komplexnější dotaz, který vrátí více výsledků z spojení.

**Požadavek**

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


**Results**

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


Pokud výsledku dotazu se nemůže vejít do jedné stránky s výsledky, pak rozhraní REST API vrátí token pokračování prostřednictvím `x-ms-continuation-token` hlavičky odpovědi. Klienty můžete stránkování výsledků zahrnutím záhlaví v následných výsledky. Počet výsledků na stránce lze také řídit prostřednictvím `x-ms-max-item-count` číslo záhlaví. Pokud zadaný dotaz obsahuje agregační funkci jako `COUNT`, pak stránce dotaz může vrátit hodnotu částečně agregované přes stránky s výsledky. Klienti musí provést druhé úrovně agregace nad těmito výsledky. Chcete-li vytvořit konečných výsledků, například, součet přes počty vrátil na jednotlivých stránkách vrátit celkového počtu.

Ke správě zásad konzistence dat pro dotazy, použijte `x-ms-consistency-level` záhlaví jako všechny požadavky REST API. Konzistence typu relace, je potřeba také odezvu na nejnovější `x-ms-session-token` hlavička Cookie v dotazu požadavku. Zásady indexování dotazované kolekce můžete ovlivnit taky konzistence výsledků dotazu. S výchozí nastavení zásady indexování, pro kolekce index je vždy aktuální pomocí obsahu dokumentu a výsledky dotazu odpovídat konzistence zvolené pro data. Pokud k Lazy je zmírnit zásady indexování, dotazy mohou vracet zastaralé výsledky. Další informace najdete v tématu [úrovně konzistence databáze Azure Cosmos][consistency-levels].

Pokud nakonfigurované zásady indexování na kolekce nepodporuje zadaný dotaz, vrátí server Azure Cosmos DB 400 "Chybný požadavek". Se vrátí pro dotazy na rozsah pro cesty, které jsou nakonfigurované pro vyhledávání hodnoty hash (rovnosti) a cesty explicitně vyloučená z indexování. `x-ms-documentdb-query-enable-scan` Záhlaví lze povolit dotazu, který chcete provést kontrolu, když indexu není k dispozici.

Podrobné metriky můžete získat na spuštění dotazu nastavením `x-ms-documentdb-populatequerymetrics` hlavičky k `True`. Další informace najdete v tématu [metriky dotazů SQL pro Azure Cosmos DB](sql-api-sql-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET) SDK
.NET SDK podporuje LINQ a SQL dotazování. Následující příklad ukazuje, jak k provedení dotazu jednoduchý filtr zavedená dříve v tomto dokumentu.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Tato ukázka porovná dvě vlastnosti rovnosti v rámci každého dokumentu a používá anonymní projekce. 

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Další příklad ukazuje spojení, vyjádřit pomocí LINQ označit více.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
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
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



Klient .NET automaticky iteruje všechny stránky výsledků dotazu v blocích foreach, jak je uvedeno výše. Možnosti dotazu byla zavedená v části REST API jsou také k dispozici v pomocí .NET SDK `FeedOptions` a `FeedResponse` třídy v metodě CreateDocumentQuery. Počet stránek se dá řídit pomocí `MaxItemCount` nastavení. 

Můžete také explicitně řídit stránkování tak, že vytvoříte `IDocumentQueryable` pomocí `IQueryable` objekt, pak načtením` ResponseContinuationToken` hodnot a jejich předání zpět jako `RequestContinuationToken` v `FeedOptions`. `EnableScanInQuery` lze nastavit pro povolení kontroly, pokud dotaz nemůže být podporována nakonfigurované zásady indexování. Pro dělené kolekce, můžete použít `PartitionKey` ke spouštění dotazu na jednoho oddílu (i když Cosmos DB může automaticky extrahovat to z text dotazu), a `EnableCrossPartitionQuery` ke spouštění dotazů, které může být nutné ke spuštění s více oddílů. 

Odkazovat na [Azure Cosmos DB .NET ukázky](https://github.com/Azure/azure-documentdb-net) pro další ukázky obsahující dotazy. 

### <a id="JavaScriptServerSideApi"></a>Rozhraní API jazyka JavaScript na straně serveru
Cosmos DB poskytuje programovací model pro spouštění logiky aplikace založené na jazyce JavaScript přímo na kolekce pomocí uložených procedur a aktivačních událostí. JavaScript logiku registrované na úrovni kolekce potom můžou provádět databázové operace na operace s dokumenty dané kolekce. Tyto operace jsou zabalená vedlejším ACID transakcí.

Následující příklad ukazuje, jak lze pomocí dokumenty dotazu v rozhraní API serveru JavaScript na dotazy z uvnitř uložené procedury a triggery.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a id="References"></a>Odkazy
1. [Úvod do Azure Cosmos DB][introduction]
2. [Azure Cosmos DB SQL specifikace](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Ukázek Azure DB Cosmos rozhraní .NET](https://github.com/Azure/azure-documentdb-net)
4. [Úrovně konzistence databáze Azure Cosmos][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. Specifikace jazyka JavaScript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9. Techniky dotazu vyhodnocení pro velké databáze [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Zpracování v systémech paralelní relační databáze, stiskněte společnosti IEEE počítače, 1994 dotazů
11. Logická jednotka, Ooi, Tan, zpracování v systémech paralelní relační databáze, stiskněte společnosti IEEE počítače, 1994 dotazů.
12. Olston Kryštof, Robert Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: není tak cizí jazyk pro zpracování dat, SIGMOD 2008.
13. G. Graefe. Cascades architektura pro optimalizaci dotazu. Eng. IEEE dat Bull., 18(3): 1995.

[1]: ./media/sql-api-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
