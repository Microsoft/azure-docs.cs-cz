---
title: Volání rozhraní API z aplikace – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Způsob volání rozhraní API Custom Decision Service z aplikace smartphone
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 08fbc1716d402c83bc2c33be82cba143c1737a55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707255"
---
# <a name="call-api-from-an-app"></a>Volání rozhraní API z aplikace

Z aplikace smartphone můžete volat rozhraní API pro Azure Custom Decision Service. Tento článek vysvětluje, jak začít s některými základními možnostmi.

Nezapomeňte nejdřív [zaregistrovat aplikaci](custom-decision-service-get-started-register.md).

Existují dvě volání rozhraní API, která z vaší aplikace smartphone napíšete Custom Decision Service: volání rozhraní API pro řazení pro získání seřazeného seznamu obsahu a volání rozhraní API pro odměnu. Tady jsou ukázková volání v [kudrlinkou](https://en.wikipedia.org/wiki/CURL).

Další informace najdete v referenčním [rozhraní API](custom-decision-service-api-reference.md).

Začněte voláním rozhraní API pro řazení. Vytvořte soubor `<request.json>`s ID sady akcí. Toto ID je název odpovídajícího informačního kanálu RSS nebo Atom, který jste zadali na portálu:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Mnoho sad akcí lze zadat následujícím způsobem:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Tento soubor JSON se pak pošle jako součást žádosti o klasifikaci:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

`<appId>` Tady je název vaší aplikace zaregistrované na portálu. Měli byste obdržet uspořádanou sadu položek obsahu, kterou můžete vykreslit ve své aplikaci. Ukázka návratového souboru vypadá takto:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

První část vráceného seznamu obsahuje seznam seřazených akcí určených jejich ID akcí. V článku je ID akce adresa URL. Celkový požadavek má také jedinečnou `<eventId>`hodnotu vytvořenou systémem.

Později můžete určit, jestli jste se na první položku obsahu v této události doznamenali, což je `<actionId3>`. Tuto `<eventId>` odměnu můžete následně nahlásit Custom Decision Service prostřednictvím rozhraní API pro odměnu, a to pomocí jiné žádosti, jako je například:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

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

## <a name="next-steps"></a>Další postup

* Podrobnější příklad najdete v [kurzu](custom-decision-service-tutorial-news.md) .
* Další informace o poskytovaných funkcích najdete v referenčním [rozhraní API](custom-decision-service-api-reference.md) .
