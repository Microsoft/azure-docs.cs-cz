---
title: Volání rozhraní API z prohlížeče - kognitivní služeb Azure | Microsoft Docs
description: Jak začít pracovat s Azure vlastní rozhodnutí služby za účelem optimalizace webové stránky tak, že volání rozhraní API přímo z prohlížeče.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins,marcozo,alekh
ms.openlocfilehash: 10236c9d8f70d9b90a896464b4f86a847ee904c2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343486"
---
# <a name="call-api-from-a-browser"></a>Volání rozhraní API z prohlížeče

Tento článek vám umožňuje provádět volání do rozhraní API služby Azure vlastní rozhodnutí přímo z prohlížeče.

Nezapomeňte [registrace vaší aplikace](custom-decision-service-get-started-register.md), první.

Můžeme začít. Aplikace je modelovaná jako s titulní stránky, která odkazuje na více stránkách článku. Titulní stránky používá vlastní rozhodnutí službu k určení pořadí jeho stránky článku. Vložte následující kód do head HTML front stránky:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

`data` Argument obsahuje pořadí adresy URL k vykreslení. Další informace najdete v tématu odkaz [rozhraní API](custom-decision-service-api-reference.md).

Pro zpracování a uživatel kliknutím na začátek článku, zavolejte na stránce front následující kód:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Zde `data` je argument `callback()` funkce. Příklad implementace najdete v tomto [kurzu](custom-decision-service-tutorial-news.md#use-the-apis).

Nakonec je třeba zadat API nastavit akce, který vrátí seznam článků (akce), aby byla považována za službou vlastního rozhodnutí. Implementujte toto rozhraní API jako informačního kanálu RSS, jak je vidět tady:

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

Tady se každý nejvyšší úrovně `<item>` element popisuje článek. `<link>` Je povinná a slouží jako akce ID službou vlastního rozhodnutí. Zadejte `<date>` (ve formátu standard RSS) Pokud máte více než 15 články. 15 nejnovější články používají. `<title>` Je volitelný a slouží k vytvoření funkce související s textu článku.

## <a name="next-steps"></a>Další postup

* Na spolupracovat [kurzu](custom-decision-service-tutorial-news.md) podrobnější příklad.
* Najdete odkaz na [rozhraní API](custom-decision-service-api-reference.md) Další informace o funkci zadané.