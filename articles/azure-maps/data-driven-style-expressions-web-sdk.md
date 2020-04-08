---
title: Výrazy stylu řízené daty ve webové sdk azure maps | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak používat výrazy stylu řízené daty v sadě Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d6009a655adcc26ebef31588eff2332a05f3a001
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804720"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Výrazy stylu řízené daty (Web SDK)

Výrazy umožňují použít obchodní logiku na možnosti stylů, které sledují vlastnosti definované v každém obrazci ve zdroji dat. Výrazy mohou filtrovat data ve zdroji dat nebo ve vrstvě. Výrazy se mohou skládat z podmíněné logiky, například if-statements. A mohou být použity k manipulaci s daty pomocí: operátory řetězce, logické operátory a matematické operátory.

Styly řízené daty snižují množství kódu potřebného k implementaci obchodní logiky kolem stylů. Při použití s vrstvami jsou výrazy vyhodnocovány v době vykreslení v samostatném vlákně. Tato funkce poskytuje vyšší výkon ve srovnání s vyhodnocením obchodní logiky ve vlákně uživatelského rozhraní.

Toto video poskytuje přehled stylingu založeného na datech v sada Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Výrazy jsou reprezentovány jako pole JSON. První prvek výrazu v poli je řetězec, který určuje název operátoru výrazu. Například "+" nebo "case". Další prvky (pokud existují) jsou argumenty výrazu. Každý argument je hodnota literálu (řetězec, číslo, `null`logická hodnota nebo ) nebo jiné pole výrazu. Následující pseudokód definuje základní strukturu výrazu. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Webová sada Azure Maps Web SDK podporuje mnoho typů výrazů. Výrazy lze použít samostatně nebo v kombinaci s jinými výrazy.

| Typ výrazů | Popis |
|---------------------|-------------|
| [Agregační výraz](#aggregate-expression) | Výraz, který definuje výpočet, který je zpracován přes sadu dat `clusterProperties` a lze `DataSource`jej použít s možností . |
| [Logické výrazy](#boolean-expressions) | Logické výrazy poskytují sadu logických operátorů výrazy pro hodnocení logické porovnání. |
| [Barevné výrazy](#color-expressions) | Barevné výrazy usnadňují vytváření a manipulaci s hodnotami barev. |
| [Podmíněné výrazy](#conditional-expressions) | Podmíněné výrazy poskytují logické operace, které jsou jako if-příkazy. |
| [Datové výrazy](#data-expressions) | Poskytuje přístup k datům vlastností v prvku. |
| [Interpolaci a krok výrazy](#interpolate-and-step-expressions) | Interpolaci a krokvýrazy lze použít k výpočtu hodnot podél interpolované křivky nebo funkce kroku. |
| [Výrazy specifické pro vrstvu](#layer-specific-expressions) | Speciální výrazy, které jsou použitelné pouze pro jednu vrstvu. |
| [Matematické výrazy](#math-expressions) | Poskytuje matematické operátory k provádění výpočtů založených na datech v rámci výrazu. |
| [Výrazy operátoru řetězce](#string-operator-expressions) | Výrazy operátoru řetězce provádějí operace převodu na řetězce, jako je například zřetězení a převod případu. |
| [Textové výrazy](#type-expressions) | Textové výrazy poskytují nástroje pro testování a převod různých datových typů, jako jsou řetězce, čísla a logické hodnoty. |
| [Výrazy vazby proměnných](#variable-binding-expressions) | Výrazy proměnné vazby ukládají výsledky výpočtu do proměnné a odkazovány jinde ve výrazu vícekrát, aniž by bylo třeba přepočítat uloženou hodnotu. |
| [Výraz Lupa](#zoom-expression) | Načte aktuální úroveň zvětšení mapy v době vykreslení. |

Všechny příklady v tomto dokumentu používají následující funkci k předvedení různých způsobů, kterými lze použít různé typy výrazů. 

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

Datové výrazy poskytují přístup k datům vlastností v prvku. 

| Expression | Návratový typ | Popis |
|------------|-------------|-------------|
| `['at', number, array]` | objekt | Načte položku z pole. |
| `['geometry-type']` | řetězec | Získá typ geometrie prvku: Point, MultiPoint, LineString, MultiLineString, Mnohonožci, Multi Polygon. |
| `['get', string]` | value | Získá hodnotu vlastnosti z vlastností aktuální funkce. Vrátí hodnotu null, pokud požadovaná vlastnost chybí. |
| `['get', string, object]` | value | Získá hodnotu vlastnosti z vlastností zadaný objekt. Vrátí hodnotu null, pokud požadovaná vlastnost chybí. |
| `['has', string]` | Boolean | Určuje, zda vlastnosti prvku mají zadanou vlastnost. |
| `['has', string, object]` | Boolean | Určuje, zda vlastnosti objektu mají zadanou vlastnost. |
| `['id']` | value | Získá ID funkce, pokud má jeden. |
| `['length', string | array]` | číslo | Získá délku řetězce nebo pole. |
| `['in', boolean | string | number, array]` | Boolean | Určuje, zda položka existuje v poli. |
| `['in', substring, string]` | Boolean | Určuje, zda existuje dílčí řetězec v řetězci. |

**Příklady**

Vlastnosti prvku lze přistupovat přímo ve výrazu pomocí výrazu. `get` Tento příklad používá hodnotu "zoneColor" prvku k určení vlastnosti color bublinové vrstvy. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Výše uvedený příklad bude fungovat dobře, pokud `zoneColor` všechny bodové prvky mají vlastnost. Pokud tomu tak není, barva bude pravděpodobně záložní k "černé". Chcete-li upravit záložní barvu, použijte `case` výraz `has` v kombinaci s výrazem ke kontrole, zda vlastnost existuje. Pokud vlastnost neexistuje, vrátí záložní barvu.

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

Vrstvy bublin a symbolů ve výchozím nastavení vykreslí souřadnice všech obrazců ve zdroji dat. Toto chování může zvýraznit vrcholy polygonu nebo čáry. Možnost `filter` hladiny lze použít k omezení typu geometrie prvků, které `['geometry-type']` vykresluje, pomocí výrazu v logickém výrazu. Následující příklad omezuje bublinovou vrstvu tak, aby byly vykresleny pouze `Point` prvky.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Následující příklad umožňuje `Point` `MultiPoint` vykreslení obou funkcí a funkcí. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Podobně se obrys polygonů vykreslí v řádkových vrstvách. Chcete-li toto chování zakázat v řádkové vrstvě, přidejte filtr, který umožňuje `LineString` a `MultiLineString` obsahuje pouze funkce.  

## <a name="math-expressions"></a>Matematické výrazy

Matematické výrazy poskytují matematické operátory k provádění výpočtů založených na datech v rámci výrazu.

| Expression | Návratový typ | Popis |
|------------|-------------|-------------|
| `['+', number, number, …]` | číslo | Vypočítá součet zadaných čísel. |
| `['-', number]` | číslo | Odečte 0 podle zadaného čísla. |
| `['-', number, number]` | číslo | Odečte první čísla o druhé číslo. |
| `['*', number, number, …]` | číslo | Vynásobí zadaná čísla společně. |
| `['/', number, number]` | číslo | Vydělí první číslo druhým číslem. |
| `['%', number, number]` | číslo | Vypočítá zbytek při vyděleníprvního čísla druhým číslem. |
| `['^', number, number]` | číslo | Vypočítá hodnotu první hodnoty zvýšené na mocninu druhého čísla. |
| `['abs', number]` | číslo | Vypočítá absolutní hodnotu zadaného čísla. |
| `['acos', number]` | číslo | Vypočítá arckosinin zadaného čísla. |
| `['asin', number]` | číslo | Vypočítá arcinsin zadaného čísla. |
| `['atan', number]` | číslo | Vypočítá arctangent zadaného čísla. |
| `['ceil', number]` | číslo | Zaokrouhlí číslo na další celé celé číslo. |
| `['cos', number]` | číslo | Vypočítá cos zadaného čísla. |
| `['e']` | číslo | Vrátí matematickou `e`konstantu . |
| `['floor', number]` | číslo | Zaokrouhlí číslo dolů na předchozí celé celé číslo. |
| `['ln', number]` | číslo | Vypočítá přirozený logaritmus zadaného čísla. |
| `['ln2']` | číslo | Vrátí matematickou `ln(2)`konstantu . |
| `['log10', number]` | číslo | Vypočítá logaritmus zadaný počet základní deset. |
| `['log2', number]` | číslo | Vypočítá logaritmus zadaný počet na základě dvou základních. |
| `['max', number, number, …]` | číslo | Vypočítá maximální počet v zadané sadě čísel. |
| `['min', number, number, …]` | číslo | Vypočítá minimální počet v zadané sadě čísel. |
| `['pi']` | číslo | Vrátí matematickou `PI`konstantu . |
| `['round', number]` | číslo | Zaokrouhlí číslo na nejbližší celé číslo. Hodnoty na půli jsou zaokrouhleny od nuly. Například `['round', -1.5]` vyhodnotí -2. |
| `['sin', number]` | číslo | Vypočítá sinus zadaného čísla. |
| `['sqrt', number]` | číslo | Vypočítá druhou odmocninu zadaného čísla. |
| `['tan', number]` | číslo | Vypočítá tečnu zadaného čísla. |

## <a name="aggregate-expression"></a>Agregační výraz

Agregační výraz definuje výpočet, který je zpracován přes sadu dat `clusterProperties` a lze `DataSource`jej použít s možností . Výstupem těchto výrazů musí být číslo nebo logická hodnota. 

Agregační výraz přebírá tři hodnoty: hodnotu operátoru a počáteční hodnotu a výraz pro načtení vlastnosti z každé funkce v datech, na které se použije agregační operace. Tento výraz má následující formát:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- Operátor: Funkce výrazu, která se pak aplikuje `mapExpression` na všechny hodnoty vypočítané pro každý bod v clusteru. Podporovaní operátoři: 
    - Pro `+`čísla: `*` `max`, , , ,`min`
    - Pro logické hodnoty: `all`,`any`
- initialValue: Počáteční hodnota, ve které je první vypočtená hodnota agregována proti.
- mapExpression: Výraz, který je použit proti každému bodu v sadě dat.

**Příklady**

Pokud všechny funkce v datové `revenue` sadě mají vlastnost, což je číslo. Poté lze vypočítat celkové výnosy všech bodů v clusteru, které jsou vytvořeny ze sady dat. Tento výpočet se provádí pomocí následujícího agregačního výrazu:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Logické výrazy

Logické výrazy poskytují sadu logických operátorů výrazy pro hodnocení logické porovnání.

Při porovnávání hodnot je porovnání striktně zadáno. Hodnoty různých typů jsou vždy považovány za nerovné. Případy, kdy jsou známy typy se liší v době analýzy jsou považovány za neplatné a způsobí chybu analýzy. 

| Expression | Návratový typ | Popis |
|------------|-------------|-------------|
| `['! ', boolean]` | Boolean | Logické negace. Vrátí, `true` pokud `false`je `false` vstup a `true`pokud je vstup . |
| `['!= ', value, value]` | Boolean | Vrátí, `true` pokud vstupní hodnoty `false` nejsou stejné, jinak. |
| `['<', value, value]` | Boolean | Vrátí, `true` pokud první vstup je přísně `false` menší než druhý, jinak. Argumenty musí být buď oba řetězce nebo obě čísla. |
| `['<=', value, value]` | Boolean | Vrátí, `true` pokud první vstup je menší nebo `false` rovno druhému, jinak. Argumenty musí být buď oba řetězce nebo obě čísla. |
| `['==', value, value]` | Boolean | Vrátí, `true` pokud jsou vstupní `false` hodnoty stejné, jinak. Argumenty musí být buď oba řetězce nebo obě čísla. |
| `['>', value, value]` | Boolean | Vrátí, `true` pokud první vstup je přísně `false` větší než druhý, jinak. Argumenty musí být buď oba řetězce nebo obě čísla. |
| `['>=' value, value]` | Boolean | Vrátí, `true` pokud první vstup je větší nebo `false` rovno druhé, jinak. Argumenty musí být buď oba řetězce nebo obě čísla. |
| `['all', boolean, boolean, …]` | Boolean | Vrátí, `true` pokud jsou `true`všechny `false` vstupy , jinak. |
| `['any', boolean, boolean, …]` | Boolean | Vrátí, `true` pokud některý ze `true` `false` vstupů jsou , jinak. |

## <a name="conditional-expressions"></a>Podmíněné výrazy

Podmíněné výrazy poskytují logické operace, které jsou jako if-příkazy.

Následující výrazy provádět operace podmíněné logiky na vstupní data. Například výraz `case` poskytuje logiku "if/then/else", zatímco `match` výraz je jako "switch-statement". 

### <a name="case-expression"></a>Výraz případu

Výraz `case` je typ podmíněného výrazu, který poskytuje logiku "if/then/else". Tento typ výrazu prochází seznamem logických podmínek. Vrátí výstupní hodnotu první logické podmínky, která má být vyhodnocena jako true.

Následující pseudokód definuje strukturu výrazu. `case` 

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

Následující příklad prochází různé logické podmínky, dokud nenajde `true`ten, který vyhodnotí na , a pak vrátí tuto přidruženou hodnotu. Pokud žádná logická podmínka `true`vyhodnotí , bude vrácena záložní hodnota. 

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

### <a name="match-expression"></a>Shodovat výraz

Výraz `match` je typ podmíněného výrazu, který poskytuje logiku příkazu switch. Vstup může být libovolný `['get', 'entityType']` výraz, například který vrací řetězec nebo číslo. Každý popisek musí být buď jedna hodnota literálu nebo pole literálových hodnot, jejichž hodnoty musí být všechny řetězce nebo všechna čísla. Vstup odpovídá, pokud se shodují některé z hodnot v poli. Každý popisek musí být jedinečný. Pokud vstupní typ neodpovídá typu popisků, výsledkem bude záložní hodnota.

Následující pseudokód definuje strukturu výrazu. `match` 

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

Následující příklad se `entityType` dívá na vlastnost prvku Bod ve vrstvě bublin hledá shodu. Pokud najde shodu, je vrácena zadaná hodnota nebo vrátí záložní hodnotu.

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

Následující příklad používá pole k zobrazení seznamu sady popisků, které by měly všechny vrátit stejnou hodnotu. Tento přístup je mnohem efektivnější než výpis jednotlivých štítků jednotlivě. V tomto případě, `entityType` pokud je nemovitost "restaurace" nebo "grocery_store", bude vrácena barva "červená".

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

Následující příklad používá výraz shody k provedení filtru typu "v poli" nebo "matice obsahuje". V tomto případě výraz filtruje data, která mají hodnotu ID, která je v seznamu povolených ID. Při použití výrazů s filtry musí být výsledkem logická hodnota.

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

### <a name="coalesce-expression"></a>Výraz coalesce

Výraz `coalesce` prochází sadou výrazů, dokud není získána první hodnota bez hodnoty null a vrátí tuto hodnotu. 

Následující pseudokód definuje strukturu výrazu. `coalesce` 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Příklad**

Následující příklad používá `coalesce` výraz k `textField` nastavení volby vrstvy symbolů. Pokud `title` vlastnost chybí funkce nebo nastavena na `null`, výraz se `subtitle` pak pokusí hledat `null`vlastnost, pokud její chybějící nebo , pak se vrátí zpět na prázdný řetězec. 

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

Následující příklad používá `coalesce` výraz k načtení první dostupné ikony obrázku, která je k dispozici v mapovém symbolu, ze seznamu zadaných názvů obrázků.

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

## <a name="type-expressions"></a>Textové výrazy

Textové výrazy poskytují nástroje pro testování a převod různých datových typů, jako jsou řetězce, čísla a logické hodnoty.

| Expression | Návratový typ | Popis |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | objekt \| pole | Vrátí hodnotu literálu nebo objektu. Tento výraz slouží k zabránění vyhodnocování pole nebo objektu jako výrazu. To je nezbytné, když pole nebo objekt musí být vráceny výrazem. |
| `['image', string]` | řetězec | Zkontroluje, zda je do symbolu mapy načteno zadané ID obrázku. Pokud je, ID je vrácena, jinak null je vrácena. |
| `['to-boolean', value]` | Boolean | Převede vstupní hodnotu na logickou hodnotu. Výsledkem `false` je, když je vstupem `null`prázdný `NaN`řetězec , `0`, `false`, nebo ; jinak `true`jeho . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Převede vstupní hodnotu na barvu. Pokud je k dispozici více hodnot, každá z nich je vyhodnocena v pořadí, dokud není získán první úspěšný převod. Pokud nelze převést žádný ze vstupů, výraz je chyba. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | číslo | Pokud je to možné, převede vstupní hodnotu na číslo. Pokud je `null` vstup `false`nebo , výsledek je 0. Pokud je `true`vstup , výsledek je 1. Pokud je vstupem řetězec, je převeden na číslo pomocí funkce řetězce [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) specifikace jazyka ECMAScript. Pokud je k dispozici více hodnot, každá z nich je vyhodnocena v pořadí, dokud není získán první úspěšný převod. Pokud nelze převést žádný ze vstupů, výraz je chyba. |
| `['to-string', value]` | řetězec | Převede vstupní hodnotu na řetězec. Pokud je `null`vstup , `""`výsledek je . Pokud je vstup logický, výsledkem `"true"` `"false"`je nebo . Pokud je vstupem číslo, je převeden na řetězec pomocí funkce [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) number specifikace jazyka ECMAScript. Pokud je vstupem barva, je převeden na řetězec `"rgba(r,g,b,a)"`barev CSS RGBA . V opačném případě je vstup převeden na řetězec pomocí funkce [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) specifikace jazyka ECMAScript. |
| `['typeof', value]` | řetězec | Vrátí řetězec popisující typ dané hodnoty. |

> [!TIP]
> Pokud se v `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` konzole prohlížeče zobrazí chybová zpráva podobná, znamená to, že někde v kódu je výraz, který má pole, které nemá řetězec pro svou první hodnotu. Pokud chcete, aby výraz vrátil pole, zabalte pole s výrazem. `literal` Následující příklad nastaví `offset` volbu ikony vrstvy symbolů, která musí být pole `match` obsahující dvě čísla, pomocí výrazu `entityType` pro výběr mezi dvěma hodnotami odsazení na základě hodnoty vlastnosti prvku bodu.
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

## <a name="color-expressions"></a>Barevné výrazy

Barevné výrazy usnadňují vytváření a manipulaci s hodnotami barev.

| Expression | Návratový typ | Popis |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Vytvoří hodnotu barvy z *červené*, *zelené*a `0` *modré* součásti, která musí být v rozsahu mezi a `255`a alfa komponentou `1`. Pokud je některá součást mimo rozsah, výraz je chyba. |
| `['rgba', number, number, number, number]` | color | Vytvoří hodnotu barvy z *červené*, *zelené*, `0` `255` *modré* součásti, které musí být v rozsahu mezi a , a alfa komponenty v rozsahu `0` a `1`. Pokud je některá součást mimo rozsah, výraz je chyba. |
| `['to-rgba']` | \[číslo, číslo, číslo, číslo\] | Vrátí pole se čtyřmi prvky obsahující *komponenty vstupní*barvy červené , *zelené*, *modré*a *alfa* v tomto pořadí. |

**Příklad**

Následující příklad vytvoří hodnotu barvy RGB, `255`která má *červenou* hodnotu , a *zelené* a `temperature` *modré* hodnoty, které jsou vypočteny vynásobením `2.5` hodnotou vlastnosti. Jak se teplota mění, barva se změní na různé odstíny *červené*.

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

## <a name="string-operator-expressions"></a>Výrazy operátoru řetězce

Výrazy operátoru řetězce provádějí operace převodu na řetězce, jako je například zřetězení a převod případu. 

| Expression | Návratový typ | Popis |
|------------|-------------|-------------|
| `['concat', string, string, …]` | řetězec | Zřetězí více řetězců dohromady. Každá hodnota musí být řetězec. Pomocí `to-string` výrazu typu můžete v případě potřeby převést jiné typy hodnot na řetězec. |
| `['downcase', string]` | řetězec | Převede zadaný řetězec na malá písmena. |
| `['upcase', string]` | řetězec | Převede zadaný řetězec na velká písmena. |

**Příklad**

Následující příklad převede `temperature` vlastnost prvku point na řetězec a potom zřetězí "°F" na jeho konec.

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

Výše uvedený výraz vykreslí špendlík na mapě s textem "64° F", který je na něm překrytý, jak je znázorněno na obrázku níže.

<center>

![Příklad](media/how-to-expressions/string-operator-expression.png) výrazu operátoru řetězce</center>

## <a name="interpolate-and-step-expressions"></a>Interpolaci a krok výrazy

Interpolaci a krokvýrazy lze použít k výpočtu hodnot podél interpolované křivky nebo funkce kroku. Tyto výrazy se ve výrazu, který vrací číselnou hodnotu jako jejich vstup, například `['get',  'temperature']`. Vstupní hodnota je vyhodnocena proti párům vstupních a výstupních hodnot, aby se určila hodnota, která nejlépe odpovídá interpolované funkci křivky nebo kroku. Výstupní hodnoty se nazývají "zastávky". Vstupní hodnoty pro každou zarážku musí být číslo a musí být ve vzestupném pořadí. Výstupní hodnoty musí být číslo a pole čísel nebo barva.

### <a name="interpolate-expression"></a>Interpolace výraz

Výraz `interpolate` lze použít k výpočtu souvislé, hladké sady hodnot interpolací mezi hodnotami stop. Výraz, `interpolate` který vrací hodnoty barev, vytvoří barevný přechod, ze kterého jsou vybrány výsledné hodnoty.

Existují tři typy metod interpolace, které `interpolate` lze použít ve výrazu:
 
* `['linear']`- Interpoluje lineárně mezi dvojicí zastávek.
* `['exponential', base]`- Interpoluje exponenciálně mezi zastávkami. Hodnota `base` řídí rychlost, jakou se zvyšuje výstup. Vyšší hodnoty činí zvýšení výstupu více směrem k high-end rozsahu. Hodnota `base` blízká hodnotě 1 vytvoří výstup, který se lineárněji zvýší.
* `['cubic-bezier', x1, y1, x2, y2]`- Interpoluje pomocí [kubické Bezierovy křivky](https://developer.mozilla.org/docs/Web/CSS/timing-function) definované danými kontrolními body.

Zde je příklad toho, jak tyto různé typy interpolací vypadají. 

| Lineární  | Exponenciální | Kubický Bezier |
|---------|-------------|--------------|
| ![Graf lineární interpolace](media/how-to-expressions/linear-interpolation.png) | ![Exponenciální interpolační graf](media/how-to-expressions/exponential-interpolation.png) | ![Graf interpolace kubických bezierů](media/how-to-expressions/bezier-curve-interpolation.png) |

Následující pseudokód definuje strukturu výrazu. `interpolate` 

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

Následující příklad používá `linear interpolate` výraz k `color` nastavení vlastnosti bublinové `temperature` vrstvy na základě vlastnosti prvku bod. Pokud `temperature` je hodnota menší než 60, bude vrácena "modrá". Pokud je to mezi 60 a méně než 70, žlutá bude vrácena. Pokud je to mezi 70 a méně než 80, "oranžová" se vrátí. Pokud je to 80 nebo vyšší, "červená" se vrátí.

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

![Příklad interpolace](media/how-to-expressions/interpolate-expression-example.png) výrazu</center>

### <a name="step-expression"></a>Výraz kroku

Výraz `step` lze použít k výpočtu diskrétní, stupňovité hodnoty výsledku vyhodnocením [po částech konstantní funkce](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definované dorazy. 

Následující pseudokód definuje strukturu výrazu. `step` 

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

Výrazy kroku vrátí výstupní hodnotu stop těsně před vstupní hodnotu nebo první vstupní hodnotu, pokud je vstup menší než první stop. 

**Příklad**

Následující příklad používá `step` výraz k `color` nastavení vlastnosti bublinové `temperature` vrstvy na základě vlastnosti prvku bod. Pokud `temperature` je hodnota menší než 60, bude vrácena "modrá". Pokud je to mezi 60 a méně než 70, "žlutá" se vrátí. Pokud je to mezi 70 a méně než 80, "oranžová" se vrátí. Pokud je to 80 nebo vyšší, "červená" se vrátí.

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

Speciální výrazy, které platí pouze pro určité hladiny.

### <a name="heat-map-density-expression"></a>Výraz hustoty tepelné mapy

Výraz hustoty tepelné mapy načte hodnotu hustoty tepelné mapy pro každý `['heatmap-density']`pixel ve vrstvě tepelné mapy a je definován jako . Tato hodnota je `0` číslo `1`mezi a . Používá se v kombinaci `interpolation` `step` s a nebo výrazem k definování barevného gradientu, který se používá k vybarvení tepelné mapy. Tento výraz lze použít pouze v [barevné volbě](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) vrstvy tepelné mapy.

> [!TIP]
> Barva v indexu 0, ve výrazu interpolace nebo výchozí barva barvy kroku, definuje barvu oblasti, kde nejsou žádná data. Barvu v indexu 0 lze použít k definování barvy pozadí. Mnozí dávají přednost nastavení této hodnoty na průhlednou nebo poloprůhlednou černou.

**Příklad**

Tento příklad používá výraz interpolace vložky k vytvoření hladkého barevného přechodu pro vykreslení tepelné mapy. 

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

Kromě použití hladkého přechodu k vybarvení tepelné mapy lze barvy zadat v `step` rámci sady rozsahů pomocí výrazu. Použití `step` výrazu pro vybarvení tepelné mapy opticky rozdělí hustotu na rozsahy, které se podobají obrysové nebo radarové mapě stylu.  

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

Další informace naleznete v dokumentaci [k vrstvě přidání tepelné mapy.](map-add-heat-map-layer.md)

### <a name="line-progress-expression"></a>Výraz průběhu řádku

Výraz průběhu řádku načte průběh podél čáry přechodu v `['line-progress']`řádkové vrstvě a je definován jako . Tato hodnota je číslo mezi 0 a 1. Používá se v kombinaci `interpolation` `step` s výrazem nebo. Tento výraz lze použít pouze s [volbou strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) čárové vrstvy. 

> [!NOTE]
> Volba `strokeGradient` řádkové vrstvy `lineMetrics` vyžaduje, aby byla nastavena `true`možnost zdroje dat na .

**Příklad**

Tento příklad `['line-progress']` používá výraz k aplikování barevného přechodu na tah čáry.

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

[Zobrazit živý příklad](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Výraz formátu textového pole

Výraz formátu textového pole lze `textField` použít s `textOptions` volbou vlastnosti vrstvy symbolů pro zajištění smíšeného formátování textu. Tento výraz umožňuje zadat sadu vstupních řetězců a možností formátování. Pro každý vstupní řetězec v tomto výrazu lze zadat následující možnosti.

 * `'font-scale'`- Určuje faktor měřítka pro velikost písma. Pokud je zadán, tato `size` hodnota přepíše vlastnost `textOptions` pro jednotlivé řetězce.
 * `'text-font'`- Určuje jednu nebo více rodin písem, které by měly být použity pro tento řetězec. Pokud je zadán, tato `font` hodnota přepíše vlastnost `textOptions` pro jednotlivé řetězce.
 * `'text-color'`- Určuje barvu, která se má použít na text při vykreslování. 

Následující pseudokód definuje strukturu výrazu formátu textového pole. 

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

Následující příklad zformátuje textové pole přidáním tučného písma a `title` zmenšením velikosti písma vlastnosti prvku. Tento příklad také `subtitle` přidá vlastnost prvku na nový řádek se zmenšenou velikostí písma a červenou barvou.

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

Tato vrstva vykreslí bodový prvek, jak je znázorněno na obrázku níže:
 
<center>

![Obrázek funkce Bod s](media/how-to-expressions/text-field-format-expression.png) formátovaného textového pole</center>

### <a name="number-format-expression"></a>Výraz formátu čísla

Výraz `number-format` lze použít pouze `textField` s možností vrstvy symbolu. Tento výraz převede zadaný počet na formátovaný řetězec. Tento výraz zabalí funkci [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) jazyka JavaScript a podporuje následující sadu možností.

 * `locale`- Zadejte tuto možnost pro převod čísel na řetězce způsobem, který je zarovnán se zadaným jazykem. Předat [značku jazyka BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) do této možnosti.
 * `currency`- Převést číslo na řetězec představující měnu. Možné hodnoty jsou [kódy měn ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), například "USD" pro americký dolar, "EUR" pro euro nebo "CNY" pro čínské RMB.
 * `'min-fraction-digits'`- Určuje minimální počet desetinných míst, které mají být zahrnuty do verze řetězce čísla.
 * `'max-fraction-digits'`- Určuje maximální počet desetinných míst, která mají být zahrnuta do řetězcové verze čísla.

Následující pseudokód definuje strukturu výrazu formátu textového pole. 

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

Následující příklad používá `number-format` výraz k `revenue` úpravě způsobu vykreslení vlastnosti `textField` prvku bodu ve volbě vrstvy symbolů tak, aby se zjevovala jako hodnota amerického dolaru.

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

Tato vrstva vykreslí bodový prvek, jak je znázorněno na obrázku níže:

<center>

![Příklad](media/how-to-expressions/number-format-expression.png) výrazu formátu čísla</center>

### <a name="image-expression"></a>Výraz obrázku

Obrazový výraz lze použít `image` `textField` s volbami a vrstvou symbolu a `fillPattern` s volbou vrstvy polygonu. Tento výraz zkontroluje, zda požadovaný obrázek existuje ve stylu `null`a vrátí buď název přeloženého obrázku, nebo v závislosti na tom, zda je obrázek aktuálně ve stylu. Tento proces ověření je synchronní a vyžaduje, aby obrázek byl přidán do stylu před vyžádáním v argumentu obrázku.

**Příklad**

Následující příklad používá `image` výraz k přidání ikony s textem ve vrstvě symbolů. 

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

Tato vrstva vykreslí textové pole ve vrstvě symbolů, jak je znázorněno na obrázku níže:

<center>

![Příklad](media/how-to-expressions/image-expression.png) výrazu obrázku</center>

## <a name="zoom-expression"></a>Výraz Lupa

Výraz `zoom` se používá k načtení aktuální úrovně přiblížení mapy v `['zoom']`době vykreslení a je definován jako . Tento výraz vrátí číslo mezi minimálním a maximálním rozsahem úrovně přiblížení mapy. Ovládací prvky interaktivnímapy Azure Maps pro web a Android podporují 25 úrovní zvětšení, očíslovaných 0 až 24. Použití `zoom` výrazu umožňuje dynamicky měnit styly při změně úrovně přiblížení mapy. Výraz `zoom` lze použít pouze `interpolate` `step` s výrazy a výrazy.

**Příklad**

Ve výchozím nastavení mají poloměry datových bodů vykreslené ve vrstvě tepelné mapy pevný poloměr obrazových bodů pro všechny úrovně zvětšení. Při přiblížení mapy se data agregují a vrstva tepelné mapy vypadá jinak. Výraz `zoom` lze použít ke zmenšení poloměru pro každou úroveň přiblížení tak, aby každý datový bod pokrýval stejnou fyzickou oblast mapy. To bude vrstva tepelné mapy vypadat více statické a konzistentní. Každá úroveň přiblížení mapy má dvakrát tolik obrazových bodů svisle a vodorovně než předchozí úroveň přiblížení. Změna poloměru tak, aby se zdvojnásobila s každou úrovní přiblížení, vytvoří tepelnou mapu, která bude vypadat konzistentní na všech úrovních přiblížení. To lze provést pomocí `zoom` výrazu `base 2 exponential interpolation` s výrazem, s poloměrem obrazových bodů nastavena na minimální `2 * Math.pow(2, minZoom - maxZoom)` úroveň zvětšení a zmenšenpoloměr pro maximální úroveň zvětšení vypočtené, jak je znázorněno níže.

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

[Zobrazit živý příklad](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Výrazy vazby proměnných

Výrazy vazby proměnné ukládají výsledky výpočtu do proměnné. Tak, že výsledky výpočtu lze odkazovat jinde ve výrazu vícekrát. Jedná se o užitečnou optimalizaci pro výrazy, které zahrnují mnoho výpočtů.

| Expression | Návratový typ | Popis |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"nechme",<br/>&nbsp;&nbsp;&nbsp;&nbsp;jméno1: řetězec,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: jakákoli,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: řetězec,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: jakákoli,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;podřízený výraz<br/>\] | | Ukládá jednu nebo více hodnot jako `var` proměnné pro použití výrazem v podřízeném výrazu, který vrací výsledek. |
| `['var', name: string]` | jakékoli | Odkazuje na proměnnou, která `let` byla vytvořena pomocí výrazu. |

**Příklad**

Tento příklad používá výraz, který vypočítá poměr výnosů vzhledem k teplotě a potom použije `case` výraz k vyhodnocení různých logických operací pro tuto hodnotu. Výraz `let` se používá k uložení výnosů vzhledem k poměru k teplotě, takže je třeba vypočítat pouze jednou. Výraz `var` odkazuje na tuto proměnnou tak často, jak je potřeba, aniž by bylo nutné ji přepočítat.

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

Další ukázky kódu, které implementují výrazy, naleznete v následujících článcích:

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
> [Možnosti bublinové vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [Možnosti linelayeru](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [Možnosti vícevrstvé vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
