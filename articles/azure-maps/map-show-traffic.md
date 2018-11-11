---
title: Zobrazit provozu s využitím Azure Maps | Dokumentace Microsoftu
description: Jak zobrazit data o provozu na mapě jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 532001a0cda22903d0bdf807ee868aef211336e0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240082"
---
# <a name="show-traffic-on-the-map"></a>Na mapě Zobrazit provoz

Tento článek ukazuje, jak zobrazit informace o provozu a incidentů na mapě.

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='456' scrolling='no' title='Zobrazení provozu na mapě' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>zobrazení provozu na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](map-create.md) pokyny.

Druhý blok kódu používá [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic) funkce třídy Mapa k vykreslení přenosové toky a incidentů na mapě.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro úplné ukázky:

> [!div class="nextstepaction"]
> [Znaková stránka vzorku](https://aka.ms/AzureMapsSamples)

Vylepšení uživatelského prostředí:

> [!div class="nextstepaction"]
> [Mapování interakce s využitím událostí myši](./map-events.md)

> [!div class="nextstepaction"]
> [Sestavování přístupné mapy](./map-accessibility.md)
