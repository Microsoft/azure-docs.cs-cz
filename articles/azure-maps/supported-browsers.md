---
title: Webové sady SDK podporované prohlížeče | Mapy Microsoft Azure
description: V tomto článku se dozvíte o podporovaných prohlížečích pro sadu Microsoft Azure Maps Web SDK a o tom, jak zkontrolovat, jestli je prohlížeč podporovaný.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988783"
---
# <a name="web-sdk-supported-browsers"></a>Podporované prohlížeče pro sadu Web SDK

Webová sada Azure Maps Web SDK poskytuje pomocnou funkci nazvanou [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Tato funkce zjistí, zda má webový prohlížeč minimální sadu funkcí WebGL potřebných pro podporu načítání a vykreslování ovládacího prvku mapy. Zde je příklad použití funkce:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Aplikace klasické pracovní plochy

Webová sada Azure Maps Web SDK podporuje následující prohlížeče pro stolní počítače:

- Microsoft Edge (aktuální a předchozí verze)
- Google Chrome (aktuální a předchozí verze)
- Mozilla Firefox (aktuální a předchozí verze)
- Apple Safari (Mac OS X) (aktuální a předchozí verze)

Viz také [Cílstarší prohlížeče](#Target-Legacy-Browsers) dále v tomto článku.

## <a name="mobile"></a>Mobilní zařízení

Webová sada Azure Maps Web SDK podporuje následující mobilní prohlížeče:

- Android
  - Aktuální verze Chromu v systému Android 6.0 a novějším
  - Chrome WebView v systému Android 6.0 a novějších
- iOS
  - Mobilní Safari na aktuální a předchozí hlavní verzi iOS
  - UIWebView a WKWebView na aktuální a předchozí hlavní verzi iOS
  - Aktuální verze Chromu pro iOS

> [!TIP]
> Pokud vkládáte mapu do mobilní aplikace pomocí ovládacího prvku WebView, můžete raději použít [balíček npm sady Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) namísto odkazování na verzi sady SDK, která je hostovaná v síti Doručování obsahu Azure. Tento přístup zkracuje dobu načítání, protože sada SDK je již na zařízení uživatele a není nutné je stahovat za běhu.

## <a name="nodejs"></a>Node.js

V souboru Node.js jsou podporovány také následující moduly sady Web SDK:

- Modul služeb ([dokumentační](how-to-use-services-module.md) | [modul npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Cílení na starší prohlížeče

Možná budete chtít cílit na starší prohlížeče, které nepodporují WebGL nebo které pro něj mají pouze omezenou podporu. V takových případech doporučujeme používat služby Azure Maps společně s ovládacím prvkem mapy s otevřeným zdrojovým kódem, jako je [Leták](https://leafletjs.com/). Tady je příklad:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapy Azure + leták" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na Pero Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps + <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Leták</a> od Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Další informace o webové sdk Azure Maps:

> [!div class="nextstepaction"]
> [Mapový ovládací prvek](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Modul služeb](how-to-use-services-module.md)
