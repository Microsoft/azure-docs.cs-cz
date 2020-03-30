---
title: Přidání vyskakovacího vyskakovacího dne k bodu na mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak přidat vyskakovací okno k bodu pomocí sady Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055953"
---
# <a name="add-a-popup-to-the-map"></a>Přidání vyskakovacího vyskakovacího místa na mapu

Tento článek ukazuje, jak přidat vyskakovací okno k bodu na mapě.

## <a name="understand-the-code"></a>Vysvětlení kódu

Následující kód přidá bodový prvek, který má `name` a `description` vlastnosti, do mapy pomocí vrstvy symbolů. Instance [popup třídy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) je vytvořena, ale není zobrazena. Události myši jsou přidány do vrstvy symbolů, aby se spustilo otevření a zavření vyskakovacího okno. Když je symbol značky nahořen, `position` vlastnost místního okna je aktualizována `content` polohou značky a volba `name` `description` je aktualizována některým i HTML, který zalomí atribut a vlastnosti prvku bodu, který je odkazován. Vyskakovací okno se `open` pak zobrazí na mapě pomocí své funkce.

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

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí.

<br/>

<iframe height='500' scrolling='no' title='Přidání místního vyskakovacího okno pomocí Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Přidat vyskakovací okno pomocí Azure Maps</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Opětovné použití místního vyskakovacího okno s více body

Existují případy, ve kterých je nejlepším přístupem vytvořit jedno vyskakovací okno a znovu jej použít. Můžete mít například velký počet bodů a chcete zobrazit pouze jedno vyskakovací okno najednou. Opětovným použitím vyskakovacího okno, počet prvků DOM vytvořených aplikací je výrazně snížena, což může poskytnout lepší výkon. Následující ukázka vytvoří 3bodové prvky. Pokud kliknete na některý z nich, zobrazí se vyskakovací okno s obsahem pro tento bodový prvek.

<br/>

<iframe height='500' scrolling='no' title='Opakované použití místních přiskupovacích možností s více piny' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero opakované použití místní<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>hospoup s více piny</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Přizpůsobení místního přemešku

Ve výchozím nastavení má vyskakovací okno bílé pozadí, šipku ukazatele v dolní části a tlačítko zavřít v pravém horním rohu. Následující ukázka změní barvu pozadí `fillColor` na černou pomocí možnosti vyskakovacího okno. Tlačítko zavřít se odstraní `CloseButton` nastavením možnosti false. Obsah HTML vyskakovacího okno používá polstrované 10 pixelů od okrajů vyskakovacího okno. Text je bílý, takže se pěkně objeví na černém pozadí.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přizpůsobené místní okno" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>pero přizpůsobené vyskakovací okno</a> podle Map Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Přidání místních šablon na mapu

Šablony automaticky otevíraného přístupu usnadňují vytváření rozložení řízených daty pro vyskakovací okno. Níže uvedené části ukazují použití různých šablon vyskakovacích prostor ů ke generování formátovaného obsahu pomocí vlastností funkcí.

> [!NOTE]
> Ve výchozím nastavení bude veškerý obsah vykreslený použít popup šablony bude v izolovaném prostoru uvnitř iframe jako bezpečnostní prvek. Existují však omezení:
>
> - Všechny skripty, formuláře, zámek ukazatele a funkce špičkové navigace jsou zakázány. Odkazy se mohou po kliknutí otevřít na nové kartě. 
> - Starší prohlížeče, které nepodporují `srcdoc` parametr v rámci iframe, budou omezeny na vykreslení malého množství obsahu.
> 
> Pokud důvěřujete datům načteným do vyskakovacích formulářů a chcete, aby tyto skripty načtené do `sandboxContent` vyskakovacích formulářů měly přístup k vaší aplikaci, můžete to zakázat nastavením možnosti vyskakovacích šablon na hodnotu false. 

### <a name="string-template"></a>Šablona řetězce

Šablona String nahradí zástupné symboly hodnotami vlastností prvku. Vlastnosti prvku nemusí být přiřazena hodnota typu String. Například `value1` obsahuje celé číslo. Tyto hodnoty jsou pak předány `popupTemplate`content vlastnost . 

Tato `numberFormat` možnost určuje formát čísla, které se má zobrazit. Pokud `numberFormat` není zadán, bude kód používat formát data vyskakovacích šablon. Tato `numberFormat` možnost formátuje čísla pomocí funkce [Number.toLocaleString.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) Chcete-li formátovat velká `numberFormat` čísla, zvažte použití možnosti s funkcemi z [formátu NumberFormat.format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Například fragment kódu níže používá `maximumFractionDigits` k omezení počtu číslic zlomku na dvě.

> [!Note]
> Existuje pouze jeden způsob, ve kterém string šablony můžete vykreslit obrázky. Nejprve musí mít šablona String značku obrázku. Hodnota předávaná značce obrázku by měla být adresou URL obrázku. Potom řetězec šablony musí `isImage` mít nastavena `HyperLinkFormatOptions`na hodnotu true v . Tato `isImage` možnost určuje, že hypertextový odkaz je pro bitovou kopii a hypertextový odkaz bude načten do značky obrazu. Po klepnutí na hypertextový odkaz se obraz otevře.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>Šablona PropertyInfo

Šablona PropertyInfo zobrazuje dostupné vlastnosti prvku. Tato `label` možnost určuje text, který se má uživateli zobrazit. Pokud `label` není zadán, zobrazí se hypertextový odkaz. A pokud je hypertextový odkaz obrázek, zobrazí se hodnota přiřazená značce alt. Určuje `dateFormat` formát data a pokud není zadán formát data, bude datum vykresleno jako řetězec. Možnost `hyperlinkFormat` vykreslí klikací odkazy, `email` podobně, možnost může být použita k vykreslení klikací e-mailové adresy.

Před PropertyInfo šablony zobrazí vlastnosti koncového uživatele, rekurzivně zkontroluje, zda vlastnosti jsou skutečně definovány pro tuto funkci. Ignoruje také zobrazení vlastností stylu a názvu. Například se `color`nezobrazí , `size` `anchor`, `strokeOpacity`, `visibility`a . Takže po dokončení kontroly cesty vlastností v back-endu, propertyinfo šablona zobrazí obsah ve formátu tabulky.

```javascript
var templateOptions = {
  content: [
    {
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
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Více šablon obsahu

Funkce může také zobrazit obsah pomocí kombinace string šablony a PropertyInfo šablony. V tomto případě šablona String vykreslí hodnoty zástupných symbolů na bílém pozadí.  A propertyinfo šablony vykreslí obrázek s plnou šířkou uvnitř tabulky. Vlastnosti v této ukázce jsou podobné vlastnostem, které jsme vysvětlili v předchozích ukázkách.

```javascript
var templateOptions = {
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
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Body bez definované šablony

Pokud šablona Popup není definována jako šablona String, šablona PropertyInfo nebo kombinace obou, použije výchozí nastavení. Když `title` a `description` jsou pouze přiřazené vlastnosti, vyskakovací šablona zobrazuje bílé pozadí, tlačítko zavřít v pravém horním rohu. A na malých a středních obrazovkách se v dolní části zobrazí šipka. Výchozí nastavení se zobrazí uvnitř tabulky pro `title` všechny `description`vlastnosti kromě a . I při pádu zpět na výchozí nastavení, popup šablony lze stále manipulovat programově. Uživatelé mohou například vypnout zjišťování hypertextových odkazů a výchozí nastavení by se stále vztahovalo na jiné vlastnosti.

Klikněte na body na mapě v CodePen. Na mapě je bod pro každou z následujících šablon automaticky otevíraných míst: Šablona řetězce, Šablona PropertyInfo a Šablona více obsahu. Existují také tři body, které ukazují, jak se šablony vykreslují pomocí výchozího nastavení.

<br/>

<iframe height='500' scrolling='no' title='Místní šablony' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na Pero <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Opakované použití místní šablony

Podobně jako při opakovaném použití vyskakovacího pole můžete znovu použít šablony automaticky otevíraných vyskakovacích hal. Tento přístup je užitečný, pokud chcete zobrazit pouze jednu šablonu automaticky otevírané okno najednou pro více bodů. Opětovným použitím šablony automaticky otevíraných vyskakovacích zařízení se sníží počet prvků DOM vytvořených aplikací, což pak zlepšuje výkon aplikace. Následující ukázka používá stejnou šablonu pro vyskakovací okno pro tři body. Pokud kliknete na některý z nich, zobrazí se vyskakovací okno s obsahem pro tento bodový prvek.

<br/>

<iframe height='500' scrolling='no' title='Znovu použítPopupŠablona' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Místní události

Automaticky otevíraná líací se mohou otevírat, zavírat a přetahovat. Třída popup poskytuje události, které vývojářům pomáhají reagovat na tyto události. Následující ukázka zvýrazní, které události se ozvou, když uživatel otevře, zavře nebo přetáhne vyskakovací okno. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Místní události" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na události Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>Popup</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [Možnosti místního nabídky](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [Místní šablona](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

Podívejte se na následující skvělé články pro úplné ukázky kódu:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidání značky HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](map-add-shape.md)
