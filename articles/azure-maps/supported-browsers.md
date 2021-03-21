---
title: Prohlížeče podporované v sadě web SDK | Mapy Microsoft Azure
description: Zjistěte, jak ověřit, jestli Azure Maps webová sada SDK podporuje prohlížeč. Zobrazí seznam podporovaných prohlížečů. Naučte se používat mapy služby se staršími prohlížeči.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6321b96cb3db570102f138dcfd949d9c32daedbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384465"
---
# <a name="web-sdk-supported-browsers"></a>Podporované prohlížeče pro sadu Web SDK

Sada Azure Maps Web SDK poskytuje pomocnou funkci nazvanou [Atlas. podporuje](/javascript/api/azure-maps-control/atlas#issupported-boolean-). Tato funkce zjistí, zda má webový prohlížeč minimální sadu funkcí WebGL potřebných pro podporu načítání a vykreslování mapového ovládacího prvku. Tady je příklad použití funkce:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Desktop

Sada Azure Maps Web SDK podporuje následující desktopové prohlížeče:

- Microsoft Edge (aktuální a předchozí verze)
- Google Chrome (aktuální a předchozí verze)
- Mozilla Firefox (aktuální a předchozí verze)
- Apple Safari (macOS X) (aktuální a předchozí verze)

Viz také [cílení na starší verze prohlížečů](#Target-Legacy-Browsers) dále v tomto článku.

## <a name="mobile"></a>Mobilní

Sada Azure Maps Web SDK podporuje následující mobilní prohlížeče:

- Android
  - Aktuální verze Chrome v Androidu 6,0 a novějších verzích
  - Webové zobrazení Chrome v Androidu 6,0 a novějším
- iOS
  - Mobilní aplikace Safari v aktuální a předchozí hlavní verzi iOS
  - UIWebView a WKWebView v aktuální a předchozí hlavní verzi iOS
  - Aktuální verze Chrome pro iOS

> [!TIP]
> Pokud vkládáte mapu do mobilní aplikace pomocí ovládacího prvku WebView, možná místo odkazování na verzi sady SDK, která je hostovaná v Azure Content Delivery Network, doporučujeme použít [balíček npm Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) . Tento přístup zkracuje dobu načítání, protože sada SDK již je na zařízení uživatele a není nutné ji stáhnout za běhu.

## <a name="nodejs"></a>Node.js

V Node.js jsou podporovány také následující moduly web SDK:

- Modul služby (npm[dokumentace](how-to-use-services-module.md)–  |  [modul](https://www.npmjs.com/package/azure-maps-rest))

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Cílové starší verze prohlížečů

Můžete chtít cílit na starší prohlížeče, které nepodporují WebGL, nebo které mají jenom omezenou podporu. V takových případech doporučujeme, abyste používali Azure Maps služby společně s open source mapovým ovládacím prvkem, jako je například [leták](https://leafletjs.com/). Tady je příklad, který využívá open source [modul plug-in Azure Maps leták](https://github.com/azure-samples/azure-maps-leaflet).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + leták" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + leták</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Další ukázky kódu používající Azure Maps v letáku najdete [tady](https://azuremapscodesamples.azurewebsites.net/?search=leaflet).

[Zde](open-source-projects.md#third-part-map-control-plugins) je několik oblíbených open source mapových ovládacích prvků, které Azure Maps tým vytvořil modul plug-in pro.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Maps Web SDK:

[Mapový ovládací prvek](how-to-use-map-control.md)

[Modul služeb](how-to-use-services-module.md)
