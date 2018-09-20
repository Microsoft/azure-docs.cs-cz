---
title: Přidat ovládací prvky mapy ve službě Azure Maps | Dokumentace Microsoftu
description: Jak přidat ovládací prvek lupy, výška ovládacího prvku, otáčení ovládacího prvku a styl ovládacího prvku pro výběr do mapy ve službě Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 4410c2ec5851ff210ca8a5fb4f482e5e12d0b8e8
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367793"
---
# <a name="add-map-controls-to-azure-maps"></a>Přidání ovládacích prvků mapa ke službě Azure Maps

Tento článek ukazuje, jak přidat ovládací prvky Mapa k mapě. Se také dozvíte, jak vytvořit mapu s všechny ovládací prvky a [výběr stylu](https://docs.microsoft.com/azure/azure-maps/choose-map-style#adding-the-style-picker).

## <a name="add-zoom-control"></a>Přidejte ovládací prvek lupy

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku přiblížení' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>přidáte ovládací prvek lupy</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu ve výše uvedeném kódu vytvoří objekt Map. Zobrazit [Vytvořte mapu](./map-create.md) pokyny o tom, jak vytvořit mapu.

Druhý blok kódu vytvoří objekt Lupa pomocí atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest).

Ovládací prvek lupy přidává možnost zvětšení do a z mapy. Třetí bloku přidá ovládací prvek Přiblížení mapy pomocí mapy [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metody.

## <a name="add-pitch-control"></a>Přidat výšku ovládacího prvku

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku od' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>přidání ovládacího prvku rozteč</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok výše uvedený kód vytvoří objekt Map stylem nastavit na stupně šedé. Zobrazit [Vytvořte mapu](./map-create.md) pokyny o tom, jak vytvořit mapu.

Druhý blok kódu vytvoří objekt výšku ovládacího prvku pomocí atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest).

Výška ovládacího prvku přidává možnost změnit výšku na mapě. Třetí bloku přidá rozteč ovládací prvek do mapy pomocí mapy [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metody.

## <a name="add-compass-control"></a>Přidání kompasu ovládacího prvku

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku otáčení' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>přidání ovládacího prvku otočit</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu ve výše uvedeném kódu vytvoří objekt Map. Zobrazit [Vytvořte mapu](./map-create.md) pokyny o tom, jak vytvořit mapu.

Druhý bloku kódu vytvoří objekt ovládacího prvku Compass pomocí atlas [ovládací prvek Compass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol). Také přidá kompasu ovládací prvek do mapy pomocí mapy [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metody.

## <a name="a-map-with-all-controls"></a>Mapa u všech ovládacích prvků

<iframe height='500' scrolling='no' title='Mapování se všechny ovládací prvky' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>mapy s všechny ovládací prvky</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu ve výše uvedeném kódu vytvoří objekt Map. Zobrazit [Vytvořte mapu](./map-create.md) pokyny o tom, jak vytvořit mapu.

Druhý blok kódu vytvoří objekt ovládacího prvku Compass pomocí atlas [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol) a přidá je do mapy pomocí mapy [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metody.

Třetí bloku kódu vytvoří objekt Lupa pomocí atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) a přidá je do mapy pomocí mapy [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metody.

Čtvrtý blok kódu vytvoří objekt výšku ovládacího prvku pomocí atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) a přidá je do mapy pomocí mapy [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metody.

Poslední blok kódu vytvoří objekt pro výběr stylu s použitím atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) a přidá je do mapy pomocí mapy [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metody.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro celý kód:

> [!div class="nextstepaction"]
> [Přidání špendlíku](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidání vyskakovacího okna](./map-add-popup.md)