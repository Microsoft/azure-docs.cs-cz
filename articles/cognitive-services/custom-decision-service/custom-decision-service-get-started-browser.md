---
title: Volání rozhraní API z prohlížeče – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Postup optimalizace webové stránky tím, že volání rozhraní API přímo z prohlížeče na Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 28ad4681242765bf2da9b1f13dc828e23cce1794
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707274"
---
# <a name="call-api-from-a-browser"></a>Volání rozhraní API z prohlížeče

Tento článek vám pomůže volat rozhraní API Azure Custom Decision Service přímo z prohlížeče.

Nezapomeňte nejdřív [zaregistrovat aplikaci](custom-decision-service-get-started-register.md).

Pusťme se do toho. Vaše aplikace je modelována tak, aby obsahovala Front-Page, která odkazuje na několik stránek článku. Na první stránce se používá Custom Decision Service k určení pořadí stránek článků. Vložte následující kód do hlavičky HTML přední stránky:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

`data` Argument obsahuje pořadí adres URL, které mají být vykresleny. Další informace najdete v referenčním [rozhraní API](custom-decision-service-api-reference.md).

Chcete-li zpracovat uživatele v horním článku, zavolejte na Front-Page následující kód:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Zde je argument `callback()`funkce. `data` Příklad implementace najdete v tomto [kurzu](custom-decision-service-tutorial-news.md#use-the-apis).

Nakonec musíte zadat rozhraní API sady akcí, které vrátí seznam článků (akcí), které se mají považovat za Custom Decision Service. Toto rozhraní API implementujte jako informační kanál RSS, jak je znázorněno zde:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Tady se v každém elementu nejvyšší `<item>` úrovně popisuje článek. `<link>` Je povinný a používá se jako ID akce Custom Decision Service. Pokud `<date>` máte více než 15 článků, zadejte (ve standardním formátu RSS). Použije se 15 nejnovějších článků. `<title>` Je volitelná a slouží k vytváření funkcí souvisejících s textem pro článek.

## <a name="next-steps"></a>Další kroky

* Podrobnější příklad najdete v [kurzu](custom-decision-service-tutorial-news.md) .
* Další informace o poskytovaných funkcích najdete v referenčním [rozhraní API](custom-decision-service-api-reference.md) .
