---
title: Zpracování událostí myši pomocí Azure Maps | Microsoft Docs
description: Jak vytvořit interaktivní mapu JavaScriptu s událostmi mapy
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: da6b183155de0fbc370751254a6842343d280874
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638973"
---
# <a name="interact-with-the-map---mouse-events"></a>Interakce s událostmi myši na mapě

V tomto článku se dozvíte, jak použít vlastnost [události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [třídy mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) k zvýraznění událostí na mapě a v různých vrstvách mapy. Také ukazuje, jak použít vlastnost události map Class k zvýraznění událostí při interakci se značkou HTML.

## <a name="interact-with-the-map"></a>Interakce s mapou

<iframe height='600' scrolling='no' title='Interakce s mapou – události myši' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interakce s mapou – události myši</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Přejděte na mapu výše a podívejte se na odpovídající události myši zvýrazněné vpravo. Kliknutím na **kartu js** můžete zobrazit a upravit kód JavaScriptu. Můžete také kliknout na tlačítko **Upravit při CodePen** a upravit kód v CodePen.

## <a name="interact-with-map-layers"></a>Interakce s vrstvami mapy

<iframe height='600' scrolling='no' title='Interakce s mapami – události vrstev' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero v <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interakci s mapou – události vrstev</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Výše uvedený kód zvýrazní názvy událostí, které se při interakci s vrstvou symbolů spustí. Vrstva symbol, bublina, čára a mnohoúhelník podporuje stejnou sadu událostí. Vrstva dlaždice nepodporuje žádné z těchto událostí.

## <a name="interact-with-html-marker"></a>Interakce s HTML značkou

<iframe height='500' scrolling='no' title='Interakce s událostmi značky HTML map' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>s událostmi značky HTML map –</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Výše uvedený kód přidá události mapy JavaScriptu do značky HTML. Také zvýrazní názvy událostí, které se spustí při interakci se značkou HTML.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Úplné příklady kódu najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Použití modulu služby Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Stránka ukázka kódu](https://aka.ms/AzureMapsSamples)
