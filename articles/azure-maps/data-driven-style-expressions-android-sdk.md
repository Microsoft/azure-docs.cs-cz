---
title: Výrazy stylu založené na datech v mapách Androidu | Mapy Microsoft Azure
description: Přečtěte si o výrazech stylu založených na datech. Přečtěte si téma jak použít tyto výrazy v Azure Maps Android SDK k úpravě stylů v mapách.
author: rbrundritt
ms.author: richbrun
ms.date: 12/1/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 61d7a295d86fd7da74dee03cd35c79feea0218ed
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681492"
---
# <a name="data-driven-style-expressions-android-sdk"></a>Výrazy stylu řízené daty (Android SDK)

Výrazy umožňují použít obchodní logiku pro stylování možností, které sledují vlastnosti definované v jednotlivých tvarech ve zdroji dat. Výrazy mohou filtrovat data ve zdroji dat nebo vrstvě. Výrazy mohou být tvořeny podmíněnou logikou, například if-statements. A lze je použít k manipulaci s daty pomocí: operátory řetězce, logické operátory a matematické operátory.

Styly řízené daty omezují množství kódu potřebného k implementaci obchodní logiky kolem stylu. Při použití s vrstvami jsou výrazy vyhodnocovány v době vykreslování v samostatném vlákně. Tato funkce poskytuje vyšší výkon v porovnání s hodnocením obchodní logiky ve vlákně uživatelského rozhraní.

Azure Maps Android SDK podporuje téměř všechny stejné výrazy stylu jako rozhraní Azure Maps Web SDK, takže všechny stejné koncepty, které jsou uvedeny ve [výrazech stylu na základě dat (Web SDK)](data-driven-style-expressions-web-sdk.md) , lze přenášet do aplikace pro Android. Všechny výrazy stylu v Azure Maps Android SDK jsou k dispozici v rámci `com.microsoft.azure.maps.mapcontrol.options.Expression` oboru názvů. Existuje mnoho různých typů výrazů stylu.

| Typ výrazů | Popis |
|---------------------|-------------|
| [Logické výrazy](#boolean-expressions) | Logické výrazy poskytují sadu logických výrazů operátorů pro vyhodnocení logických porovnání. |
| [Výrazy barev](#color-expressions) | Výrazy s barvami usnadňují vytváření a manipulaci s hodnotami barev. |
| [Podmíněné výrazy](#conditional-expressions) | Podmíněné výrazy poskytují operace logiky, které jsou například if-statements. |
| [Datové výrazy](#data-expressions) | Poskytuje přístup k datům vlastností ve funkci. |
| [Interpolovat a krokovat výrazy](#interpolate-and-step-expressions) | Výrazy interpolování a Step lze použít k výpočtu hodnot podél interpolované křivky nebo funkce kroku. |
| [Výrazy založené na formátu JSON](#json-based-expressions) | Usnadňuje opakované použití stylu nezpracované výrazy založené na formátu JSON vytvořené pro webovou sadu pomocí Android SDK. |  
| [Výrazy specifické pro vrstvu](#layer-specific-expressions) | Speciální výrazy, které platí pouze pro jednu vrstvu. |
| [Matematické výrazy](#math-expressions) | Poskytuje matematické operátory pro provádění výpočtů řízených daty v rámci rozhraní Expression Framework. |
| [Výrazy operátorů řetězce](#string-operator-expressions) | Výrazy operátoru řetězce provádějí operace převodu na řetězcích, jako je zřetězení a převod případu. |
| [Výrazy typu](#type-expressions) | Výrazy typu poskytují nástroje pro testování a převod různých typů dat, jako jsou řetězce, čísla a logické hodnoty. |
| [Výrazy vazeb proměnných](#variable-binding-expressions) | Výrazy vazeb proměnných ukládají výsledky výpočtu do proměnné a odkazují jinde ve výrazu víckrát bez nutnosti přepočítat uloženou hodnotu. |
| [Výraz lupy](#zoom-expression) | Načte aktuální úroveň přiblížení mapy v době vykreslování. |

Všechny příklady v této části dokumentu používají následující funkci k předvedení různých způsobů, jak lze tyto výrazy použít.

```json
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
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

Následující kód ukazuje, jak ručně vytvořit tuto funkci pro zobrazení v angličtině v aplikaci.

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

Následující kód ukazuje, jak rekonstruovat verzi dokument objektu JSON do funkce pro zobrazení objektivu JSON v aplikaci.

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

## <a name="json-based-expressions"></a>Výrazy založené na formátu JSON

Sada Azure Maps Web SDK podporuje také výrazy stylu řízené daty, které jsou reprezentovány pomocí pole JSON. Tyto stejné výrazy lze znovu vytvořit pomocí nativní `Expression` třídy v Android SDK. Alternativně lze tyto výrazy založené na JSON převést na řetězec pomocí webové funkce, jako je `JSON.stringify` a předaná `Expression.raw(String rawExpression)` metodě. Například proveďte následující výraz JSON.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

Dokument verze výše uvedeného výrazu by byla `"['get','title']"` a mohla by se číst do Android SDK následujícím způsobem.

```java
Expression exp = Expression.raw("['get','title']")
```

Použití tohoto přístupu může zjednodušit opakované použití výrazů stylu mezi mobilními a webovými aplikacemi, které používají Azure Maps.

Toto video poskytuje přehled stylů založených na datech v Azure Maps.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>Datové výrazy

Datové výrazy poskytují přístup k datům vlastností ve funkci.

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `accumulated()` | číslo | Získá hodnotu zatím shromážděné hodnoty vlastnosti clusteru. |
| `at(number | Expression, Expression)` | hodnota | Načte položku z pole. |
| `geometryType()` | řetězec | Získá typ geometrie funkce: Point, MultiPoint, LineString, MultiLineString, mnohoúhelník, promnohoúhelník. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | hodnota | Získá hodnotu vlastnosti z vlastností poskytnutého objektu. Vrátí hodnotu null, pokud chybí požadovaná vlastnost. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | Určuje, zda vlastnosti funkce mají zadanou vlastnost. |
| `id()` | hodnota | Získá ID funkce, pokud má jednu. |
| `in(string | number | Expression, Expression)` | boolean | Určuje, jestli položka existuje v poli. |
| `length(string | Expression)` | číslo | Získá délku řetězce nebo pole. |
| `properties()`| hodnota | Získá objekt vlastností funkce. |

Následující výrazy stylu Web SDK nejsou podporovány v Android SDK:

- index-z
- průřez

**Příklady**

K vlastnostem funkce lze získat přímý pøístup ve výrazu pomocí `get` výrazu. V tomto příkladu je použita `zoneColor` hodnota funkce k určení vlastnosti Color pro bublinovou vrstvu.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

Výše uvedený příklad bude fungovat správně, pokud všechny funkce bodu mají `zoneColor` vlastnost. Pokud ne, barva se nejspíš vrátí na "Black". Chcete-li upravit záložní barvu, použijte `switchCase` výraz v kombinaci s `has` výrazem k ověření, zda vlastnost existuje. Pokud vlastnost neexistuje, vrátí se záložní barva.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

Vrstvy bublinových a symbolů budou ve výchozím nastavení vykreslovat souřadnice všech tvarů ve zdroji dat. Toto chování může zvýraznit vrcholy mnohoúhelníku nebo čáry. `filter`Možnost vrstvy lze použít k omezení typu geometrie funkcí, které vykresluje, pomocí `geometryType` výrazu v rámci logického výrazu. Následující příklad omezuje bublinovou vrstvu tak, aby `Point` byly vykresleny pouze funkce.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(eq(geometryType(), "Point"))
);
```

Následující příklad umožňuje vykreslení obou `Point` `MultiPoint` funkcí a. 

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

Podobně, obrys mnohoúhelníků se vykreslí do vrstev čar. Chcete-li toto chování zakázat v linkové vrstvě, přidejte filtr, který povoluje `LineString` pouze `MultiLineString` funkce a.  

Tady jsou některé další příklady použití datových výrazů:

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>Matematické výrazy

Matematické výrazy poskytují matematické operátory pro provádění výpočtů řízených daty v rámci rozhraní Expression Framework.

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `abs(number | Expression)` | číslo | Vypočítá absolutní hodnotu zadaného čísla. |
| `acos(number | Expression)` | číslo | Vypočítá Arkus kosinus zadaného čísla. |
| `asin(number | Expression)` | číslo | Vypočítá Arkus sinus zadaného čísla. |
| `atan(number | Expression)` | číslo | Vypočítá arkustangens zadaného čísla. |
| `ceil(number | Expression)` | číslo | Zaokrouhlí číslo nahoru na nejbližší celé číslo. |
| `cos(number | Expression)` | číslo | Vypočítá cos zadaného čísla. |
| `division(number, number)` \| `division(Expression, Expression)` | číslo | Vydělí první číslo druhým číslem. Ekvivalentní výraz sady web SDK: `/` |
| `e()` | číslo | Vrátí matematickou konstantu `e` . |
| `floor(number | Expression)` | číslo | Zaokrouhlí číslo dolů na předchozí celé číslo. |
| `log10(number | Expression)` | číslo | Vypočítá dekadický logaritmus zadaného čísla. |
| `log2(number | Expression)` | číslo | Vypočítá dekadický logaritmus zadaného čísla. |
| `ln(number | Expression)` | číslo | Vypočítá přirozený logaritmus určeného čísla. |
| `ln2()` | číslo | Vrátí matematickou konstantu `ln(2)` . |
| `max(numbers... | expressions...)` | číslo | Vypočítá maximální počet v zadané sadě čísel. |
| `min(numbers... | expressions...)` | číslo | Vypočítá minimální číslo v zadané sadě čísel. |
| `mod(number, number)` \| | `mod(Expression, Expression)` | číslo | Vypočítá zbytek při dělení prvního čísla druhým číslem. Ekvivalentní výraz sady web SDK: `%` |
| `pi()` | číslo | Vrátí matematickou konstantu `PI` . |
| `pow(number, number)` \| `pow(Expression, Expression)` | číslo | Vypočítá hodnotu první hodnoty vyvolanou mocninou druhého čísla. |
| `product(numbers... | expressions...)` | číslo | Vynásobí zadaná čísla dohromady. Ekvivalentní výraz sady web SDK: `*` |
| `round(number | Expression)` | číslo | Zaokrouhlí číslo na nejbližší celé číslo. Hodnoty v polovině se zaokrouhlují směrem od nuly. Například je `round(-1.5)` vyhodnocen jako `-2` . |
| `sin(number | Expression)` | číslo | Vypočítá sinus zadaného čísla. |
| `sqrt(number | Expression)` | číslo | Vypočítá druhou odmocninu určeného čísla. |
| `subtract(number | Expression` | číslo | Odečte 0 zadaným číslem. |
| `subtract(number | Expression, number | Expression)` | číslo | Odečte první čísla o druhé číslo. |
| `sum(numbers... | expressions...)` | číslo | Vypočítá součet zadaných čísel. |
| `tan(number | Expression)` | číslo | Vypočítá tangens zadaného čísla. |

## <a name="boolean-expressions"></a>Logické výrazy

Logické výrazy poskytují sadu logických výrazů operátorů pro vyhodnocení logických porovnání.

Při porovnávání hodnot je porovnání striktně typované. Hodnoty různých typů jsou vždy považovány za nerovné. Případy, kde se označují, že typy jsou odlišné v době analýzy, jsou považovány za neplatné a vytvoří chybu analýzy.

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | Vrátí `true` , zda jsou všechny vstupy `true` , `false` jinak. |
| `any(Expression...)` | boolean | Vrátí `true` , zda je některý ze vstupů `true` , `false` jinak. |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Vrátí `true` , zda jsou vstupní hodnoty stejné, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Vrátí `true` , zda je první vstup striktně větší než druhý, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Vrátí `true` , zda je první zadání větší než nebo rovno druhému, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Vrátí `true` , zda je první vstup striktně menší než druhý, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Vrátí `true` , zda je první vstup menší nebo roven druhému, `false` jinak. Argumenty musí být buď řetězce, nebo obě čísla. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Vrátí `true` , zda vstupní hodnoty nejsou stejné, `false` jinak. |
| `not(Expression | boolean)` | boolean | Logická negace. Vrátí `true` , zda je vstup `false` , a `false` Pokud je vstup `true` . |

## <a name="conditional-expressions"></a>Podmíněné výrazy

Podmíněné výrazy poskytují operace logiky, které jsou například if-statements.

Následující výrazy provádějí Podmíněné logické operace na vstupních datech. Například `switchCase` výraz poskytuje logiku "if/then/else", zatímco `match` výraz je jako "Switch-Statement". 

### <a name="switch-case-expression"></a>Výraz přepínače Case

`switchCase`Výraz je typ podmíněného výrazu, který poskytuje logiku "if/then/else". Tento typ kroků výrazu se seznamem logických podmínek. Vrátí výstupní hodnotu první logické podmínky pro vyhodnocení na hodnotu true.

Následující pseudokódu definuje strukturu `switchCase` výrazu.

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**Příklad**

Následující příklad provádí v rámci různých logických podmínek, dokud nenajde ten, který je vyhodnocen jako `true` a následně vrací přidruženou hodnotu. Pokud se vyhodnotí žádná logická podmínka `true` , vrátí se záložní hodnota.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

### <a name="match-expression"></a>Výraz shody

`match`Výraz je typ podmíněného výrazu, který poskytuje příkaz typu Switch-Logic. Vstup může být libovolný výraz `get( "entityType")` , například, který vrátí řetězec nebo číslo. Každé zastavení musí mít popisek, který je buď hodnotou jediného literálu, nebo polem hodnot literálů, jejichž hodnoty musí být všechny řetězce nebo všechna čísla. Vstup se shoduje, pokud kterákoli z hodnot v poli odpovídá. Každé označení zastavení musí být jedinečné. Pokud typ vstupu neodpovídá typu popisků, bude výsledkem výchozí záložní hodnota.

Následující pseudokódu definuje strukturu `match` výrazu.

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**Příklady**

Následující příklad prohlíží `entityType` vlastnost bodu v bublinové vrstvě vyhledává shodu. Pokud najde shodu, je vrácena zadaná hodnota nebo vrátí záložní hodnotu.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

Následující příklad používá pole k vypsání sady popisků, které by měly vracet stejnou hodnotu. Tento přístup je mnohem efektivnější než uvedení každého popisku jednotlivě. V takovém případě, pokud `entityType` je vlastnost "restaurace" nebo "grocery_store", bude vrácena barva "Red".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

### <a name="coalesce-expression"></a>Výraz COALESCE

`coalesce`Kroky výrazu pomocí sady výrazů, dokud není získána první hodnota, která není null, a vrátí tuto hodnotu.

Následující pseudokódu definuje strukturu `coalesce` výrazu.

```java
coalesce(Expression... input)
```

**Příklad**

Následující příklad používá `coalesce` výraz pro nastavení `textField` Možnosti pro vrstvu symbolů. Pokud `title` vlastnost ve funkci chybí nebo je nastavená na `null` , výraz se pak pokusí vyhledat `subTitle` vlastnost, pokud chybí, nebo se `null` vrátí do prázdného řetězce. 

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

## <a name="type-expressions"></a>Výrazy typu

Výrazy typu poskytují nástroje pro testování a převod různých typů dat, jako jsou řetězce, čísla a logické hodnoty.

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `array(Expression)` | Objekt [] | Vyhodnotí, že vstup je pole. |
| `bool(Expression)` | boolean | Vyhodnotí, že vstupní hodnota je logická hodnota. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | Collator | Vrátí komplet pro použití v operacích porovnání závislých na národním prostředí. Možnosti s rozlišením velkých a malých písmen a s diakritikou jsou výchozí na false. Argument locale určuje značku jazyka IETF národního prostředí, které se má použít. Pokud není zadáno, použije se výchozí národní prostředí. Pokud požadované národní prostředí není k dispozici, komplet bude používat základní národní prostředí definované systémem. K otestování výsledků nouzového chování národního prostředí použijte vyřešený-locale.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | datový \| objekt řetězce logického čísla \| \| \| [] | Vrátí literálovou hodnotu pole nebo objektu. Tento výraz použijte k zabránění vyhodnocení pole nebo objektu jako výrazu. To je nezbytné, pokud musí být pole nebo objekt vráceny výrazem. |
| `number(Expression)` | číslo | Vyhodnotí, že vstupní hodnota je číslo. |
| `object(Expression)` | Objekt | Vyhodnotí, že vstupní hodnota je objekt. |
| `string(Expression)` | řetězec | Vyhodnotí, že vstupní hodnota je řetězec. |
| `toArray(Expression)` | Objekt [] | Převede výraz na pole objektu JSON. |
| `toBool(Expression)` | boolean | Převede vstupní hodnotu na logickou hodnotu. |
| `toNumber(Expression)` | číslo | Pokud je to možné, převede vstupní hodnotu na číslo. |
| `toString(Expression)` | řetězec | Převede vstupní hodnotu na řetězec. |
| `typeoOf(Expression)` | řetězec | Vrátí řetězec popisující typ dané hodnoty. |

## <a name="color-expressions"></a>Výrazy barev

Výrazy s barvami usnadňují vytváření a manipulaci s hodnotami barev.

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `color(int)` | color | Převede celočíselnou hodnotu barvy na výraz barvy. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | Vytvoří hodnotu barvy z *červených*, *zelených* a *modrých* komponent, které musí být v rozsahu mezi `0` a `255` a komponentou alfa `1` . Pokud je některá součást mimo rozsah, je výraz chybou. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | Vytvoří hodnotu barvy z *červené*, *zelené* a *modré* komponenty, která musí být v rozsahu od do `0` `255` a. alfa komponenta v rámci rozsahu `0` a `1` . Pokud je některá součást mimo rozsah, je výraz chybou. |
| `toColor(Expression)` | color  | Převede vstupní hodnotu na barvu. |
| `toRgba(Expression)` | color | Vrátí pole se čtyřmi prvky obsahující červenou, zelenou, modrou a alfa komponentu vstupní barvy v tomto pořadí. |

**Příklad**

Následující příklad vytvoří hodnotu barvy RGB s *červenou* hodnotou `255` a *zelenou* a *modrou* hodnotou, která je vypočítána vynásobením `2.5` hodnotou `temperature` Vlastnosti. Při změně teploty se barva změní na různé odstíny *červené*.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

Pokud jsou všechny parametry barev čísla, není nutné je zabalit pomocí `literal` výrazu. Příklad:

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

> [!TIP]
> Hodnoty barvy řetězce lze převést na barvu pomocí `android.graphics.Color.parseColor` metody. Následující převede hexadecimální řetězec barvy na barevný výraz, který lze použít s vrstvou.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>Výrazy operátorů řetězce

Výrazy operátoru řetězce provádějí operace převodu na řetězcích, jako je zřetězení a převod případu. 

| Výraz | Návratový typ | Popis |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | řetězec | Zřetězí více řetězců dohromady. Každá hodnota musí být řetězec. Použijte `toString` výraz Type pro převod ostatních typů hodnot na řetězec v případě potřeby. |
| `downcase(string)` \| `downcase(Expression)` | řetězec | Převede zadaný řetězec na malá písmena. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | Určuje, zda vstupní řetězec používá znakovou sadu podporovanou aktuálním zásobníkem písem. Příklad: `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | řetězec | Vrátí značku jazyka IETF národního prostředí, které používá poskytnutý řadicí modul. Dá se použít k určení výchozího národního prostředí systému nebo k určení, jestli se požadované národní prostředí úspěšně načetlo. |
| `upcase(string)` \| `upcase(Expression)` | řetězec | Převede zadaný řetězec na velká písmena. |

**Příklad**

Následující příklad převede `temperature` vlastnost bodu na řetězec a poté zřetězí "°F" na konci.

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

Výše uvedený výraz vykreslí kód PIN na mapě s textem "64 °F", jak je znázorněno na obrázku níže.

![Příklad výrazu řetězce operátoru](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>Interpolovat a krokovat výrazy

Výrazy interpolování a Step lze použít k výpočtu hodnot podél interpolované křivky nebo funkce kroku. Tyto výrazy přebírají výraz, který jako vstup vrátí číselnou hodnotu, například `get("temperature")` . Vstupní hodnota je vyhodnocena proti dvojicím vstupních a výstupních hodnot, aby bylo možné určit hodnotu, která nejlépe odpovídá interpolované křivce nebo funkci kroku. Výstupní hodnoty se nazývají "zastaví". Vstupní hodnoty pro každou stopu musí být číslo ve vzestupném pořadí. Výstupní hodnoty musí být číslo, pole čísel nebo barva.

### <a name="interpolate-expression"></a>Výraz interpolování

`interpolate`Výraz lze použít k výpočtu souvislé a hladké sady hodnot interpolací mezi hodnotami zastavení. `interpolate`Výraz, který vrací hodnoty barvy, vytváří barevný přechod, ve kterém jsou hodnoty výsledků vybrány z. `interpolate`Výraz má následující formáty:

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

Existují tři typy metod interpolace, které lze použít ve `interpolate` výrazu:

| Název | Popis | 
|------|-------------|
| `linear()` | Interpoluje linearitu mezi dvojicí zastávky.  |
| `exponential(number)` \| `exponential(Expression)` | Interpoluje exponenciálně mezi zastávkami. Je určena "základní" a určuje rychlost, s jakou se výstup zvyšuje. Vyšší hodnoty zvyšují objem výstupu směrem k hornímu konci rozsahu. "Základní" hodnota blížící se 1 vytvoří výstup, který se zvýší lineárně.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | Interpoluje použití [Bézierovy křivky krychle](https://developer.mozilla.org/docs/Web/CSS/timing-function) definované danými řídicími body. |

`stop`Výraz má formát `stop(stop, value)` .
 
Tady je příklad toho, jak tyto různé typy interpolace vypadají. 

| Lineární  | Exponenciální | Krychlová Bézierova krychle |
|---------|-------------|--------------|
| ![Graf lineární interpolace](media/how-to-expressions/linear-interpolation.png) | ![Graf exponenciální interpolace](media/how-to-expressions/exponential-interpolation.png) | ![Graf interpolace Bézierovy křivky krychle](media/how-to-expressions/bezier-curve-interpolation.png) |

**Příklad**

Následující příklad používá `linear interpolate` výraz pro nastavení `bubbleColor` vlastnosti bublinové vrstvy na základě `temperature` vlastnosti funkce Point. Pokud `temperature` je hodnota menší než 60, bude vrácena "modrá". Pokud je mezi 60 a menší než 70, vrátí se žlutá. Pokud je mezi 70 a menší než 80, vrátí se "oranžová" ( `#FFA500` ). Pokud je 80 nebo větší, vrátí se "Red".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

Následující obrázek ukazuje, jak jsou vybrány barvy pro výše uvedený výraz.

![Příklad interpolování výrazu](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Výraz kroku

`step`Výraz lze použít pro výpočet diskrétních a nejovlivněných výsledných hodnot vyhodnocením [funkce konstantní](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) , která je definována funkcí zastavení. 

`interpolate`Výraz má následující formáty:

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

Výrazy kroku vracejí výstupní hodnotu ukončení těsně před vstupní hodnotou nebo první vstupní hodnotu, pokud je vstup menší než první zarážka. 

**Příklad**

Následující příklad používá `step` výraz pro nastavení `bubbleColor` vlastnosti bublinové vrstvy na základě `temperature` vlastnosti funkce Point. Pokud `temperature` je hodnota menší než 60, bude vrácena "modrá". Pokud je mezi 60 a menší než 70, vrátí se "žlutá". Pokud je mezi 70 a menší než 80, bude vrácena "oranžová". Pokud je 80 nebo větší, vrátí se "Red".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

Následující obrázek ukazuje, jak jsou vybrány barvy pro výše uvedený výraz.
 
![Příklad výrazu kroku](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>Výrazy specifické pro vrstvu

Speciální výrazy, které se vztahují pouze na konkrétní vrstvy.

### <a name="heat-map-density-expression"></a>Výraz hustoty tepelné mapy

Výraz hustoty tepelné mapy načte hodnotu hustoty tepelné mapy pro každý pixel v vrstvě Heat mapy a je definován jako `heatmapDensity` . Tato hodnota je číslo mezi `0` a `1` . Používá se v kombinaci s `interpolation` `step` výrazem or k definování barevného přechodu, který slouží k zabarvovatí Heat mapy. Tento výraz lze použít pouze v `heatmapColor` Možnosti vrstvy Heat mapy.

> [!TIP]
> Barva v indexu 0, ve výrazu interpolace nebo ve výchozí barvě barvy kroku, definuje barvu oblasti, kde nejsou žádná data. Barva na indexu 0 se dá použít k definování barvy pozadí. Mnoho preferuje nastavení této hodnoty na transparentní nebo částečně průhlednou černou.

**Příklad**

Tento příklad používá výraz interpolace linie k vytvoření hladkého barevného přechodu pro vykreslování Heat mapy. 

```java
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

Kromě použití hladkého přechodu na zabarvovat Heat mapy lze barvy zadat v rámci sady rozsahů pomocí `step` výrazu. Použití `step` výrazu pro Colorizing Heat mapy vizuálně rozdělí hustotu do rozsahů, které se podobají obrysu nebo rozvržení paprskového stylu.  

```java 
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

Další informace najdete v dokumentaci k [Přidání vrstvy Heat mapy](map-add-heat-map-layer-android.md) .

### <a name="text-field-format-expression"></a>Výraz formátu textového pole

`format`Výraz lze použít s `textField` možností vrstvy symbolů pro zajištění smíšeného formátování textu. Tento výraz trvá v jednom nebo více `formatEntry` výrazech, které určují řetězec a sadu `formatOptions` pro připojení k textovému poli.

| Výraz | Popis |
|------------|-------------|
| `format(Expression...)` | Vrátí formátovaný text obsahující poznámky pro použití v položkách textových polí ve smíšeném formátu. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | Vrátí položku formátovaného řetězce pro použití ve `format` výrazu. |

K dispozici jsou následující možnosti formátu:

| Výraz | Popis |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | Určuje faktor škálování pro velikost písma. Je-li tento parametr zadán, tato hodnota přepíše `textSize` vlastnost pro jednotlivé řetězce. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | Určuje barvu, která má být použita pro text při vykreslování. |

**Příklad**

Následující příklad formátuje textové pole přidáním tučného písma a vertikálního navýšení velikosti písma `title` vlastnosti funkce. Tento příklad také přidá `subTitle` vlastnost funkce na nový řádek, se zvětšenou velikostí písma.

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

Tato vrstva bude vykreslovat funkci bodu, jak je znázorněno na následujícím obrázku:

![Obrázek funkce Point s formátovaným textovým polem](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Výraz lupy

`zoom`Výraz se používá k načtení aktuální úrovně přiblížení mapy v době vykreslování a je definován jako `zoom()` . Tento výraz vrací číslo mezi minimálním a maximálním rozsahem úrovně přiblížení mapy. Azure Maps interaktivní ovládací prvky mapování pro web a Android podporují 25 úrovní přiblížení s čísly 0 až 24. Použití `zoom` výrazu umožňuje dynamicky upravovat styly při změně úrovně přiblížení mapy. `zoom`Výraz lze použít pouze s `interpolate` `step` výrazy a.

**Příklad**

Ve výchozím nastavení mají poloměry datových bodů vykreslených v vrstvě Heat mapy u všech úrovní přiblížení pevný obraz v pixelech. Jak je mapa zvětšena, agregace dat a vrstva Heat mapy vypadá jinak. `zoom`Výraz lze použít k horizontálnímu škálování poloměru pro každou úroveň přiblížení tak, že každý datový bod pokrývá stejnou fyzickou oblast mapy. Vrstva Heat mapy bude mít víc statických a konzistentních. Každá úroveň přiblížení mapy má dvakrát a vodorovně tolik pixelů jako předchozí úroveň přiblížení. Změna velikosti poloměru, například při zdvojnásobení s každou úrovní přiblížení, vytvoří Heat mapu, která bude vypadat konzistentně na všech úrovních přiblížení. Dá se to provést pomocí `zoom` výrazu s `base 2 exponential interpolation` výrazem, který má nastavené poloměru pixelů pro minimální úroveň přiblížení a poloměr škálování pro maximální úroveň přiblížení, jak je `2 * Math.pow(2, minZoom - maxZoom)` znázorněno níže.

```java 
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

## <a name="variable-binding-expressions"></a>Výrazy vazeb proměnných

Výrazy vazeb proměnných ukládají výsledky výpočtu v proměnné. Takže je možné výsledky výpočtů odkazovat jinde ve výrazu vícekrát. Je užitečná optimalizace pro výrazy, které zahrnují mnoho výpočtů.

| Výraz | Návratový typ | Popis |
|--------------|---------------|--------------|
| `let(Expression... input)` | | Ukládá jednu nebo více hodnot jako proměnné pro použití `var` výrazem v podřízeném výrazu, který vrací výsledek. |
| `var(Expression expression)` \| `var(string variableName)` | Objekt | Odkazuje na proměnnou, která byla vytvořena pomocí `let` výrazu. |

**Příklad**

V tomto příkladu se používá výraz, který vypočítá tržby vzhledem k poměru teploty a pak použije `case` výraz k vyhodnocení různých logických operací s touto hodnotou. `let`Výraz se používá k uložení výnosů vzhledem k poměru teploty, aby jej bylo nutné vypočítat pouze jednou. `var`Výraz odkazuje na tuto proměnnou tak často, jak je potřeba, aniž by bylo nutné je přepočítat.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o vrstvách, které podporují výrazy:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidat Heat mapu](map-add-heat-map-layer-android.md)
