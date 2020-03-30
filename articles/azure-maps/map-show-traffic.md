---
title: Zobrazit provoz na mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak zobrazit data o provozu na mapě pomocí sady Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534810"
---
# <a name="show-traffic-on-the-map"></a>Zobrazit provoz na mapě

V Azure Maps jsou dostupné dva typy provozních dat:

- Údaje o incidentu – se skládají z dat bodů a čar pro věci, jako je výstavba, uzavírky silnic a nehody.
- Údaje o toku - poskytuje metriky o toku provozu na silnicích. Data dopravního toku se často používají k barvení silnic. Barvy jsou založeny na tom, jak velký provoz zpomaluje tok, vzhledem k omezení rychlosti, nebo jiné metriky. Data o toku provozu v Azure Maps mají tři různé metriky měření:
    - `relative`- je relativní vzhledem k rychlosti volného toku vozovky.
    - `absolute`- je absolutní rychlost všech vozidel na silnici.
    - `relative-delay`- zobrazí oblasti, které jsou pomalejší než průměrné očekávané zpoždění.

Následující kód ukazuje, jak zobrazit dopravní údaje na mapě.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí.

<br/>

<iframe height='500' scrolling='no' title='Zobrazení provozu na mapě' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero Zobrazit provoz<a href='https://codepen.io/azuremaps'>@azuremaps</a>na <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>mapě</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Možnosti překrytí provozu

Následující nástroj umožňuje přepínat mezi různými nastaveními překrytí provozu a zjistit, jak se vykreslování mění. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Možnosti překrytí provozu" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>možnosti překrytí provozu</a> pera podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Možnosti provozu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Vylepšete své uživatelské prostředí:

> [!div class="nextstepaction"]
> [Mapovat interakci s událostmi myši](map-events.md)

> [!div class="nextstepaction"]
> [Vytvoření přístupné mapy](map-accessibility.md)

> [!div class="nextstepaction"]
> [Ukázková stránka kódu](https://aka.ms/AzureMapsSamples)
