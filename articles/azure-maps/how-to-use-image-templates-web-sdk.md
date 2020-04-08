---
title: Šablony obrázků v sada Azure Maps Web SDK | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak používat šablony obrázků se značkami HTML a různými vrstvami v sadě Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: ee8e8ee4ca64de0390b6fa34e36fb4d06348a8ac
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804805"
---
# <a name="how-to-use-image-templates"></a>Používání šablon obrázků

Obrázky lze použít se značkami HTML a různými vrstvami v rámci webové sady Azure Maps SDK:

 - Vrstvy symbolů mohou vykreslovat body na mapě pomocí ikony obrázku. Symboly lze také vykreslit podél cesty čar.
 - Vrstvy polygonu lze vykreslit pomocí obrazu vzorku výplně. 
 - Značky HTML mohou vykreslovat body pomocí obrazů a dalších prvků HTML.

Chcete-li zajistit dobrý výkon s vrstvami, načtěte obrázky do zdroje pohyblivosti obrazu mapy před vykreslením. [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), symbollayer, předem načte několik symbolů v několika barvách do symbolu mapy sprite, ve výchozím nastavení. Tyto značky obrázky a další jsou k dispozici jako Šablony SVG. Lze je použít k vytvoření obrázků s vlastními měřítky nebo jako primární a sekundární barvy zákazníka. Celkem je k dispozici 42 šablon obrázků: 27 ikon symbolů a 15 vzorců náplně polygonů.

Šablony obrázků lze přidat do zdrojů mapového obrázku `map.imageSprite.createFromTemplate` pomocí funkce. Tato funkce umožňuje předat až pět parametrů;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

Jedná `id` se o jedinečný identifikátor, který vytvoříte. Obrázek `id` je přiřazen, když je přidán do pohyblivého symbolu mapy. Tento identifikátor použijte ve vrstvách k určení, který obrazový prostředek má být vykreslen. Určuje, `templateName` kterou šablonu obrázku má být používána. Tato `color` volba nastaví primární barvu `secondaryColor` obrazu a volby nastaví sekundární barvu obrazu. Tato `scale` volba změní měřítko šablony obrazu před její aplikací na pohyblivost obrazu. Když je obraz aplikován na pohyblivost obrazu, převede se na PNG. Chcete-li zajistit ostré vykreslování, je lepší zvětšit velikost šablony obrazu před přidáním do pohyblivého symbolu, než ji zvětšit ve vrstvě.

Tato funkce asynchronně načte obraz do symbolu obrázku. Proto vrátí Promise, který můžete čekat na dokončení této funkce.

Následující kód ukazuje, jak vytvořit obraz z jedné z předdefinovaných šablon a použít ho s vrstvou symbolů.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Použití šablony obrazu s vrstvou symbolu

Jakmile je šablona obrazu načtena do pohyblivého symbolu mapy, může být vykreslena jako symbol `image` ve vrstvě symbolů odkazem na ID prostředku obrazu ve volbě `iconOptions`.

Následující ukázka vykreslí vrstvu symbolů pomocí šablony `marker-flat` obrazu s teal primární barvou a bílou sekundární barvou. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vrstva symbolů s vestavěnou šablonou ikon" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na vrstvu Symbol pera s<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>vestavěnou šablonou ikon</a> od Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Použití šablony obrázku podél cesty čáry

Jakmile je šablona obrazu načtena do pohyblivého symbolu mapy, může být vykreslena podél cesty čáry přidáním `lineSpacing`linestringu ke zdroji dat a použitím `image` vrstvy `iconOptions`symbolů s volbou a odkazem na ID obrazového prostředku ve volbě th . 

Následující ukázka vykreslí růžovou čáru na mapě `car` a použije vrstvu symbolu pomocí šablony obrazu s vybíravější modrou primární barvou a bílou sekundární barvou. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Řádková vrstva s vestavěnou šablonou ikon" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na vrstvu Pen <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>Line s vestavěnou šablonou ikon</a> od Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Pokud šablona obrázku ukazuje `rotation` nahoru, nastavte volbu ikony vrstvy symbolu na 90, pokud chcete, aby ukazovala stejným směrem jako čára.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Použití šablony obrazu s vrstvou vícesnímek

Jakmile je šablona obrazu načtena do pohyblivého symbolu obrázku mapy, může být vykreslena jako vzorek výplně ve vrstvě polygonu odkazem na ID prostředku obrazu ve `fillPattern` volbě vrstvy.

Následující ukázka vykreslí vrstvu polygonu `dot` pomocí šablony obrazu s červenou primární barvou a průhlednou sekundární barvou.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vyplnění polygonu vestavěnou šablonou ikon" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>polygon Pen Fill s předdefinovanou šablonou ikon</a> od Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Nastavení sekundární barvy vzorků výplně usnadňuje zobrazení podkladové mapy, která bude stále poskytovat primární vzorek. 

## <a name="use-an-image-template-with-an-html-marker"></a>Použití šablony obrazu se značkou HTML

Šablonu obrázku lze načíst pomocí `altas.getImageTemplate` funkce a použít jako obsah značky HTML. Šablonu lze předat `htmlContent` možnosti značky a poté ji `color` `secondaryColor`přizpůsobit `text` pomocí možností , a .

Následující ukázka `marker-arrow` používá šablonu s červenou primární barvou, růžovou sekundární barvou a textovou hodnotou "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Značka HTML s vestavěnou šablonou ikon" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>značku HTML pera s vestavěnou šablonou ikon</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Vytvoření vlastních opakovaně použitelných šablon

Pokud vaše aplikace používá stejnou ikonu s různými ikonami nebo pokud vytváříte modul, který přidává další šablony obrázků, můžete tyto ikony snadno přidat a načíst z webové sady Azure Maps SDK. V oboru `atlas` názvů použijte následující statické funkce.

| Name (Název) | Návratový typ | Popis | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Přidá vlastní šablonu obrázku SVG do oboru názvů atlasu. |
|  `getImageTemplate(templateName: string, scale?: number)`| řetězec | Načte šablonu SVG podle názvu. |
| `getAllImageTemplateNames()` | řetězec[] |  Načte šablonu SVG podle názvu. |

Šablony obrázků SVG podporují následující zástupné hodnoty:

| Zástupný symbol | Popis |
|-|-|
| `{color}` | Primární barva. | 
| `{secondaryColor}` | Sekundární barva. | 
| `{scale}` | Obraz SVG se při přidání do pohybového symbolu mapy převede na obrázek png. Tento zástupný symbol lze použít k škálování šablony před převodem, aby bylo zajištěno, že se vykresluje jasně. | 
| `{text}` | Umístění pro vykreslení textu při použití se značkou HTML. |

Následující příklad ukazuje, jak vzít šablonu SVG a přidat ji do sady Azure Maps web SDK jako opakovaně použitelnou šablonu ikon. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přidání vlastní šablony ikon do oboru názvů atlasu" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>pero Přidat vlastní šablonu ikony atlasu jmenný prostor</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Seznam šablon obrázků

V této tabulce jsou uvedeny všechny šablony bitových obrázků, které jsou aktuálně dostupné v rámci sady Azure Maps web SDK. Název šablony je nad každým obrázkem. Ve výchozím nastavení je primární barva modrá a sekundární barva bílá. Chcete-li, aby byla sekundární barva lépe vidět na bílém pozadí, mají následující obrázky sekundární barvu nastavenou na černou.

**Šablony ikon symbolů**

|||||
|:-:|:-:|:-:|:-:|
| Značku | značka-tlustý | značka-kruh | značka-plochá |
|![ikona značky](./media/image-templates/marker.png)|![ikona o tloušťce značky](./media/image-templates/marker-thick.png)|![ikona značkovacího kruhu](./media/image-templates/marker-circle.png)|![ikona ploché značky](./media/image-templates/marker-flat.png)|
||||
| znaková čtverec | marker-čtverec-cluster | značka-šipka | značka-kuličkový kolík | 
|![ikona znaku znaku znaku znaku značky](./media/image-templates/marker-square.png)|![ikona znaku značky-čtverec-cluster](./media/image-templates/marker-square-cluster.png)|![ikona znaku značkovací šipky](./media/image-templates/marker-arrow.png)|![ikona značkovacího kuličkového kolíku](./media/image-templates/marker-ball-pin.png)|
||||
| značka-čtverec-zaoblené | značka-čtverec-zaoblený cluster | flag | vlajkový trojúhelník |
| ![ikona se zaobleným čtverec](./media/image-templates/marker-square-rounded.png) | ![ikona zaokrouhleného clusteru](./media/image-templates/marker-square-rounded-cluster.png) | ![ikona příznaku](./media/image-templates/flag.png) | ![ikona trojúhelníku vlajky](./media/image-templates/flag-triangle.png) |
||||
| trojúhelník | trojúhelník-tlustý | trojúhelník-šipka nahoru | trojúhelník-šipka-vlevo |
| ![ikona trojúhelníku](./media/image-templates/triangle.png) | ![ikona o tloušťce trojúhelníku](./media/image-templates/triangle-thick.png) | ![ikona se šipkou trojúhelníku](./media/image-templates/triangle-arrow-up.png) | ![ikona trojúhelník-šipka-vlevo](./media/image-templates/triangle-arrow-left.png) |
||||
| Šestiúhelník | šestiúhelník-tlustý | šestiúhelník-zaoblené | šestihranný-zaoblený-tlustý |
| ![ikona šestiúhelníku](./media/image-templates/hexagon.png) | ![ikona o tloušťce šestiúhelníku](./media/image-templates/hexagon-thick.png) | ![šestiúhelníková ikona](./media/image-templates/hexagon-rounded.png) | ![ikona tloušťky šestiúhelníku](./media/image-templates/hexagon-rounded-thick.png) |
||||
| Pin | zaoblení kolíku | zaoblený čtverec | zaoblený-čtverec-tlustý |
| ![Ikona připnutí](./media/image-templates/pin.png) | ![ikona zaokrouhlení na pin](./media/image-templates/pin-round.png) | ![ikona zaobleného čtverce](./media/image-templates/rounded-square.png) | ![ikona se zaobleným čtverečným thosem](./media/image-templates/rounded-square-thick.png) |
||||
| šipka nahoru | šipka-nahoru-tenký | car ||
| ![ikona šipky nahoru](./media/image-templates/arrow-up.png) | ![šipka-nahoru-tenký ikonu](./media/image-templates/arrow-up-thin.png) | ![ikona vozu](./media/image-templates/car.png) | |

**Šablony vzorových vzorků náplně polygonu**

|||||
|:-:|:-:|:-:|:-:|
| Kontrola | otočený šachovní | Kruhy | kruhy rozmístěné |
| ![ikona kontroly](./media/image-templates/checker.png) | ![ikona otočená šachovní](./media/image-templates/checker-rotated.png) | ![ikona kruhů](./media/image-templates/circles.png) | ![ikona rozmístěných kruhů](./media/image-templates/circles-spaced.png) |
|||||
| diagonální zařazovací | diagonální čáry dolů | diagonální pruhy nahoru | diagonální pruhy dolů |
| ![ikona diagonálního zařazovacího](./media/image-templates/diagonal-lines-up.png) | ![ikona diagonálního svozíníní](./media/image-templates/diagonal-lines-down.png) | ![ikona diagonálních pruhů](./media/image-templates/diagonal-stripes-up.png) | ![ikona diagonálních pruhů dolů](./media/image-templates/diagonal-stripes-down.png) |
|||||
| mřížky | otočené čáry mřížky | otočené pruhy | x-výplň |
| ![ikona mřížky](./media/image-templates/grid-lines.png) | ![ikona otočených čar](./media/image-templates/rotated-grid-lines.png) | ![ikona otočených pruhů](./media/image-templates/rotated-grid-stripes.png) | ![ikona x-fill](./media/image-templates/x-fill.png) |
|||||
| cik-cak | cik-cak-vertikální | Tečky |  |
| ![cik-cak ikona](./media/image-templates/zig-zag.png) | ![cik-cak-svislá ikona](./media/image-templates/zig-zag-vertical.png) | ![ikona tečky](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Vyzkoušejte jej nyní, nástroj

Pomocí následujícího nástroje můžete různými způsoby vykreslit různé šablony vestavěných bitových obrázků a přizpůsobit primární a sekundární barvy a měřítko.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Možnosti šablony ikony" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>možnosti</a> šablony<a href='https://codepen.io/azuremaps'>@azuremaps</a>Ikona pera podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [obor názvů atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Další ukázky kódu, kde lze použít šablony obrázků, naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidat html tvůrci](map-add-bubble-layer.md)
