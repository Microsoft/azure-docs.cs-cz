---
title: Zobrazit provoz na mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak zobrazit data o přenosech na mapě pomocí webové sady SDK Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8107b16a3db76fb0e248f1681463b73cc4ee21cf
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208416"
---
# <a name="show-traffic-on-the-map"></a>Zobrazit provoz na mapě

V Azure Maps jsou k dispozici dva typy dat přenosů:

- Data o incidentech – skládají se z bodů a řádkových dat pro věci, jako jsou konstrukce, uzavření provozu a nehody.
- Data toku – poskytuje metriky toku provozu na cestách. Data toku provozu se často používají k obarvení cest. Barvy jsou založené na tom, kolik přenosů zpomaluje tok, relativně k limitu rychlosti nebo jiné metriky. Data toku provozu v Azure Maps mají tři různé metriky měření:
    - `relative` – je relativní vzhledem k rychlosti volného toku provozu.
    - `absolute` – jedná se o absolutní rychlost všech vozidel na cestách.
    - `relative-delay` – zobrazí oblasti, které jsou pomalejší než průměrná očekávaná prodleva.

Následující kód ukazuje, jak zobrazit data o přenosech na mapě.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Zobrazení provozu na mapě' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Zobrazit provoz na mapě</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Možnosti překrytí provozu

Následující nástroj umožňuje přepínat mezi různými nastaveními překryvu provozu, abyste viděli, jak se vykreslování mění. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Možnosti překrytí provozu" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Projděte si <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Možnosti překrytí provozu</a> perem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Vylepšete uživatelské prostředí:

> [!div class="nextstepaction"]
> [Mapování interakce s událostmi myši](map-events.md)

> [!div class="nextstepaction"]
> [Sestavení přístupné mapy](map-accessibility.md)

> [!div class="nextstepaction"]
> [Stránka ukázka kódu](https://aka.ms/AzureMapsSamples)
