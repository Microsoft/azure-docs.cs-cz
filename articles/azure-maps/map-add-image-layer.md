---
title: Přidání vrstvy obrázku do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak překrýt bitovou kopii na mapě pomocí sady Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209711"
---
# <a name="add-an-image-layer-to-a-map"></a>Přidání vrstvy obrazu do mapy

Tento článek ukazuje, jak překrýt obrázek na pevnou sadu souřadnic. Zde je několik příkladů různých typů obrázků, které lze na mapách překonané:

* Snímky pořízené z dronů
* Půdorysy budov
* Historické nebo jiné specializované mapové obrázky
* Plány pracovních míst
* Snímky meteorologických radarů

> [!TIP]
> [Obrazová vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) je snadný způsob, jak překrýt obraz na mapě. Všimněte si, že prohlížeče mohou mít potíže s načítáním velkého obrázku. V takovém případě zvažte rozdělení obrázku na dlaždice a jejich načtení do mapy jako [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Vrstva obrazu podporuje následující formáty obrazů:

- JPEG
- PNG
- BMP
- GIF (bez animací)

## <a name="add-an-image-layer"></a>Přidání vrstvy obrázků

Následující kód překryje obrázek [mapy Newarku v New Jersey z roku 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapě. [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) je vytvořen předáním adresy URL do obrázku a souřadnice pro čtyři rohy ve formátu `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

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

Tady je kompletní ukázka spuštěného kódu předchozího kódu.

<br/>

<iframe height='500' scrolling='no' title='Jednoduchá vrstva obrazu' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/eQodRo/'>pero jednoduché vrstvy obrazu</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Import souboru KML jako překrytí terénu

Tato ukázka ukazuje, jak přidat informace o překrytí kml terénu jako vrstvu obrazu na mapě. Pozemní překryvy KML poskytují souřadnice severu, jihu, východu a západu a otáčení proti směru hodinových ručiček. Vrstva obrazu však očekává souřadnice pro každý roh obrazu. Pozemní překrytí KML v této ukázce je pro katedrálu Chartres a pochází z [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Kód používá statickou `getCoordinatesFromEdges` funkci z [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) třídy. Vypočítá čtyři rohy obrazu pomocí severní, jižní, východní, západní a rotace informace o překrytí terénu KML.

<br/>

<iframe height='500' scrolling='no' title='Překrytí terénu KML jako vrstva obrazu' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>překrytí pera KML ground jako vrstva obrazu</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Přizpůsobení vrstvy obrazu

Vrstva obrazu má mnoho možností stylingu. Zde je nástroj, jak je vyzkoušet.

<br/>

<iframe height='700' scrolling='no' title='Volby vrstvy obrazu' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>možnosti vrstvy pera</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Obrazová vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Další ukázky kódu, které chcete přidat do map, naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidání vrstvy dlaždic](./map-add-tile-layer.md)