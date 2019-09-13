---
title: Zpracování událostí pomocí Azure Maps | Microsoft Docs
description: Jak vytvořit interaktivní mapu webové sady SDK s událostmi mapy
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c5b4ed73d7bc4d89a67280a0bb183f374ae093d8
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899436"
---
# <a name="interact-with-the-map"></a>Interakce s mapou

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

V následující tabulce jsou uvedeny všechny podporované události třídy map.

| Událost             | Popis |
|-------------------|-------------|
| boxzoomend        | Je aktivována při ukončení interakce "box zoom".|
| boxzoomstart      | Je aktivována, když se spustí interakce "box zoom".|
| Klepněte             | Je aktivována, když se na mapě stiskne a uvolní polohovací zařízení ve stejném bodě.|
| zavřít             | Je aktivována, když se místní nabídka uzavře ručně nebo programově.|
| nabídku       | Je aktivována při kliknutí na pravé tlačítko myši.|
| přidávané         | Je aktivována, když jsou do zdroje dat přidány obrazce.|
| dataremoved       | Je aktivována, když dojde k odebrání obrazců ze zdroje dat.|
| datasourceupdated | Je aktivována při aktualizaci objektu DataSource.|
| DblClick          | Je aktivována, když se na mapě dvakrát klikne na polohovací zařízení dvakrát.|
| Myší              | Tato možnost se opakovaně vyvolala během interakce "přetáhnout na pan" na mapě, v místní nabídce nebo na značce HTML.|
| dragend           | Je aktivována, když dojde k ukončení interakce "přetáhnout do pánev" na mapě, v místní nabídce nebo na značce HTML.|
| dragstart         | Je aktivována, když se spustí interakce "přetáhnout na pan" na mapě, v místní nabídce nebo na značku HTML.|
| chyba             | Je aktivována, když dojde k chybě.|
| KeyDown           | Je aktivována při stisknutí klávesy.|
| klávesy          | Je aktivována při stisknutí klávesy, která vytvoří znak typable (klíč ANSI).|
| události KeyUp             | Je aktivována při uvolnění klávesy.|
| layeradded        | Je aktivována, když dojde k přidání vrstvy do mapy.|
| load              | Je aktivována ihned po stažení všech potřebných prostředků a prvním vizuálním dokončením vykreslování mapy.|
| MouseDown         | Je aktivována, když se v mapě stiskne polohovací zařízení.|
| MouseMove         | Je aktivována, když se v mapě přesune polohovací zařízení.|
| myš          | Aktivuje se v případě, že zařízení opustí plátno na místě.|
| MouseOver         | Je aktivována, když se v mapě přesune polohovací zařízení.|
| MouseUp           | Je aktivována, když se v mapě uvolní polohovací zařízení.|
| Pøesunout              | Tato možnost se opakovaně vyvolala během animovaného přechodu z jednoho zobrazení na jiný, jako výsledek interakce uživatele nebo metody.|
| moveend           | Vyvoláno bezprostředně poté, co mapa dokončí přechod z jednoho zobrazení na jiný, jako výsledek interakce uživatele nebo metody.|
| movestart         | Aktivováno těsně před tím, než mapa zahájí přechod z jednoho zobrazení na jiný, jako výsledek interakce uživatele nebo metody.|
| open (otevírá)              | Je aktivována, když dojde k ručnímu otevření nebo programově překryvného okna.|
| teče             | Vyvoláno při změně sklonu mapy (náklonu) v důsledku interakce uživatele nebo metod.|
| pitchend          | Je aktivována ihned po změně sklonu mapy (náklon) v důsledku zásahu uživatele nebo metod.|
| pitchstart        | Je aktivována při každé změně sklonu mapy (náklonu) v důsledku zásahu uživatele nebo metod.|
| připraveno             | Je aktivována, když jsou načteny minimální požadované prostředky mapy před tím, než je mapa připravena k programovému používání.|
| Činit            | <p> Je aktivována při každém vykreslení mapy na obrazovku v důsledku:<ul><li>Změna umístění mapy, přiblížení, sklonu nebo označení.</li><li>Změna stylu mapy.</li><li>Změna zdroje DataSource.</li><li>Načítají se vektorová dlaždice, soubor. JSON, piktogram nebo Sprite.</li></ul></p>|
| velikost            | Je aktivována ihned po změně velikosti mapy.|
| před            | Vyvoláno opakovaně během interakce "přetažením k otočení".|
| rotateend         | Je aktivována, když je ukončena interakce "přetažením k otočení".|
| rotatestart       | Je aktivována, když se spustí interakce "přetáhnout k otočení".|
| shapechanged      | Je aktivována, když dojde ke změně vlastnosti objektu Shape.|
| sourceadded       | Je aktivována, když je do mapy přidán zdroj dat nebo VectorTileSource.|
| sourceremoved     | Je aktivována, když dojde k odebrání zdroje dat nebo VectorTileSource z mapy.|
| styledata         | Je aktivována, když styl mapy načítá nebo mění.|
| tokenacquired     | Je aktivována při získání přístupového tokenu AAD.|
| touchcancel       | Je aktivována, když dojde k události touchcancel v rámci mapy.|
| touchend          | Je aktivována, když dojde k události touchend v rámci mapy.|
| touchmove         | Je aktivována, když dojde k události TouchMove v rámci mapy.|
| touchstart        | Je aktivována, když dojde k události touchstart v rámci mapy.|
| dvoukolových             | Je aktivována, když dojde k události kolečka myši v rámci mapy.|
| Přibliž              | Se opakovaně vyvolala během animovaného přechodu z jedné úrovně přiblížení na jinou v důsledku interakce uživatele nebo metody.|
| zoomend           | Vyvoláno bezprostředně poté, co mapa dokončí přechod z jedné úrovně zvětšení na jinou, jako výsledek interakce uživatele nebo metody.|
| zoomstart         | Je aktivována těsně před tím, než mapa zahájí přechod z jedné úrovně zvětšení na jinou, jako výsledek interakce uživatele nebo metody.|


## <a name="next-steps"></a>Další postup

Úplné příklady kódu najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Použití modulu služby Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
