---
title: Přidat jednoduchou datovou vrstvu | Mapy Microsoft Azure
description: Naučte se, jak přidat jednoduchou datovou vrstvu pomocí modulu pro prostorové vstupně-výstupní operace, který poskytuje Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3fa54e3227496c11fcafc2f42e980daa5c716365
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370983"
---
# <a name="add-a-simple-data-layer"></a>Přidat jednoduchou datovou vrstvu

Modul pro prostorové vstupně-výstupní operace poskytuje třídu `SimpleDataLayer`. Tato třída usnadňuje vykreslování stylů funkcí na mapě. Může dokonce vykreslovat sady dat, které mají vlastnosti stylu a sady dat, které obsahují smíšené typy geometrie. Jednoduchá Datová vrstva dosahuje této funkce zabalením více vrstev vykreslování a pomocí výrazů stylu. Výrazy stylu hledají společné vlastnosti stylu funkcí uvnitř těchto zabalených vrstev. Funkce `atlas.io.read` a funkce `atlas.io.write` tyto vlastnosti používají ke čtení a zápisu stylů do podporovaného formátu souboru. Po přidání vlastností do podporovaného formátu souboru je možné soubor použít k různým účelům. Soubor lze například použít k zobrazení funkcí stylu na mapě.

Kromě funkcí pro stylování nabízí `SimpleDataLayer` integrovanou místní funkci s místní šablonou. Automaticky otevírané okno se zobrazí, když se klikne na funkci. V případě potřeby lze zakázat výchozí místní funkci. Tato vrstva také podporuje data v clusteru. Po kliknutí na cluster se tato mapa přiblíží ke clusteru a rozšíří se na jednotlivé body a podclustery.

Třída `SimpleDataLayer` je určena pro použití ve velkých sadách dat s mnoha typy geometrie a mnoho stylů použitých pro funkce. Při použití Tato třída přidá režii šesti vrstev obsahujících výrazy stylu. Existují však případy, kdy je efektivnější používat základní vrstvy vykreslování. Například použijte základní vrstvu pro vykreslení několika typů geometrie a několika stylů na funkci.

## <a name="default-supported-style-properties"></a>Výchozí podporované vlastnosti stylu

Jak bylo zmíněno dříve, jednoduchá Datová vrstva zalomí několik základních vrstev vykreslování: bublinový, symbol, spojnicový, Mnohoúhelníkový a vytlačený mnohoúhelník. Potom používá výrazy k hledání platných vlastností stylu u jednotlivých funkcí.

Vlastnosti stylu Azure Maps a GitHub jsou dvě hlavní sady podporovaných názvů vlastností. Většina názvů vlastností různých možností vrstvy Azure Maps je podporovaná jako styl vlastností funkcí v jednoduché datové vrstvě. Do některých možností vrstvy byly přidány výrazy pro podporu názvů vlastností stylu, které se běžně používají v GitHubu. Tyto názvy vlastností jsou definované [podporou mapy pro INjson v rámci GitHubu](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)a používají se pro styly souborů. JSON, které se ukládají a vykreslují v rámci platformy. Všechny vlastnosti stylu GitHubu jsou podporované v jednoduché datové vrstvě s výjimkou vlastností `marker-symbol` stylů.

Pokud čtečka nalezne méně běžné vlastnosti stylu, převede ji na nejbližší vlastnost stylu Azure Maps. Kromě toho mohou být výchozí výrazy stylu přepsány pomocí funkce `getLayers` v jednoduché datové vrstvě a aktualizace možností na kterékoli z vrstev.

V další části najdete podrobné informace o výchozích vlastnostech stylu, které jsou podporovány jednoduchou datovou vrstvou. Pořadí podporovaného názvu vlastnosti je také prioritou vlastnosti. Pokud jsou definovány dvě vlastnosti stylu pro stejnou možnost vrstvy, pak první v seznamu má vyšší prioritu.

## <a name="simple-data-layer-options"></a>Možnosti jednoduchých datových vrstev

### <a name="bubble-layer-style-properties"></a>Vlastnosti stylu bublinové vrstvy

Pokud je funkce `Point` nebo `MultiPoint`a tato funkce nemá vlastnost `image`, která by se použila jako vlastní ikona pro vykreslení bodu jako symbol, pak se tato funkce vykreslí pomocí `BubbleLayer`.

| Možnost vrstvy | Podporované názvy vlastností | Výchozí hodnota |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>`marker-size`<sup>2</sup>`scale`<sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] hodnoty `size` a `scale` se považují za skalární hodnoty a vynásobí se `8`

\[2\] Pokud je zadána možnost `marker-size` GitHubu, použijí se pro protokol RADIUS následující hodnoty.

| Velikost značky | zaoblen |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Clustery se také vykreslují pomocí bublinové vrstvy. Ve výchozím nastavení je poloměr clusteru nastavený na `16`. Barva clusteru se liší v závislosti na počtu bodů v clusteru, jak je definováno níže:

| počet bodů | Barva    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>Vlastnosti stylu symbolu

Pokud je funkce `Point` nebo `MultiPoint`a funkce a má vlastnost `image`, která by se použila jako vlastní ikona pro vykreslení bodu jako symbolu, funkce se vykreslí pomocí `SymbolLayer`.

| Možnost vrstvy | Podporované názvy vlastností | Výchozí hodnota |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size``marker-size`<sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] Pokud je zadána možnost `marker-size` GitHubu, použijí se pro možnost velikost ikony následující hodnoty.

| Velikost značky | Velikost symbolu |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Pokud je funkce Point clusterem, bude vlastnost `point_count_abbreviated` vykreslena jako textový popisek. Nebude vykreslen žádný obrázek.

### <a name="line-style-properties"></a>Vlastnosti stylu čáry

Pokud je funkce `LineString`, `MultiLineString`, `Polygon`nebo `MultiPolygon`, funkce se vykreslí s `LineLayer`.

| Možnost vrstvy | Podporované názvy vlastností | Výchozí hodnota |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Vlastnosti stylu mnohoúhelníku

Pokud je funkce `Polygon` nebo `MultiPolygon`a funkce buď nemá vlastnost `height` nebo má vlastnost `height` hodnotu nula, funkce bude vykreslena s `PolygonLayer`.

| Možnost vrstvy | Podporované názvy vlastností | Výchozí hodnota |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Vlastnosti stylu vytlačeného mnohoúhelníku

Pokud je funkce `Polygon` nebo `MultiPolygon`a má vlastnost `height` s hodnotou větší než 0, funkce bude vykreslena pomocí `PolygonExtrusionLayer`.

| Možnost vrstvy | Podporované názvy vlastností | Výchozí hodnota |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="use-a-simple-data-layer"></a>Použití jednoduché datové vrstvy

Třída `SimpleDataLayer` se používá jako jiné vrstvy vykreslování. Následující kód ukazuje, jak použít jednoduchou datovou vrstvu v mapě:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Přidejte funkce do zdroje dat. Jednoduchá Datová vrstva pak vyhledá, jak nejlépe vykreslíte funkce. Styly pro jednotlivé funkce lze nastavit jako vlastnosti funkce. Následující kód ukazuje funkci bodu zobrazení typu injson s vlastností `color` nastavenou na hodnotu `red`. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

Následující kód vykreslí funkci výše uvedeného bodu pomocí jednoduché datové vrstvy. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Použití jednoduché datové vrstvy" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Podívejte se, jak pero <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>použít jednoduchou datovou vrstvu</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Skutečná síla jednoduché datové vrstvy nastane v těchto případech:

- Zdroj dat obsahuje několik různých typů funkcí; ani
- Funkce v datové sadě mají pro sebe nastavené různé vlastnosti stylu; ani
- Nejste si jistí, co sada dat přesně obsahuje.

Například při analýze datových kanálů XML nemusíte znát přesné styly a typy geometrie funkcí. Následující příklad znázorňuje sílu jednoduché datové vrstvy vykreslením funkcí souboru KML. Ukazuje také různé možnosti, které poskytuje třída jednoduché datové vrstvy.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Možnosti jednoduchých datových vrstev" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Přečtěte si <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>Možnosti jednoduché datové vrstvy</a> pera Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Čtení a zápis prostorových dat](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Přidat vrstvu mapy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Připojení ke službě WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaném formátu dat](spatial-io-supported-data-format-details.md)
