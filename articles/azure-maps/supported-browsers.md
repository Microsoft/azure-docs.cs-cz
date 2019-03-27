---
title: Podporované webové prohlížeče - Azure Maps | Dokumentace Microsoftu
description: Další informace o podporovaných webových prohlížečů pro sadu SDK webové aplikace Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 2678fa7c9290c7ec0a27288e7739fde4bb0870fd
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501268"
---
# <a name="supported-web-browsers"></a>Podporované webové prohlížeče

Azure Maps Web SDK poskytuje pomocnou funkci [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) ke zjištění, zda má webový prohlížeč minimální požadované WebGL funkcí pro podporu načítání a vykreslování mapového ovládacího prvku. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Add your map code here.
}
```

Sada SDK webové mapy Azure podporuje následující webové prohlížeče.

## <a name="desktop"></a>Plocha

- Aktuální a předchozí verze Microsoft Edge 
- Aktuální a předchozí verzi Chrome 
- Aktuální a předchozí verzi Firefoxu 
- Aktuální a předchozí verze Safari (Mac OS X) 

Viz také [cílit na starší verzi prohlížeče](#Target-Legacy-Browsers).

## <a name="mobile"></a>Mobilní

-  Android
    * Aktuální verzi Chrome na Android 6.0 +
    * Chrome WebView na Android 6.0 +
- iOS
    * Mobilní Safari na aktuální a předchozí hlavní verzi iOS
    * UIWebView a WKWebView na aktuální a předchozí hlavní verzi iOS
    * Aktuální verzi Chrome pro iOS

> [!TIP]
> Pokud vkládáte obsah mapy v mobilní aplikaci zobrazení ovládacího prvku WebView, můžete zjistit pomocí [balíčku npm sady Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) vhodnější odkazující na hostované CDN verzi sady SDK. Tím se sníží doba načítání a sady SDK se již být na zařízení uživatele není třeba stahovat za běhu.

## <a name="nodejs"></a>Node.js

Následující moduly Web SDK jsou také podporovány v Node.js:

- Modul služby ([dokumentaci](how-to-use-services-module.md) | [modul npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Starší verze prohlížeče

Pokud je potřeba cílit na starší webové prohlížeče, které nemusí podporovat nebo mají omezenou podporu pro WebGL, se doporučuje použít mapy služeb Azure v kombinaci s ovládací prvek mapa open source, jako [informace](https://leafletjs.com/). 

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + informace" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobrazit pera <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + informace</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>