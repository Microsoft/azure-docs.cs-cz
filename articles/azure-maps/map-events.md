---
title: Zpracování událostí myši pomocí Azure Maps | Microsoft Docs
description: Jak vytvořit interaktivní mapu webové sady SDK s událostmi mapy
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 37a3fc3178fe5caeacedfd355a6065ee189a5890
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976530"
---
# <a name="interact-with-the-map---mouse-events"></a>Interakce s událostmi myši na mapě

V tomto článku se dozvíte, jak použít vlastnost [události třídy mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) k zvýraznění událostí na mapě a v různých vrstvách mapy. Také ukazuje, jak použít vlastnost události map Class k zvýraznění událostí při interakci se značkou HTML.

## <a name="interact-with-the-map"></a>Interakce s mapou

Začněte s mapou níže a podívejte se na odpovídající události myši zvýrazněné vpravo. Kliknutím na **kartu js** můžete zobrazit a upravit kód JavaScriptu. Můžete také kliknout na tlačítko **Upravit při CodePen** a upravit kód v CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interakce s mapou – události myši' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interakce s mapou – události myši</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interakce s vrstvami mapy

Následující kód zvýrazní název události, které se při interakci s vrstvou symbolů spustí. Vrstva symbol, bublina, čára a mnohoúhelník podporuje stejnou sadu událostí. Heat mapa a vrstvy dlaždic nepodporují žádnou z těchto událostí.

<br/>

<iframe height='600' scrolling='no' title='Interakce s mapami – události vrstev' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero v <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interakci s mapou – události vrstev</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interakce s HTML značkou

Následující kód přidá události mapy JavaScriptu do značky HTML. Také zvýrazní názvy událostí, které se spustí při interakci se značkou HTML.

<br/>

<iframe height='500' scrolling='no' title='Interakce s událostmi značky HTML map' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>s událostmi značky HTML map –</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Úplné příklady kódu najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Použití modulu služby Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
