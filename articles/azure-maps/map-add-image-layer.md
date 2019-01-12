---
title: Přidání vrstvy Image do Azure Maps | Dokumentace Microsoftu
description: Postup přidání vrstva s obrázkem do mapy jazyka Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5396fefca3a60dea7a503f8b4e84cc575753ea30
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229111"
---
# <a name="add-an-image-layer-to-a-map"></a>Přidejte na mapu vrstva s obrázkem

Tento článek popisuje, jak můžete překryv obrázku, který má pevně danou sadu souřadnice na mapě. Existuje mnoho scénářů, ve kterých se provádí zakreslovat bitovou kopii na mapě. Tady je několik příkladů typu Image často jako překryvný obrázek na mapy;

* Obrázky zachycených dronů.
* Vytváření floorplans.
* Historické nebo další Image specializovaného mapy.
* Plány úlohy lokalit.
* Bitové kopie této možnosti taky přemýšlíte o počasí.

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) je rychlý snadný způsob, jak překryvný obrázek na mapě. Ale pokud je velký obrázek, v prohlížeči může usilovat o to, ho načíst. V takovém případě zvažte rozdělení svou image do dlaždice a jejich načtení do mapy jako [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

## <a name="add-an-image-layer"></a>Přidání vrstvy obrázků

Tento příklad ukazuje, jak překryvný obrázek [mapování Newyorském New Jersey z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapě.

<br/>

<iframe height='500' scrolling='no' title='Vrstva jednoduché obrázků' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/eQodRo/'>vrstvy jednoduché Image</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) vytvoří předá adresu URL do obrázku a souřadnice pro čtyři rohy ve formátu `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

## <a name="import-a-kml-ground-overlay"></a>Importovat překrytí základu KML

Tento příklad ukazuje, jak překryv KML základu překrytí informace jako vrstva s obrázkem na mapě. KML základu překrytí poskytují sever – jih, – východ a souřadnice – západ a proti směru hodinových ručiček otočení, kde jako na obrázku vrstva očekává, že souřadnice pro každý roh obrázku. Překrytí základu KML v této ukázce je Chartres cathedral a Source z [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

<br/>

<iframe height='500' scrolling='no' title='Překrytí základu KML jako vrstva obrázků' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML světlá překrytí jako vrstva obrázků</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Výše uvedený kód používá statickou `getCoordinatesFromEdges` funkce [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) třídy k výpočtu čtyři rohy image z na severu, březen – západ a otočení informace KML světlá překrytí.


## <a name="customize-an-image-layer"></a>Přizpůsobení vrstva s obrázkem

Vrstva Image obsahuje celou řadu možností stylu. Zde je nástroj, který je vyzkoušet.

<br/>

<iframe height='700' scrolling='no' title='Obrázek možností vrstvy' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>vrstvy možných Imagí</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro další ukázky kódu pro přidání do vaše mapy:

> [!div class="nextstepaction"]
> [Přidat Dlaždicovou vrstvu](./map-add-tile-layer.md)