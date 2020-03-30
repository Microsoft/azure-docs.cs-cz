---
title: Přidání značky HTML do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak přidat značku HTML do mapy pomocí sady Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2700d42c25d58911fb275ad9ce6c5610cd22624d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536764"
---
# <a name="add-html-markers-to-the-map"></a>Přidání značek HTML do mapy

Tento článek ukazuje, jak přidat vlastní HTML, například obrazový soubor do mapy jako značku HTML.

> [!NOTE]
> Značky HTML se nepřipojují ke zdrojům dat. Místo toho informace o poloze je přidán přímo do `markers` značky a značka je přidána do maps vlastnost, která je [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Na rozdíl od většiny vrstev ve webovém ovládacím prvku Azure Maps Web, které používají WebGL pro vykreslování, značky HTML používají tradiční prvky DOM pro vykreslování. Jako takový, více HTML značky přidány na stránku, tím více prvků DOM existují. Výkon může snížit po přidání několika set HTML značek. U větších datových sad zvažte buď clustering data nebo pomocí symbolu nebo bublinové vrstvy.

## <a name="add-an-html-marker"></a>Přidání značky HTML

Třída [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) má výchozí styl. Značku můžete přizpůsobit nastavením barvy a možností textu značky. Výchozí styl třídy značky HTML je šablona `{color}` SVG, která má zástupný symbol a. `{text}` Nastavte vlastnosti barev a textu v možnostech značky HTML pro rychlé přizpůsobení. 

Následující kód vytvoří značku HTML a nastaví vlastnost color na "DodgerBlue" a vlastnost text na "10". K značce je připojeno `click` vyskakovací okno a událost se používá k přepínání viditelnosti vyskakovacího centra.

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí.

<br/>

<iframe height='500' scrolling='no' title='Přidání značky HTML do mapy' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero Přidat značku HTML<a href='https://codepen.io/azuremaps'>@azuremaps</a>na <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>mapu</a> pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Vytvořit značku HTML se šablonou SVG

Výchozí `htmlContent` hodnota značky Html je šablona SVG se složkami `{color}` míst a `{text}` v ní. Můžete vytvořit vlastní řetězce SVG a přidat tyto stejné zástupné symboly do svg tak, aby nastavení `color` a `text` možnosti značky aktualizovat tyto zástupné symboly v SVG.

<br/>

<iframe height='500' scrolling='no' title='Značka HTML s vlastní šablonou SVG' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na značku HTML pera s<a href='https://codepen.io/azuremaps'>@azuremaps</a>vlastní <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>šablonou SVG</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Webová sada Azure Maps poskytuje několik šablon bitových obrázků SVG, které lze použít se značkami HTML. Další informace naleznete v dokumentu [Jak používat šablony obrázků.](how-to-use-image-templates-web-sdk.md)

## <a name="add-a-css-styled-html-marker"></a>Přidání značky HTML ve stylu CSS

Jednou z výhod html značek je, že existuje mnoho skvělých přizpůsobení, které lze dosáhnout pomocí CSS. V této ukázce se obsah HtmlMarker skládá z HTML a CSS, které vytvářejí animovaný pin, který klesne na místo a pulsy.

<br/>

<iframe height='500' scrolling='no' title='Zdroj dat HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero HTML<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>DataSource</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Přetahovatelné značky HTML

Tato ukázka ukazuje, jak vytvořit značku HTML, která lze přetáhnout. Značky HTML `drag` `dragstart`podporují `dragend` a události.

<br/>

<iframe height='500' scrolling='no' title='Přetáhnoutelná značka HTML' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>značku HTML přetahovaného</a> perem podle Map Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Přidání událostí myši ke značkám HTML

Tyto ukázky ukazují, jak přidat myši a přetáhnout události do značky HTML.

<br/>

<iframe height='500' scrolling='no' title='Přidání událostí myši do značek HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>přidání události myši do HTML značky</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Značka Html](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Možnosti htmlznaček](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Další příklady kódu, které chcete přidat do map, najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Používání šablon obrázků](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](./map-add-bubble-layer.md)
