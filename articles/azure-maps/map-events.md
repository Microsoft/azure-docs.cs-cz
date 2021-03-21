---
title: Zpracování událostí mapy | Mapy Microsoft Azure
description: Zjistěte, které události se aktivují, když uživatelé komunikují s mapami. Zobrazí seznam všech podporovaných událostí mapy. Viz jak používat Azure Maps Web SDK pro zpracování událostí.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 8121ceb68bdea9332316a9508bf6f4731e05b0ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890781"
---
# <a name="interact-with-the-map"></a>Interakce s mapou

V tomto článku se dozvíte, jak používat [třídu událostí mapy](/javascript/api/azure-maps-control/atlas.map#events). Vlastnost zvýrazní události na mapě a v různých vrstvách mapy. Můžete také zvýraznit události při interakci se značkou HTML.

## <a name="interact-with-the-map"></a>Interakce s mapou

Začněte s mapou níže a podívejte se na odpovídající události myši zvýrazněné vpravo. Kliknutím na **kartu js** můžete zobrazit a upravit kód JavaScriptu. Můžete také kliknout na **Upravit v CodePen** a upravit kód v CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interakce s mapou – události myši' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interakce s mapou – události myši</a> podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interakce s vrstvami mapy

Následující kód zvýrazní událost, která je aktivována při interakci s vrstvou symbolů. Vrstva symbol, bublina, čára a mnohoúhelník podporuje stejnou sadu událostí. Heat mapa a vrstvy dlaždic nepodporují žádnou z těchto událostí.

<br/>

<iframe height='600' scrolling='no' title='Interakce s mapami – události vrstev' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero v <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interakci s mapou – události vrstev</a> podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interakce s HTML značkou

Následující kód přidá události mapy JavaScriptu do značky HTML. Také zvýrazní názvy událostí, které se spustí při interakci se značkou HTML.

<br/>

<iframe height='500' scrolling='no' title='Interakce s událostmi značky HTML map' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>s událostmi značky HTML map –</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

V následující tabulce jsou uvedeny všechny podporované události třídy map.

| Událost               | Description |
|---------------------|-------------|
| `boxzoomend`        | Je aktivována při ukončení interakce "box zoom".|
| `boxzoomstart`      | Je aktivována, když se spustí interakce "box zoom".|
| `click`             | Je aktivována, když se na mapě stiskne a uvolní polohovací zařízení ve stejném bodě.|
| `close`             | Je aktivována, když se místní nabídka uzavře ručně nebo programově.|
| `contextmenu`       | Je aktivována při kliknutí na pravé tlačítko myši.|
| `data`              | Je aktivována, když se načtou nebo změní všechna data mapování. |
| `dataadded`         | Je aktivována, když dojde k přidání obrazců do `DataSource` .|
| `dataremoved`       | Je aktivována, když dojde k odebrání obrazců z `DataSource` .|
| `datasourceupdated` | `DataSource`Je aktivována, když dojde k aktualizaci objektu.|
| `dblclick`          | Je aktivována, když se na mapě dvakrát klikne na polohovací zařízení dvakrát.|
| `drag`              | Tato možnost se opakovaně vyvolala během interakce "přetáhnout na pan" na mapě, v místní nabídce nebo na značce HTML.|
| `dragend`           | Je aktivována, když dojde k ukončení interakce "přetáhnout do pánev" na mapě, v místní nabídce nebo na značce HTML.|
| `dragstart`         | Je aktivována, když se spustí interakce "přetáhnout na pan" na mapě, v místní nabídce nebo na značku HTML.|
| `error`             | Je aktivována, když dojde k chybě.|
| `idle`              | <p>Je aktivována po posledním snímku vykresleném před tím, než mapování vstoupí do stavu "nečinné":<ul><li>Neprobíhá žádné přechody kamery.</li><li>Všechny aktuálně požadované dlaždice se načetly.</li><li>Všechny animace slábnutí/přechodu se dokončily.</li></ul></p>|
| `keydown`           | Je aktivována při stisknutí klávesy.|
| `keypress`          | Je aktivována při stisknutí klávesy, která vytvoří znak typable (klíč ANSI).|
| `keyup`             | Je aktivována při uvolnění klávesy.|
| `layeradded`        | Je aktivována, když dojde k přidání vrstvy do mapy.|
| `layerremoved`      | Je aktivována, když dojde k odebrání vrstvy z mapy.|
| `load`              | Je aktivována ihned po stažení všech potřebných prostředků a prvním vizuálním dokončením vykreslování mapy.|
| `mousedown`         | Je aktivována, když se na mapě stiskne polohovací zařízení nebo když se nachází nad prvkem.|
| `mouseenter`        | Je aktivována při počátečním přesunutí polohovacího zařízení na mapě nebo elementu. |
| `mouseleave`        | Je aktivována, když je polohovací zařízení přesunuto z mapy nebo elementu. |
| `mousemove`         | Je aktivována, když se polohovací zařízení přesune v rámci mapy nebo elementu.|
| `mouseout`          | Vyvoláno, když zařízení v místě opustí plátno mapy. opustí prvek.|
| `mouseover`         | Je aktivována, když se na mapě nebo v prvku přesune polohovací zařízení.|
| `mouseup`           | Je aktivována, když se na mapě uvolní polohovací zařízení nebo když se nachází nad prvkem.|
| `move`              | Tato možnost se opakovaně vyvolala během animovaného přechodu z jednoho zobrazení na jiný, jako výsledek interakce uživatele nebo metody.|
| `moveend`           | Vyvoláno bezprostředně poté, co mapa dokončí přechod z jednoho zobrazení na jiný, jako výsledek interakce uživatele nebo metody.|
| `movestart`         | Aktivováno těsně před tím, než mapa zahájí přechod z jednoho zobrazení na jiný, jako výsledek interakce uživatele nebo metody.|
| `open`              | Je aktivována, když dojde k ručnímu otevření nebo programově překryvného okna.|
| `pitch`             | Vyvoláno při změně sklonu mapy (náklonu) v důsledku interakce uživatele nebo metod.|
| `pitchend`          | Je aktivována ihned po změně sklonu mapy (náklon) v důsledku zásahu uživatele nebo metod.|
| `pitchstart`        | Je aktivována při každé změně sklonu mapy (náklonu) v důsledku zásahu uživatele nebo metod.|
| `ready`             | Je aktivována, když jsou načteny minimální požadované prostředky mapy před tím, než je mapa připravena k programovému používání.|
| `render`            | <p>Je aktivována při každém vykreslení mapy na obrazovku v důsledku:<ul><li>Změna umístění mapy, přiblížení, sklonu nebo označení.</li><li>Změna stylu mapy.</li><li>Změna `DataSource` zdroje.</li><li>Načítají se vektorová dlaždice, soubor. JSON, piktogram nebo Sprite.</li></ul></p>|
| `resize`            | Je aktivována ihned po změně velikosti mapy.|
| `rotate`            | Vyvoláno opakovaně během interakce "přetažením k otočení".|
| `rotateend`         | Je aktivována, když je ukončena interakce "přetažením k otočení".|
| `rotatestart`       | Je aktivována, když se spustí interakce "přetáhnout k otočení".|
| `shapechanged`      | Je aktivována, když dojde ke změně vlastnosti objektu Shape.|
| `sourcedata`        | Je aktivována, když se jeden ze zdrojů mapy načítá nebo mění, včetně toho, jestli se dlaždice patřící ke zdroji načítá nebo mění. |
| `sourceadded`       | Je aktivována, `DataSource` Když `VectorTileSource` dojde k přidání nebo k mapě.|
| `sourceremoved`     | Je aktivována, když dojde k `DataSource` `VectorTileSource` Odebrání nebo z mapy.|
| `styledata`         | Je aktivována, když styl mapy načítá nebo mění.|
| `styleimagemissing` | Je aktivována, když se vrstva pokusí načíst obrázek z Sprite obrázku, který neexistuje. |
| `tokenacquired`     | Je aktivována při získání přístupového tokenu Azure Active Directory.|
| `touchcancel`       | Je aktivována, když `touchcancel` dojde k události v rámci mapy.|
| `touchend`          | Je aktivována, když `touchend` dojde k události v rámci mapy.|
| `touchmove`         | Je aktivována, když `touchmove` dojde k události v rámci mapy.|
| `touchstart`        | Je aktivována, když `touchstart` dojde k události v rámci mapy.|
| `wheel`             | Je aktivována, když dojde k události kolečka myši v rámci mapy.|
| `zoom`              | Se opakovaně vyvolala během animovaného přechodu z jedné úrovně přiblížení na jinou v důsledku interakce uživatele nebo metody.|
| `zoomend`           | Vyvoláno bezprostředně poté, co mapa dokončí přechod z jedné úrovně zvětšení na jinou, jako výsledek interakce uživatele nebo metody.|
| `zoomstart`         | Je aktivována těsně před tím, než mapa zahájí přechod z jedné úrovně zvětšení na jinou, jako výsledek interakce uživatele nebo metody.|


## <a name="next-steps"></a>Další kroky

Úplné příklady kódu najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Použití modulu služby Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](/samples/browse/?products=azure-maps)