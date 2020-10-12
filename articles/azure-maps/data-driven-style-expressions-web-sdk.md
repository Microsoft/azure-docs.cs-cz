---
title: Výrazy stylu řízené daty v sadě Azure Maps Web SDK | Mapy Microsoft Azure
description: Přečtěte si o výrazech stylu založených na datech. Informace o tom, jak pomocí těchto výrazů v sadě Azure Maps Web SDK upravovat styly v mapách.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 539145836849bb66bcf1f12a97ea405fe84c47bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311372"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Výrazy stylu řízené daty (webová sada SDK)

Výrazy umožňují použít obchodní logiku pro stylování možností, které sledují vlastnosti definované v jednotlivých tvarech ve zdroji dat. Výrazy mohou filtrovat data ve zdroji dat nebo vrstvě. Výrazy mohou být tvořeny podmíněnou logikou, například if-statements. A lze je použít k manipulaci s daty pomocí: operátory řetězce, logické operátory a matematické operátory.

Styly řízené daty omezují množství kódu potřebného k implementaci obchodní logiky kolem stylu. Při použití s vrstvami jsou výrazy vyhodnocovány v době vykreslování v samostatném vlákně. Tato funkce poskytuje vyšší výkon v porovnání s hodnocením obchodní logiky ve vlákně uživatelského rozhraní.

Toto video poskytuje přehled stylů řízených daty v sadě Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Výrazy jsou reprezentovány jako pole JSON. Prvním prvkem výrazu v poli je řetězec, který určuje název operátoru výrazu. Například "+" nebo "Case". Další prvky (pokud existují) jsou argumenty výrazu. Každý argument je buď hodnota literálu (řetězec, číslo, logická hodnota nebo `null` ), nebo jiné pole výrazu. Následující pseudokódu definuje základní strukturu výrazu. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Sada Azure Maps Web SDK podporuje mnoho typů výrazů. Výrazy lze použít na vlastní nebo v kombinaci s jinými výrazy.

| Typ výrazů | Description |
|---------------------|-------------|
| [Agregační výraz](#aggregate-expression) | Výraz definující výpočet, který je zpracován prostřednictvím sady dat a lze jej použít s `clusterProperties` možností `DataSource` . |
| [Logické výrazy](#boolean-expressions) | Logické výrazy poskytují sadu logických výrazů operátorů pro vyhodnocení logických porovnání. |
| [Výrazy barev](#color-expressions) | Výrazy s barvami usnadňují vytváření a manipulaci s hodnotami barev. |
| [Podmíněné výrazy](#conditional-expressions) | Podmíněné výrazy poskytují operace logiky, které jsou například if-statements. |
| [Datové výrazy](#data-expressions) | Poskytuje přístup k datům vlastností ve funkci. |
| [Interpolovat a krokovat výrazy](#interpolate-and-step-expressions) | Výrazy interpolování a Step lze použít k výpočtu hodnot podél interpolované křivky nebo funkce kroku. |
| [Výrazy specifické pro vrstvu](#layer-specific-expressions) | Speciální výrazy, které platí pouze pro jednu vrstvu. |
| [Matematické výrazy](#math-expressions) | Poskytuje matematické operátory pro provádění výpočtů řízených daty v rámci rozhraní Expression Framework. |
| [Výrazy operátorů řetězce](#string-operator-expressions) | Výrazy operátoru řetězce provádějí operace převodu na řetězcích, jako je zřetězení a převod případu. |
| [Výrazy typu](#type-expressions) | Výrazy typu poskytují nástroje pro testování a převod různých typů dat, jako jsou řetězce, čísla a logické hodnoty. |
| [Výrazy vazeb proměnných](#variable-binding-expressions) | Výrazy vazeb proměnných ukládají výsledky výpočtu do proměnné a odkazují jinde ve výrazu víckrát bez nutnosti přepočítat uloženou hodnotu. |
| [Výraz lupy](#zoom-expression) | Načte aktuální úroveň přiblížení mapy v době vykreslování. |

Všechny příklady v tomto dokumentu používají následující funkci k předvedení různých způsobů, jak lze použít různé typy výrazů. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red",
        "abcArray": ['a', 'b', 'c'],
        "array2d": [['a', 'b'], ['x', 'y']],
        "_style": {
            "fillColor": 'red'
        }
    }
}
```

## <a name="data-expressions"></a>Datové výrazy

Datové výrazy poskytují přístup k datům vlastností ve funkci. 

| Výraz | Návratový typ | Description |
|------------|-------------|-------------|
| `['at', number, array]` | object | Načte položku z pole. |
| `['geometry-type']` | řetězec | Získá typ geometrie funkce: Point, MultiPoint, LineString, MultiLineString, mnohoúhelník, promnohoúhelník. |
| `['get', string]` | value | Získá hodnotu vlastnosti z vlastností aktuální funkce. Vrátí hodnotu null, pokud chybí požadovaná vlastnost. |
| `['get', string, object]` | value | Získá hodnotu vlastnosti z vlastností poskytnutého objektu. Vrátí hodnotu null, pokud chybí požadovaná vlastnost. |
| `['has', string]` | boolean | Určuje, zda vlastnosti funkce mají zadanou vlastnost. |
| `['has', string, object]` | boolean | Určuje, zda vlastnosti objektu mají zadanou vlastnost. |
| `['id']` | value | Získá ID funkce, pokud má jednu. |
| `['length', string | array]` | číslo | Získá délku řetězce nebo pole. |
| `['in', boolean | string | number, array]` | boolean | Určuje, jestli položka existuje v poli. |
| `['in', substring, string]` | boolean | Určuje, zda podřetězec existuje v řetězci. |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | číslo | Vrátí první pozici, kde lze najít položku v poli nebo podřetězec v řetězci, nebo `-1` Pokud nelze nalézt vstup. Přijme volitelný index z místa, kde začíná vyhledávání. |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | `string`\|pole | Vrátí položku z pole nebo podřetězce z řetězce ze zadaného počátečního indexu nebo mezi počátečním indexem a koncovým indexem, pokud je nastavena. Vrácená hodnota je včetně počátečního indexu, ale ne koncového indexu. |

**Příklady**

K vlastnostem funkce lze získat přímý pøístup ve výrazu pomocí `get` výrazu. V tomto příkladu je použita hodnota funkce "zoneColor", která určuje vlastnost Color pro bublinovou vrstvu. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Výše uvedený příklad bude fungovat správně, pokud všechny funkce bodu mají `zoneColor` vlastnost. Pokud ne, barva bude nejspíš přechodná na "Black". Chcete-li upravit záložní barvu, použijte `case` výraz v kombinaci s `has` výrazem k ověření, zda vlastnost existuje. Pokud vlastnost neexistuje, vrátí se záložní barva.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Vrstvy bublinových a symbolů budou ve výchozím nastavení vykreslovat souřadnice všech tvarů ve zdroji dat. Toto chování může zvýraznit vrcholy mnohoúhelníku nebo čáry. `filter`Možnost vrstvy lze použít k omezení typu geometrie funkcí, které vykresluje, pomocí `['geometry-type']` výrazu v rámci logického výrazu. Následující příklad omezuje bublinovou vrstvu tak, aby `Point` byly vykresleny pouze funkce.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Následující příklad umožňuje vykreslení obou `Point` `MultiPoint` funkcí a. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Podobně, obrys mnohoúhelníků se vykreslí do vrstev čar. Chcete-li toto chování zakázat v linkové vrstvě, přidejte filtr, který povoluje `LineString` pouze `MultiLineString` funkce a.  

Tady jsou některé další příklady použití datových výrazů:

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>Matematické výrazy

Matematické výrazy poskytují matematické operátory pro provádění výpočtů řízených daty v rámci rozhraní Expression Framework.

| Výraz | Návratový typ | Description |
|------------|-------------|-------------|
| `['+', number, number, …]` | číslo | Vypočítá součet zadaných čísel. |
| `['-', number]` | číslo | Odečte 0 zadaným číslem. |
| `['-', number, number]` | číslo | Odečte první čísla o druhé číslo. |
| `['*', number, number, …]` | číslo | Vynásobí zadaná čísla dohromady. |
| `['/', number, number]` | číslo | Vydělí první číslo druhým číslem. |
| `['%', number, number]` | číslo | Vypočítá zbytek při dělení prvního čísla druhým číslem. |
| `['^', number, number]` | číslo | Vypočítá hodnotu první hodnoty vyvolanou mocninou druhého čísla. |
| `['abs', number]` | číslo | Vypočítá absolutní hodnotu zadaného čísla. |
| `['acos', number]` | číslo | Vypočítá Arkus kosinus zadaného čísla. |
| `['asin', number]` | číslo | Vypočítá Arkus sinus zadaného čísla. |
| `['atan', number]` | číslo | Vypočítá arkustangens zadaného čísla. |
| `['ceil', number]` | číslo | Zaokrouhlí číslo nahoru na nejbližší celé číslo. |
| `['cos', number]` | číslo | Vypočítá cos zadaného čísla. |
| `['e']` | číslo | Vrátí matematickou konstantu `e` . |
| `['floor', number]` | číslo | Zaokrouhlí číslo dolů na předchozí celé číslo. |
| `['ln', number]` | číslo | Vypočítá přirozený logaritmus určeného čísla. |
| `['ln2']` | číslo | Vrátí matematickou konstantu `ln(2)` . |
| `['log10', number]` | číslo | Vypočítá dekadický logaritmus zadaného čísla. |
| `['log2', number]` | číslo | Vypočítá dekadický logaritmus zadaného čísla. |
| `['max', number, number, …]` | číslo | Vypočítá maximální počet v zadané sadě čísel. |
| `['min', number, number, …]` | číslo | Vypočítá minimální číslo v zadané sadě čísel. |
| `['pi']` | číslo | Vrátí matematickou konstantu `PI` . |
| `['round', number]` | číslo | Zaokrouhlí číslo na nejbližší celé číslo. Hodnoty v polovině se zaokrouhlují směrem od nuly. Například `['round', -1.5]` vyhodnotí na-2. |
| `['sin', number]` | číslo | Vypočítá sinus zadaného čísla. |
| `['sqrt', number]` | číslo | Vypočítá druhou odmocninu určeného čísla. |
| `['tan', number]` | číslo | Vypočítá tangens zadaného čísla. |

## <a name="aggregate-expression"></a>Agregační výraz

Agregační výraz definuje výpočet, který je zpracován přes sadu dat a lze jej použít s `clusterProperties` možností `DataSource` . Výstup těchto výrazů musí být číslo nebo logická hodnota. 

Agregační výraz přebírá tři hodnoty: hodnotu operátoru a počáteční hodnotu a výraz pro načtení vlastnosti z každé funkce v datech pro použití agregační operace na. Tento výraz má následující formát:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator: funkce výrazu, která se pak použije na proti všem hodnotám vypočítaným `mapExpression` pro každý bod v clusteru. Podporované operátory: 
    - Pro čísla: `+` , `*` , `max` , `min`
    - Pro logické hodnoty: `all` , `any`
- initialValue: počáteční hodnota, ve které je agregována první Počítaná hodnota.
- mapExpression: výraz, který se aplikuje na každý bod v datové sadě.

**Příklady**

Pokud všechny funkce v datové sadě mají `revenue` vlastnost, která je číslo. Pak lze vypočítat celkový výnos všech bodů v clusteru, které jsou vytvořeny ze sady dat. Tento výpočet se provádí pomocí následujícího agregačního výrazu: `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Logické výrazy

Logické výrazy poskytují sadu logických výrazů operátorů pro vyhodnocení logických porovnání.

Při porovnávání hodnot je porovnání striktně typované. Hodnoty různých typů jsou vždy považovány za nerovné. Případy, kde se označují, že typy jsou odlišné v době analýzy, jsou považovány za neplatné a vytvoří chybu analýzy. 

| Výraz | Návratový typ | Description |
|------------|-------------|-------------|
| `['!', boolean]` | boolean | Logická negace. Vrátí `true` , zda je vstup `false` , a `false` Pokud je vstup `true` . |
| `['!=', value, value]` | boolean | Vrátí `true` , zda vstupní hodnoty nejsou stejné, `false` jinak. |
| `['<', value, value]` | boolean | Vrátí `true` , zda je první vstup striktně menší než druhý, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `['<=', value, value]` | boolean | Vrátí `true` , zda je první vstup menší nebo roven druhému, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `['==', value, value]` | boolean | Vrátí `true` , zda jsou vstupní hodnoty stejné, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `['>', value, value]` | boolean | Vrátí `true` , zda je první vstup striktně větší než druhý, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `['>=' value, value]` | boolean | Vrátí `true` , zda je první zadání větší než nebo rovno druhému, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `['all', boolean, boolean, …]` | boolean | Vrátí `true` , zda jsou všechny vstupy `true` , `false` jinak. |
| `['any', boolean, boolean, …]` | boolean | Vrátí `true` , zda je některý ze vstupů `true` , `false` jinak. |

## <a name="conditional-expressions"></a>Podmíněné výrazy

Podmíněné výrazy poskytují operace logiky, které jsou například if-statements.

Následující výrazy provádějí Podmíněné logické operace na vstupních datech. Například `case` výraz poskytuje logiku "if/then/else", zatímco `match` výraz je jako "Switch-Statement". 

### <a name="case-expression"></a>Výraz Case

`case`Výraz je typ podmíněného výrazu, který poskytuje logiku "if/then/else". Tento typ kroků výrazu se seznamem logických podmínek. Vrátí výstupní hodnotu první logické podmínky pro vyhodnocení na hodnotu true.

Následující pseudokódu definuje strukturu `case` výrazu. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Příklad**

Následující příklad provádí v rámci různých logických podmínek, dokud nenajde ten, který je vyhodnocen jako `true` a následně vrací přidruženou hodnotu. Pokud se vyhodnotí žádná logická podmínka `true` , vrátí se záložní hodnota. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Výraz shody

`match`Výraz je typ podmíněného výrazu, který poskytuje příkaz typu Switch-Logic. Vstup může být libovolný výraz `['get', 'entityType']` , například, který vrátí řetězec nebo číslo. Každý popisek musí být buď hodnota jednoho literálu, nebo pole hodnot literálu, jejichž hodnoty musí být všechny řetězce nebo všechna čísla. Vstup se shoduje, pokud kterákoli z hodnot v poli odpovídá. Každý popisek musí být jedinečný. Pokud typ vstupu neodpovídá typu popisků, bude výsledkem záložní hodnota.

Následující pseudokódu definuje strukturu `match` výrazu. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Příklady**

Následující příklad prohlíží `entityType` vlastnost bodu v bublinové vrstvě vyhledává shodu. Pokud najde shodu, je vrácena zadaná hodnota nebo vrátí záložní hodnotu.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

Následující příklad používá pole k vypsání sady popisků, které by měly vracet stejnou hodnotu. Tento přístup je mnohem efektivnější než uvedení každého popisku jednotlivě. V takovém případě, pokud `entityType` je vlastnost "restaurace" nebo "grocery_store", bude vrácena barva "Red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

Následující příklad používá výraz shody k provedení filtru typu "in Array" nebo "Array Contains". V tomto případě výraz filtruje data s hodnotou ID, která je v seznamu povolených ID. Při použití výrazů s filtry musí být výsledkem logická hodnota.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Výraz COALESCE

`coalesce`Kroky výrazu pomocí sady výrazů, dokud není získána první hodnota, která není null, a vrátí tuto hodnotu. 

Následující pseudokódu definuje strukturu `coalesce` výrazu. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Příklad**

Následující příklad používá `coalesce` výraz pro nastavení `textField` Možnosti pro vrstvu symbolů. Pokud `title` vlastnost ve funkci chybí nebo je nastavená na `null` , výraz se pak pokusí vyhledat `subtitle` vlastnost, pokud chybí, nebo se `null` vrátí do prázdného řetězce. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

Následující příklad používá `coalesce` výraz pro načtení první dostupné ikony obrázku, která je k dispozici v spriti mapy ze seznamu zadaných názvů obrázků.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Výrazy typu

Výrazy typu poskytují nástroje pro testování a převod různých typů dat, jako jsou řetězce, čísla a logické hodnoty.

| Výraz | Návratový typ | Description |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | array – \| objekt | Vrátí literálovou hodnotu pole nebo objektu. Tento výraz použijte k zabránění vyhodnocení pole nebo objektu jako výrazu. To je nezbytné, pokud musí být pole nebo objekt vráceny výrazem. |
| `['image', string]` | řetězec | Kontroluje, zda je zadané ID obrázku načteno do Sprite obrázku mapy. Pokud je, vrátí se ID, jinak se vrátí hodnota null. |
| `['to-boolean', value]` | boolean | Převede vstupní hodnotu na logickou hodnotu. Výsledkem je, že `false` vstup je prázdný řetězec,,, `0` `false` nebo, `null` `NaN` v opačném případě `true` . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Převede vstupní hodnotu na barvu. Pokud je zadáno více hodnot, je každá z nich vyhodnocována v pořadí, dokud nebude získán první úspěšný převod. Pokud žádný ze vstupů nelze převést, je výraz chybou. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | číslo | Pokud je to možné, převede vstupní hodnotu na číslo. Pokud je vstup `null` nebo `false` , výsledkem je 0. Pokud je vstup `true` , výsledkem je 1. Pokud je vstup řetězcem, je převeden na číslo pomocí funkce řetězce [tonumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) ve specifikaci jazyka ECMAScript. Pokud je zadáno více hodnot, je každá z nich vyhodnocována v pořadí, dokud nebude získán první úspěšný převod. Pokud žádný ze vstupů nelze převést, je výraz chybou. |
| `['to-string', value]` | řetězec | Převede vstupní hodnotu na řetězec. Pokud je vstup `null` , výsledkem je `""` . Pokud je vstup logická hodnota, výsledek je `"true"` nebo `"false"` . Pokud je vstup číslo, je převedeno na řetězec pomocí funkce [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number specifikace jazyka ECMAScript. Pokud je vstup barva, je převedena na RGBA řetězec barvy CSS `"rgba(r,g,b,a)"` . V opačném případě je vstup převeden na řetězec pomocí funkce [JSON. Stringify](https://tc39.github.io/ecma262/#sec-json.stringify) specifikace jazyka ECMAScript. |
| `['typeof', value]` | řetězec | Vrátí řetězec popisující typ dané hodnoty. |

> [!TIP]
> Pokud se chybová zpráva podobná `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` zobrazení v konzole prohlížeče, znamená to, že ve vašem kódu je výraz, který má pole, které nemá řetězec pro svou první hodnotu. Pokud chcete, aby výraz vrátil pole, zabalte pole pomocí `literal` výrazu. Následující příklad nastaví `offset` možnost ikony pro vrstvu symbolu, která musí být pole obsahující dvě čísla, a to pomocí `match` výrazu pro výběr mezi dvěma hodnotami posunu na základě hodnoty  `entityType` vlastnosti funkce Point.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Výrazy barev

Výrazy s barvami usnadňují vytváření a manipulaci s hodnotami barev.

| Výraz | Návratový typ | Description |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Vytvoří hodnotu barvy z *červených*, *zelených*a *modrých* komponent, které musí být v rozsahu mezi `0` a `255` a komponentou alfa `1` . Pokud je některá součást mimo rozsah, je výraz chybou. |
| `['rgba', number, number, number, number]` | color | Vytvoří hodnotu barvy z *červené*, *zelené*a *modré* komponenty, která musí být v rozsahu od do `0` `255` a. alfa komponenta v rámci rozsahu `0` a `1` . Pokud je některá součást mimo rozsah, je výraz chybou. |
| `['to-rgba']` | \[číslo, číslo, číslo, číslo\] | Vrátí pole se čtyřmi prvky obsahující *červenou*, *zelenou*, *modrou*a *alfa* komponentu vstupní barvy v tomto pořadí. |

**Příklad**

Následující příklad vytvoří hodnotu barvy RGB s *červenou* hodnotou `255` a *zelenou* a *modrou* hodnotou, která je vypočítána vynásobením `2.5` hodnotou `temperature` Vlastnosti. Při změně teploty se barva změní na různé odstíny *červené*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Výrazy operátorů řetězce

Výrazy operátoru řetězce provádějí operace převodu na řetězcích, jako je zřetězení a převod případu. 

| Výraz | Návratový typ | Description |
|------------|-------------|-------------|
| `['concat', string, string, …]` | řetězec | Zřetězí více řetězců dohromady. Každá hodnota musí být řetězec. Použijte `to-string` výraz Type pro převod ostatních typů hodnot na řetězec v případě potřeby. |
| `['downcase', string]` | řetězec | Převede zadaný řetězec na malá písmena. |
| `['upcase', string]` | řetězec | Převede zadaný řetězec na velká písmena. |

**Příklad**

Následující příklad převede `temperature` vlastnost bodu na řetězec a poté zřetězí "°F" na konci.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

Výše uvedený výraz vykreslí kód PIN na mapě s textem "64 °F", jak je znázorněno na obrázku níže.

<center>

![Příklad ](media/how-to-expressions/string-operator-expression.png) výrazu řetězce operátoru </center>

## <a name="interpolate-and-step-expressions"></a>Interpolovat a krokovat výrazy

Výrazy interpolování a Step lze použít k výpočtu hodnot podél interpolované křivky nebo funkce kroku. Tyto výrazy přebírají výraz, který jako vstup vrátí číselnou hodnotu, například `['get',  'temperature']` . Vstupní hodnota je vyhodnocena proti dvojicím vstupních a výstupních hodnot, aby bylo možné určit hodnotu, která nejlépe odpovídá interpolované křivce nebo funkci kroku. Výstupní hodnoty se nazývají "zastaví". Vstupní hodnoty pro každou stopu musí být číslo ve vzestupném pořadí. Výstupní hodnoty musí být číslo, pole čísel nebo barva.

### <a name="interpolate-expression"></a>Výraz interpolování

`interpolate`Výraz lze použít k výpočtu souvislé a hladké sady hodnot interpolací mezi hodnotami zastavení. `interpolate`Výraz, který vrací hodnoty barvy, vytváří barevný přechod, ve kterém jsou hodnoty výsledků vybrány z.

Existují tři typy metod interpolace, které lze použít ve `interpolate` výrazu:
 
* `['linear']` -Interpoluje lineární poměr mezi dvojicí zarážek.
* `['exponential', base]` – Interpoluje exponenciálně mezi zastávkami. `base`Hodnota určuje rychlost, s jakou se výstup zvyšuje. Vyšší hodnoty zvyšují objem výstupu směrem k hornímu konci rozsahu. `base`Hodnota blížící se 1 vytvoří výstup, který se zvýší lineárně.
* `['cubic-bezier', x1, y1, x2, y2]` -Interpoluje použití [Bézierovy křivky krychle](https://developer.mozilla.org/docs/Web/CSS/timing-function) definované danými řídicími body.

Tady je příklad toho, jak tyto různé typy interpolace vypadají. 

| Lineární  | Exponenciální | Krychlová Bézierova krychle |
|---------|-------------|--------------|
| ![Graf lineární interpolace](media/how-to-expressions/linear-interpolation.png) | ![Graf exponenciální interpolace](media/how-to-expressions/exponential-interpolation.png) | ![Graf interpolace Bézierovy křivky krychle](media/how-to-expressions/bezier-curve-interpolation.png) |

Následující pseudokódu definuje strukturu `interpolate` výrazu. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Příklad**

Následující příklad používá `linear interpolate` výraz pro nastavení `color` vlastnosti bublinové vrstvy na základě `temperature` vlastnosti funkce Point. Pokud `temperature` je hodnota menší než 60, bude vrácena "modrá". Pokud je mezi 60 a menší než 70, vrátí se žlutá. Pokud je mezi 70 a menší než 80, bude vrácena "oranžová". Pokud je 80 nebo větší, vrátí se "Red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Následující obrázek ukazuje, jak jsou vybrány barvy pro výše uvedený výraz.
 
<center>

![Příklad ](media/how-to-expressions/interpolate-expression-example.png) interpolování výrazu </center>

### <a name="step-expression"></a>Výraz kroku

`step`Výraz lze použít pro výpočet diskrétních a nejovlivněných výsledných hodnot vyhodnocením [funkce konstantní](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) , která je definována funkcí zastavení. 

Následující pseudokódu definuje strukturu `step` výrazu. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Výrazy kroku vracejí výstupní hodnotu ukončení těsně před vstupní hodnotou nebo první vstupní hodnotu, pokud je vstup menší než první zarážka. 

**Příklad**

Následující příklad používá `step` výraz pro nastavení `color` vlastnosti bublinové vrstvy na základě `temperature` vlastnosti funkce Point. Pokud `temperature` je hodnota menší než 60, bude vrácena "modrá". Pokud je mezi 60 a menší než 70, vrátí se "žlutá". Pokud je mezi 70 a menší než 80, bude vrácena "oranžová". Pokud je 80 nebo větší, vrátí se "Red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Následující obrázek ukazuje, jak jsou vybrány barvy pro výše uvedený výraz.
 
<center>

![Příklad výrazu kroku](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Výrazy specifické pro vrstvu

Speciální výrazy, které se vztahují pouze na konkrétní vrstvy.

### <a name="heat-map-density-expression"></a>Výraz hustoty tepelné mapy

Výraz hustoty tepelné mapy načte hodnotu hustoty tepelné mapy pro každý pixel v vrstvě Heat mapy a je definován jako `['heatmap-density']` . Tato hodnota je číslo mezi `0` a `1` . Používá se v kombinaci s `interpolation` `step` výrazem or k definování barevného přechodu, který slouží k zabarvovatí Heat mapy. Tento výraz lze použít pouze v [Možnosti barva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color) vrstvy Heat mapy.

> [!TIP]
> Barva v indexu 0, ve výrazu interpolace nebo ve výchozí barvě barvy kroku, definuje barvu oblasti, kde nejsou žádná data. Barva na indexu 0 se dá použít k definování barvy pozadí. Mnoho preferuje nastavení této hodnoty na transparentní nebo částečně průhlednou černou.

**Příklad**

Tento příklad používá výraz interpolace linie k vytvoření hladkého barevného přechodu pro vykreslování Heat mapy. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Kromě použití hladkého přechodu na zabarvovat Heat mapy lze barvy zadat v rámci sady rozsahů pomocí `step` výrazu. Použití `step` výrazu pro Colorizing Heat mapy vizuálně rozdělí hustotu do rozsahů, které se podobají obrysu nebo rozvržení paprskového stylu.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Další informace najdete v dokumentaci k [Přidání vrstvy Heat mapy](map-add-heat-map-layer.md) .

### <a name="line-progress-expression"></a>Výraz průběhu řádku

Výraz průběhu řádku načítá průběh čáry přechodu v čárové vrstvě a je definován jako `['line-progress']` . Tato hodnota je číslo mezi 0 a 1. Používá se v kombinaci s `interpolation` `step` výrazem or. Tento výraz se dá použít jenom s [možností strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient) vrstvy čáry. 

> [!NOTE]
> `strokeGradient`Možnost čáry spojnice vyžaduje `lineMetrics` možnost nastavení zdroje dat na hodnotu `true` .

**Příklad**

V tomto příkladu se používá `['line-progress']` výraz pro použití barevného přechodu na tah čáry.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Zobrazit příklad v reálném čase](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Výraz formátu textového pole

Výraz formátu textového pole lze použít s `textField` možností vlastnosti vrstvy symbolů `textOptions` pro zajištění smíšeného formátování textu. Tento výraz umožňuje zadat sadu vstupních řetězců a možností formátování. Pro každý vstupní řetězec v tomto výrazu lze zadat následující možnosti.

 * `'font-scale'` -Určuje faktor škálování pro velikost písma. Je-li tento parametr zadán, tato hodnota přepíše `size` vlastnost `textOptions` pro jednotlivé řetězce.
 * `'text-font'` -Určuje jednu nebo více rodin písem, které by měly být použity pro tento řetězec. Je-li tento parametr zadán, tato hodnota přepíše `font` vlastnost `textOptions` pro jednotlivé řetězce.
 * `'text-color'` -Určuje barvu, která má být použita pro text při vykreslování. 

Následující pseudokódu definuje strukturu výrazu formátu textového pole. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**Příklad**

Následující příklad formátuje textové pole přidáním tučného písma a vertikálního navýšení velikosti písma `title` vlastnosti funkce. Tento příklad také přidá `subtitle` vlastnost funkce na nový řádek, se zvětšenou velikostí písma a barvou červenou.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

Tato vrstva bude vykreslovat funkci bodu, jak je znázorněno na následujícím obrázku:
 
<center>

![Obrázek funkce Point s formátovaným textovým polem ](media/how-to-expressions/text-field-format-expression.png)</center>

### <a name="number-format-expression"></a>Výraz číselného formátu

`number-format`Výraz lze použít pouze s `textField` možností vrstvy symbolů. Tento výraz převede zadané číslo na formátovaný řetězec. Tento výraz zalomí funkci [Number. ToLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) jazyka JavaScript a podporuje následující sadu možností.

 * `locale` -Tuto možnost zadejte pro převod čísel na řetězce způsobem, který se zarovnává se zadaným jazykem. Předat do této možnosti [značku jazyka BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) .
 * `currency` – Pro převod čísla na řetězec představující měnu. Možné hodnoty jsou [kódy měny ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), jako je například USD pro americký dolar, EUR pro euro nebo "CNY" pro čínské RMB.
 * `'min-fraction-digits'` -Určuje minimální počet desetinných míst, která mají být zahrnuta do řetězcové verze čísla.
 * `'max-fraction-digits'` -Určuje maximální počet desetinných míst, která mají být zahrnuta do řetězcové verze čísla.

Následující pseudokódu definuje strukturu výrazu formátu textového pole. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Příklad**

Následující příklad používá `number-format` výraz pro úpravu způsobu, jakým `revenue` je vlastnost bodu vykreslena v `textField` Možnosti vrstvy symbolů tak, aby se zobrazila hodnota US dolar.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Tato vrstva bude vykreslovat funkci bodu, jak je znázorněno na následujícím obrázku:

<center>

![Příklad ](media/how-to-expressions/number-format-expression.png) výrazu formátu čísla </center>

### <a name="image-expression"></a>Výraz obrázku

Výraz obrázku lze použít s `image` možnostmi a v rámci `textField` vrstvy symbolů a `fillPattern` možností mnohoúhelníkové vrstvy. Tento výraz kontroluje, zda požadovaný obrázek existuje ve stylu a vrátí buď název přeložené bitové kopie, nebo v `null` závislosti na tom, zda je obrázek aktuálně ve stylu. Tento proces ověřování je synchronní a vyžaduje, aby byl obrázek přidán do stylu před vyžádáním v argumentu obrázku.

**Příklad**

Následující příklad používá `image` výraz pro přidání ikony vložené s textem do vrstvy symbolů. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Tato vrstva vykreslí textové pole v symbolové vrstvě, jak je znázorněno na následujícím obrázku:

<center>

![Příklad ](media/how-to-expressions/image-expression.png) výrazu obrázku </center>

## <a name="zoom-expression"></a>Výraz lupy

`zoom`Výraz se používá k načtení aktuální úrovně přiblížení mapy v době vykreslování a je definován jako `['zoom']` . Tento výraz vrací číslo mezi minimálním a maximálním rozsahem úrovně přiblížení mapy. Azure Maps interaktivní ovládací prvky mapování pro web a Android podporují 25 úrovní přiblížení s čísly 0 až 24. Použití `zoom` výrazu umožňuje dynamicky upravovat styly při změně úrovně přiblížení mapy. `zoom`Výraz lze použít pouze s `interpolate` `step` výrazy a.

**Příklad**

Ve výchozím nastavení mají poloměry datových bodů vykreslených v vrstvě Heat mapy u všech úrovní přiblížení pevný obraz v pixelech. Jak je mapa zvětšena, agregace dat a vrstva Heat mapy vypadá jinak. `zoom`Výraz lze použít k horizontálnímu škálování poloměru pro každou úroveň přiblížení tak, že každý datový bod pokrývá stejnou fyzickou oblast mapy. Vrstva Heat mapy bude mít víc statických a konzistentních. Každá úroveň přiblížení mapy má dvakrát a vodorovně tolik pixelů jako předchozí úroveň přiblížení. Změna velikosti poloměru, například při zdvojnásobení s každou úrovní přiblížení, vytvoří Heat mapu, která bude vypadat konzistentně na všech úrovních přiblížení. Dá se to provést pomocí `zoom` výrazu s `base 2 exponential interpolation` výrazem, který má nastavené poloměru pixelů pro minimální úroveň přiblížení a poloměr škálování pro maximální úroveň přiblížení, jak je `2 * Math.pow(2, minZoom - maxZoom)` znázorněno níže.

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Zobrazit příklad v reálném čase](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Výrazy vazeb proměnných

Výrazy vazeb proměnných ukládají výsledky výpočtu v proměnné. Takže je možné výsledky výpočtů odkazovat jinde ve výrazu vícekrát. Je užitečná optimalizace pro výrazy, které zahrnují mnoho výpočtů.

| Výraz | Návratový typ | Description |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;let,<br/>&nbsp;&nbsp;&nbsp;&nbsp;název1: řetězec,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Hodnota1: Any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;název2: řetězec,<br/>&nbsp;&nbsp;&nbsp;&nbsp;hodnota2: Any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Ukládá jednu nebo více hodnot jako proměnné pro použití `var` výrazem v podřízeném výrazu, který vrací výsledek. |
| `['var', name: string]` | Libovolný | Odkazuje na proměnnou, která byla vytvořena pomocí `let` výrazu. |

**Příklad**

V tomto příkladu se používá výraz, který vypočítá tržby vzhledem k poměru teploty a pak použije `case` výraz k vyhodnocení různých logických operací s touto hodnotou. `let`Výraz se používá k uložení výnosů vzhledem k poměru teploty, aby jej bylo nutné vypočítat pouze jednou. `var`Výraz odkazuje na tuto proměnnou tak často, jak je potřeba, aniž by bylo nutné je přepočítat.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Další kroky

Další ukázky kódu, které implementují výrazy, najdete v následujících článcích:

> [!div class="nextstepaction"] 
> [Přidání vrstvy symbolů](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Přidání vrstvy bublin](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Přidání vrstvy heat mapy](map-add-heat-map-layer.md)

Přečtěte si další informace o možnostech vrstvy, které podporují výrazy:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions)
