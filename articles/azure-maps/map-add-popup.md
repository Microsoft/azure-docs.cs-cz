---
title: Přidat automaticky otevírané okno s Azure Maps | Microsoft Docs
description: Postup přidání automaticky otevíraného okna do Azure Maps webové sady SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cde6c745034d0963bd372e36e6e5a046113c202b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976554"
---
# <a name="add-a-popup-to-the-map"></a>Přidat místní nabídku na mapu

V tomto článku se dozvíte, jak přidat místní nabídku do bodu na mapě.

## <a name="understand-the-code"></a>Vysvětlení kódu

Následující kód přidá funkci Point, která má `name` a `description` vlastnosti, na mapu pomocí vrstvy symbolů. Instance [automaticky otevírané třídy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) je vytvořena, ale není zobrazena. Události myši se přidají do vrstvy symbolů, které aktivují otevírání a zavírání automaticky otevírané okno, když ukazatel myši setrvá a odkrývá značku symbolu. Je-li symbol značky umístěn na místě, je vlastnost `position` překryvného objektu aktualizována pomocí pozice značky `content` a možnost je aktualizována s použitím `name` kódu HTML, který zabalí vlastnosti a `description` funkce bodu, které jsou najetí myší. Automaticky otevíraná okna se pak zobrazí na mapě pomocí `open` funkce.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Přidat automaticky otevírané okno pomocí Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Přidání otevřeného okna pomocí Azure Maps</a> pomocí Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Opakované použití automaticky otevíraného okna s více body

Pokud máte velký počet bodů a chcete zobrazit jenom jedno automaticky otevírané okno najednou, nejlepším řešením je vytvořit jedno automaticky otevírané okno a znovu ho použít místo vytvoření místní nabídky pro každou funkci bodu. Po znovu použití překryvného seznamu je počet elementů modelu DOM vytvořených aplikací výrazně snížen, což může poskytovat lepší výkon. Následující ukázka vytvoří funkce 3 body. Pokud kliknete na kterýkoli z nich, zobrazí se automaticky otevírané okno s obsahem této funkce Point.

<br/>

<iframe height='500' scrolling='no' title='Opakované použití automaticky otevíraného okna s více PIN kódy' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>automaticky otevírané okno s více kolíky</a> pomocí Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Přizpůsobení automaticky otevíraného okna

Ve výchozím nastavení má místní nabídka bílé pozadí, šipku ukazatele dole a tlačítko Zavřít v pravém horním rohu. V následujícím příkladu se barva pozadí změní na černou pomocí `fillColor` možnosti automaticky otevírané okno. Tlačítko Zavřít se odebere nastavením `shoCloseButton` možnosti na false. Obsah HTML automaticky otevíraného okna používá v okrajích překryvného textu 10 pixelů a text je vytvořen bíle, takže se na černém pozadí zobrazí úhledný text.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přizpůsobené automaticky otevírané okno" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>automaticky otevírané</a> pero přizpůsobené<a href='https://codepen.io/azuremaps'>@azuremaps</a>pomocí Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Překryvné události

Automaticky otevíraná okna lze otevřít, zavřít a přetáhnout. Automaticky otevíraná Třída poskytuje události, které vývojářům umožňují reagovat na tyto akce. Následující ukázka zvýrazní události, které se aktivují, když otevřete, zavřete nebo přetáhnete místní nabídku. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Překryvné události" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
V CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'></a>se podívejte na <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>místní Azure Maps události</a> pera.
</iframe>

## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Oken](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Úplné ukázky kódu najdete v následujících skvělých článcích:

> [!div class="nextstepaction"]
> [Přidat vrstvu symbolů](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidat značku HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Přidat řádkovou vrstvu](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidat mnohoúhelníkovou vrstvu](map-add-shape.md)