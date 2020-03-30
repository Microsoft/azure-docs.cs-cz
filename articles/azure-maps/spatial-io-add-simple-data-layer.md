---
title: Přidání jednoduché datové vrstvy | Mapy Microsoft Azure
description: Zjistěte, jak přidat jednoduchou datovou vrstvu pomocí modulu Prostorové IO poskytované webovou sadou Azure Maps.
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7671d07a468a9f67a4851ec828fe18896d7a6c66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334282"
---
# <a name="add-a-simple-data-layer"></a>Přidání jednoduché datové vrstvy

Prostorový iO modul `SimpleDataLayer` poskytuje třídu. Tato třída usnadňuje vykreslení stylizované funkce na mapě. Může dokonce vykreslit datové sady, které mají vlastnosti stylu a datové sady, které obsahují smíšené typy geometrie. Jednoduchá datová vrstva dosahuje této funkce obtékáním více vrstev vykreslování a pomocí výrazů stylu. Výrazy stylu vyhledávají společné vlastnosti stylu prvků uvnitř těchto zabalených vrstev. Funkce `atlas.io.read` a `atlas.io.write` funkce používají tyto vlastnosti ke čtení a zápisu stylů do podporovaného formátu souboru. Po přidání vlastností do podporovaného formátu souboru lze soubor použít pro různé účely. Soubor lze například použít k zobrazení stylizované funkce na mapě.

Kromě funkcí stylingu `SimpleDataLayer` poskytuje vestavěná funkce vyskakovacího přístupu s šablonou automaticky otevíraného přístupu. Místní okno se zobrazí po klepnutí na prvek. Výchozí funkce vyskakovacího dne může být v případě potřeby zakázána. Tato vrstva také podporuje clusterovaná data. Po klepnutí na cluster se mapa přiblíží ke clusteru a rozbalí ji do jednotlivých bodů a podclusterů.

Třída `SimpleDataLayer` je určena pro použití na velkých datových sadách s mnoha typy geometrie a mnoha styly aplikovanými na prvky. Při použití tato třída přidá režii šesti vrstev obsahujících výrazy stylu. Takže existují případy, kdy je efektivnější použít základní vrstvy vykreslování. Pomocí základní vrstvy můžete například vykreslit několik typů geometrie a několik stylů na prvku.

## <a name="use-a-simple-data-layer"></a>Použití jednoduché datové vrstvy

Třída `SimpleDataLayer` se používá jako ostatní vrstvy vykreslování se používají. Níže uvedený kód ukazuje, jak používat jednoduchou datovou vrstvu v mapě:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Přidejte funkce do zdroje dat. Jednoduchá datová vrstva pak zjistí, jak nejlépe vykreslit funkce. Styly pro jednotlivé prvky lze nastavit jako vlastnosti prvku. Následující kód ukazuje funkci bodu GeoJSON s vlastností nastavenou `color` na `red`. 

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

Následující kód vykreslí výše uvedený bodový prvek pomocí jednoduché datové vrstvy. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Použití jednoduché datové vrstvy" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Podívejte se na pero Použití jednoduché<a href='https://codepen.io/azuremaps'>@azuremaps</a>datové <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>vrstvy</a> pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Skutečná síla jednoduché datové vrstvy přichází, když:

- Existuje několik různých typů funkcí ve zdroji dat; Nebo
- Funkce v sadě dat mají několik vlastností stylu jednotlivě nastavených na nich; Nebo
- Nejste si jisti, co přesně obsahuje sada dat.

Například při analýzě datových kanálů XML pravděpodobně neznáte přesné styly a typy geometrie prvků. Následující ukázka ukazuje sílu jednoduché datové vrstvy vykreslením funkcí souboru KML. Také ukazuje různé možnosti, které poskytuje jednoduché třídy datové vrstvy.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Jednoduché volby datové vrstvy" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Podívejte se na <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>možnosti datové vrstvy Pero jednoduché</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Tato jednoduchá datová vrstva používá třídu [šablony automaticky otevíraných přístupů](map-add-popup.md#add-popup-templates-to-the-map) k zobrazení pozic KML nebo vlastností prvku jako tabulky. Ve výchozím nastavení bude veškerý obsah vykreslený v automaticky otevíraném rámečku v izolovaném prostoru v rámečku iframe jako funkce zabezpečení. Existují však omezení:
>
> - Všechny skripty, formuláře, zámek ukazatele a funkce špičkové navigace jsou zakázány. Odkazy se mohou po kliknutí otevřít na nové kartě. 
> - Starší prohlížeče, které nepodporují `srcdoc` parametr v rámci iframe, budou omezeny na vykreslení malého množství obsahu.
> 
> Pokud důvěřujete datům načteným do vyskakovacích formulářů a chcete, aby tyto skripty načtené do `sandboxContent` vyskakovacích formulářů měly přístup k vaší aplikaci, můžete to zakázat nastavením možnosti vyskakovacích šablon na hodnotu false. 

## <a name="default-supported-style-properties"></a>Výchozí podporované vlastnosti stylu

Jak již bylo zmíněno dříve, jednoduchá datová vrstva zabalí několik vrstev jádrového vykreslování: bublina, symbol, čára, polygon a vysunutý polygon. Potom používá výrazy k hledání platných vlastností stylu u jednotlivých funkcí.

Azure Maps a Vlastnosti stylu GitHub jsou dvě hlavní sady podporovaných názvů vlastností. Většina názvů vlastností různých možností vrstvy azure maps jsou podporovány jako vlastnosti stylu funkcí v jednoduché datové vrstvě. Výrazy byly přidány do některé možnosti vrstvy pro podporu názvů vlastností stylu, které jsou běžně používané GitHub. Tyto názvy vlastností jsou definovány [podporou mapy GeoJSON githubu](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)a používají se ke stylu souborů GeoJSON, které jsou uloženy a vykresleny v rámci platformy. Všechny vlastnosti stylu GitHubu jsou podporovány v jednoduché `marker-symbol` datové vrstvě, s výjimkou vlastností stylu.

Pokud čtenář narazí na méně běžné vlastnosti stylu, převede ji na nejbližší vlastnost stylu Azure Maps. Kromě toho mohou být výchozí výrazy stylu `getLayers` přepsány pomocí funkce jednoduché datové vrstvy a aktualizací voleb v libovolné vrstvě.

V následujících částech jsou uvedeny podrobnosti o výchozích vlastnostech stylu, které jsou podporovány jednoduchou datovou vrstvou. Pořadí název podporované vlastnosti je také prioritou vlastnosti. Pokud jsou pro stejnou možnost vrstvy definovány dvě vlastnosti stylu, má první vlastnostv seznamu vyšší prioritu.

### <a name="bubble-layer-style-properties"></a>Vlastnosti stylu bublinkové vrstvy

Pokud je prvek `Point` a `MultiPoint`nebo a , a `image` funkce nemá vlastnost, která by byla použita jako vlastní ikona k vykreslení `BubbleLayer`bodu jako symbolu, bude tato funkce vykreslena pomocí .

| Vrstva, volba | Podporované názvy vlastností | Výchozí hodnota |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup> `marker-size` <sup>2</sup>, `scale`2 , <sup>1.</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size` Hodnoty `scale` a jsou považovány za skalární hodnoty a budou vynásobeny`8`

\[2\] Pokud je `marker-size` zadána možnost GitHub, budou pro poloměr použity následující hodnoty.

| Velikost značky | Poloměr |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Clustery jsou také vykresleny pomocí bublinové vrstvy. Ve výchozím nastavení je poloměr `16`clusteru nastaven na hodnotu . Barva clusteru se liší v závislosti na počtu bodů v clusteru, jak je definováno níže:

| Počet bodů | Barvy    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt;10     | `green`  |

### <a name="symbol-style-properties"></a>Vlastnosti stylu symbolu

Pokud je prvek `Point` a `MultiPoint`nebo a funkce `image` a má vlastnost, která by byla použita jako vlastní ikona k vykreslení `SymbolLayer`bodu jako symbol, bude tato funkce vykreslena pomocí .

| Vrstva, volba | Podporované názvy vlastností | Výchozí hodnota |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1.</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] Pokud je `marker-size` zadána možnost GitHub, budou pro volbu velikosti ikony použity následující hodnoty.

| Velikost značky | Symbol size |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Pokud je bodovým prvkem `point_count_abbreviated` cluster, bude vlastnost vykreslena jako textový popisek. Žádný obrázek nebude vykreslen.

### <a name="line-style-properties"></a>Vlastnosti stylu čáry

Pokud je tato `LineString` `MultiLineString`funkce `Polygon`a `MultiPolygon`, , , nebo , `LineLayer`bude tato funkce vykreslena pomocí .

| Vrstva, volba | Podporované názvy vlastností | Výchozí hodnota |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Vlastnosti stylu polyganu

Pokud je funkce `Polygon` a `MultiPolygon`nebo a , a funkce `height` buď `height` nemá vlastnost nebo je vlastnost nula, `PolygonLayer`bude funkce vykreslena pomocí .

| Vrstva, volba | Podporované názvy vlastností | Výchozí hodnota |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Vlastnosti vysunutého polygonového stylu

Pokud je prvek `Polygon` a `MultiPolygon`nebo a `height` má vlastnost s hodnotou větší než 0, `PolygonExtrusionLayer`bude tato funkce vykreslena pomocí .

| Vrstva, volba | Podporované názvy vlastností | Výchozí hodnota |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Další ukázky kódu, které chcete přidat do map, naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Čtení a zápis prostorových dat](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Přidání mapové vrstvy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Připojení ke službě WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaném formátu dat](spatial-io-supported-data-format-details.md)
