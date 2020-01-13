---
title: Přidat značku HTML pro mapování | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak přidat značku HTML k mapě pomocí webové sady SDK Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5a15b3b9e51772d8c7e5312968fecb180069e2a7
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911248"
---
# <a name="add-html-markers-to-the-map"></a>Přidání značek HTML do mapy

V tomto článku se dozvíte, jak přidat vlastní kód HTML, jako je například soubor obrázku, na mapu jako značku HTML.

> [!NOTE]
> Značky HTML se nepřipojují ke zdrojům dat. Místo toho se informace o poloze přidávají přímo do značky a značka se přidá do vlastnosti `markers` mapy, která je [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Na rozdíl od většiny vrstev ve webovém ovládacím prvku Azure Maps, které používají WebGL pro vykreslování, používají značky HTML tradiční prvky DOM pro vykreslování. V takovém případě více značek HTML přidalo stránku, další prvky modelu DOM jsou. Výkon se může snížit po přidání několika stovek značek HTML. U větších datových sad zvažte buď vytvoření clusterů dat, nebo použití symbolu nebo bublinové vrstvy.

## <a name="add-an-html-marker"></a>Přidat značku HTML

Třída [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) má výchozí styl. Značku lze přizpůsobit nastavením možnosti barva a text značky. Výchozí styl třídy značek HTML je šablona SVG, která má `{color}` a `{text}` zástupný symbol. Nastavte vlastnosti barva a text v možnostech značek HTML pro rychlé přizpůsobení. 

Následující kód vytvoří značku HTML a nastaví vlastnost Color na "DodgerBlue" a vlastnost text na "10". Automaticky otevíraná okna jsou připojena k značce a `click` událost slouží k přepnutí viditelnosti místní nabídky.

```javascript
//Create a HTML marker and add it to the map.
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

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Přidání značky HTML do mapy' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>jak pero Přidá značku HTML na mapu</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Vytvořit značku HTML šablony SVG

Výchozí `htmlContent` značky HTML je šablona SVG se složkami umístění `{color}` a `{text}`. Můžete vytvořit vlastní řetězce SVG a přidat stejné zástupné symboly do formátu SVG, který nastaví `color` a `text` možnosti značky aktualizují tyto zástupné symboly ve formátu SVG.

<br/>

<iframe height='500' scrolling='no' title='HTML značka s vlastní šablonou SVG' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>značku HTML pera s vlastní šablonou SVG</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Sada Azure Maps Web SDK poskytuje několik šablon obrázků SVG, které lze použít se značkami HTML. Další informace najdete v dokumentu [použití šablon obrázků](how-to-use-image-templates-web-sdk.md) .

## <a name="add-a-css-styled-html-marker"></a>Přidat značku HTML ve stylu CSS

Jednou z výhod značek HTML je, že existuje mnoho skvělých přizpůsobení, která lze dosáhnout pomocí šablon stylů CSS. V této ukázce obsah HtmlMarker se skládá z HTML a CSS, které vytvoří animovaný kód PIN, který se zahodí na místo a Pulse.

<br/>

<iframe height='500' scrolling='no' title='Zdroj dat HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>zdroj dat HTML</a> pera Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Přetažené značky HTML

Tento příklad ukazuje, jak nastavit přetáhnutí značky HTML. Značky HTML podporují události `drag`, `dragstart`a `dragend`.

<br/>

<iframe height='500' scrolling='no' title='Přetažená značka HTML' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>značku HTML přetaženou</a> perem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Přidání událostí myši do značek HTML

Tyto ukázky ukazují, jak přidat události myší a přetáhnout je do značky HTML.

<br/>

<iframe height='500' scrolling='no' title='Přidávání událostí myši do značek HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>přidávání událostí myši do značek HTML</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Další příklady kódu pro přidání do map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Používání šablon obrázků](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidat vrstvu symbolů](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidat bublinovou vrstvu](./map-add-bubble-layer.md)