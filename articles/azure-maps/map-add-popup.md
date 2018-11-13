---
title: Přidání vyskakovacího okna s Azure Maps | Dokumentace Microsoftu
description: Postup přidání automaticky otevíraného okna na mapu jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0be10c155398133887fadb1fe9954068f3afb9d9
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568110"
---
# <a name="add-a-popup-to-the-map"></a>Přidání vyskakovacího okna mapy

Tento článek ukazuje, jak přidat automaticky otevíraného okna na bod na mapě.

## <a name="understand-the-code"></a>Vysvětlení kódu

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Přidání místní nabídky registrace s využitím Azure mapa' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Přidání místní nabídky registrace s využitím Azure mapa</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny. Vytvoří také HTML obsahu, který se má zobrazit v místní nabídce.

Druhý bloku kódu vytvoří objekt zdroje dat pomocí [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídy. Bod je [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) třídy. Objekt bod s vlastnostmi název a popis pak je vytvořen a přidán do zdroje dat.

A [symbol vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá k vykreslení dat na základě bodu zabalené v textu nebo ikony [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě.  Symbol vrstvy se vytvoří ve třetí bloku kódu. Zdroj dat je přidaný do vrstvy symbol, který se pak přidá do mapy.

Čtvrtý bloku kódu vytvoří [místní nabídky objektu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) prostřednictvím `new atlas.Popup()`. Automaticky otevírané okno Vlastnosti, například pozici a pixelOffset jsou součástí [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). PopupOptions lze definovat v konstruktoru automaticky otevírané okno, nebo prostřednictvím [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkce třídy automaticky otevíraného okna. A `mouseover` se pak vytvoří naslouchací proces událostí pro vrstvu symbol.

Poslední blok kódu vytvoří funkci, která se aktivuje `mouseover` naslouchací proces událostí. Nastaví obsah a vlastnosti automaticky otevíraného okna a přidá objekt automaticky otevírané okno do mapy.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Automaticky otevíraného okna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Naleznete v následujících článcích skvělé pro úplné ukázky:

> [!div class="nextstepaction"]
> [Přidání obrazce](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidat vlastního HTML](./map-add-custom-html.md)
