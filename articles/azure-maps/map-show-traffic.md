---
title: Zobrazit provoz s Azure mapy | Microsoft Docs
description: Postup zobrazení přenosů dat na mapu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6ff7a0270509c244fc97bd04d8ba648fd262dc58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600115"
---
# <a name="show-traffic-on-the-map"></a>Provoz zobrazit na mapě

Tento článek ukazuje, jak zobrazit informace o provozu a incidentů na mapě. 

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='456' scrolling='no' title='Provoz zobrazit na mapě' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Zobrazit provoz na mapě</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [vytvoření mapy](map-create.md) pokyny.

Druhý blok kódu používá [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic) funkce třídy map k vykreslení tok přenosů dat a incidentů na mapě.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 
* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic)

Další příklady kódu pro přidání do vaší mapy najdete v následujících článcích: 
* [Interakci s mapy – události myši](./map-events.md)
* [Sestavování přístupné mapy](./map-accessibility.md)

Podívejte se na naše [znaková stránka ukázka](http://aka.ms/AzureMapsSamples) pro více scénářů mapování.
