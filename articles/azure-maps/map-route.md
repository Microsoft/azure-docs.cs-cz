---
title: Zobrazit pokynů s Azure mapy | Microsoft Docs
description: 'Postupy: zobrazení směrech mezi dvěma umístěními na mapě Javascript'
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
ms.openlocfilehash: 9007afd1bc4d2361addc2a554fab1330174e88e7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="show-directions-from-a-to-b"></a>Zobrazit pokyny od A do B 

Tento článek ukazuje, jak provést žádost o trasy a trasy zobrazit na mapě. 

## <a name="understand-the-code"></a>Pochopení kódu

<iframe height='500' scrolling='no' title='Zobrazit pokyny od A do B na mapě' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>zobrazit pokynů z A do B na mapě</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [vytvoření mapy](./map-create.md) pokyny.

Druhý blok kódu vytvoří a přidá PIN na mapě představují počáteční a koncový bod trasy. Můžete zobrazit [přidat pin na mapě](map-add-pin.md) pokyny.

Používá třetí blok kódu [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) funkce třídy map nastavit pole ohraničující mapy podle počáteční a koncový bod trasy.

Odešle čtvrtý blok kódu [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [rozhraní API Azure map trasy](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Poslední blok kódu analyzuje příchozí odpovědi. Pro úspěšné odpovědi shromáždí informace o zeměpisné šířky a délky pro každý waypoint. Vytvoří se pole řádků připojením každý waypoint k jeho následné waypoint. Přidá všechny tyto řádky do mapy k vykreslení trasy. Můžete zobrazit [přidejte řádek na mapě](./map-add-shape.md#addALine) pokyny.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 

* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
