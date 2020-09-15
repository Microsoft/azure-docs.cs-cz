---
title: Zobrazit provoz na mapě | Mapy Microsoft Azure
description: Zjistěte, jak přidat data o provozu do map. Přečtěte si o datech toku a podívejte se, jak pomocí Azure Maps webové sady SDK přidat data incidentů a data toků do map.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: cd59bb411a598603ccef215cd9a56b7619115e72
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090563"
---
# <a name="show-traffic-on-the-map"></a>Zobrazit provoz na mapě

V Azure Maps jsou k dispozici dva typy dat přenosů:

- Data o incidentech – skládají se z bodů a řádkových dat pro věci, jako jsou konstrukce, uzavření provozu a nehody.
- Data toku – poskytuje metriky toku provozu na cestách. Data toku provozu se často používají k obarvení cest. Barvy jsou založené na tom, kolik přenosů zpomaluje tok, relativně k limitu rychlosti nebo jiné metriky. Data toku provozu v Azure Maps mají tři různé metriky měření:
    - `relative` – je relativní vzhledem k rychlosti volného toku provozu.
    - `absolute` – je absolutní rychlost všech vozidel na cestách.
    - `relative-delay` -Zobrazí oblasti, které jsou pomalejší než průměrná očekávaná prodleva.

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

<iframe height='500' scrolling='no' title='Zobrazení provozu na mapě' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Zobrazit provoz na mapě</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Možnosti překrytí provozu

Následující nástroj umožňuje přepínat mezi různými nastaveními překryvu provozu, abyste viděli, jak se vykreslování mění. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Možnosti překrytí provozu" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Projděte si <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Možnosti překrytí provozu</a> perem podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="add-traffic-controls"></a>Přidat ovládací prvky provozu

Existují dva různé ovládací prvky pro přenos dat, které lze přidat do mapy. První ovládací prvek `TrafficControl` přidá přepínací tlačítko, které se dá použít k zapnutí a vypnutí provozu. Možnosti tohoto ovládacího prvku umožňují určit, kdy se nastavení provozu mají použít při zobrazení provozu. Ve výchozím nastavení bude tento ovládací prvek zobrazovat relativní tok provozu a data incidentů, ale můžete to změnit tak, aby v případě potřeby zobrazovala absolutní tok přenosů a žádné incidenty. Druhý ovládací prvek, `TrafficLegendControl` přidá k mapě legendu toku přenosu dat, která pomáhá uživateli pochopit, co znamenají, že vypadá barva kódu. Tento ovládací prvek se zobrazí pouze na mapě, pokud jsou data toku přenosu zobrazena na mapě a budou skryta ve všech ostatních časech.

Následující kód ukazuje, jak přidat ovládací prvky pro přenos do mapy.

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Řízení provozu" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Prohlédněte si <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>ovládací prvky přenosů</a> perem pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Vylepšete uživatelské prostředí:

> [!div class="nextstepaction"]
> [Mapování interakce s událostmi myši](map-events.md)

> [!div class="nextstepaction"]
> [Sestavení přístupné mapy](map-accessibility.md)

> [!div class="nextstepaction"]
> [Stránka ukázka kódu](https://aka.ms/AzureMapsSamples)
