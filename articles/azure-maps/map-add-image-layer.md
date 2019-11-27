---
title: Přidat vrstvu obrázku do Azure Maps | Microsoft Docs
description: Jak přidat vrstvu obrázku do Azure Maps webové sady SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fadaaf7c64b11a6d6d94c68234f8288d1b3f8d07
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480487"
---
# <a name="add-an-image-layer-to-a-map"></a>Přidat vrstvu obrázku do mapy

V tomto článku se dozvíte, jak můžete překrýt obrázek do pevné sady souřadnic na mapě. Existuje mnoho scénářů, ve kterých je překrytí obrázku na mapě provedeno. Tady je několik příkladů typu imagí, které se často překrývají na mapách.

* Obrázky zachycené z DRONY zachraňují životy.
* Sestavování floorplans.
* Historické nebo jiné specializované image map.
* Modrotisky webů úloh.
* Obrázky paprsků s počasí

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) je rychlý jednoduchý způsob, jak překrýt obrázek na mapě. Pokud je však obrázek velký, prohlížeč je může bojovat načíst. V takovém případě zvažte rozdělení obrázku do dlaždic a jejich načtení do mapy jako [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Vrstva image podporuje následující formáty obrázků:

- JPEG
- PNG
- BMP
- GIF (žádné animace)

## <a name="add-an-image-layer"></a>Přidání vrstvy obrázků

V následujícím kódu překrývá obrázek [mapy newyorském New Jersey z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapě. [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) je vytvořen PŘEDÁNÍM adresy URL obrázku a souřadnicemi pro čtyři rohy ve formátu `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

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

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Jednoduchá vrstva obrázku' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na se zobrazí <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Jednoduchá Azure Maps vrstva obrázku</a> pera.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Import překrytí základní desky KML

V této ukázce se dozvíte, jak překrýt informace o překrytí KML základní desky jako vrstvu obrázku na mapě. KML překryvná deska poskytují souřadnice Severní, Jižní, východní a západní délky hodinových ručiček, zatímco vrstva obrázku očekává souřadnice pro každý roh obrázku. Překrytí základní desky KML v této ukázce je Chartres Cathedral a zdroj z [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Následující kód používá funkci static `getCoordinatesFromEdges` třídy [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) k výpočtu čtyř rohů obrázku z informací o Severní, Jižní, východní, západní a rotaci z KMLové překryvné desky.

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