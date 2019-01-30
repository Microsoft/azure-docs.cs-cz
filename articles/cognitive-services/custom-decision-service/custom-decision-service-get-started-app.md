---
title: Volání rozhraní API z aplikace – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Jak volat rozhraní API pro vlastní rozhodnutí služeb z aplikace pro smartphone.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ms.openlocfilehash: fed45ec10eca26c58bc3b3c70991c1f7f468e393
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219409"
---
# <a name="call-api-from-an-app"></a>Volání rozhraní API z aplikace

Volání rozhraní API služeb Azure vlastního rozhodnutí z aplikace pro smartphone. Tento článek vysvětluje, jak začít pracovat s některých základních možností.

Nezapomeňte [registrace vaší aplikace](custom-decision-service-get-started-register.md), první.

Existují dvě volání rozhraní API, provádět z aplikace pro smartphone Custom Decision Service: volání rozhraní API pro hodnocení získat seřazený seznam obsahu a volání rozhraní API Reward hlášení odměny. Tady je ukázka volání v [cURL](https://en.wikipedia.org/wiki/CURL).

Další informace najdete v tématu referenční [API](custom-decision-service-api-reference.md).

Začněte s volání rozhraní API pro hodnocení. Vytvoření souboru `<request.json>`, který má akci nastavit ID. Toto ID je název odpovídající RSS nebo Atom feed, který jste zadali v portálu:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Mnoho sad akce lze upravit následujícím způsobem:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Tento soubor JSON je odeslaný jako součást požadavku pořadí:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Tady `<appId>` je název vaší aplikace zaregistrovat na portálu. Měli byste obdržet uspořádanou sadu obsahu položky, které můžete zobrazit v aplikaci. Ukázka návratový vypadá takto:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

První část vrácená má seznam seřazený akce určená jejich ID akce. ID akce pro příslušný článek, je adresa URL. Celkový požadavek má také jedinečnou `<eventId>`, které byly vytvořeny v systému.

Později, můžete zadat, pokud jste sledovali klikněte na první položku obsahu z této události, která je `<actionId3>`. Může vytvářet zprávy odměny v tomto `<eventId>` Custom Decision Service přes rozhraní API odměnu, prováděním jiné žádostí o jako například:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

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

Tady se každý nejvyšší úrovně `<item>` element popisuje článek. `<link>` Je povinný a slouží jako ID akce pomocí služby Custom Decision Service. Zadejte `<date>` (ve standardním formátu RSS) Pokud jste víc než 15 články. 15 nejnovější články používají. `<title>` Je volitelný a slouží k vytvoření funkce související s textu článku.

## <a name="next-steps"></a>Další postup

* Seznámení se základními [kurzu](custom-decision-service-tutorial-news.md) podrobnější příklad.
* Referenční příručce [API](custom-decision-service-api-reference.md) Další informace o funkcích, které zadaná.
