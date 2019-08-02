---
title: Zobrazit provoz s Azure Maps | Microsoft Docs
description: Jak zobrazit data o přenosech na mapě JavaScriptu
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 55bfc434082b2d5b7de193e969fc34f710657cdb
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638627"
---
# <a name="show-traffic-on-the-map"></a>Zobrazit provoz na mapě

V tomto článku se dozvíte, jak zobrazit informace o provozu a incidentech na mapě.

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='456' scrolling='no' title='Zobrazení provozu na mapě' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Zobrazit provoz na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy. Pokyny najdete v tématu [vytvoření mapy](map-create.md) .

Druhý blok kódu používá funkci [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) v rámci funkce [naslouchacího procesu naslouchacího](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) prvku mapy k vykreslování přenosových toků a incidentů na mapě.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Úplné ukázky kódu najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Stránka ukázka kódu](https://aka.ms/AzureMapsSamples)

Vylepšete uživatelské prostředí:

> [!div class="nextstepaction"]
> [Mapování interakce s událostmi myši](./map-events.md)

> [!div class="nextstepaction"]
> [Sestavení přístupné mapy](./map-accessibility.md)
