---
title: Zpracování událostí mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak vytvořit interaktivní mapu sady Web SDK s událostmi mapy pomocí webové sady Microsoft Azure Maps SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b97371d1b63ad4abfe1635e426df1449ab5f3f14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534894"
---
# <a name="interact-with-the-map"></a>Interakce s mapou

Tento článek ukazuje, jak používat [třídu mapových událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events). Vlastnost zvýrazní události na mapě a na různých vrstvách mapy. Můžete také zvýraznit události při interakci se značkou HTML.

## <a name="interact-with-the-map"></a>Interakce s mapou

Hrajte si s mapou níže a podívejte se na odpovídající události myši zvýrazněné vpravo. Kliknutím na **kartu JS** můžete zobrazit a upravit kód JavaScriptu. Můžete také kliknout na **Upravit na CodePen** upravit kód na CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interakce s mapou – události myši' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interakce s mapou – události myši</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interakce s vrstvami mapy

Následující kód zvýrazní vypálenou událost při interakci s vrstvou symbolu. Symbol, bublina, čára a vícevrstvá vrstva podporují stejnou sadu událostí. Tepelná mapa a vrstvy dlaždic nepodporují žádnou z těchto událostí.

<br/>

<iframe height='600' scrolling='no' title='Interakce s mapou – události vrstvy' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interakci s mapou – vrstva události</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interakce se značkou HTML

Následující kód přidá události mapy Javascript u značky HTML. Také zvýrazní název událostí, které se při interakci se značkou HTML rozpálijí.

<br/>

<iframe height='500' scrolling='no' title='Interakce s mapou - události HTML Marker' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>pero Interakci s mapou - HTML Marker události</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

V následující tabulce jsou uvedeny všechny podporované události mapové třídy.

| Událost               | Popis |
|---------------------|-------------|
| `boxzoomend`        | Aktivováno, když končí interakce "box zoom".|
| `boxzoomstart`      | Aktivováno při spuštění interakce "box zoom".|
| `click`             | Je aktivováno při stisknutí a uvolnění polohovacího zařízení ve stejném bodě na mapě.|
| `close`             | Aktivováno, když je vyskakovací okno uzavřeno ručně nebo programově.|
| `contextmenu`       | Aktivováno při klepnutí na pravé tlačítko myši.|
| `data`              | Aktivováno při načítání nebo změnách libovolných mapových dat. |
| `dataadded`         | Vypalován při `DataSource`přidání obrazců do .|
| `dataremoved`       | Vypalován při `DataSource`odebrání obrazců z .|
| `datasourceupdated` | Aktivováno `DataSource` při aktualizaci objektu.|
| `dblclick`          | Je aktivována při kliknutí na polohovací zařízení dvakrát ve stejném bodě na mapě.|
| `drag`              | Opakovaně se používá během interakce "přetažení na posouvání" na mapě, vyskakovacím období nebo značce HTML.|
| `dragend`           | Je aktivována, když interakce "přetažení posouvá" končí na mapě, vyskakovacím období nebo značce HTML.|
| `dragstart`         | Je aktivována, když se interakce "přetažení posouvá" spustí na mapě, vyskakovacím okno nebo značce HTML.|
| `error`             | Aktivována, když dojde k chybě.|
| `idle`              | <p>Fired po posledním snímku vykresleném před tím, než mapa přejde do stavu "nečinnosti":<ul><li>Neprobíhají žádné přechody kamery.</li><li>Všechny aktuálně požadované dlaždice byly načteny.</li><li>Všechny animace fade/transition byly dokončeny.</li></ul></p>|
| `keydown`           | Aktivováno, když je stisknuto tlačítko dolů.|
| `keypress`          | Je aktivována při stisknutí klávesy, která vytváří znak, který lze psát (klávesa ANSI).|
| `keyup`             | Aktivováno při uvolnění klíče.|
| `layeradded`        | Aktivováno při přidání vrstvy do mapy.|
| `layerremoved`      | Aktivováno při odebrání vrstvy z mapy.|
| `load`              | Aktivováno ihned po stažení všech potřebných prostředků a došlo k prvnímu vizuálně úplnému vykreslování mapy.|
| `mousedown`         | Vypaluje se při stisknutí polohovacího zařízení v mapě nebo nad elementem.|
| `mouseenter`        | Aktivováno, když je polohovací zařízení zpočátku přesunuto přes mapu nebo prvek. |
| `mouseleave`        | Aktivováno při přesunutí polohovacího zařízení z mapy nebo prvku. |
| `mousemove`         | Aktivováno při přesunutí polohovacího zařízení v rámci mapy nebo prvku.|
| `mouseout`          | Vystřelil, když bod zařízení opustí mapové plátno naše listy prvek.|
| `mouseover`         | Aktivováno při přesunutí polohovacího zařízení přes mapu nebo prvek.|
| `mouseup`           | Aktivováno při uvolnění polohovacího zařízení v rámci mapy nebo v horní části prvku.|
| `move`              | Opakovaně aktivována během animovaného přechodu z jednoho zobrazení do druhého, v důsledku interakce uživatele nebo metody.|
| `moveend`           | Aktivováno těsně po dokončení mapy přechod z jednoho zobrazení do druhého, v důsledku interakce uživatele nebo metody.|
| `movestart`         | Aktivováno těsně před mapa začne přechod z jednoho zobrazení do druhého, v důsledku interakce uživatele nebo metody.|
| `open`              | Aktivováno při ručním nebo programově otevření vyskakovacího okně.|
| `pitch`             | Aktivováno vždy, když se změní rozteč (náklon) mapy v důsledku interakce s uživatelem nebo metod.|
| `pitchend`          | Vypalován bezprostředně po rozteč mapy (náklon) dokončí měnící se v důsledku interakce uživatele nebo metody.|
| `pitchstart`        | Aktivováno vždy, když rozteč mapy (náklon) začne měnit v důsledku interakce uživatele nebo metod.|
| `ready`             | Aktivováno při načtení minimálních požadovaných mapových prostředků před tím, než je mapa připravena k programové interakci.|
| `render`            | <p>Vypalován vždy, když je mapa nakreslena na obrazovku, v důsledku:<ul><li>Změna polohy, přiblížení, rozteče nebo směrů mapy.</li><li>Změna stylu mapy.</li><li>Změna `DataSource` zdroje.</li><li>Načítání vektorové dlaždice, souboru GeoJSON, glyfu nebo pohyblivého symbolu.</li></ul></p>|
| `resize`            | Vystřelil ihned po velikosti mapy.|
| `rotate`            | Opakovaně vypalován během interakce "drag to rotate".|
| `rotateend`         | Aktivováno, když končí interakce "drag to rotate".|
| `rotatestart`       | Aktivováno při spuštění interakce "drag to rotate".|
| `shapechanged`      | Aktivováno při změně vlastnosti objektu tvaru.|
| `sourcedata`        | Aktivováno, když se jeden ze zdrojů mapy načte nebo změní, včetně pokud se dlaždice patřící ke zdroji načte nebo změní. |
| `sourceadded`       | Aktivováno `DataSource` při `VectorTileSource` nebo je přidán do mapy.|
| `sourceremoved`     | Aktivováno, `DataSource` `VectorTileSource` když nebo je odebránz mapy.|
| `styledata`         | Aktivováno, když se styl mapy načte nebo změní.|
| `styleimagemissing` | Aktivováno, když se vrstva pokusí načíst obraz z obrázku, který neexistuje |
| `tokenacquired`     | Aktivováno při získání přístupového tokenu AAD.|
| `touchcancel`       | Aktivována, když dojde k touchcancel události v rámci mapy.|
| `touchend`          | Aktivováno, když dojde k události touch-endu v rámci mapy.|
| `touchmove`         | Aktivována, když dojde k touchmove události v rámci mapy.|
| `touchstart`        | Aktivována, když dojde k události touchstart v rámci mapy.|
| `wheel`             | Aktivována, když dojde k události kolečka myši v rámci mapy.|
| `zoom`              | Opakovaně aktivována během animovaného přechodu z jedné úrovně zvětšení na jinou, v důsledku interakce uživatele nebo metody.|
| `zoomend`           | Aktivováno těsně po dokončení mapy přechod z jedné úrovně zvětšení na jinou, v důsledku interakce uživatele nebo metody.|
| `zoomstart`         | Aktivováno těsně před tím, než mapa začne přechod z jedné úrovně zvětšení na jinou, v důsledku interakce uživatele nebo metod.|


## <a name="next-steps"></a>Další kroky

Úplné příklady kódu naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Použití modulu Služby Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
