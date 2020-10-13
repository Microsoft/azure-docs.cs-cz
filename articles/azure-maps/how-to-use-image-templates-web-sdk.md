---
title: Šablony obrázků v sadě Azure Maps Web SDK | Mapy Microsoft Azure
description: Naučte se, jak přidat ikony obrázků a vyplněné mnohoúhelníky do map pomocí Azure Maps webové sady SDK. Zobrazit dostupné šablony vzoru obrázku a výplně.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: f43c0086cfd6e51e0fbcd5d30911aa6c8e9a6518
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335547"
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

`id`Je jedinečný identifikátor, který vytvoříte. `id`Je přiřazen k obrázku při jeho přidání do Sprite obrázku mapy. Pomocí tohoto identifikátoru v vrstvách určete, který prostředek obrázku se má vykreslit. `templateName`Určuje, která šablona obrázku se má použít. `color`Možnost nastaví primární barvu obrázku a `secondaryColor` Možnosti nastaví vedlejší barvu obrázku. `scale`Možnost škáluje šablonu obrázku předtím, než ji použijete na Sprite obrazu. Když se obrázek použije na Sprite obrázku, převede se na PNG. Aby se zajistilo ostré vykreslování, je lepší škálovat šablonu obrázku před přidáním do Sprite, než je můžete škálovat ve vrstvě.

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

Jakmile je šablona obrázku načtena do Sprite obrázku mapy, lze ji vykreslit jako symbol v vrstvě symbolů odkazem na ID prostředku obrázku v `image` možnosti `iconOptions` .

Následující ukázka vykreslí vrstvu symbolů pomocí `marker-flat` šablony obrázku se šedozelená primární barvou a bílou sekundární barvou. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Symbolová vrstva s vestavěnou šablonou ikon" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>vrstvu symbolů pera s integrovanou šablonou ikon</a> podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Použití šablony obrázku v cestě čáry

Jakmile je šablona obrázku načtena do Sprite obrázku mapy, lze ji vykreslit podél cesty řádku přidáním LineString ke zdroji dat a použitím vrstvy symbolů s `lineSpacing` možností a odkazem na ID prostředku obrázku v `image` Možnosti th `iconOptions` . 

Následující ukázka vykreslí růžovou čáru na mapě a používá vrstvu symbolů pomocí `car` šablony obrázku s jasně modrou primární barvou a bílou sekundární barvou. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Spojnicová vrstva s integrovanou šablonou ikon" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>vrstvu čáry pera s integrovanou šablonou ikon</a> podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Pokud má šablona obrázku body, nastavte `rotation` možnost ikony vrstvy symbolů na 90, pokud chcete, aby ukazovala ve stejném směru jako řádek.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Použití šablony obrázku s mnohoúhelníkovou vrstvou

Jakmile je šablona obrázku načtena do Sprite obrázku mapy, může být vykreslena jako vzorek výplně v mnohoúhelníkové vrstvě odkazem na ID prostředku obrázku v `fillPattern` Možnosti vrstvy.

Následující ukázka vykreslí mnohoúhelníkovou vrstvu pomocí `dot` šablony obrázku s červenou primární barvou a průhlednou sekundární barvou.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vyplnit mnohoúhelník pomocí předdefinované šablony ikon" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se, jak se v CodePen () na nachází <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>mnohoúhelník výplně perem pomocí předdefinované Azure Maps šablony ikon</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

> [!TIP]
> Nastavení vedlejší barvy vzorů výplně usnadňuje zobrazení základní mapy, která bude stále poskytovat primární vzor. 

## <a name="use-an-image-template-with-an-html-marker"></a>Použití šablony obrázku se značkou HTML

Šablonu obrázku lze načíst pomocí `altas.getImageTemplate` funkce a použít ji jako obsah značky HTML. Šablonu lze předat do `htmlContent` možnosti značky a následně přizpůsobit pomocí `color` možností, a `secondaryColor` `text` .

Následující ukázka používá `marker-arrow` šablonu s červenou primární barvou, růžovou sekundární barvou a textovou hodnotou "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Značka HTML s integrovanou šablonou ikon" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>značku HTML pera s integrovanou šablonou ikon</a> podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Šablony obrázků lze použít i mimo mapu. GetImageTemplate atanh vrátí řetězec SVG, který obsahuje zástupné symboly; `{color}`, `{secondaryColor}`, `{scale}`, `{text}`. Chcete-li vytvořit platný řetězec SVG, nahraďte tyto zástupné hodnoty. Pak můžete buď přidat řetězec SVG přímo do modelu DOM HTML, nebo ho převést na identifikátor URI dat a vložit ho do značky obrázku. Například:
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>Vytváření vlastních opakovaně použitelných šablon

Pokud vaše aplikace používá stejnou ikonu s různými ikonami nebo pokud vytváříte modul, který přidává další šablony obrázků, můžete tyto ikony snadno přidat a načíst z Azure Maps webové sady SDK. V oboru názvů použijte následující statické funkce `atlas` .

| Name | Návratový typ | Description | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Přidá do oboru názvů Atlas vlastní šablonu obrázku SVG. |
|  `getImageTemplate(templateName: string, scale?: number)`| řetězec | Načte šablonu SVG podle názvu. |
| `getAllImageTemplateNames()` | řetězec [] |  Načte šablonu SVG podle názvu. |

Šablony obrázků SVG podporují následující zástupné hodnoty:

| Zástupný symbol | Description |
|-|-|
| `{color}` | Primární barva. | 
| `{secondaryColor}` | Vedlejší barva. | 
| `{scale}` | Obrázek SVG se po přidání do Sprite obrázku mapy převede na obrázek PNG. Tento zástupný symbol lze použít ke změně velikosti šablony před jejich převodem, aby bylo zajištěno, že bude vykreslena jasně. | 
| `{text}` | Umístění pro vykreslení textu při použití se značkou HTML |

Následující příklad ukazuje, jak převzít šablonu SVG a přidat ji do sady Azure Maps Web SDK jako opakovaně použitelnou šablonu ikony. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přidat šablonu vlastní ikony do oboru názvů Atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Přidání vlastní ikony do oboru názvů Atlas</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Seznam šablon obrázků

Tato tabulka obsahuje seznam všech šablon imagí, které jsou aktuálně k dispozici v sadě Azure Maps Web SDK. Název šablony je nad každým obrázkem. Ve výchozím nastavení je primární barva modrá a vedlejší barva je bílá. Pro snadnější zobrazení sekundární barvy na bílém pozadí mají následující obrázky nastavenou sekundární barvu na černou.

**Šablony ikon symbolů**

:::row:::
   :::column span="":::
      značky
   :::column-end:::
   :::column span="":::
      Značka – silná
   :::column-end:::
   :::column span="":::
      Značka – kroužek
   :::column-end:::
   :::column span="":::
      Značka – plochá
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona značky](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![Značka – tlustá ikona](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![Značka – ikona kruhu](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![Značka – plochá ikona](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      Značka – čtvercový
   :::column-end:::
   :::column span="":::
      Marker – čtvercový – cluster
   :::column-end:::
   :::column span="":::
      značka – šipka
   :::column-end:::
   :::column span="":::
      Fix – míč – PIN
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona se čtvercovými značkami](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![Značka – čtvercový – ikona clusteru](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![Marker – ikona šipky](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![Fix – míč – ikona kódu PIN](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      Značka – čtvercový – zaoblený
   :::column-end:::
   :::column span="":::
      znak – čtvercový – zaoblený – cluster
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      příznak – trojúhelník
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![Značka – čtvercově zaoblená ikona](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![ikona se čtvercový-zaoblenými clustery](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![Ikona příznaku](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![ikona s označením trojúhelníku](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      trojúhelník
   :::column-end:::
   :::column span="":::
      trojúhelníkově silné
   :::column-end:::
   :::column span="":::
      trojúhelník – šipka nahoru
   :::column-end:::
   :::column span="":::
      trojúhelník – šipka doleva
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona trojúhelníku](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![trojúhelníkově tlustá ikona](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![trojúhelník – ikona šipky nahoru](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![trojúhelník – ikona šipky vlevo](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      šestiúhelník
   :::column-end:::
   :::column span="":::
      šestiúhelníka tlustá
   :::column-end:::
   :::column span="":::
      šestiúhelník – zaoblené
   :::column-end:::
   :::column span="":::
      šestiúhelníková zaoblená – silná
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona šestiúhelníku](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![šestiúhelník – tlustá ikona](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![ikona se zaokrouhlením na šestiúhelník](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![šestiúhelník – zaoblený – ikona silného](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      kotvicí
   :::column-end:::
   :::column span="":::
      připnout – zaokrouhlit
   :::column-end:::
   :::column span="":::
      Zaoblený – čtvercový
   :::column-end:::
   :::column span="":::
      Zaoblený – čtvercový – silný
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![Ikona připnutí](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![ikona kulatého připnutí](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![ikona s zaobleným čtvercem](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![ikona s kulatým čtvercem a tlustou ikonou](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      Šipka nahoru
   :::column-end:::
   :::column span="":::
      Šipka nahoru – tenká
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona šipky nahoru](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![Šipka nahoru – ikona tenké](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![ikona auta](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**Šablony vzorku výplně mnohoúhelníku**

:::row:::
   :::column span="":::
      Kontrola
   :::column-end:::
   :::column span="":::
      Kontrola – otočená
   :::column-end:::
   :::column span="":::
      kruhu
   :::column-end:::
   :::column span="":::
      kruhy – volné místo
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![Ikona kontroly](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![Checker – ikona otočená](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![ikona kruhů](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![kruhy – ikona s mezerami](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      diagonální – řádky
   :::column-end:::
   :::column span="":::
      diagonální čáry – dolů
   :::column-end:::
   :::column span="":::
      šikmé – proložení
   :::column-end:::
   :::column span="":::
      šikmé – rozložené
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona diagonálního řádku](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![Úhlopříčka-čáry – ikona](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![ikona diagonálního obložení](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![ikona diagonálního pruhu](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      Mřížka – čáry
   :::column-end:::
   :::column span="":::
      otočené-mřížky – čáry
   :::column-end:::
   :::column span="":::
      otočené-mřížky – pruhy
   :::column-end:::
   :::column span="":::
      x – výplň
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ikona čar mřížky](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![ikona otočení-mřížka-čáry](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![ikona otočení-mřížka-pruhy](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![ikona výplně x](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      cik – cak
   :::column-end:::
   :::column span="":::
      cik-cak – vertikální
   :::column-end:::
   :::column span="":::
      palec
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![cik – ikona cak](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![cik-cak – ikona svislého](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![ikona teček](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**Předem nahrané ikony obrázků**

Mapa předvede sadu ikon do Sprite obrázku mapy pomocí `marker` `pin` šablon, a `pin-round` . Tyto názvy ikon a jejich hodnoty barvy jsou uvedeny v následující tabulce.

| název ikony | color | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>Vyzkoušet Nástroj hned

Pomocí následujícího nástroje můžete vykreslit různé předdefinované šablony imagí různými způsoby a přizpůsobit primární a sekundární barvy a škálování.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Možnosti šablony ikony" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
V CodePen () na se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Možnosti šablony s ikonami</a> pera <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [obor názvů Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas#functions
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
