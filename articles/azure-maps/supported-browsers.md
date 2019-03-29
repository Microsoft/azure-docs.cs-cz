---
title: Sada Web SDK podporovaných prohlížečích – Azure Maps | Dokumentace Microsoftu
description: Další informace o podporovaných prohlížečích pro sadu SDK webové aplikace Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: bc876fbf0eb15f887d57d4ddcca2301ef7233afa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577335"
---
# <a name="web-sdk-supported-browsers"></a>Sada Web SDK podporované prohlížeče

Azure Maps Web SDK poskytuje pomocnou funkci [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) ke zjištění, zda má webový prohlížeč minimální požadované WebGL funkcí pro podporu načítání a vykreslování mapového ovládacího prvku. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Plocha

Sada SDK webové mapy Azure podporuje následující stolních počítačů.

- Aktuální a předchozí verze Microsoft Edge 
- Aktuální a předchozí verzi Chrome 
- Aktuální a předchozí verzi Firefoxu 
- Aktuální a předchozí verze Safari (Mac OS X) 

Viz také [cílit na starší verzi prohlížeče](#Target-Legacy-Browsers).

## <a name="mobile"></a>Mobilní

Sada SDK webové mapy Azure podporuje následující mobilní prohlížeče.

-  Android
    * Aktuální verzi Chrome na Android 6.0 +
    * Chrome WebView na Android 6.0 +
- iOS
    * Mobilní Safari na aktuální a předchozí hlavní verzi iOS
    * UIWebView a WKWebView na aktuální a předchozí hlavní verzi iOS
    * Aktuální verzi Chrome pro iOS

> [!TIP]
> Pokud vkládáte obsah mapy v mobilní aplikaci pomocí ovládacího prvku WebView, možná dáte přednost použití [balíčku npm sady Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) namísto odkazování CDN hostované verzi sady SDK. Tím se sníží doba načítání a sady SDK se již být na zařízení uživatele není třeba stahovat za běhu.

## <a name="nodejs"></a>Node.js

Následující moduly Web SDK jsou také podporovány v Node.js:

- Modul služby ([dokumentaci](how-to-use-services-module.md) | [modul npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Starší verze prohlížeče

Pokud je potřeba cílit na starší prohlížeče, které nemusí podporovat nebo mají omezenou podporu pro WebGL, se doporučuje použít mapy služeb Azure v kombinaci s ovládací prvek mapa open source, jako [informace](https://leafletjs.com/). 


<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + informace" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobrazit pera <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + informace</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další postup

Další informace o Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Mapový ovládací prvek](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Modul služby](how-to-use-services-module.md)
