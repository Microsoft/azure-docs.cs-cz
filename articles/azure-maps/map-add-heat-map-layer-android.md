---
title: Přidat vrstvu Heat mapy do map pro Android | Mapy Microsoft Azure
description: Přečtěte si, jak vytvořit Heat mapu. Podívejte se, jak pomocí sady Azure MapsAndroid SDK přidat vrstvu Heat mapy do mapy. Přečtěte si, jak přizpůsobit vrstvy Heat mapy.
author: rbrundritt
ms.author: richbrun
ms.date: 12/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 4de59bd0b2a9dc9b11acf55a59b82724d2c7b862
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681605"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Přidat vrstvu Heat mapy (Android SDK)

Heat mapy, označované také jako mapy hustoty bodů, jsou typem vizualizace dat. Slouží k reprezentaci hustoty dat pomocí rozsahu barev a zobrazení dat "aktivních bodů" na mapě. Heat mapy představují skvělý způsob vykreslování datových sad s velkým počtem bodů. 

Vykreslení desítky tisíc bodů, protože symboly mohou pokrývat většinu oblastí rozvržení. Tento případ nejspíš způsobí, že mnoho symbolů překrývá. Je obtížné získat lepší informace o datech. Vizualizace stejné datové sady jako Heat mapy však usnadňuje zobrazení hustoty a relativní hustoty jednotlivých datových bodů.

Můžete použít Heat mapy v mnoha různých scénářích, mezi které patří:

- **Data o teplotě**: poskytuje přibližné hodnoty pro to, co je teplota mezi dvěma datovými body.
- **Data pro senzory hluku**: ukazuje nejen intenzitu hluku, kde je senzor, ale může také poskytnout přehled o rozptylu v rámci určité vzdálenosti. Úroveň hluku v některém webu nemusí být vysoká. Pokud se oblast pokrytí hluku od více senzorů překrývá, je možné, že se tato překrývající oblast dostanou o vyšší úrovně hluku. V takovém případě by se překrytá oblast zobrazila v Heat mapě.
- **GPS Trace**: zahrnuje rychlost jako mapu s váženou výškou, kde je intenzita jednotlivých datových bodů založena na rychlosti. Tato funkce například poskytuje způsob, jak zjistit, kde bylo vozidlo urychleno.

> [!TIP]
> Vrstvy Heat mapy ve výchozím nastavení vykreslují souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslí pouze funkce geometrie bodu, nastavte `filter` možnost vrstvy na `eq(geometryType(), "Point")` . Pokud chcete zahrnout i funkce systému MultiPoint, nastavte `filter` možnost vrstvy na `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Předpoklady

Ujistěte se, že jste dokončili kroky v [rychlém startu: vytvoření dokumentu aplikace pro Android](quick-android-map.md) . Bloky kódu v tomto článku lze vložit do `onReady` obslužné rutiny události Maps.

## <a name="add-a-heat-map-layer"></a>Přidání vrstvy heat mapy

Chcete-li vykreslit zdroj dat bodů jako Heat mapu, předejte zdroj dat do instance `HeatMapLayer` třídy a přidejte jej do mapy.

Následující ukázka kódu načte z minulého týdne zemětřeseníový informační kanál pro střední hodnotu a vykresluje je jako Heat mapa. Každý datový bod se vykreslí s poloměrem 10 pixelů na všech úrovních přiblížení. Aby bylo zajištěno lepší uživatelské prostředí, je Heat mapa pod vrstvou popisku, aby popisky zůstaly jasně viditelné. Data v této ukázce se nacházejí v [programu sadě USGS zemětřesení nebezpečí](https://earthquake.usgs.gov/). Tato ukázka načte data ze služby data ze sítě JSON z webu pomocí bloku kódu nástroje pro import dat, který je k dispozici v dokumentu [vytvořit zdroj dat](create-data-source-android-sdk.md) .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

Následující snímek obrazovky ukazuje mapu načítající Heat mapu pomocí výše uvedeného kódu.

![Mapování pomocí vrstvy Heat mapy s nedávnými zemětřesení](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>Přizpůsobení vrstvy Heat mapy

Předchozí příklad upravil Heat mapu nastavením možnosti poloměr a krytí. Vrstva Heat mapy nabízí několik možností přizpůsobení, včetně:

- `heatmapRadius`: Definuje poloměr pixelu, ve kterém se má vykreslovat každý datový bod. Poloměr můžete nastavit jako pevné číslo nebo jako výraz. Pomocí výrazu můžete škálovat poloměr na základě úrovně přiblížení a znázornit konzistentní prostorové oblasti na mapě (například poloměr poloměru 5 km).
- `heatmapColor`: Určuje, jak je Heat mapa barevně barevná. Barevný přechod je běžnou funkcí Heat mapy. Efekt můžete dosáhnout pomocí `interpolate` výrazu. Můžete také použít `step` výraz pro Colorizing Heat mapy a rozvrhnout hustotu vizuálně do rozsahů, které se podobají obrysu nebo rozvržení paprskového stylu. Tyto palety barev definují barvy z minima na maximální hodnotu hustoty.

  Hodnoty barvy pro Heat mapy se určují jako výraz na `heatmapDensity` hodnotu. Barva oblasti, kde nejsou definována žádná data na indexu 0 výrazu "interpolace" nebo výchozí barva výrazu "steped". Tuto hodnotu lze použít k definování barvy pozadí. Tato hodnota je často nastavená na transparentní nebo na černou, která je částečně průhledná. 

  Tady jsou příklady barevných výrazů:

  | Výraz barvy interpolace | Výraz se zvýrazněnou barvou |
  |--------------------------------|--------------------------|
  | interpolovat<br/>&nbsp;&nbsp;&nbsp;&nbsp;lineární (); <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0; barva (Color. TRANSPARENT));<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0,01; Color (Color. purpurová));<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0.5; Color (parseColor ("#fb00fb")));<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (1; Barva (parseColor ("#00c3ff")))<br/>)` | Krok<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Color (Color. TRANSPARENTed);<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0,01; Color (parseColor ("#000080")));<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0,25; Color (parseColor ("#000080")));<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0,5; barva (Color. zelená));<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (0,5; barva (Color. YELLOW));<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop (1; Barva (Color. RED))<br/>) |

- `heatmapOpacity`: Určuje, jak je vrstva Heat mapy neprůhledná nebo průhledná.
- `heatmapIntensity`: Aplikuje násobitel na váhu každého datového bodu pro zvýšení celkové intenzity heatmapu. Způsobuje rozdíl v hmotnosti datových bodů, což usnadňuje vizualizaci.
- `heatmapWeight`: Ve výchozím nastavení mají všechny datové body váhu 1 a mají stejnou váhu. Možnost váhy funguje jako násobitel a můžete ji nastavit jako číslo nebo výraz. Pokud je číslo nastavené jako váha, je rovnocenným umístěním jednotlivých datových bodů na mapě dvakrát. Pokud je například váha `2` , pak je tato hustota dvojitá. Nastavení možnosti váhy na číslo vykreslí Heat mapu podobným způsobem, jak použít možnost intenzita.

  Pokud však použijete výraz, může být váha každého datového bodu založena na vlastnostech jednotlivých datových bodů. Předpokládejme například, že každý datový bod představuje objekt zemětřesení. Hodnota velikosti byla důležitou metrikou pro každý datový bod zemětřesení. K zemětřesení dochází po celou dobu, ale většina má nízkou velikost a nevšimla se. Pomocí hodnoty velikost ve výrazu přiřaďte váhu každému datovému bodu. Když použijete hodnotu velikost pro přiřazení váhy, získáte lepší reprezentaci významnosti zemětřesení v rámci Heat mapy.
- `minZoom` a `maxZoom` : rozsah úrovně přiblížení, kde má být vrstva zobrazena.
- `filter`: Filtrovací výraz použitý k omezení načítání ze zdroje a vykresleného ve vrstvě.
- `sourceLayer`: Pokud je zdroj dat připojený k vrstvě zdrojem vektorové dlaždice, je nutné zadat zdrojovou vrstvu v rámci vektorových dlaždic.
- `visible`: Skryje nebo zobrazí vrstvu.

Toto je příklad Heat mapy, kde se výraz liniové interpolace používá k vytvoření plynule barevného přechodu barvy. `mag`Vlastnost definovaná v datech se používá s exponenciální interpolací pro nastavení váhy nebo relevance jednotlivých datových bodů.

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

Následující snímek obrazovky ukazuje výše uvedenou vlastní vrstvu Heat mapy pomocí stejných dat z předchozího příkladu Heat mapy.

![Mapování s vlastní vrstvou Heat mapy z posledních zemětřesení](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>Konzistentní Heat mapa s přiblížením

Ve výchozím nastavení mají poloměry datových bodů vykreslených v vrstvě Heat mapy u všech úrovní přiblížení pevný obraz v pixelech. Při přiblížení mapy se data agreguje společně a vrstva Heat mapy vypadá jinak. Následující video ukazuje výchozí chování Heat mapy, kde při přiblížení mapy udržuje poloměr obrazového bodu.

![Animace znázorňující zvětšení mapy s vrstvou Heat mapy ukazující konzistenci velikosti pixelů](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

Použijte `zoom` výraz pro horizontální navýšení kapacity poloměru pro každou úroveň přiblížení, takže každý datový bod pokrývá stejnou fyzickou oblast mapy. Tento výraz vytvoří vrstvu Heat mapy, která vypadá spolehlivě a je konzistentní. Každá úroveň přiblížení mapy má dvakrát a vodorovně tolik pixelů jako předchozí úroveň přiblížení.

Škálování poloměru tak, aby dvojnásobek každé úrovně přiblížení vytvoří Heat mapu, která vypadá konzistentně na všech úrovních přiblížení. Chcete-li použít toto škálování, použijte `zoom` se `exponential interpolation` výrazem základní 2 s poloměrem, který je nastavený pro minimální úroveň přiblížení, a s poloměrem na škálované poloměr pro maximální úroveň přiblížení, jak je `2 * Math.pow(2, minZoom - maxZoom)` znázorněno v následující ukázce. Přiblížením mapy zjistíte, jak se Heat mapa škáluje s úrovní přiblížení.

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

Následující video ukazuje mapu s výše uvedeným kódem, který škáluje poloměr v době, kdy se mapa přiblíží, aby se vytvořilo konzistentní vykreslování Heat mapy napříč úrovněmi přiblížení.

![Animace znázorňující zvětšení mapy s vrstvou Heat mapy ukazující konzistenci geoprostorové velikosti](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>Další kroky

Další příklady kódu pro přidání do map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-android-sdk.md)
