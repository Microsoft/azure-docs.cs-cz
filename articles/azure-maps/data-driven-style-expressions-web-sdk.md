---
title: S daty styl výrazů v sadě SDK webové mapy Azure | Dokumentace Microsoftu
description: Jak používat výrazy style s daty v Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3b234ca37783fe557baf307f198de9636b06a382
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60904918"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Výrazy s daty styl (Sada Web SDK)

Výrazy umožňují použít obchodní logiku pro používání stylů pro možnosti, které sledovat vlastnosti definované v jednotlivých tvarů ve zdroji dat. Výrazy můžete také použít k filtrování dat ve zdroji dat nebo vrstvě. Výrazy se může skládat z podmíněnou logiku, jako jsou příkazy if a slouží také k manipulaci s daty řetězce, logické a matematické operátory. 

Styly řízené daty můžete snížit množství kódu, které jsou potřeba implementovat obchodní logiku kolem stylů. Při použití s vrstvami, výrazy jsou vyhodnocovány v době vykreslování na samostatném vlákně, které poskytuje vyšší výkon při porovnání s vyhodnocování obchodní logiky na vlákně UI.

Následující video poskytuje přehled o stylu s daty v Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Výrazy jsou reprezentovány ve formě pole JSON. První prvek výrazu v poli je řetězec, který určuje název operátor výrazu. Například "+" nebo "případ". Další prvky (pokud existuje) jsou argumenty pro výraz. Každý argument představuje hodnotu literálu (řetězec, číslo, logickou hodnotu, nebo `null`), nebo jiný výraz pole. Následujícím pseudokódu definuje základní struktura výrazu. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Sada SDK webové mapy Azure podporuje různé typy výrazů, které lze použít samostatně nebo v kombinaci s jinými výrazy.

| Typ výrazu | Popis |
|---------------------|-------------|
| [Výrazy logických hodnot](#boolean-expressions) | Logické výrazy poskytují sadu logické operátory výrazů za vaše rozhodnutí vyzkoušet logická porovnání. |
| [Barva výrazy](#color-expressions) | Barva výrazy usnadňují vytváření a manipulaci s hodnot barev. |
| [Podmíněné výrazy](#conditional-expressions) | Podmíněné výrazy poskytují logiku operací, které jsou podobné příkazy if. |
| [Výrazy dat](#data-expressions) | Poskytuje přístup k datům vlastnost ve funkci. |
| [Interpolovat a výrazy kroku](#interpolate-and-step-expressions) | Interpolovat a výrazy kroku lze použít k výpočtu hodnoty interpolovaná funkce křivky nebo krok. |
| [Určité výrazy vrstvy](#layer-specific-expressions) | Speciální výrazy, které se vztahují jenom k jedné vrstvy. |
| [Matematické výrazy](#math-expressions) | Poskytuje matematické operátory, abyste mohli provádět výpočty s daty v rámci výrazu. |
| [Řetězcové výrazy – operátor](#string-operator-expressions) | Řetězcové výrazy operátor provádět operace převodu řetězců, jako je například zřetězení a převod tento případ. |
| [Výrazy typu](#type-expressions) | Výrazy typu poskytují nástroje pro testování a převod různé datové typy, jako jsou řetězce, čísla a logické hodnoty. |
| [Vazby proměnné výrazů](#variable-binding-expressions) | Výrazy proměnné vazby let výsledky výpočtu uložené v proměnné a odkazovat kdekoli ve výrazu více než jednou aniž by bylo nutné přepočítat uloženou hodnotu. |
| [Výraz přiblížení](#zoom-expression) | Načte aktuální úroveň přiblížení mapy v době vykreslování. |

Všechny příklady v tomto dokumentu použije následující funkci k ukazují různé způsoby, můžete použít různé druhy výrazů. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {     
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Výrazy dat

Výrazy data poskytují přístup k datům vlastnost ve funkci. 

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `['at', number, array]` | objekt | Načte položku z pole. |
| `['geometry-type']` | string | Získá typ geometry tuto funkci: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | value | Získá hodnotu z vlastnosti aktuální funkce. Vrátí hodnotu null, pokud chybí požadovaná vlastnost. |
| `['get', string, object]` | value | Získá hodnotu z vlastnosti zadaného objektu. Vrátí hodnotu null, pokud chybí požadovaná vlastnost. |
| `['has', string]` | Boolean | Určuje, pokud vlastnosti funkce mít zadanou vlastnost. |
| `['has', string, object]` | Boolean | Určuje, pokud vlastnosti objektu mít zadanou vlastnost. |
| `['id']` | value | Získá ID tuto funkci, pokud jej obsahuje. |
| `['length', string | array]` | číslo | Získá délku řetězce nebo pole. |

**Příklady**

Vlastnosti funkce lze přistupovat přímo ve výrazu pomocí `get` výrazu. Následující příklad používá hodnotu "zoneColor" funkce pro určení této vlastnosti Barva bublinu vrstvy. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Výše uvedený příklad bude fungovat správně, pokud máte všechny funkce bodu `zoneColor` vlastností, ale pokud ne, barva bude pravděpodobně vrátit zpět k "black". Chcete-li změnit barvu pro použití náhradní lokality `case` výraz lze použít v kombinaci s `has` výraz zkontrolujte, jestli existuje vlastnost, a pokud není místo toho vrátí barvu pro použití náhradní lokality.

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

Bublinu a symbol vrstvy budou vykreslovat souřadnice všechny obrazce ve zdroji dat ve výchozím nastavení. To můžete udělat zvýrazněte vrcholy mnohoúhelník nebo řádku. `filter` Možnost vrstvy jde použít k omezení typu geometry vykreslí pomocí funkcí `['geometry-type']` výrazu v rámci logického výrazu. Následující příklad omezuje vrstvu bublinu takže jen `Point` jsou funkce generovány.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Následující příklad povolí obě `Point` a `MultiPoint` funkce k vykreslení. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Podobně osnovy mnohoúhelníky vykreslí v řádku vrstvy. Pokud chcete zakázat toto chování ve vrstvě řádku, přidejte filtr, který umožňuje pouze `LineString` a `MultiLineString` funkce.  

## <a name="math-expressions"></a>Matematické výrazy

Matematické výrazy poskytují matematické operátory provádět výpočty s daty v rámci výrazu.

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `['+', number, number, …]` | číslo | Vypočítá součet zadaných čísel. |
| `['-', number]` | číslo | Odečte 0 zadaným číslem. |
| `['-', number, number]` | číslo | Odečte první čísla ve druhé číslo. |
| `['*', number, number, …]` | číslo | Vynásobí zadané společně. |
| `['/', number, number]` | číslo | Vydělí číslo první ve druhé číslo. |
| `['%', number, number]` | číslo | Vypočítá zbytek po dělení první číslo v druhé číslo. |
| `['^', number, number]` | číslo | Vypočítá hodnotu první hodnota umocněné na druhé číslo. |
| `['abs', number]` | číslo | Vypočítá absolutní hodnotu logaritmu určeného čísla. |
| `['acos', number]` | číslo | Vypočítá arkuskosinus zadané číslo. |
| `['asin', number]` | číslo | Vypočítá arkussinus logaritmu určeného čísla. |
| `['atan', number]` | číslo | Vypočítá arkustangens výrazu zadané číslo. |
| `['ceil', number]` | číslo | Zaokrouhlí číslo nahoru na další celá čísla. |
| `['cos', number]` | číslo | Vypočítá cos zadané číslo. |
| `['e']` | číslo | Matematické konstanty vrací `e`. |
| `['floor', number]` | číslo | Zaokrouhlí číslo dolů předchozí celá čísla. |
| `['ln', number]` | číslo | Vypočítá přirozený logaritmus zadaného čísla. |
| `['ln2']` | číslo | Matematické konstanty vrací `ln(2)`. |
| `['log10', number]` | číslo | Vypočítá logaritmus o základu 10 zadané číslo. |
| `['log2', number]` | číslo | Vypočítá logaritmus dvě základ logaritmu určeného čísla. |
| `['max', number, number, …]` | číslo | Vypočítá maximální počet v zadané sadě čísel. |
| `['min', number, number, …]` | číslo | Vypočítá minimální počet v zadané sadě čísel. |
| `['pi']` | číslo | Matematické konstanty vrací `PI`. |
| `['round', number]` | číslo | Zaokrouhlí číslo na nejbližší celé číslo. Středové hodnoty jsou zaokrouhleny směrem od nuly. Například `['round', -1.5]` vyhodnocen jako -2. |
| `['sin', number]` | číslo | Vypočítá sinus zadaného čísla. |
| `['sqrt', number]` | číslo | Vypočítá druhou odmocninu logaritmu určeného čísla. |
| `['tan', number]` | číslo | Vypočítá tangens zadaného čísla. |
## <a name="boolean-expressions"></a>Logické výrazy

Logické výrazy poskytují sadu logické operátory výrazů za vaše rozhodnutí vyzkoušet logická porovnání.

Při porovnávání hodnot, je výhradně typu porovnání. Hodnoty různých typů vždy považovány za nerovnost. Případy, ve kterém jsou známé jako currentcolor různé typy jsou považovány za neplatné a způsobí chybu analýzy. 

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `['! ', boolean]` | Boolean | Logická negace. Vrátí `true` Pokud je vstup `false`, a `false` Pokud je vstup `true`. |
| `['!= ', value, value]` | Boolean | Vrátí `true` Pokud vstupní hodnoty nejsou shodné, `false` jinak. |
| `['<', value, value]` | Boolean | Vrátí `true` Pokud prvního vstupu je striktně menší než druhé, `false` jinak. Argumenty se musí být buď obě řetězce nebo obou čísel. |
| `['<=', value, value]` | Boolean | Vrátí `true` Pokud prvního vstupu je menší než nebo rovná druhé, `false` jinak. Argumenty se musí být buď obě řetězce nebo obou čísel. |
| `['==', value, value]` | Boolean | Vrátí `true` Pokud vstupní hodnoty jsou si rovny, `false` jinak. Argumenty se musí být buď obě řetězce nebo obou čísel. |
| `['>', value, value]` | Boolean | Vrátí `true` Pokud prvního vstupu je výhradně větší než druhý, `false` jinak. Argumenty se musí být buď obě řetězce nebo obou čísel. |
| `['>=' value, value]` | Boolean | Vrátí `true` Pokud prvního vstupu je větší než nebo roven druhému, `false` jinak. Argumenty se musí být buď obě řetězce nebo obou čísel. |
| `['all', boolean, boolean, …]` | Boolean | Vrátí `true` Pokud jsou všechny vstupy `true`, `false` jinak. |
| `['any', boolean, boolean, …]` | Boolean | Vrátí `true` Jestliže má některý ze vstupních `true`, `false` jinak. |

## <a name="conditional-expressions"></a>Podmíněné výrazy

Podmíněné výrazy poskytují logiku operací, které jsou podobné příkazy if.

Následující výrazy provádět operace podmíněnou logiku na vstupní data. Například `case` výraz poskytuje "if/then/else" logiku při `match` výrazu je jako "-příkazu switch". 

### <a name="case-expression"></a>Výraz Case

A `case` výrazu je typ podmíněného výrazu, který obsahuje příkaz if, třeba logiku (if/then/else). Tento typ výrazu provede seznam logických podmínkách a vrátí výstupní hodnota z první logickou podmínku, která má hodnotu true.

Definuje strukturu následujícím pseudokódu `case` výrazu. 

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

Následující příklad provede různých logických podmínkách, dokud nenajde ten, který se vyhodnotí jako `true`a potom vrátí, které přidružené hodnoty. Pokud se žádná logická podmínka vyhodnotí jako `true`, vrátí se hodnoty fallback. 

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

### <a name="match-expression"></a>Odpovídat výrazu

A `match` výrazu je typ podmíněného výrazu, který poskytuje příkazu switch třeba logiku. Libovolný výraz může být například vstup `['get', 'entityType']` , která vrací řetězec nebo číslo. Každý popisek musí být buď jednu hodnotu literálu nebo pole hodnot literálů, jejichž hodnoty musí být všechny řetězce nebo všechna čísla. Vstup odpovídá, pokud některý z hodnoty pole shodují. Každý popisek musí být jedinečný. Pokud vstupní typ neodpovídá typu popisky, výsledkem bude hodnoty fallback.

Definuje strukturu následujícím pseudokódu `match` výrazu. 

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

Následující příklad zobrazuje `entityType` vlastnost funkce bodu ve vrstvě bublinu vyhledá shodu. Pokud najde shodu, zadat, která je vrácena hodnota nebo vrátí hodnotu pro použití náhradní lokality.

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

Následující příklad používá sadu popisků, které by měl vrátit stejnou hodnotu pole. To je mnohem efektivnější, než seznamu jednotlivě každému popisku. V takovém případě pokud `entityType` je vlastnost "restaurace" nebo "grocery_store", bude vrácen barvu "red".

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

### <a name="coalesce-expression"></a>Sloučený výraz

A `coalesce` výraz provede sada výrazů, dokud se získá první nenulovou hodnotu a vrátí tuto hodnotu. 

Definuje strukturu následujícím pseudokódu `coalesce` výrazu. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Příklad**

Následující příklad používá `coalesce` výraz k nastavení `textField` možnost symbol vrstvy. Pokud `title` chybí vlastnost z funkce nebo nastavte na `null`, výraz zkusí pak hledá `subtitle` vlastnost, pokud jeho chybí nebo `null`, se pak přejde zpět na prázdný řetězec. 

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

Výrazy typu poskytují nástroje pro testování a převod různé datové typy, jako jsou řetězce, čísla a logické hodnoty.

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | pole \| objektu | Vrátí hodnotu literálu pole nebo objekt. Pomocí tohoto výrazu zabránit je vyhodnocen jako výraz pole nebo objekt. Toto je potřeba, pokud je pole nebo objekt musí být vrácen výrazu. |
| `['to-boolean', value]` | Boolean | Převede vstupní hodnotu na logickou hodnotu. Výsledkem je `false` při vstupu je prázdný řetězec, `0`, `false`, `null`, nebo `NaN`; jinak vrátí hodnotu jeho `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Převede vstupní hodnotu barvy. Pokud jsou k dispozici více hodnot, každý z nich se vyhodnocují v pořadí, dokud není dosaženo první úspěšný převod. Pokud žádný ze vstupů lze převést, výraz se o chybu. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | číslo | Převede vstupní hodnotu na číslo, pokud je to možné. Pokud je vstup `null` nebo `false`, výsledkem je 0. Pokud je vstup `true`, výsledek je 1. Pokud je vstupní řetězec, je převedena na čísla pomocí [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) řetězec funkce ECMAScript Language Specification. Pokud jsou k dispozici více hodnot, každý z nich se vyhodnocují v pořadí, dokud není dosaženo první úspěšný převod. Pokud žádný ze vstupů lze převést, výraz se o chybu. |
| `['to-string', value]` | string | Převede vstupní hodnotu na řetězec. Pokud je vstup `null`, výsledkem je `""`. Pokud je vstup logickou hodnotu, výsledek je `"true"` nebo `"false"`. Pokud je vstup několik, je převedena na řetězec pomocí [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) číslo funkce ECMAScript Language Specification. Pokud je vstup barvu, je převedena na řetězce barvy šablon stylů CSS RGBA `"rgba(r,g,b,a)"`. V opačném případě je vstup převeden na řetězec pomocí [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) funkce ECMAScript Language Specification. |
| `['typeof', value]` | string | Vrátí řetězec, který popisuje typ předané hodnoty. |

> [!TIP]
> Pokud chybová zpráva podobná `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` se zobrazí v konzole prohlížeče to znamená, že je výraz někde v kódu, které obsahuje pole, která nemá řetězec pro svůj první hodnota. Pokud chcete výraz, který má vrátit pole, zabalit pole s `literal` výrazu. Následující příklad nastaví na ikonu `offset` možnost symbol vrstvu, která musí být pole obsahující dvě čísla pomocí `match` výraz si vybrat mezi dvě hodnoty posunu podle hodnoty `entityType` vlastnosti bodu funkce.
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

## <a name="color-expressions"></a>Barva výrazy

Barva výrazy usnadňují vytváření a manipulaci s hodnot barev.

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Vytvoří hodnotu barvy z *red*, *zelené*, a *modré* součásti, které musí být v rozsahu mezi `0` a `255`a alfa součást `1`. Pokud jakékoliv součásti je mimo rozsah, výraz se o chybu. |
| `['rgba', number, number, number, number]` | color | Vytvoří hodnotu barvy z *red*, *zelené*, *modré* součásti, které musí být v rozsahu mezi `0` a `255`a hodnota alfa v rozsahu `0` a `1`. Pokud jakékoliv součásti je mimo rozsah, výraz se o chybu. |
| `['to-rgba']` | \[číslo, číslo, číslo, číslo\] | Vrací čtyřech prvcích pole obsahující vstupní barva *red*, *zelené*, *modré*, a *alfa* komponenty v tomto pořadí. |

**Příklad**

Následující příklad vytvoří a hodnota barvy RGB, který má *red* hodnotu `255`, a *zelené* a *modré* hodnoty, které se počítají vynásobením `2.5` hodnotou `temperature` vlastnost. Jako změny teploty barva se změní na různých odstínů *red*.

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

## <a name="string-operator-expressions"></a>Řetězcové výrazy – operátor

Řetězcové výrazy operátor provádět operace převodu řetězců, jako je například zřetězení a převod tento případ. 

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Zřetězí několik řetězců společně. Každá hodnota musí být řetězec. Použití `to-string` zadejte výraz pro převod jiných typů hodnot do řetězce v případě potřeby. |
| `['downcase', string]` | string | Převede zadaný řetězec na malá písmena. |
| `['upcase', string]` | string | Převede zadaný řetězec na velká písmena. |

**Příklad**

Následující příklad převádí `temperature` vlastnosti bodu běží na procesorech do řetězce a zřetězí "° F" na konec.

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

Výše uvedeném výrazu vykreslí špendlíku na mapě s textem "64° F" překrývající nad ji jak je znázorněno na následujícím obrázku.

<center>

![Příklad výraz řetězce – operátor](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Interpolovat a výrazy kroku

Interpolovat a výrazy kroku lze použít k výpočtu hodnoty interpolovaná funkce křivky nebo krok. Tyto výrazy se výraz, který vrací numerickou hodnotu jako svůj vstup například `['get',  'temperature']`. Vstupní hodnota je porovnán dvojice hodnot vstupní a výstupní, nazývá "přestane", k určení hodnoty, která nejlíp odpovídá funkci interpolované křivky nebo kroku. Vstupní hodnoty pro každý stop musí být číslo a je ve vzestupném pořadí. Výstupní hodnoty musí být číslo a pole čísel nebo barvu.

### <a name="interpolate-expression"></a>Interpolovat výraz

`interpolate` Výraz lze použít pro výpočet průběžného, technologie smooth sadu hodnot určené interpolací mezi hodnoty. `interpolate` Výraz, který vrací hodnoty barev vytvoří barvu přechod, ve které výsledku jsou vybrané hodnoty z.

Existují tři typy interpolace, metody, které můžete použít `interpolate` výraz:
 
* `['linear']` -Interpolaci lineárně pár zastaví.
* `['exponential', base]` -Interpolaci exponenciálně zastaví. `base` Hodnotu ovládá rychlost, jakou výstup zvyšuje. Vyšší hodnoty byl výstup více zvýšit na vysoké konec rozsahu. A `base` hodnota blížící se 1 vytvoří výstup, který zvyšuje více lineárně.
* `['cubic-bezier', x1, y1, x2, y2]` -Argument interpolaci pomocí [kubické Bézierovy křivky](https://developer.mozilla.org/docs/Web/CSS/timing-function) definované body daného ovládacího prvku.

Tady je příklad toho, co tyto různé druhy interpolace vypadat. 

| Lineární  | Exponenciální | Kubické Bézierovy křivky |
|---------|-------------|--------------|
| ![Lineární interpolace grafu](media/how-to-expressions/linear-interpolation.png) | ![Graphu exponenciální interpolace](media/how-to-expressions/exponential-interpolation.png) | ![Graf interpolace kubické Bézierovy křivky](media/how-to-expressions/bezier-curve-interpolation.png) |

Definuje strukturu následujícím pseudokódu `interpolate` výrazu. 

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

V následujícím příkladu `linear interpolate` výraz k nastavení `color` na základě vlastností bublinu vrstvy `temperature` vlastnosti bodu funkce. Pokud `temperature` hodnota je menší než 60, "blue" bude vrácen, pokud mezi 60 a míň než 70, žlutá vrátí se, pokud mezi 70 a nižší než 80, "oranžové" bude vrácen, pokud se vrátí 80 nebo vyšší, "red".

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

Následující obrázek znázorňuje, jak je možné zvolit barvy pro výše uvedeném výrazu.
 
<center>

![Interpolovat příklad výrazu](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Výraz kroku

A `step` výraz lze použít k výpočtu hodnoty diskrétní, stupňovité výsledek vyhodnocením [konstantní rozdělení piecewise funkce](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) určené zastaví. 

Definuje strukturu následujícím pseudokódu `step` výrazu. 

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

Výrazy kroku vrátit hodnotu výstup stop těsně před vstupní hodnotu nebo první vstupní hodnotu, pokud vstup je menší než první stop. 

**Příklad**

V následujícím příkladu `step` výraz k nastavení `color` na základě vlastností bublinu vrstvy `temperature` vlastnosti bodu funkce. Pokud `temperature` hodnota je menší než 60, "blue" bude vrácen, pokud mezi 60 a míň než 70, "žlutý" bude vrácen, pokud mezi 70 a nižší než 80, "oranžové" bude vrácen, pokud se vrátí 80 nebo vyšší, "red".

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

Následující obrázek znázorňuje, jak je možné zvolit barvy pro výše uvedeném výrazu.
 
<center>

![Příklad výraz kroku](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Určité výrazy vrstvy

Speciální výrazy, které se vztahují jenom na konkrétní vrstvy.

### <a name="heat-map-density-expression"></a>Výraz hustota Heat mapy

Výraz heat mapa hustota načte hodnotu hustota heat mapa pro každý pixel ve vrstvě heat mapy a je definován jako `['heatmap-density']`. Tato hodnota je číslo mezi `0` a `1` a se používá v kombinaci s `interpolation` nebo `step` výraz a definujte barva přechodu použitá barevně zvýrazňovat heat mapa. Tento výraz lze použít pouze v [barva možnost](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) vrstvy heat mapy.

> [!TIP]
> Barvy v indexu 0 ve výrazu interpolace nebo výchozí barvu barvu krok definuje barvu, oblasti, kde není žádná data a je možné definovat barvu pozadí. Mnoho chtít nastavit tuto hodnotu a je transparentní nebo poloprůhledného black. 

**Příklad**

Tento příklad používá výraz interpolace jakákoliv k vytvoření hladkého barva přechodu pro vykreslení heat mapa. 

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

Kromě použití hladkého přechodu barevně zvýrazňovat heat mapa, barvy můžete pomocí definované v rámci oblasti `step` výrazu. Použití `step` výraz pro barevné označování heat mapa rozdělí hustotu vizuálně do rozsahů více tak podobná rozvrh nebo této možnosti taky přemýšlíte mapa style.  

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

Další informace najdete v tématu [přidat vrstvu heat mapy](map-add-heat-map-layer.md) dokumentaci.

### <a name="line-progress-expression"></a>Výraz průběh čáry

Průběh výrazu řádku obnoví průběh přechodu řádkem čárovou vrstvu a je definován jako `['line-progress']`. Tato hodnota je číslo mezi 0 a 1 a se používá v kombinaci s `interpolation` nebo `step` výrazu. Tento výraz lze použít pouze s [strokeGradient možnost]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) vrstvy řádku. 

> [!NOTE]
> `strokeGradient` Vyžaduje možnost čárovou vrstvu `lineMetrics` možnost nastavit na zdroj dat `true`.

**Příklad**

V následujícím příkladu `['line-progress']` výraz použití barev přechodu stroke řádku.

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

[Viz příklad za provozu](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Výraz formátu textové pole

Textové pole formátu výraz lze použít s `textField` možnost vrstvy symbol `textOptions` vlastnosti smíšených textových formátování. Tento výraz povolí sadu vstupního řetězce a možnosti formátování zadání. Tyto možnosti lze zadat pro každý vstupní řetězec v tomto výrazu.

 * `'font-scale'` -Určuje měřítko pro velikost písma. Je-li zadána, tato hodnota se přepíše `size` vlastnost `textOptions` pro jednotlivé řetězce.
 * `'text-font'` : Určuje jeden nebo více rodin písem, které byste měli použít pro tento řetězec. Je-li zadána, tato hodnota se přepíše `font` vlastnost `textOptions` pro jednotlivé řetězce.

Následujícím pseudokódu definuje strukturu výraz formátu textové pole. 

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

Následující příklad formátuje text pole přidáním tučné písmo a škálování velikost písma `title` vlastnost funkce. V tomto příkladu přidá také `subtitle` vlastnost funkce na nový řádek s s horizontálně navyšovanou kapacitou dolů velikost písma.

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

Tato vrstva bude vykreslení funkci bodu, jak je znázorněno na následujícím obrázku:
 
<center>

![Obrázek funkce bod s polem formátovaný text](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Výraz pro formátování čísla

`number-format` Výrazu použít jenom s `textField` možnost symbol vrstvy. Tento výraz převede zadané číslo formátovaný řetězec. Tento výraz zabalí v jazyce JavaScript [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) fungovat a podporuje následující sadu možností.

 * `locale` -Zadejte tuto možnost pro převod čísel na řetězce tak, aby zarovnal zadaný jazyk. Předat [značku jazyka BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) do této možnosti.
 * `currency` – Chcete-li převést číslo na řetězec představující měny. Možné hodnoty jsou [měny ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), jako je například "USD" pro AMERICKÝ dolar, "EUR" euro nebo "CNY" pro čínské desku vzdálené správy.
 * `'min-fraction-digits'` : Určuje minimální počet desetinných míst, které mají být zahrnuty verze řetězce číslo.
 * `'max-fraction-digits'` : Určuje maximální počet desetinných míst, které mají být zahrnuty verze řetězce číslo.

Následujícím pseudokódu definuje strukturu výraz formátu textové pole. 

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

V následujícím příkladu `number-format` výraz změnit způsob, jak `revenue` vlastnost funkce bod se vykreslí v `textField` možnost symbolu vrstvy tak, aby se zobrazila hodnota AMERICKÝ dolar.

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

Tato vrstva bude vykreslení funkci bodu, jak je znázorněno na následujícím obrázku:

<center>

![Příklad výrazu číslo formátu](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Výraz přiblížení

A `zoom` výraz slouží k načtení aktuální úroveň přiblížení mapy v době vykreslování a je definován jako `['zoom']`. Tento výraz vrátí číslo v rozsahu úrovni rozsahu minimální a maximální Přiblížení mapy. Pomocí tohoto výrazu povoluje stylů dynamicky měnit úroveň přiblížení mapy při změně. `zoom` Výraz se dá použít jenom s `interpolate` a `step` výrazy.

**Příklad**

Ve výchozím nastavení mají poloměry datových bodů ve vrstvě heat mapa vykreslena pevné pixel radius pro všechny úrovně přiblížení. Jak mapu postupně zvětšován, dohromady data agregace a vrstvě heat mapa vypadá jinak. A `zoom` výrazu je možné škálovat pomocí protokolu radius pro každou úroveň přiblížení tak, aby každý datový bod zahrnuje stejné fyzické oblasti na mapě. Díky tomu budou vrstvu heat mapy, podívejte se, statické a konzistentní vzhledem k aplikacím. Každá úroveň přiblížení mapy má dvakrát tolik pixelů vertikální i horizontální jako předchozí úroveň přiblížení. Škálování poloměr tak, aby ho zdvojnásobuje s každou úroveň přiblížení vytvoří heat mapu, která vypadá konzistentní vzhledem k aplikacím na všech úrovních zvětšení. Můžete to udělat pomocí `zoom` výrazem `base 2 exponential interpolation` výrazu, jak je znázorněno níže. 

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

[Viz příklad za provozu](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Vazby proměnné výrazů

Vazby proměnné výrazů uložit výsledky výpočtu v proměnné tak, aby jej lze odkazovat jinde ve výrazu více než jednou aniž by bylo nutné přepočítat ho. To je užitečné optimalizace pro výrazy, které zahrnují mnoho výpočtů

| Výraz | Návratový typ | Popis |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"let"<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: řetězec,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Hodnota1: žádné,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Name2: řetězec,<br/>&nbsp;&nbsp;&nbsp;&nbsp;hodnota2: žádné,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Uloží jednu nebo více hodnot jako proměnné pro použití `var` výrazu v podřízených výraz, který vrátí výsledek. |
| `['var', name: string]` | Všechny | Odkazuje na proměnnou, která byla vytvořena pomocí `let` výrazu. |

**Příklad**

Tento příklad používá výraz, který vypočítá výnosy vzhledem k poměr teplotu a pak používá `case` výraz k vyhodnocení různých logických operací na této hodnotě. `let` Výrazu se používá k ukládání výnosy vzhledem k teploty poměr tak, že je potřeba vypočítat jednou a `var` výraz odkazuje na tuto proměnnou tak často, podle potřeby, aniž by bylo nutné přepočítat ho.

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

Naleznete v následujících článcích pro další ukázky kódu, které implementují výrazy:

> [!div class="nextstepaction"] 
> [Přidat vrstvu symbol](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Přidat vrstvu bublinový](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [Přidat tvary](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Přidat vrstvu heat mapy](map-add-heat-map-layer.md)

Další informace o možnostech vrstvy, které podporují výrazy:

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
