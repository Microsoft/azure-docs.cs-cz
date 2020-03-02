---
title: Přidat automaticky otevírané okno do bodu na mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak přidat automaticky otevíraná okna do bodu pomocí webové sady SDK Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e44b6fe8341e40fb68fdbf153bf4f4bc5fec7acb
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206567"
---
# <a name="add-a-popup-to-the-map"></a>Přidat místní nabídku na mapu

V tomto článku se dozvíte, jak přidat místní nabídku do bodu na mapě.

## <a name="understand-the-code"></a>Vysvětlení kódu

Následující kód přidá funkci Point, která má `name` a `description` vlastností, na mapu pomocí vrstvy symbolů. Instance [automaticky otevírané třídy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) je vytvořena, ale není zobrazena. Události myši se přidají do vrstvy symbolů, které aktivují otevírání a zavírání automaticky otevíraného okna. Je-li symbol značky umístěn na místě, je vlastnost automaticky otevíraná okna `position` aktualizována umístěním značky a možnost `content` je aktualizována pomocí jazyka HTML, který zabalí `name` a `description` vlastnosti bodu, který je ponechán. Automaticky otevíraná okna se pak zobrazí na mapě pomocí funkce `open`.

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

<iframe height='500' scrolling='no' title='Přidat automaticky otevírané okno pomocí Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Přidání otevřeného okna pomocí Azure Maps</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Opakované použití automaticky otevíraného okna s více body

K dispozici jsou případy, kdy nejlepším přístupem je vytvořit jednu místní nabídku a znovu ji použít. Například můžete mít velký počet bodů a chcete najednou zobrazit pouze jedno okno. Po znovu použití automaticky otevírané okno se počet prvků modelu DOM vytvořených aplikací významně sníží, což může poskytovat lepší výkon. Následující ukázka vytvoří funkce 3 body. Pokud kliknete na kterýkoli z nich, zobrazí se automaticky otevírané okno s obsahem této funkce Point.

<br/>

<iframe height='500' scrolling='no' title='Opakované použití automaticky otevíraného okna s více PIN kódy' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>automaticky otevírané okno s více kolíky</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Přizpůsobení automaticky otevíraného okna

Ve výchozím nastavení má místní nabídka bílé pozadí, šipku ukazatele dole a tlačítko Zavřít v pravém horním rohu. V následujícím příkladu se barva pozadí změní na černou pomocí možnosti `fillColor` automaticky otevírané okno. Tlačítko Zavřít se odebere nastavením možnosti `CloseButton` na hodnotu NEPRAVDA. Obsah HTML automaticky otevíraného okna používá čalounění 10 pixelů od okrajů automaticky otevíraného okna. Text je bílý, takže se zobrazí na černém pozadí.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přizpůsobené automaticky otevírané okno" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Prohlédněte si <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>automaticky otevírané</a> pero pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Přidat k mapě automaticky otevírané šablony

Místní šablony usnadňují vytváření rozložení řízených daty pro automaticky otevíraná okna. Následující části demonstrují použití různých šablon automaticky otevíraných oken pro generování formátovaného obsahu pomocí vlastností funkcí.

### <a name="string-template"></a>Šablona řetězce

Šablona řetězce nahrazuje zástupné symboly hodnotami vlastností funkce. Vlastnostem funkce nemusí být přiřazena hodnota typu String. Například `value1` obsahuje celé číslo. Tyto hodnoty jsou poté předány vlastnosti Content `popupTemplate`. 

Možnost `numberFormat` určuje formát čísla, který se má zobrazit. Pokud `numberFormat` není zadán, bude kód používat automaticky otevíraná okna šablony Data Format. Možnost `numberFormat` formátuje čísla pomocí funkce [Number. toLocaleString –](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) . Chcete-li formátovat Velká čísla, zvažte použití možnosti `numberFormat` s funkcemi z [NumberFormat. Format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Například fragment kódu níže používá `maximumFractionDigits` k omezení počtu číslic zlomku na dva.

> [!Note]
> Existuje pouze jeden způsob, jak může šablona řetězců vykreslit obrázky. Nejprve musí mít šablona řetězce v ní značku obrázku. Hodnota předaná značce obrázku musí být adresa URL obrázku. Šablona řetězců musí mít v `HyperLinkFormatOptions``isImage` nastavenou na hodnotu true. Možnost `isImage` určuje, že hypertextový odkaz je pro obrázek, a hypertextový odkaz bude načten do značky obrázku. Po kliknutí na hypertextový odkaz bude obrázek otevřen.

```javascript
new atlas.data.Feature(new atlas.data.Point([-20, -20]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    popupTemplate: {
        content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
        numberFormat: {
            maximumFractionDigits: 2
        }
    }
}),
```

### <a name="propertyinfo-template"></a>Šablona PropertyInfo

V šabloně PropertyInfo se zobrazí dostupné vlastnosti funkce. Možnost `label` Určuje text, který se má uživateli zobrazit. Pokud `label` není zadán, zobrazí se hypertextový odkaz. A pokud je hypertextový odkaz obrázkem, zobrazí se hodnota přiřazená značce "ALT". `dateFormat` určuje formát data a pokud formát data není zadán, bude datum vykresleno jako řetězec. Možnost `hyperlinkFormat` vykresluje odkazy na kliknutí, podobně, možnost `email` lze použít k vykreslení e-mailových adres, na které lze kliknout.

Předtím, než šablona PropertyInfo zobrazí vlastnosti koncovému uživateli, rekurzivně kontroluje, zda jsou vlastnosti pro tuto funkci opravdu definovány. Ignoruje také zobrazení vlastností stylu a nadpisu. Například se nezobrazí `color`, `size`, `anchor`, `strokeOpacity`a `visibility`. Takže jakmile se kontrola cesty vlastnosti dokončí v back-endu, šablona PropertyInfo zobrazí obsah ve formátu tabulky.

```javascript
new atlas.data.Feature(new atlas.data.Point([20, -20]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com',
    popupTemplate: {
        content: [{
    propertyPath: 'createDate',
    label: 'Created Date'
    },
    {
    propertyPath: 'dateNumber',
    label: 'Formatted date from number',
    dateFormat: {
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        timeZone: 'UTC',
        timeZoneName: 'short'
    }
    },
    {
    propertyPath: 'url',
    label: 'Code samples',
    hideLabel: true,
    hyperlinkFormat: {
        lable: 'Go to code samples!',
        target: '_blank'
    }
    },
    {
    propertyPath: 'email',
    label: 'Email us',
    hideLabel: true,
    hyperlinkFormat: {
        target: '_blank',
        scheme: 'mailto:'
        }
    }
        ]
    }
}),

```

### <a name="multiple-content-templates"></a>Více šablon obsahu

Funkce může také zobrazit obsah pomocí kombinace šablony řetězce a šablony PropertyInfo. V tomto případě šablona řetězce vykresluje hodnoty zástupných symbolů na bílém pozadí.  A šablona PropertyInfo vykresluje obrázek v celé šířce v tabulce. Vlastnosti v této ukázce jsou podobné vlastnostem, které jsme vysvětlili v předchozích ukázkách.

```javascript
new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg',
    popupTemplate: {
    content: [
      'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
      [{
        propertyPath: 'imageLink',
        label: 'Image',
        hideImageLabel: true,
        hyperlinkFormat: {
          isImage: true
        }
      }]
    ],
    numberFormat: {
      maximumFractionDigits: 2
    }
    }
    }),
]);
```

### <a name="points-without-a-defined-template"></a>Body bez definované šablony

Pokud není automaticky otevíraná šablona definovaná jako šablona řetězce, šablona PropertyInfo nebo kombinace obou, pak použije výchozí nastavení. Pokud jsou jedinou přiřazenými vlastnostmi `title` a `description`, zobrazí se v automaticky otevřeném okně bílé pozadí, tlačítko Zavřít v pravém horním rohu. Na malých a středních obrazovkách se zobrazuje šipka dole. Výchozí nastavení se zobrazí v tabulce pro všechny vlastnosti kromě `title` a `description`. I když se vrátí k výchozímu nastavení, automaticky otevíraná šablona může pracovat programově. Například uživatelé můžou vypnout detekci hypertextových odkazů a výchozí nastavení se budou dál vztahovat na další vlastnosti.

Klikněte na body na mapě v CodePen. K dispozici je bod na mapě pro každou z následujících překryvných šablon: Šablona řetězce, šablona PropertyInfo a více šablon obsahu. Existují také tři body, které ukazují, jak šablony vykreslují pomocí výchozího nastavení.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na Azure Maps <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> pera na CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'></a>.
</iframe>

## <a name="reuse-popup-template"></a>Znovu použít místní šablonu

Podobně jako opakované použití místní nabídky můžete znovu použít automaticky otevírané šablony. Tento přístup je užitečný, pokud chcete najednou zobrazit jenom jednu automaticky otevíraná okna, a to pro více bodů. Po znovu použití automaticky otevírané šablony je počet elementů modelu DOM vytvořených aplikací snížen, což pak zlepšuje výkon aplikace. Následující příklad používá stejnou šablonu místní nabídky pro tři body. Pokud kliknete na kterýkoli z nich, zobrazí se automaticky otevírané okno s obsahem této funkce Point.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na Azure Maps <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> pera na CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'></a>.
</iframe>

## <a name="popup-events"></a>Překryvné události

Automaticky otevíraná okna lze otevřít, zavřít a přetáhnout. Automaticky otevíraná Třída poskytuje události, které vývojářům umožňují reagovat na tyto události. Následující ukázka zvýrazní, které události se aktivují, když uživatel otevře, zavře nebo přetáhne automaticky otevírané okno. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Překryvné události" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
V CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'></a>se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>události místního</a> pera.
</iframe>

## <a name="next-steps"></a>Další kroky

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
