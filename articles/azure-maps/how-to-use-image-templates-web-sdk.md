---
title: Šablony obrázků v sadě Azure Maps Web SDK | Mapy Microsoft Azure
description: V tomto článku se naučíte, jak používat šablony obrázků se značkami HTML a různými vrstvami v sadě Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: cb182a5db77a517b11fb1863665f8c54d58b254a
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911576"
---
# <a name="how-to-use-image-templates"></a>Používání šablon obrázků

Obrázky lze použít se značkami HTML a různými vrstvami v Azure Maps webové sadě SDK:

 - Vrstvy symbolů mohou vykreslovat body na mapě pomocí ikony obrázku. Symboly je také možné vykreslit podél cesty čáry.
 - Vrstvy mnohoúhelníků lze vykreslit pomocí obrázku vzorku výplně. 
 - Značky HTML mohou vykreslovat body pomocí obrázků a dalších prvků HTML.

Aby bylo zajištěno dobrý výkon pomocí vrstev, je nutné před vykreslením načíst do prostředku Sprite obrázku mapy. [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions) z SymbolLayer předvede několik obrázků značek v několik barev do Sprite obrázku mapy ve výchozím nastavení. Tyto stejné obrázky značek a další jsou k dispozici jako šablony SVG a lze je použít k vytvoření obrázků s vlastními měřítki a také primární a sekundární barvou zákazníka. V celku jsou k dispozici 42 šablon imagí; 27 ikon symbolů a 15 vzorků výplně mnohoúhelníku.

Šablony obrázků lze přidat k prostředkům Sprite obrázku mapy pomocí funkce `map.imageSprite.createFromTemplate`. Tato funkce umožňuje předat až pět parametrů;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

kde `id` je jedinečný identifikátor, který vytvoříte, který je přiřazen k obrázku při jeho přidání do Sprite obrázku mapy. Pomocí tohoto identifikátoru v vrstvách určete, který prostředek obrázku se má vykreslit. `templateName` určuje, která šablona obrázku se má použít. Možnost `color` nastaví primární barvu obrázku a možnosti `secondaryColor` nastaví vedlejší barvu obrázku. Možnost `scale` škáluje šablonu obrázku předtím, než ji použijete na Sprite obrazu. Když se obrázek použije na Sprite obrázku, převede se na PNG. Aby bylo zajištěno ostré vykreslování, je lepší škálovat šablonu obrázku před přidáním do Sprite, než je možné škálovat ve vrstvě.

Tato funkce asynchronně načte obrázek do Sprite obrazu a vrátí příslib, který můžete počkat na dokončení této funkce.

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

Jakmile je šablona obrázku načtena do Sprite obrázku mapy, může být vykreslena jako symbol v vrstvě symbolů odkazem na ID prostředku obrázku v možnosti `image` `iconOptions`.

Následující ukázka vykresluje symbolovou vrstvu pomocí šablony `marker-flat` obrázku se šedozelená primární barvou a bílou sekundární barvou. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Symbolová vrstva s vestavěnou šablonou ikon" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
V <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na se podívejte na <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>vrstvu symbolů pera s integrovanou Azure Maps šablonou ikon</a> .
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Použití šablony obrázku v cestě čáry

Jakmile je šablona obrázku načtena do Sprite obrázku mapy, lze ji vykreslit podél cesty řádku přidáním LineString ke zdroji dat a použitím vrstvy symbolů s možností `lineSpacing`a odkazem na ID prostředku obrázku v možnosti `image` `iconOptions`. 

Následující ukázka vykresluje růžovou čáru na mapě a používá vrstvu symbolů pomocí šablony `car` obrázku s modrou primární barvou jasně a bílou sekundární barvou. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Spojnicová vrstva s integrovanou šablonou ikon" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>se podívejte na <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>vrstvu čáry pera s vestavěnou šablonou ikon</a> .
</iframe>

> [!TIP]
> Pokud je v šabloně obrázku uveden seznam, nastavte možnost `rotation` ikony vrstvy symbolů na 90, pokud chcete, aby ukazovaly ve stejném směru jako řádek.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Použití šablony obrázku s mnohoúhelníkovou vrstvou

Jakmile je šablona obrázku načtena do Sprite obrázku mapy, může být vykreslena jako vzorek výplně v mnohoúhelníkové vrstvě odkazem na ID prostředku obrázku v `fillPattern` možnosti vrstvy.

Následující ukázka vykreslí mnohoúhelníkovou vrstvu pomocí šablony `dot` obrázku s červenou primární barvou a průhlednou sekundární barvou.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vyplnit mnohoúhelník pomocí předdefinované šablony ikon" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
V CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'></a>se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>výplň</a> pera.
</iframe>

> [!TIP]
> Nastavení vedlejší barvy vzorů výplně usnadňuje zobrazení základní mapy, která bude stále poskytovat primární vzor. 

## <a name="use-an-image-template-with-an-html-marker"></a>Použití šablony obrázku se značkou HTML

Šablonu obrázku lze načíst pomocí funkce `altas.getImageTemplate` a použít ji jako obsah značky HTML. Šablonu lze předat do `htmlContent` možnosti značky a pak ji přizpůsobit pomocí možností `color`, `secondaryColor`a `text`.

Následující ukázka používá šablonu `marker-arrow` s červenou primární barvou, růžovou sekundární barvou a textovou hodnotou "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Značka HTML s integrovanou šablonou ikon" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>značku HTML pera s integrovanou šablonou ikon</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Vytváření vlastních opakovaně použitelných šablon

Pokud vaše aplikace používá stejnou ikonu s různými ikonami nebo pokud vytváříte modul, který přidává další šablony obrázků, můžete tyto ikony snadno přidat z Azure Maps webové sady SDK a načíst je pomocí následujících statických funkcí v oboru názvů `atlas`.

| Name (Název) | Návratový typ | Popis | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Přidá do oboru názvů Atlas vlastní šablonu obrázku SVG. |
|  `getImageTemplate(templateName: string, scale?: number)`| string | Načte šablonu SVG podle názvu. |
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

<iframe height="500" style="width: 100%;" scrolling="no" title="Přidat šablonu vlastní ikony do oboru názvů Atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Přidání vlastní ikony do oboru názvů Atlas</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Seznam šablon obrázků

V následující tabulce jsou uvedeny všechny šablony imagí, které jsou aktuálně dostupné v sadě Azure Maps Web SDK, s názvem šablony nad každým obrázkem. Ve výchozím nastavení je primární barva modrá a vedlejší barva je bílá. Pro snadnější zobrazení sekundární barvy na bílém pozadí mají následující obrázky nastavenou sekundární barvu na černou.

**Šablony ikon symbolů**

|||||
|:-:|:-:|:-:|:-:|
| Značky | Značka – silná | Značka – kroužek | Značka – plochá |
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
| pin | připnout – zaokrouhlit | Zaoblený – čtvercový | Zaoblený – čtvercový – silný |
| ![Ikona připnutí](./media/image-templates/pin.png) | ![ikona kulatého připnutí](./media/image-templates/pin-round.png) | ![ikona s zaobleným čtvercem](./media/image-templates/rounded-square.png) | ![ikona s kulatým čtvercem a tlustou ikonou](./media/image-templates/rounded-square-thick.png) |
||||
| Šipka nahoru | Šipka nahoru – tenká | car ||
| ![ikona šipky nahoru](./media/image-templates/arrow-up.png) | ![Šipka nahoru – ikona tenké](./media/image-templates/arrow-up-thin.png) | ![ikona auta](./media/image-templates/car.png) | |

**Šablony vzorku výplně mnohoúhelníku**

|||||
|:-:|:-:|:-:|:-:|
| Kontrola | Kontrola – otočená | kružnice | kruhy – volné místo |
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
V <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Možnosti šablony ikony</a> pera.
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
> [Přidat vrstvu symbolů](map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidat řádkovou vrstvu](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidat mnohoúhelníkovou vrstvu](map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidání tvůrců HTML](map-add-bubble-layer.md)
