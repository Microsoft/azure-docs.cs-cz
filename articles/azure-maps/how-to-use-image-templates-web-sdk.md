---
title: Šablony obrázků v sadě Azure Maps Web SDK | Mapy Microsoft Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804805"
---
# <a name="how-to-use-image-templates"></a>Používání šablon obrázků

Obrázky lze použít se značkami HTML a různými vrstvami v Azure Maps webové sadě SDK:

 - Vrstvy symbolů mohou vykreslovat body na mapě pomocí ikony obrázku. Symboly je také možné vykreslit podél cesty čáry.
 - Vrstvy mnohoúhelníků lze vykreslit pomocí obrázku vzorku výplně. 
 - Značky HTML mohou vykreslovat body pomocí obrázků a dalších prvků HTML.

Chcete-li zajistit dobrý výkon pomocí vrstev, načtěte obrázky do prostředku Sprite obrázku mapy před vykreslením. [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)SymbolLayer, předem načte několik obrázků značek v několik barev do Sprite obrázku mapy, ve výchozím nastavení. Tyto obrázky značek a další jsou k dispozici jako šablony SVG. Dají se použít k vytvoření obrázků s vlastními měřítki nebo k jejich použití jako primární a sekundární barvy zákazníka. V součtu jsou k dispozici 42 šablon imagí: 27 ikon symbolů a 15 vzorů výplně mnohoúhelníku.

Šablony obrázků lze přidat k prostředkům Sprite obrázku mapy pomocí `map.imageSprite.createFromTemplate` funkce. Tato funkce umožňuje předat až pět parametrů;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id` Je jedinečný identifikátor, který vytvoříte. `id` Je přiřazen k obrázku při jeho přidání do Sprite obrázku mapy. Pomocí tohoto identifikátoru v vrstvách určete, který prostředek obrázku se má vykreslit. Určuje `templateName` , která šablona obrázku se má použít. `color` Možnost nastaví primární barvu obrázku a `secondaryColor` možnosti nastaví vedlejší barvu obrázku. `scale` Možnost škáluje šablonu obrázku předtím, než ji použijete na Sprite obrazu. Když se obrázek použije na Sprite obrázku, převede se na PNG. Aby se zajistilo ostré vykreslování, je lepší škálovat šablonu obrázku před přidáním do Sprite, než je můžete škálovat ve vrstvě.

Tato funkce asynchronně načte obrázek do Sprite obrazu. Proto vrátí příslib, který můžete počkat na dokončení této funkce.

Následující kód ukazuje, jak vytvořit obrázek z jedné z předdefinovaných šablon a použít jej s vrstvou symbolů.

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

## <a name="use-an-image-template-with-a-symbol-layer"></a>Použití šablony obrázku s vrstvou symbolů

Jakmile je šablona obrázku načtena do Sprite obrázku mapy, lze ji vykreslit jako symbol v vrstvě symbolů odkazem na ID prostředku obrázku v `image` možnosti. `iconOptions`

Následující ukázka vykreslí vrstvu symbolů pomocí šablony `marker-flat` obrázku se šedozelená primární barvou a bílou sekundární barvou. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Symbolová vrstva s vestavěnou šablonou ikon" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>vrstvu symbolů pera s integrovanou šablonou ikon</a> podle Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Použití šablony obrázku v cestě čáry

Jakmile je šablona obrázku načtena do Sprite obrázku mapy, lze ji vykreslit podél cesty řádku přidáním LineString ke zdroji dat a použitím vrstvy symbolů s `lineSpacing`možností a odkazem na ID prostředku obrázku v `image` možnosti th. `iconOptions` 

Následující ukázka vykreslí růžovou čáru na mapě a používá vrstvu symbolů pomocí šablony `car` obrázku s jasně modrou primární barvou a bílou sekundární barvou. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Spojnicová vrstva s integrovanou šablonou ikon" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>vrstvu čáry pera s integrovanou šablonou ikon</a> podle Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Pokud má šablona obrázku body, nastavte možnost `rotation` ikony vrstvy symbolů na 90, pokud chcete, aby ukazovala ve stejném směru jako řádek.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Použití šablony obrázku s mnohoúhelníkovou vrstvou

Jakmile je šablona obrázku načtena do Sprite obrázku mapy, může být vykreslena jako vzorek výplně v mnohoúhelníkové vrstvě odkazem na ID prostředku obrázku v `fillPattern` možnosti vrstvy.

Následující ukázka vykreslí mnohoúhelníkovou vrstvu pomocí šablony `dot` obrázku s červenou primární barvou a průhlednou sekundární barvou.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vyplnit mnohoúhelník pomocí předdefinované šablony ikon" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se, jak se v CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>nachází <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>mnohoúhelník výplně perem pomocí předdefinované Azure Maps šablony ikon</a> .
</iframe>

> [!TIP]
> Nastavení vedlejší barvy vzorů výplně usnadňuje zobrazení základní mapy, která bude stále poskytovat primární vzor. 

## <a name="use-an-image-template-with-an-html-marker"></a>Použití šablony obrázku se značkou HTML

Šablonu obrázku lze načíst pomocí `altas.getImageTemplate` funkce a použít ji jako obsah značky HTML. Šablonu `htmlContent` lze předat do možnosti značky a následně přizpůsobit pomocí `color` `secondaryColor` `text` možností, a.

Následující ukázka používá `marker-arrow` šablonu s červenou primární barvou, růžovou sekundární barvou a textovou hodnotou "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Značka HTML s integrovanou šablonou ikon" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>značku HTML pera s integrovanou šablonou ikon</a> podle Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Vytváření vlastních opakovaně použitelných šablon

Pokud vaše aplikace používá stejnou ikonu s různými ikonami nebo pokud vytváříte modul, který přidává další šablony obrázků, můžete tyto ikony snadno přidat a načíst z Azure Maps webové sady SDK. V `atlas` oboru názvů použijte následující statické funkce.

| Název | Návratový typ | Popis | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Přidá do oboru názvů Atlas vlastní šablonu obrázku SVG. |
|  `getImageTemplate(templateName: string, scale?: number)`| řetězec | Načte šablonu SVG podle názvu. |
| `getAllImageTemplateNames()` | řetězec [] |  Načte šablonu SVG podle názvu. |

Šablony obrázků SVG podporují následující zástupné hodnoty:

| Zástupný symbol | Popis |
|-|-|
| `{color}` | Primární barva. | 
| `{secondaryColor}` | Vedlejší barva. | 
| `{scale}` | Obrázek SVG se po přidání do Sprite obrázku mapy převede na obrázek PNG. Tento zástupný symbol lze použít ke změně velikosti šablony před jejich převodem, aby bylo zajištěno, že bude vykreslena jasně. | 
| `{text}` | Umístění pro vykreslení textu při použití se značkou HTML |

Následující příklad ukazuje, jak převzít šablonu SVG a přidat ji do sady Azure Maps Web SDK jako opakovaně použitelnou šablonu ikony. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přidat šablonu vlastní ikony do oboru názvů Atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Přidání vlastní ikony do oboru názvů Atlas</a> pomocí Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Seznam šablon obrázků

Tato tabulka obsahuje seznam všech šablon imagí, které jsou aktuálně k dispozici v sadě Azure Maps Web SDK. Název šablony je nad každým obrázkem. Ve výchozím nastavení je primární barva modrá a vedlejší barva je bílá. Pro snadnější zobrazení sekundární barvy na bílém pozadí mají následující obrázky nastavenou sekundární barvu na černou.

**Šablony ikon symbolů**

|||||
|:-:|:-:|:-:|:-:|
| značky | Značka – silná | Značka – kroužek | Značka – plochá |
|![ikona značky](./media/image-templates/marker.png)|![Značka – tlustá ikona](./media/image-templates/marker-thick.png)|![Značka – ikona kruhu](./media/image-templates/marker-circle.png)|![Značka – plochá ikona](./media/image-templates/marker-flat.png)|
||||
| Značka – čtvercový | Marker – čtvercový – cluster | značka – šipka | Fix – míč – PIN | 
|![ikona se čtvercovými značkami](./media/image-templates/marker-square.png)|![Značka – čtvercový – ikona clusteru](./media/image-templates/marker-square-cluster.png)|![Marker – ikona šipky](./media/image-templates/marker-arrow.png)|![Fix – míč – ikona kódu PIN](./media/image-templates/marker-ball-pin.png)|
||||
| Značka – čtvercový – zaoblený | znak – čtvercový – zaoblený – cluster | flag | příznak – trojúhelník |
| ![Značka – čtvercově zaoblená ikona](./media/image-templates/marker-square-rounded.png) | ![ikona se čtvercový-zaoblenými clustery](./media/image-templates/marker-square-rounded-cluster.png) | ![Ikona příznaku](./media/image-templates/flag.png) | ![ikona s označením trojúhelníku](./media/image-templates/flag-triangle.png) |
||||
| trojúhelník | trojúhelníkově silné | trojúhelník – šipka nahoru | trojúhelník – šipka doleva |
| ![ikona trojúhelníku](./media/image-templates/triangle.png) | ![trojúhelníkově tlustá ikona](./media/image-templates/triangle-thick.png) | ![trojúhelník – ikona šipky nahoru](./media/image-templates/triangle-arrow-up.png) | ![trojúhelník – ikona šipky vlevo](./media/image-templates/triangle-arrow-left.png) |
||||
| šestiúhelník | šestiúhelníka tlustá | šestiúhelník – zaoblené | šestiúhelníková zaoblená – silná |
| ![ikona šestiúhelníku](./media/image-templates/hexagon.png) | ![šestiúhelník – tlustá ikona](./media/image-templates/hexagon-thick.png) | ![ikona se zaokrouhlením na šestiúhelník](./media/image-templates/hexagon-rounded.png) | ![šestiúhelník – zaoblený – ikona silného](./media/image-templates/hexagon-rounded-thick.png) |
||||
| kotvicí | připnout – zaokrouhlit | Zaoblený – čtvercový | Zaoblený – čtvercový – silný |
| ![Ikona připnutí](./media/image-templates/pin.png) | ![ikona kulatého připnutí](./media/image-templates/pin-round.png) | ![ikona s zaobleným čtvercem](./media/image-templates/rounded-square.png) | ![ikona s kulatým čtvercem a tlustou ikonou](./media/image-templates/rounded-square-thick.png) |
||||
| Šipka nahoru | Šipka nahoru – tenká | car ||
| ![ikona šipky nahoru](./media/image-templates/arrow-up.png) | ![Šipka nahoru – ikona tenké](./media/image-templates/arrow-up-thin.png) | ![ikona auta](./media/image-templates/car.png) | |

**Šablony vzorku výplně mnohoúhelníku**

|||||
|:-:|:-:|:-:|:-:|
| Kontrola | Kontrola – otočená | kruhu | kruhy – volné místo |
| ![Ikona kontroly](./media/image-templates/checker.png) | ![Checker – ikona otočená](./media/image-templates/checker-rotated.png) | ![ikona kruhů](./media/image-templates/circles.png) | ![kruhy – ikona s mezerami](./media/image-templates/circles-spaced.png) |
|||||
| diagonální – řádky | diagonální čáry – dolů | šikmé – proložení | šikmé – rozložené |
| ![ikona diagonálního řádku](./media/image-templates/diagonal-lines-up.png) | ![Úhlopříčka-čáry – ikona](./media/image-templates/diagonal-lines-down.png) | ![ikona diagonálního obložení](./media/image-templates/diagonal-stripes-up.png) | ![ikona diagonálního pruhu](./media/image-templates/diagonal-stripes-down.png) |
|||||
| Mřížka – čáry | otočené-mřížky – čáry | otočené-mřížky – pruhy | x – výplň |
| ![ikona čar mřížky](./media/image-templates/grid-lines.png) | ![ikona otočení-mřížka-čáry](./media/image-templates/rotated-grid-lines.png) | ![ikona otočení-mřížka-pruhy](./media/image-templates/rotated-grid-stripes.png) | ![ikona výplně x](./media/image-templates/x-fill.png) |
|||||
| cik – cak | cik-cak – vertikální | palec |  |
| ![cik – ikona cak](./media/image-templates/zig-zag.png) | ![cik-cak – ikona svislého](./media/image-templates/zig-zag-vertical.png) | ![ikona teček](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Vyzkoušet Nástroj hned

Pomocí následujícího nástroje můžete vykreslit různé předdefinované šablony imagí různými způsoby a přizpůsobit primární a sekundární barvy a škálování.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Možnosti šablony ikony" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
V CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Možnosti šablony s ikonami</a> pera.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [obor názvů Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

V následujících článcích najdete další ukázky kódu, kde je možné použít šablony obrázků:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidání tvůrců HTML](map-add-bubble-layer.md)
