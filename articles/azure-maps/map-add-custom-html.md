---
title: Přidat značku HTML do Azure Maps | Dokumentace Microsoftu
description: Jak přidat značku HTML do mapy jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 21689cf7fe3df7ca7e362ba49824fadd5daaa3ea
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888762"
---
# <a name="add-html-markers-to-the-map"></a>Přidejte značky HTML do mapy

Tento článek ukazuje, jak přidat vlastního HTML, jako je například soubor bitové kopie do mapy jako značka HTML.

> [!NOTE]
> Ke zdrojům dat se nepřipojí značky HTML. Místo toho informací o poloze se přidá přímo do značky a značky se přidá do mapy `markers` vlastnost, která je [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Na rozdíl od většiny vrstvy v ovládací prvek map Web Azure, které používají WebGL pro vykreslování značky HTML pomocí tradičních prvků modelu DOM pro vykreslování. V důsledku toho přidali další značky HTML stránku, víc elementů modelu DOM, existují. Po přidání několika značek hundered HTML může snížit výkon. Pro větší sady dat vezměte v úvahu clustering vaše data nebo pomocí symbolu nebo bublinu vrstvy.

## <a name="add-an-html-marker"></a>Přidat značku HTML

HtmlMarker třída nemá výchozí styl. Značky můžete přizpůsobit tak, že nastavíte barvu a text možnosti značky. Výchozí styl třídy HtmlMarker je šablonu SVG, který má zástupný symbol barvu a text. Vlastnosti barvy a textového nastavena v možnostech nástroje HtmlMarker pro rychlé přizpůsobení. 

<br/>

<iframe height='500' scrolling='no' title='Přidat značku HTML do mapy' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>přidat značku HTML k mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu přidá [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) na mapy pomocí [značky](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) vlastnost [mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) třídy. HtmlMarker se přidávají do mapy v rámci [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkce, která se ujistěte, že se zobrazí po načtení mapy plně.

## <a name="create-svg-templated-html-marker"></a>Vytvoření značky HTML bez vizuálního vzhledu SVG

Výchozí hodnota `htmlContent` značky Html je šablonu SVG s místo složky `{color}` a `{text}` v ní. Můžete vytvořit vlastní SVG řetězce a přidat tyto stejné zástupné symboly do vaší SVG tak, aby nastavení `color` a `text` možnosti značky aktualizace těchto zástupných symbolů ve vašich SVG.

<br/>

<iframe height='500' scrolling='no' title='Značka HTML se šablonou vlastního SVG' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>značky HTML se šablonou vlastního SVG</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-css-styled-html-marker"></a>Přidat že šablony stylů CSS styl značky HTML

Jednou z výhod značky HTML je, že existují mnoho skvělých úprav, které lze dosáhnout pomocí šablon stylů CSS. V této ukázce obsah HtmlMarker se skládá z HTML a CSS, vytvoříte animované PIN kód, který se sníží na místě a počtu impulsů.

<br/>

<iframe height='500' scrolling='no' title='Zdroj dat ve formátu HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Přetažitelného značek HTML

Tato ukázka předvádí, jak je nechat přetažitelného značku HTML. Podpora značek HTML `drag`, `dragstart` a `dragend` události.

<br/>

<iframe height='500' scrolling='no' title='Přetažitelného značky HTML' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Přetažitelného značky HTML</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Přidání události myši do značky HTML

Tyto ukázky předvádějí, jak přidat události myši do značku HTML tak, že přidáte standardních událostí JavaScript obsah HTML značky. 

Pokud `htmlContent` značky je prvek modelu DOM (div, img...), můžete přidat události přímo pro ně. 

<br/>

<iframe height='500' scrolling='no' title='Přidání událostí myši do značky HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>přidání událostí myši do značky HTML</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pokud `htmlContent` je řetězec (např.) Podobně výchozí nastavení), můžete vytvořit prvek modelu DOM a přidejte řetězec jako innerHTML do něj a pak přidejte prvek modelu DOM jako htmlContent. Pokud `htmlContent` je řetězec bez vizuálního vzhledu, budete muset najít/nahradit `{text}` a `{color}` nejdříve hodnoty v řetězci.

<br/>

<iframe height='500' scrolling='no' title='Přidání událostí myši do výchozí značky HTML' src='//codepen.io/azuremaps/embed/ZmZLBa/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ZmZLBa/'>přidání události myši výchozí značky HTML</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidat vrstvu symbol](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidat vrstvu bublinový](./map-add-bubble-layer.md)