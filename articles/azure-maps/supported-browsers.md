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
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62738384"
---
# <a name="web-sdk-supported-browsers"></a>Podporované prohlížeče pro sadu Web SDK

Azure Maps Web SDK poskytuje pomocnou funkci volat [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Tato funkce zjistí, zda má webový prohlížeč minimální sada funkcí WebGL potřeba pro podporu načítání a vykreslování mapového ovládacího prvku. Tady je příklad toho, jak použít funkci:

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Plocha

Sada SDK webové mapy Azure podporuje následující počítačových prohlížečů:

- Microsoft Edge (aktuální a předchozí verze)
- Google Chrome (aktuální a předchozí verze)
- Mozilla Firefox (aktuální a předchozí verze)
- Apple Safari (Mac OS X) (aktuální a předchozí verze)

Viz také [cílit na starší verzi prohlížeče](#Target-Legacy-Browsers) dále v tomto článku.

## <a name="mobile"></a>Mobilní

Sada SDK webové mapy Azure podporuje následující mobilní prohlížeče:

- Android
  - Aktuální verzi Chrome na Android 6.0 nebo novější
  - Chrome WebView na Android 6.0 nebo novější
- iOS
  - Mobilní Safari na aktuální a předchozí hlavní verzi iOS
  - UIWebView a WKWebView na aktuální a předchozí hlavní verzi iOS
  - Aktuální verzi Chrome pro iOS

> [!TIP]
> Pokud vkládání využíváte mapu v mobilní aplikaci s použitím ovládacího prvku WebView, budete pravděpodobně chtít použít [balíčku npm sady Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) namísto odkazování na verzi sady SDK, která je hostována v Azure Content Delivery Síť. Tento přístup snižuje dobu načítání, protože sada SDK je již být na zařízení uživatele a není třeba stahovat v době běhu.

## <a name="nodejs"></a>Node.js

Následující moduly Web SDK jsou také podporovány v Node.js:

- Modul služby ([dokumentaci](how-to-use-services-module.md) | [modul npm](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Starší verze prohlížeče

Můžete cílit na starší prohlížeče, které nepodporují WebGL nebo, který má omezenou podporu. V takovém případě doporučujeme používat služby Azure Maps společně open source mapový ovládací prvek jako [informace](https://leafletjs.com/). Tady je příklad:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + informace" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobrazit pera <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + informace</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další postup

Další informace o Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Mapový ovládací prvek](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Modul služby](how-to-use-services-module.md)
