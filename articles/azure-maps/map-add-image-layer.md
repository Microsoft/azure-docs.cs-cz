---
title: Přidat vrstvu obrázku do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak překrýt obrázek na mapě pomocí webové sady SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d2260e5c9e4125be1c4b0fa5e9d7c39a8e03d43f
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911198"
---
# <a name="add-an-image-layer-to-a-map"></a>Přidat vrstvu obrázku do mapy

V tomto článku se dozvíte, jak můžete překrýt obrázek na pevně danou sadu souřadnic na mapě. Tady je několik příkladů typu imagí, které se často překrývají na mapách:

* Obrázky zachycené z DRONY zachraňují životy
* Sestavování floorplans
* Historické nebo jiné specializované image map
* Modrotisky webů úloh
* Obrázky paprsků s počasí

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) je jednoduchý způsob, jak překrýt obrázek na mapě. Všimněte si, že prohlížeče mohou mít potíže při načítání velkého obrázku. V takovém případě zvažte rozdělení obrázku do dlaždic a jejich načtení do mapy jako [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Vrstva image podporuje následující formáty obrázků:

- JPEG
- PNG
- BMP
- GIF (žádné animace)

## <a name="add-an-image-layer"></a>Přidání vrstvy obrázků

Následující kód překrývá obrázek [mapy newyorském, New Jersey, z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapě. [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) se vytvoří PŘEDÁNÍM adresy URL obrázku a souřadnicemi pro čtyři rohy ve formátu `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Zde je ukázka kompletního běžícího kódu předchozího kódu.

<br/>

<iframe height='500' scrolling='no' title='Jednoduchá vrstva obrázku' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na se zobrazí <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Jednoduchá Azure Maps vrstva obrázku</a> pera.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Import překrytí základní desky KML

Tento další příklad ukazuje, jak překrýt informace o překrytí KML, jako vrstvu obrázku na mapě. KML překryvná deska poskytují souřadnice Severní, Jižní, východní a západní osy a rotace po směru hodinových ručiček, zatímco vrstva obrázku očekává souřadnice pro každý roh obrázku. Překrytí základní desky KML v této ukázce je Chartres Cathedral a je ze zdroje [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Následující kód používá funkci static `getCoordinatesFromEdges` třídy [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) . Vypočítá čtyři rohy obrázku z informací o Severní, Jižní, východní a západní oblasti a informace o rotaci z překrytí základní desky KML.

<br/>

<iframe height='500' scrolling='no' title='Základní překrytí KML jako vrstva obrázku' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazení KML (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>v <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>podobě Azure Maps obrázku na pozadí se dokryje</a> .
</iframe>

## <a name="customize-an-image-layer"></a>Přizpůsobení vrstvy obrázku

Vrstva obrázku má mnoho možností stylů. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy obrázku' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Možnosti vrstvy obrázku</a> pera.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidat dlaždicovou vrstvu](./map-add-tile-layer.md)