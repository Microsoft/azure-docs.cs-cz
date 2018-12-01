---
title: Zpracování události myši s Azure Maps | Dokumentace Microsoftu
description: Jak provádět interaktivní mapu jazyka Javascript pomocí mapy událostí
author: jingjing-z
ms.author: jinzh
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b9174d98dd6d4dfb5353d6976d074bb4c91373dc
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678317"
---
# <a name="interact-with-the-map---mouse-events"></a>Interakce s mapou - události myši

V tomto článku se dozvíte, jak používat [map – třída](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) vlastnost zvýrazněte události na mapě a na různých vrstev mapy. Je také ukazuje, jak pomocí vlastnosti události mapování třídy zvýrazněte události, kdy budete moct používat značky HTML.

## <a name="interact-with-the-map"></a>Interakce s mapou

<iframe height='600' scrolling='no' title='Interakce s mapou – události myši' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interakce s mapou – události myši</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pohrajte si s mapě výše a zobrazte odpovídající události myši zvýrazněný na pravé straně. Můžete kliknout na **JS kartu** k zobrazení a úpravy kódu jazyka JavaScript. Můžete také kliknout na **upravit na CodePen** tlačítko a úpravy kódu na CodePen.

## <a name="interact-with-map-layers"></a>Pracovat s vrstev mapy

<iframe height='600' scrolling='no' title='Interakce s mapou – vrstva událostí' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interakce s mapou – vrstva událostí</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Výše uvedený kód ukazuje název události, které vzplaňte nahoru, když pracujete s vrstvou Symbol. Symbol, bublin, řádku a mnohoúhelníku vrstvy všechny podporují stejnou sadu událostí. Dlaždicová vrstva nepodporuje některé z těchto událostí.

## <a name="interact-with-html-marker"></a>Pracovat s značky HTML

<iframe height='500' scrolling='no' title='Interakce s mapou – značky HTML události' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interakce s mapou – značky HTML události</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Výše uvedený kód přidá značku HTML Javascript mapy událostí. Také zvýrazní název události, které vzplaňte si, jak budete moct používat značky HTML.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro úplné příklady:

> [!div class="nextstepaction"]
> [Zobrazení výsledků hledání](./map-search-location.md)

> [!div class="nextstepaction"]
> [Znaková stránka vzorku](https://aka.ms/AzureMapsSamples)