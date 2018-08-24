---
title: Zobrazení pokynů pro trasu s Azure Maps | Dokumentace Microsoftu
description: Jak zobrazit směrech mezi dvěma umístěními na mapě jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 52462c1c5a2a1a9698a2b51708e63b1bb1664f6e
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745532"
---
# <a name="show-directions-from-a-to-b"></a>Zobrazení pokynů pro trasu z A do B 

Tento článek ukazuje, jak vytvořit žádost o trasa a trasy na mapě zobrazit. 

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Zobrazení pokynů pro trasu z A do B na mapě' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>zobrazení pokynů pro trasu z A do B na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu vytvoří a přidá špendlíky na mapě představující počáteční a koncový bod trasy. Můžete zobrazit [přidání špendlíku na mapě](map-add-pin.md) pokyny.

Třetí blok kódu používá [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funkce třídy map nastavit ohraničující rámeček mapy podle počáteční a koncový bod trasy.

Odešle čtvrtý bloku kódu [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [trasy rozhraní API služby Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Poslední blok kódu analyzuje příchozí odpovědi. Pro úspěšné odpovědi shromáždí informace o zeměpisné šířce a délce pro každý bod na trase. Každý bod na trase propojíte svůj další bod na trase vytvoří pole řádků. Přidá všechny tyto řádky do mapy vykreslovat trasy. Můžete zobrazit [přidejte řádek na mapě](./map-add-shape.md#addALine) pokyny.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 

* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích: 
* [Na mapě Zobrazit provoz](./map-show-traffic.md)
* [Interakce s mapou - události myši](./map-events.md)
