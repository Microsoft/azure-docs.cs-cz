---
title: Přidat obrazce pomocí mapy Azure | Microsoft Docs
description: Postup přidání obrazce mapy Javascript
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: fb9ec0713d3db465cf835346465e70c4455b38ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-shape-to-a-map"></a>Přidání obrazce mapy

Tento článek ukazuje, jak přidat řádek, kruh a mnohoúhelníku do mapy. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Přidá řádek

<iframe height='500' scrolling='no' title='Přidá řádek do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/qomaKv/'>přidá řádek do mapy</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [vytvoření mapy](./map-create.md) pokyny.

V druhé bloku kódu se vytvoří řádek. Řádek je [funkce](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) z LineString s LineStringProperties jako jeho vlastnost funkce. Použití `new atlas.data.Feature(new atlas.data.LineString())` vytvořte řádek a zadejte jeho vlastnosti. 

Vrstva řádku je pole řádků. Používá posledního bloku kódu [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings) funkce třídy map přidat řádku vrstvy mapy a definovat vlastnosti vrstvy řádku. Informace naleznete ve vlastnostech vrstvy řádku v [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Přidat kruh

<iframe height='500' scrolling='no' title='Přidání kruh do mapy' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>přidat kruh na mapu</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [vytvoření mapy](./map-create.md) pokyny.

V druhé bloku kódu se vytvoří kruh. Je kruh [funkce](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) z [bodu](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) s [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circleproperties?view=azure-iot-typescript-latest) jako jeho vlastnost funkce. Použití `new atlas.data.Feature(new atlas.data.Point())` kruh vytvořit a definovat jeho vlastnosti.

Vrstva kroužek je pole kroužky. Používá posledního bloku kódu [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addcircles) funkce třídy map přidat kruh vrstvy mapy a definovat vlastnosti vrstvy kruh. Informace naleznete ve vlastnostech vrstvy kruh v [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Přidat mnohoúhelníku
<iframe height='500' scrolling='no' title='Přidání mnohoúhelníku do mapy ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>přidat mnohoúhelníku na mapu </a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [vytvoření mapy](./map-create.md) pokyny.

V druhé bloku kódu se vytvoří mnohoúhelníku. Je mnohoúhelníku [funkce](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) z [mnohoúhelníku](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygon?view=azure-iot-typescript-latest) s [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonproperties?view=azure-iot-typescript-latest) jako jeho vlastnost funkce. Použití `new atlas.data.Feature(new atlas.data.Polygon())` mnohoúhelníku vytvořit a definovat jeho vlastnosti. Zadejte seřazené souřadnice mnohoúhelníku cesty v konstruktoru mnohoúhelníku.

Vrstva mnohoúhelníku je pole mnohoúhelníky. Používá posledního bloku kódu [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpolygons) funkce třídy map přidat vrstvě mnohoúhelníku mapy a definovat jeho vlastnosti. Informace naleznete ve vlastnostech vrstvy mnohoúhelníku na [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonlayeroptions?view=azure-iot-typescript-latest). 
