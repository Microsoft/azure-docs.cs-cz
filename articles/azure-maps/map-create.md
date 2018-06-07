---
title: Vytvoření mapování mapy Azure | Microsoft Docs
description: Postup vytvoření mapy Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ec85854e5d9b7ee0d5e2c54881a417ba6cbb366e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599809"
---
# <a name="create-a-map"></a>Vytvoření mapy

Tento článek ukazuje, jak k vytvoření mapy.  

## <a name="understand-the-code"></a>Vysvětlení kódu

Existují dva způsoby, můžete vytvořit mapu. Nastavit kamera mapy bodem center a úroveň přiblížení nebo nastavit rozsah fotoaparát mapy zadáním indiánský motiv ohraničujícího bod a severovýchod ohraničujícího bodu.

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>Nastavení kamera

<iframe height='310' scrolling='no' title='Vytvoření mapy prostřednictvím CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>vytvoření mapy prostřednictvím CameraOptions</a> pomocí Azure kg (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše, kódu [objekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) se vytváří prostřednictvím `new atlas.Map()`. Vlastnosti mapy, jako je například Centrování a přiblížení úroveň jsou součástí [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraoptions?view=azure-iot-typescript-latest). CameraOptions lze definovat v konstruktoru mapy nebo prostřednictvím [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera) funkce třídy map.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>Nastavení rozsah fotoaparát

<iframe height='310' scrolling='no' title='Vytvoření mapy prostřednictvím CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>vytvoření mapy prostřednictvím CameraBoundsOptions</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše, kódu [objekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) je vytvořený pomocí `new atlas.Map()`. Vlastnosti mapy, jako je například ohraničujícího pole jsou součástí [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraboundsoptions?view=azure-iot-typescript-latest). CameraBoundsOptions lze definovat pomocí [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) funkce třídy map.

## <a name="try-out-the-code"></a>Vyzkoušet kód 

Podívejte se na výše uvedeném ukázkovém kódu. Můžete upravit kód JavaScript na kartě JS vlevo a podívejte se změny zobrazení mapy na kartě výsledek na pravé straně. Můžete také kliknutím na tlačítko "Upravit na CodePen" a upravit kód v CodePen. 

<a id="relatedReference"></a>

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 
* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    
Další příklady kódu pro přidání do vaší mapy najdete v následujících článcích: 
* [Přidání kódu pin](./map-add-pin.md)
* [Přidat místní okno](map-add-popup.md)
    

