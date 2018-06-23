---
title: Volání rozhraní API z aplikace - kognitivní služby Azure | Microsoft Docs
description: Jak začít se službou Azure vlastní rozhodnutí při volání rozhraní API z aplikace pro smartphone.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 2d02b0deaaa701dd0b4818638827c04e2c946558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343801"
---
# <a name="call-api-from-an-app"></a>Volání rozhraní API z aplikace

Volání rozhraní API služby Azure vlastní rozhodnutí z aplikace pro smartphone. Tento článek vysvětluje, jak začít pracovat s některé základní možnosti.

Nezapomeňte [registrace vaší aplikace](custom-decision-service-get-started-register.md), první.

Existují dvě volání rozhraní API můžete provádět z vaší aplikace pro smartphone vlastní rozhodnutí službě: volání rozhraní API řazení získat seřazený seznam obsahu a volat rozhraní API potřebu sestavy odměny. Tady je ukázka volání v [cURL](https://en.wikipedia.org/wiki/CURL).

Další informace najdete v tématu odkaz [rozhraní API](custom-decision-service-api-reference.md).

Začněte u volání rozhraní API řazení. Vytvoření souboru `<request.json>`, které akce nastavil ID. Toto ID je název odpovídající RSS nebo informační kanál Atom, zda jste zadali na portálu:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Mnoho sad akce můžete nastavit následujícím způsobem:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Tento soubor JSON odeslán jako součást požadavku hodnocení:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Zde `<appId>` je název vaší aplikace zaregistrovat na portálu. Měli byste obdržet uspořádanou sadu obsahu položky, které může vykreslit ve vaší aplikaci. Ukázka návratový vypadá takto:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

První část návratový má seznam seřazený akce, určí akcí jejich ID. Akce ID článku, je adresa URL. Celkový požadavek má také jedinečnou `<eventId>`, které byly vytvořeny v systému.

Později, můžete zadat, pokud jste zaznamenali, a klikněte na první obsahu položky z této události, která je `<actionId3>`. Pak můžete sestavu odměny v tomto `<eventId>` vlastní rozhodnutí Service pomocí rozhraní API potřebu, s jinou požadavků, jako:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

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

Tady se každý nejvyšší úrovně `<item>` element popisuje článek. `<link>` Je povinná a slouží jako akce ID službou vlastního rozhodnutí. Zadejte `<date>` (ve formátu standard RSS) Pokud jste více než 15 články. 15 nejnovější články používají. `<title>` Je volitelný a slouží k vytvoření funkce související s textu článku.

## <a name="next-steps"></a>Další postup

* Na spolupracovat [kurzu](custom-decision-service-tutorial-news.md) podrobnější příklad.
* Najdete odkaz na [rozhraní API](custom-decision-service-api-reference.md) Další informace o funkci zadané.