---
title: Zobrazit pokynů s Azure mapy | Microsoft Docs
description: 'Postupy: zobrazení směrech mezi dvěma umístěními na mapě Javascript'
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 5e9ab73ddc16517e17894cddd9bc102f3941f00c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "35759796"
---
# <a name="show-directions-from-a-to-b"></a>Zobrazení pokynů pro trasu z A do B 

Tento článek ukazuje, jak provést žádost o trasy a trasy zobrazit na mapě. 

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Zobrazit pokyny od A do B na mapě' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>zobrazit pokynů z A do B na mapě</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [vytvoření mapy](./map-create.md) pokyny.

Druhý blok kódu vytvoří a přidá PIN na mapě představují počáteční a koncový bod trasy. Můžete zobrazit [přidat pin na mapě](map-add-pin.md) pokyny.

Používá třetí blok kódu [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) funkce třídy map nastavit pole ohraničující mapy podle počáteční a koncový bod trasy.

Odešle čtvrtý blok kódu [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [rozhraní API Azure map trasy](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Poslední blok kódu analyzuje příchozí odpovědi. Pro úspěšné odpovědi shromáždí informace o zeměpisné šířky a délky pro každý waypoint. Vytvoří se pole řádků připojením každý waypoint k jeho následné waypoint. Přidá všechny tyto řádky do mapy k vykreslení trasy. Můžete zobrazit [přidejte řádek na mapě](./map-add-shape.md#addALine) pokyny.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 

* [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)

Další příklady kódu pro přidání do vaší mapy najdete v následujících článcích: 
* [Provoz zobrazit na mapě](./map-show-traffic.md)
* [Interakci s mapy - události myši](./map-events.md)
