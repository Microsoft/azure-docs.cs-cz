---
title: Přidejte mnohoúhelníkovou vrstvu vytlačení do Azure Maps | Microsoft Docs
description: Postup přidání mnohoúhelníkové vrstvy vytlačení do Azure Maps webové sady SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fdb5e2b78d9e5817c5a5d139cdf0b34744ed011f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170708"
---
# <a name="add-an-extrusion-polygon-layer-to-the-map"></a>Přidat mnohoúhelníkovou vrstvu vytlačení do mapy

V tomto článku se dozvíte, jak použít vrstvu vysunutí mnohoúhelníku k vykreslení oblastí @no__t 0 a `MultiPolygon` funkce geometrií jako vytlačené obrazce na mapě. Sada Azure Maps Web SDK také podporuje vytváření geometrií kroužků, jak je definováno v [rozšířeném schématu pro stanovení biojson](extend-geojson.md#circle). Tyto kružnice jsou transformované na mnohoúhelníky při vykreslování na mapě. Všechny funkce geometrií je také možné snadno aktualizovat, pokud jsou zabaleny pomocí nástroje [Atlas. Třída Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)


## <a name="use-a-polygon-extrusion-layer"></a>Použití vrstvy vysunutí mnohoúhelníku

Když je [vrstva vysunutí mnohoúhelníku](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) připojena ke zdroji dat a načtena na mapě, vykreslí oblasti funkcí `Polygon` a `MultiPolygon` jako vytlačené tvary. Vlastnosti `height` a `base` vrstvy vysunutí mnohoúhelníku definují základní vzdálenost od základu a výšky vytlačeného obrazce v **měřičích**. Následující kód ukazuje, jak vytvořit mnohoúhelník, přidat ho do zdroje dat a vykreslit pomocí třídy vrstev vytlačení mnohoúhelníku.

> [!Note]
> Hodnota `base` definovaná ve vrstvě vytlačení mnohoúhelníku by měla být menší nebo rovna hodnotě `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vytlačený mnohoúhelník" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>vytlačený mnohoúhelník</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-multipolygons"></a>Přidat data řízená více mnohoúhelníků

Mapu kartogramy můžete vykreslit pomocí vrstvy vytlačení mnohoúhelníku, když nastavíte její vlastnosti `height` a `fillColor` v poměru k měření statistické proměnné ve funkci `Polygon` a `MultiPolygon` geometrií. Následující ukázka kódu ukazuje vytlačenou mapu kartogramyu U. S na základě měření hustoty populace podle stavu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vytlačená mapa kartogramy" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Viz Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>v <a href='https://codepen.io/azuremaps/pen/eYYYNox'>mapě vytlačené</a> perem.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Přidání kruhu k mapě

Azure Maps používá rozšířenou verzi schématu injson, která poskytuje definici pro kruhy, jak je uvedeno [zde](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). Vytlačený kruh se dá na mapě vykreslit vytvořením funkce @no__t 0 s vlastností `subType` `Circle` a číslovanou hodnotou `Radius`, která představuje poloměr v **metrech**. Příklad:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps webová sada SDK převede tyto funkce `Point` na funkce `Polygon` v digestoři a lze je vykreslit na mapě pomocí vrstvy vytlačení mnohoúhelníku, jak je znázorněno v následujícím příkladu kódu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mnohoúhelník prostorů pro pomocí dronů" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>pomocí dronů pro vzdušný Azure Maps prostor</a> na <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>).
</iframe>


## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Postupně](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [vrstva vysunutí mnohoúhelníku](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Další zdroje informací:

> [!div class="nextstepaction"]
> [Azure Maps rozšíření pro injson Specification](extend-geojson.md#circle)
