---
title: Volání rozhraní API z prohlížeče – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Jak optimalizovat webovou stránku pomocí provádí volání rozhraní API pro Custom Decision Service přímo z prohlížeče.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.openlocfilehash: 2b356e2f0fe9235d49dffa7417cd3894059f9caf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867558"
---
# <a name="call-api-from-a-browser"></a>Volání rozhraní API z prohlížeče

Tento článek pomáhá provádět volání do rozhraní API služeb Azure vlastního rozhodnutí přímo z prohlížeče.

Nezapomeňte [registrace vaší aplikace](custom-decision-service-get-started-register.md), první.

Pusťme se do toho. Vaše aplikace je modelovaná jako s front-stránka, která odkazuje na několik stránky článku. Na přední stránce pomocí služby Custom Decision Service určuje řazení její stránky článku. Vložte následující kód do začátku na přední stránce HTML:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

`data` Argument obsahuje pořadí adres URL pro vykreslení. Další informace najdete v tématu referenční [API](custom-decision-service-api-reference.md).

Pro zpracování kliknutí uživatele na začátek článku, zavolejte na přední stránce následující kód:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Tady `data` je argumentem `callback()` funkce. Příklad implementace najdete v tomto [kurzu](custom-decision-service-tutorial-news.md#use-the-apis).

Nakonec budete muset poskytnout API nastavit akci, která vrátí seznam článků (akce), aby bylo považováno za pomocí služby Custom Decision Service. Implementace tohoto rozhraní API jako informačního kanálu RSS, jak je znázorněno zde:

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

Tady se každý nejvyšší úrovně `<item>` element popisuje článek. `<link>` Je povinný a slouží jako ID akce pomocí služby Custom Decision Service. Zadejte `<date>` (ve standardním formátu RSS) Pokud máte více než 15 články. 15 nejnovější články používají. `<title>` Je volitelný a slouží k vytvoření funkce související s textu článku.

## <a name="next-steps"></a>Další postup

* Seznámení se základními [kurzu](custom-decision-service-tutorial-news.md) podrobnější příklad.
* Referenční příručce [API](custom-decision-service-api-reference.md) Další informace o funkcích, které zadaná.
