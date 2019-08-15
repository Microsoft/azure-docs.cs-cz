---
title: Výrazy stylu řízené daty v sadě Azure Maps Web SDK | Microsoft Docs
description: Použití výrazů se stylem založených na datech v sadě Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 507af54b8b4c2e7c67538a1a25a040c7ee5fdfd5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976324"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Výrazy stylu řízené daty (webová sada SDK)

Výrazy umožňují použít obchodní logiku pro stylování možností, které sledují vlastnosti definované v jednotlivých tvarech ve zdroji dat. Výrazy lze také použít k filtrování dat v datovém zdroji nebo ve vrstvě. Výrazy mohou být tvořeny podmíněnou logikou, například if-statements a lze je také použít k manipulaci s daty. řetězcové, logické a matematické operátory. 

Styly řízené daty mohou snížit množství kódu potřebného k implementaci obchodní logiky kolem stylu. Při použití s vrstvami jsou výrazy vyhodnocovány v době vykreslování v samostatném vlákně, které poskytuje vyšší výkon při porovnání s hodnocením obchodní logiky ve vlákně uživatelského rozhraní.

Následující video poskytuje přehled stylů řízených daty v sadě Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Výrazy jsou reprezentovány jako pole JSON. Prvním prvkem výrazu v poli je řetězec, který určuje název operátoru výrazu. Například "+" nebo "Case". Další prvky (pokud existují) jsou argumenty výrazu. Každý argument je buď hodnota literálu (řetězec, číslo, logická hodnota nebo `null`), nebo jiné pole výrazu. Následující pseudokódu definuje výraz základní struktury. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Sada Azure Maps Web SDK podporuje mnoho typů, které lze použít samostatně nebo v kombinaci s jinými výrazy.

| Typ výrazů | Popis |
|---------------------|-------------|
| [Agregační výraz](#aggregate-expression) | Výraz definující výpočet, který je zpracován v sadě dat a lze jej použít s `clusterProperties` možností. `DataSource` |
| [Logické výrazy](#boolean-expressions) | Logické výrazy poskytují sadu logických výrazů operátorů pro vyhodnocení logických porovnání. |
| [Výrazy barev](#color-expressions) | Výrazy s barvami usnadňují vytváření a manipulaci s hodnotami barev. |
| [Podmíněné výrazy](#conditional-expressions) | Podmíněné výrazy poskytují operace logiky, které jsou například if-statements. |
| [Datové výrazy](#data-expressions) | Poskytuje přístup k datům vlastností ve funkci. |
| [Interpolovat a krokovat výrazy](#interpolate-and-step-expressions) | Výrazy interpolování a Step lze použít k výpočtu hodnot podél interpolované křivky nebo funkce kroku. |
| [Výrazy specifické pro vrstvu](#layer-specific-expressions) | Speciální výrazy, které platí pouze pro jednu vrstvu. |
| [Matematické výrazy](#math-expressions) | Poskytuje matematické operátory pro provádění výpočtů řízených daty v rámci rozhraní Expression Framework. |
| [Výrazy operátorů řetězce](#string-operator-expressions) | Výrazy operátoru řetězce provádějí operace převodu na řetězcích, jako je zřetězení a převod případu. |
| [Výrazy typu](#type-expressions) | Výrazy typu poskytují nástroje pro testování a převod různých typů dat, jako jsou řetězce, čísla a logické hodnoty. |
| [Výrazy vazeb proměnných](#variable-binding-expressions) | Výrazy vazeb proměnných umožňují, aby výsledky výpočtu byly uloženy v proměnné a odkazovaly na jiné místo ve výrazu, aniž by bylo nutné znovu vypočítat uloženou hodnotu. |
| [Výraz lupy](#zoom-expression) | Načte aktuální úroveň přiblížení mapy v době vykreslování. |

Všechny příklady v tomto dokumentu budou používat následující funkci k demonstraci různých způsobů, jak lze použít různé typy výrazů. 

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
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Datové výrazy

Datové výrazy poskytují přístup k datům vlastností ve funkci. 

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `['at', number, array]` | objekt | Načte položku z pole. |
| `['geometry-type']` | řetězec | Získá typ geometrie funkce: Point, MultiPoint, LineString, MultiLineString, mnohoúhelník, promnohoúhelník. |
| `['get', string]` | value | Získá hodnotu vlastnosti z vlastností aktuální funkce. Vrátí hodnotu null, pokud chybí požadovaná vlastnost. |
| `['get', string, object]` | value | Získá hodnotu vlastnosti z vlastností poskytnutého objektu. Vrátí hodnotu null, pokud chybí požadovaná vlastnost. |
| `['has', string]` | boolean | Určuje, zda vlastnosti funkce mají zadanou vlastnost. |
| `['has', string, object]` | boolean | Určuje, zda vlastnosti objektu mají zadanou vlastnost. |
| `['id']` | value | Získá ID funkce, pokud má jednu. |
| `['length', string | array]` | číslo | Získá délku řetězce nebo pole. |

**Příklady**

K vlastnostem funkce lze získat přímý pøístup ve výrazu pomocí `get` výrazu. V následujícím příkladu je použita hodnota funkce "zoneColor", která určuje vlastnost Color pro bublinovou vrstvu. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Výše uvedený příklad bude fungovat správně, pokud všechny funkce bodu mají `zoneColor` vlastnost, ale pokud ne, barva se pravděpodobně vrátí na "Black". Chcete-li upravit záložní barvu, `case` lze výraz použít v kombinaci `has` s výrazem pro kontrolu, zda vlastnost existuje, a pokud nevrátí záložní barvu.

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

Vrstvy bublinových a symbolů budou ve výchozím nastavení vykreslovat souřadnice všech tvarů ve zdroji dat. To se dá udělat, pokud chcete zvýraznit vrcholy mnohoúhelníku nebo čáry. Možnost vrstvy lze použít k omezení typu geometrie funkcí vykreslí `['geometry-type']` pomocí výrazu v rámci logického výrazu. `filter` Následující příklad omezuje bublinovou vrstvu tak, aby byly `Point` vykresleny pouze funkce.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Následující příklad umožní `Point` vykreslování funkcí a `MultiPoint` . 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Podobně, obrys mnohoúhelníků se vykreslí do vrstev čar. Chcete-li toto chování zakázat v linkové vrstvě, přidejte filtr, který povoluje `LineString` pouze `MultiLineString` funkce a.  

## <a name="math-expressions"></a>Matematické výrazy

Matematické výrazy poskytují matematické operátory pro provádění výpočtů řízených daty v rámci rozhraní Expression Framework.

| Výraz | Návratový typ | Popis |
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
| `['e']` | číslo | Vrátí matematickou konstantu `e`. |
| `['floor', number]` | číslo | Zaokrouhlí číslo dolů na předchozí celé číslo. |
| `['ln', number]` | číslo | Vypočítá přirozený logaritmus určeného čísla. |
| `['ln2']` | číslo | Vrátí matematickou konstantu `ln(2)`. |
| `['log10', number]` | číslo | Vypočítá dekadický logaritmus zadaného čísla. |
| `['log2', number]` | číslo | Vypočítá dekadický logaritmus zadaného čísla. |
| `['max', number, number, …]` | číslo | Vypočítá maximální počet v zadané sadě čísel. |
| `['min', number, number, …]` | číslo | Vypočítá minimální číslo v zadané sadě čísel. |
| `['pi']` | číslo | Vrátí matematickou konstantu `PI`. |
| `['round', number]` | číslo | Zaokrouhlí číslo na nejbližší celé číslo. Hodnoty v polovině se zaokrouhlují směrem od nuly. Například `['round', -1.5]` vyhodnotí na-2. |
| `['sin', number]` | číslo | Vypočítá sinus zadaného čísla. |
| `['sqrt', number]` | číslo | Vypočítá druhou odmocninu určeného čísla. |
| `['tan', number]` | číslo | Vypočítá tangens zadaného čísla. |

## <a name="aggregate-expression"></a>Agregační výraz

Agregační výraz definuje výpočet, který je zpracován přes sadu dat a lze jej použít s `clusterProperties` možností. `DataSource` Výstup těchto výrazů musí být číslo nebo logická hodnota. 

Agregační výraz má tři hodnoty; hodnota operátoru a počáteční hodnota a výraz pro načtení vlastnosti z každé funkce v datech, pro které se má použít agregační operace. Tento výraz má následující formát:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- podnikatel Funkce výrazu, která je následně použita na proti všem hodnotám vypočítaným `mapExpression` pro každý bod v clusteru. Podporované operátory; 
    - Pro čísla: `+`, `*`, `max`,`min`
    - Pro logické hodnoty: `all`,`any`
- initialValue: Počáteční hodnota, ve které je agregována první Počítaná hodnota.
- mapExpression: Výraz, který se aplikuje na každý bod v datové sadě.

**Příklady**

Pokud všechny funkce v sadě dat mají `revenue` vlastnost, která je číslo. Celkový objem tržeb všech bodů v clusteru vytvořeném ze sady dat lze vypočítat pomocí následujícího agregačního výrazu:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Logické výrazy

Logické výrazy poskytují sadu logických výrazů operátorů pro vyhodnocení logických porovnání.

Při porovnávání hodnot je porovnání striktně typované. Hodnoty různých typů jsou vždy považovány za nerovné. Případy, kde se označují, že typy jsou odlišné v době analýzy, jsou považovány za neplatné a vytvoří chybu analýzy. 

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Logická negace. Vrátí `true` , zda je `false`vstup, a `false` Pokud je `true`vstup. |
| `['!= ', value, value]` | boolean | Vrátí `true` , zda vstupní hodnoty nejsou stejné, `false` jinak. |
| `['<', value, value]` | boolean | Vrátí `true` , zda je první vstup striktně menší než druhý, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `['<=', value, value]` | boolean | Vrátí `true` , zda je první vstup menší nebo roven druhému, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `['==', value, value]` | boolean | Vrátí `true` , zda jsou vstupní hodnoty stejné, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `['>', value, value]` | boolean | Vrátí `true` , zda je první vstup striktně větší než druhý, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `['>=' value, value]` | boolean | Vrátí `true` , zda je první zadání větší než nebo rovno druhému, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `['all', boolean, boolean, …]` | boolean | Vrátí `true` , zda jsou `true`všechny vstupy, `false` jinak. |
| `['any', boolean, boolean, …]` | boolean | Vrátí `true` , zda je `true`některý ze vstupů `false` , jinak. |

## <a name="conditional-expressions"></a>Podmíněné výrazy

Podmíněné výrazy poskytují operace logiky, které jsou například if-statements.

Následující výrazy provádějí Podmíněné logické operace na vstupních datech. Například `case` výraz poskytuje logiku "if/then/else", `match` zatímco výraz je jako "Switch-Statement". 

### <a name="case-expression"></a>Výraz Case

`case` Výraz je typ podmíněného výrazu, který poskytuje výraz IF, jako je Logic (if/then/else). Tento typ kroků výrazu provede seznam logických podmínek a vrátí výstupní hodnotu první logické podmínky, která je pravdivá.

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

Následující příklad provádí v rámci různých logických podmínek `true`, dokud nenajde ten, který je vyhodnocen jako a následně vrací přidruženou hodnotu. Pokud se `true`vyhodnotí žádná logická podmínka, vrátí se záložní hodnota. 

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

`match` Výraz je typ podmíněného výrazu, který poskytuje příkaz typu Switch-Logic. Vstup může být libovolný výraz `['get', 'entityType']` , například, který vrátí řetězec nebo číslo. Každý popisek musí být buď hodnota jednoho literálu, nebo pole hodnot literálu, jejichž hodnoty musí být všechny řetězce nebo všechna čísla. Vstup se shoduje, pokud kterákoli z hodnot v poli odpovídá. Každý popisek musí být jedinečný. Pokud typ vstupu neodpovídá typu popisků, bude výsledkem záložní hodnota.

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

Následující příklad používá pole k vypsání sady popisků, které by měly vracet stejnou hodnotu. To je mnohem efektivnější než jednotlivé popisky jednotlivě. V takovém případě, pokud `entityType` je vlastnost "restaurace" nebo "grocery_store", bude vrácena barva "Red".

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

Následující příklad používá výraz shody k provedení filtru typu "in Array" nebo "Array Contains", v tomto případě filtrování dat s hodnotou ID, která je v seznamu povolených ID. Při použití výrazů s filtry musí být výsledkem logická hodnota.

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

Kroky `coalesce` výrazu pomocí sady výrazů, dokud není získána první hodnota, která není null, a vrátí tuto hodnotu. 

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

Následující příklad používá `coalesce` výraz pro `textField` nastavení možnosti pro vrstvu symbolů. Pokud vlastnost ve funkci chybí nebo je nastavená na `null`, výraz se `subtitle` pak pokusí vyhledat vlastnost, pokud chybí, nebo `null`se vrátí do prázdného řetězce. `title` 

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

## <a name="type-expressions"></a>Výrazy typu

Výrazy typu poskytují nástroje pro testování a převod různých typů dat, jako jsou řetězce, čísla a logické hodnoty.

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | array \| – objekt | Vrátí literálovou hodnotu pole nebo objektu. Tento výraz použijte k zabránění vyhodnocení pole nebo objektu jako výrazu. To je nezbytné, pokud musí být pole nebo objekt vráceny výrazem. |
| `['to-boolean', value]` | boolean | Převede vstupní hodnotu na logickou hodnotu. Výsledkem je `false` , že vstup je prázdný řetězec `false` `null`, `0`,, nebo `NaN`, v opačném případě `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | barva | Převede vstupní hodnotu na barvu. Pokud je zadáno více hodnot, je každá z nich vyhodnocována v pořadí, dokud nebude získán první úspěšný převod. Pokud žádný ze vstupů nelze převést, je výraz chybou. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | číslo | Pokud je to možné, převede vstupní hodnotu na číslo. Pokud je `null` vstup nebo `false`, výsledkem je 0. Pokud je `true`vstup, výsledkem je 1. Pokud je vstup řetězcem, je převeden na číslo pomocí funkce řetězce [tonumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) ve specifikaci jazyka ECMAScript. Pokud je zadáno více hodnot, je každá z nich vyhodnocována v pořadí, dokud nebude získán první úspěšný převod. Pokud žádný ze vstupů nelze převést, je výraz chybou. |
| `['to-string', value]` | řetězec | Převede vstupní hodnotu na řetězec. Pokud je `null`vstup, výsledkem je `""`. Pokud je vstup logická hodnota, výsledek je `"true"` nebo. `"false"` Pokud je vstup číslo, je převedeno na řetězec pomocí funkce [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number specifikace jazyka ECMAScript. Pokud je vstup barva, je převedena na RGBA řetězec `"rgba(r,g,b,a)"`barvy CSS. V opačném případě je vstup převeden na řetězec pomocí funkce [JSON. Stringify](https://tc39.github.io/ecma262/#sec-json.stringify) specifikace jazyka ECMAScript. |
| `['typeof', value]` | řetězec | Vrátí řetězec popisující typ dané hodnoty. |

> [!TIP]
> Pokud `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` se chybová zpráva podobná zobrazení v konzole prohlížeče zobrazí, znamená to, že ve vašem kódu je výraz, který má pole, které nemá řetězec pro svou první hodnotu. Pokud chcete, aby výraz vrátil pole, zabalte pole pomocí `literal` výrazu. Následující příklad nastaví možnost ikony `offset` pro vrstvu symbolu, která musí být pole obsahující dvě čísla, a to `match` pomocí výrazu pro výběr dvou hodnot posunu na základě hodnoty `entityType` vlastnosti bodu. zapnut.
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
>             //If there is no title, try getting the subtitle. 
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

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | barva | Vytvoří hodnotu barvy z červených, *zelených*a *modrých* komponent, které musí `0` být `255`v rozsahu mezi a a komponentou `1`alfa. Pokud je některá součást mimo rozsah, je výraz chybou. |
| `['rgba', number, number, number, number]` | barva | Vytvoří hodnotu barvy z *červené*, *zelené*a *modré* `0` komponenty `255`, která musí být v rozsahu od do `0` a. alfa komponenta v rámci rozsahu a `1`. Pokud je některá součást mimo rozsah, je výraz chybou. |
| `['to-rgba']` | \[číslo, číslo, číslo, číslo\] | Vrátí pole se čtyřmi prvky obsahující *červenou*, zelenou,modroua *alfa* komponentu vstupní barvy v tomto pořadí. |

**Příklad**

Následující příklad vytvoří hodnotu barvy RGB s *červenou* hodnotou `255`a *zelenou* a modrou hodnotou, která je vypočítána vynásobením `2.5` hodnotou `temperature` vlastnosti. Při změně teploty se barva změní na různé odstíny *červené*.

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

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `['concat', string, string, …]` | řetězec | Zřetězí více řetězců dohromady. Každá hodnota musí být řetězec. Použijte výraz `to-string` Type pro převod ostatních typů hodnot na řetězec v případě potřeby. |
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

![Příklad](media/how-to-expressions/string-operator-expression.png) výrazu řetězce operátoru</center>

## <a name="interpolate-and-step-expressions"></a>Interpolovat a krokovat výrazy

Výrazy interpolování a Step lze použít k výpočtu hodnot podél interpolované křivky nebo funkce kroku. Tyto výrazy přebírají výraz, který jako vstup vrátí číselnou hodnotu, například `['get',  'temperature']`. Vstupní hodnota je vyhodnocena proti dvojicím vstupních a výstupních hodnot, nazývaných "zastavení", k určení hodnoty, která nejlépe odpovídá interpolované křivce nebo funkci kroku. Vstupní hodnoty pro každou stopu musí být číslo ve vzestupném pořadí. Výstupní hodnoty musí být číslo, pole čísel nebo barva.

### <a name="interpolate-expression"></a>Výraz interpolování

`interpolate` Výraz lze použít k výpočtu souvislé a hladké sady hodnot interpolací mezi hodnotami zastavení. `interpolate` Výraz, který vrací hodnoty barvy, vytváří barevný přechod, ve kterém jsou hodnoty výsledků vybrány z.

Existují tři typy metod interpolace, které lze použít ve `interpolate` výrazu:
 
* `['linear']`-Interpoluje lineární poměr mezi dvojicí zarážek.
* `['exponential', base]`– Interpoluje exponenciálně mezi zastávkami. `base` Hodnota určuje rychlost, s jakou se výstup zvyšuje. Vyšší hodnoty zvyšují objem výstupu směrem k hornímu konci rozsahu. `base` Hodnota blížící se 1 vytvoří výstup, který se zvýší lineárně.
* `['cubic-bezier', x1, y1, x2, y2]`-Interpoluje použití [Bézierovy křivky krychle](https://developer.mozilla.org/docs/Web/CSS/timing-function) definované danými řídicími body.

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

Následující příklad používá `linear interpolate` výraz pro `color` nastavení vlastnosti `temperature` bublinové vrstvy na základě vlastnosti funkce Point. Pokud je `temperature` hodnota menší než 60, vrátí se "Blue", pokud je mezi 60 a menší než 70, bude vrácena žlutá, pokud mezi 70 a menší než 80 se vrátí "oranžová", pokud se vrátí "Red".

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

![Příklad](media/how-to-expressions/interpolate-expression-example.png) interpolování výrazu</center>

### <a name="step-expression"></a>Výraz kroku

Výraz lze použít pro výpočet diskrétních a nejovlivněných výsledných hodnot vyhodnocením [funkce konstantní](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) , která je definována funkcí zastavení. `step` 

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

Následující příklad používá `step` výraz pro `color` nastavení vlastnosti `temperature` bublinové vrstvy na základě vlastnosti funkce Point. Pokud je `temperature` hodnota menší než 60, vrátí se "Blue", pokud mezi 60 a menší než 70 se vrátí "žlutá", pokud je mezi 70 a menší než 80, vrátí se "oranžová", pokud se vrátí hodnota "Red".

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

Výraz hustoty tepelné mapy načte hodnotu hustoty tepelné mapy pro každý pixel v vrstvě Heat mapy a je definován jako `['heatmap-density']`. Tato hodnota je číslo `0` mezi a `1` a `interpolation` používá se v kombinaci s výrazem or `step` k definování barevného přechodu, který se používá k zabarvovatí Heat mapy. Tento výraz lze použít pouze v [Možnosti barva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) vrstvy Heat mapy.

> [!TIP]
> Barva v indexu 0 ve výrazu interpolace nebo výchozí barva kroku barvy definuje barvu oblasti, kde nejsou žádná data, a lze ji použít k definování barvy pozadí. Mnoho preferuje nastavení této hodnoty na transparentní nebo částečně průhlednou černou. 

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

Kromě použití hladkého přechodu na zabarvovat Heat mapy lze barvy zadat v rámci sady rozsahů pomocí `step` výrazu. `step` Použití výrazu pro colorizingí Heat mapy rozdělí hustotu vizuálně do rozsahů, které se tak podobají obrysovým rozvržením obrysu nebo paprskového stylu.  

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

Výraz průběhu řádku načítá průběh čáry přechodu v čárové vrstvě a je definován jako `['line-progress']`. Tato hodnota je číslo mezi 0 a 1 a používá se v kombinaci s `interpolation` výrazem or. `step` Tento výraz se dá použít jenom s [možností strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) vrstvy čáry. 

> [!NOTE]
> Možnost čáry spojnice `lineMetrics` vyžaduje možnost nastavení `true`zdroje dat na hodnotu. `strokeGradient`

**Příklad**

Následující příklad používá `['line-progress']` výraz pro použití barevného přechodu na tah čáry.

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

Výraz formátu textového pole lze použít s `textField` možností vlastnosti vrstvy `textOptions` symbolů pro zajištění smíšeného formátování textu. Tento výraz umožňuje zadat sadu vstupních řetězců a možností formátování. Pro každý vstupní řetězec v tomto výrazu lze zadat následující možnosti.

 * `'font-scale'`-Určuje faktor škálování pro velikost písma. Je-li tento parametr zadán, tato `size` hodnota přepíše vlastnost `textOptions` pro jednotlivé řetězce.
 * `'text-font'`-Určuje jednu nebo více rodin písem, které by měly být použity pro tento řetězec. Je-li tento parametr zadán, tato `font` hodnota přepíše vlastnost `textOptions` pro jednotlivé řetězce.

Následující pseudokódu definuje strukturu výrazu formátu textového pole. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**Příklad**

Následující příklad formátuje textové pole přidáním tučného písma a vertikálního navýšení velikosti `title` písma vlastnosti funkce. Tento příklad také přidá `subtitle` vlastnost funkce na nový řádek, se zvětšenou velikostí písma.

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
            { 'font-scale': 0.75 }
        ]
    }
});
```

Tato vrstva bude vykreslovat funkci bodu, jak je znázorněno na následujícím obrázku:
 
<center>

![Obrázek funkce Point s formátovaným textovým](media/how-to-expressions/text-field-format-expression.png) polem</center>

### <a name="number-format-expression"></a>Výraz číselného formátu

Výraz lze použít pouze `textField` s možností vrstvy symbolů. `number-format` Tento výraz převede zadané číslo na formátovaný řetězec. Tento výraz zalomí funkci [Number. ToLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) jazyka JavaScript a podporuje následující sadu možností.

 * `locale`-Tuto možnost zadejte pro převod čísel na řetězce způsobem, který se zarovnává se zadaným jazykem. Předat do této možnosti [značku jazyka BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) .
 * `currency`– Pro převod čísla na řetězec představující měnu. Možné hodnoty jsou [kódy měny ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), jako je například USD pro americký dolar, EUR pro euro nebo "CNY" pro čínské RMB.
 * `'min-fraction-digits'`-Určuje minimální počet desetinných míst, která mají být zahrnuta do řetězcové verze čísla.
 * `'max-fraction-digits'`-Určuje maximální počet desetinných míst, která mají být zahrnuta do řetězcové verze čísla.

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

Následující příklad používá `number-format` výraz pro úpravu `revenue` způsobu, jakým je vlastnost bodu vykreslena v `textField` možnosti vrstvy symbolů tak, aby se zobrazila hodnota US dolar.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Tato vrstva bude vykreslovat funkci bodu, jak je znázorněno na následujícím obrázku:

<center>

![Příklad](media/how-to-expressions/number-format-expression.png) výrazu formátu čísla</center>

## <a name="zoom-expression"></a>Výraz lupy

Výraz se používá k načtení aktuální úrovně přiblížení mapy v době vykreslování a je definován jako `['zoom']`. `zoom` Tento výraz vrací číslo mezi minimálním a maximálním rozsahem úrovně přiblížení mapy. Použití tohoto výrazu umožňuje upravovat styly dynamicky při změně úrovně přiblížení mapy. Výraz lze použít pouze s `interpolate` výrazy a `step`. `zoom`

**Příklad**

Ve výchozím nastavení mají poloměry datových bodů vykreslených v vrstvě Heat mapy u všech úrovní přiblížení pevný obraz v pixelech. Jak je mapa zvětšena dohromady a vrstva Heat mapy vypadá jinak. `zoom` Výraz lze použít k horizontálnímu škálování poloměru pro každou úroveň přiblížení tak, že každý datový bod pokrývá stejnou fyzickou oblast mapy. Díky tomu bude vrstva Heat mapy vypadat spolehlivě a konzistentní. Každá úroveň přiblížení mapy má dvakrát a vodorovně tolik pixelů jako předchozí úroveň přiblížení. Změna velikosti poloměru tak, aby se u každé úrovně přiblížení zdvojnásoba, vytvořila Heat mapu, která bude vypadat konzistentně na všech úrovních přiblížení. To lze provést pomocí `zoom` výrazu `base 2 exponential interpolation` s výrazem, jak je znázorněno níže. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Zobrazit příklad v reálném čase](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Výrazy vazeb proměnných

Výrazy vazeb proměnných ukládají výsledky výpočtu v proměnné tak, aby na ně bylo možné odkazovat jinde ve výrazu, aniž by bylo nutné je přepočítat. Toto je užitečná optimalizace pro výrazy, které zahrnují mnoho výpočtů.

| Výraz | Návratový typ | Popis |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;let,<br/>&nbsp;&nbsp;&nbsp;&nbsp;název1: řetězec,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Hodnota1: Any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;název2: řetězec,<br/>&nbsp;&nbsp;&nbsp;&nbsp;hodnota2: Any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Ukládá jednu nebo více hodnot jako proměnné pro použití `var` výrazem v podřízeném výrazu, který vrací výsledek. |
| `['var', name: string]` | libovolné | Odkazuje na proměnnou, která byla vytvořena pomocí `let` výrazu. |

**Příklad**

V tomto příkladu se používá výraz, který vypočítá tržby vzhledem k poměru teploty a `case` pak použije výraz k vyhodnocení různých logických operací s touto hodnotou. Výraz se používá k uložení výnosů vzhledem k poměru teploty, takže je nutné ji vypočítat pouze jednou `var` a výraz odkazuje na tuto proměnnou podle potřeby, aniž by bylo nutné je přepočítat. `let`

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

## <a name="next-steps"></a>Další postup

Další ukázky kódu, které implementují výrazy, najdete v následujících článcích:

> [!div class="nextstepaction"] 
> [Přidat vrstvu symbolů](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Přidat bublinovou vrstvu](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidat řádkovou vrstvu](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidat mnohoúhelníkovou vrstvu](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Přidat vrstvu Heat mapy](map-add-heat-map-layer.md)

Přečtěte si další informace o možnostech vrstvy, které podporují výrazy:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
