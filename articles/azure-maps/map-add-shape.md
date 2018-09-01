---
title: Přidání obrazce s Azure Maps | Dokumentace Microsoftu
description: Přidání obrazce do mapy jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5315e7d45ef3af838f26422655cf6971af6f903e
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382546"
---
# <a name="add-a-shape-to-a-map"></a>Přidání obrazce do mapy

Tento článek ukazuje, jak přidat řádek, kruh a mnohoúhelníku do mapy. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Přidat řádek

<iframe height='500' scrolling='no' title='Přidání řádku do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/qomaKv/'>přidá řádek do mapy</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu se vytvoří řádku. Řádek je [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z LineString s LineStringProperties jako jeho vlastnost funkce. Použití `new atlas.data.Feature(new atlas.data.LineString())` vytvoření čáry a definovat jeho vlastnosti. 

Čárová vrstva je pole řádků. Poslední blok kódu používá [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) funkce třídy mapy přidat čárovou vrstvu mapy a definovat vlastnosti čárovou vrstvu. Zobrazit vlastnosti čárovou vrstvu na [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Přidejte kruh

<iframe height='500' scrolling='no' title='Přidejte kruh do mapy' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>přidejte kruh k mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu se vytvoří kruh. Kruh se [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) s [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.circleproperties?view=azure-iot-typescript-latest) jako jeho vlastnost funkce. Použití `new atlas.data.Feature(new atlas.data.Point())` vytvoříte kruh a definovat jeho vlastnosti.

Vrstva kruhu je pole kruzích. Poslední blok kódu používá [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcircles) funkce mapování třídy přidejte kruh vrstvy do mapy a definovat vlastnosti vrstvy kruh. Zobrazit vlastnosti vrstvy kruhu na [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Přidat mnohoúhelníku
<iframe height='500' scrolling='no' title='Přidat mnohoúhelníku mapy ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>přidat mnohoúhelníku mapy </a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu se vytvoří mnohoúhelníku. Mnohoúhelník je [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [mnohoúhelníku](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) s [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonproperties?view=azure-iot-typescript-latest) jako jeho vlastnost funkce. Použití `new atlas.data.Feature(new atlas.data.Polygon())` mnohoúhelníku vytvořit a definovat jeho vlastnosti. Poskytuje seřazený souřadnice mnohoúhelníku cesty v konstruktoru mnohoúhelníku.

Mnohoúhelníková vrstva je pole mnohoúhelníku. Poslední blok kódu používá [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpolygons) funkce třídy mapy přidat mnohoúhelníkovou vrstvu mapy a definovat jeho vlastnosti. Zobrazit vlastnosti vrstvy mnohoúhelníků na [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Další postup
Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích:
* [Přidat vlastního HTML](./map-add-custom-html.md)
* [Zobrazení výsledků hledání](./map-search-location.md)


