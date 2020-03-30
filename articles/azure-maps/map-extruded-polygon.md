---
title: Přidání vrstvy vysunutí polyguna do mapy | Mapy Microsoft Azure
description: Jak přidat vrstvu vysunutí polygonu do sady Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334543"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Přidání vrstvy vysunutí polygunu do mapy

Tento článek ukazuje, jak použít vrstvu vysunutí polygunu k vykreslení oblastí `Polygon` geometrií a `MultiPolygon` prvků jako vysunutých tvarů. Webová sada Azure Maps Podporuje vykreslování geometrií Circle, jak je definováno v [rozšířeném schématu GeoJSON](extend-geojson.md#circle). Tyto kruhy mohou být transformovány do polygonů při vykreslení na mapě. Všechny geometrie prvků lze snadno aktualizovat, když jsou zabaleny [do atlasu. Třída tvarů.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-extrusion-layer"></a>Použití vrstvy vysunutí polygonu

Připojte [vrstvu vysunutí polygonu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) ke zdroji dat. Pak ji nahrál na mapu. Vrstva vysunutí polygonu vykreslí oblasti `Polygon` a a `MultiPolygon` prvky jako vysunuté tvary. A `height` `base` vlastnosti vrstvy vysunutí polygunu definují základní vzdálenost od země a výšku vysunutého tvaru v **metrech**. Následující kód ukazuje, jak vytvořit polygon, přidat jej do zdroje dat a vykreslit pomocí třídy vysunutí polygonu.

> [!Note]
> Hodnota `base` definovaná ve vrstvě vysunutí polygonu by `height`měla být menší nebo rovna hodnotě .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extrudovaný polygon" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>pero extrudovaný polygon</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Přidání víceletých polygonů řízených daty

Choropleth mapa může být vykreslen pomocí vrstvy vysunutí polygonu. Nastavte `height` vlastnosti a `fillColor` vysunutí vrstvy na měření `Polygon` statistické `MultiPolygon` proměnné v geometrii prvku a prvku. Následující ukázka kódu ukazuje extrudované choropleth mapy USA na základě měření hustoty obyvatelstva podle státu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extrudovaná mapa choroplethu" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/eYYYNox'>pero extrudované choropleth mapy</a> Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Přidání kruhu do mapy

Azure Maps používá rozšířenou verzi schématu GeoJSON, která poskytuje definici kruhů, jak je uvedeno [zde](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). Vysunutá kružnice může být vykreslena na `subType` mapě `Circle` vytvořením `Radius` `point` prvku s vlastností a očíslovnou vlastností představující poloměr v **metrech**. Například:

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

Sada Azure Maps Web SDK převádí tyto `Point` funkce na `Polygon` funkce pod kapotou. Tyto `Point` funkce lze vykreslit na mapě pomocí vrstvy vysunutí polygonu, jak je znázorněno v následujícím vzorku kódu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polygon vzdušného prostoru dronů" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>polygon vzdušného prostoru pera drone</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Přizpůsobení vrstvy vysunutí polygonu

Vrstva vysunutí mnohonoru má několik možností stylu. Zde je nástroj, jak je vyzkoušet.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na Pero <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Mnohoúhelník](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [polygonová vytlačovací vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Další prostředky:

> [!div class="nextstepaction"]
> [Rozšíření specifikace Azure Maps GeoJSON](extend-geojson.md#circle)
