---
title: Koncový bod pro náhled adresy URL projektu
titlesuffix: Azure Cognitive Services
description: Souhrn koncového bodu adresy URL náhledu
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 3ef5ebd4ec88deac8c49430f36956d3711c8c535
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706941"
---
# <a name="project-url-preview-endpoint"></a>Koncový bod pro náhled adresy URL projektu

Rozhraní API pro náhled adresy URL zahrnuje jeden koncový bod.

## <a name="endpoint"></a>Koncový bod
Pokud chcete získat Náhled adresy URL, odešlete požadavek do následujícího koncového bodu. Pro jiné specifikace použijte záhlaví a parametry adresy URL.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Parametry dotazu
|Name|Value|type|Požadováno|  
|----------|-----------|----------|--------------|  
|q|Adresa URL náhledu|Řetězec |Ano|
|safeSearch|Neplatný obsah pro dospělé nebo kradený obsah je zablokován s kódem chyby 400 a příznak *isFamilyFriendly* se nevrátí. <p>Pro zákonný obsah pro dospělé níže je toto chování. Stavový kód vrátí 200 a příznak *isFamilyFriendly* je nastaven na hodnotu false.<ul><li>Bezpečné hledání = Strict: Název, popis, adresa URL a obrázek nebudou vráceny.</li><li>Bezpečné hledání = střední; Získejte název, adresu URL a popis, ale ne popisný obrázek.</li><li>Bezpečné hledání = off; Získá všechny objekty a elementy odpovědi – název, adresu URL, popis a obrázek.</li></ul> |Řetězec|Není nutné. </br> Výchozí hodnota je bezpečné hledání = Strict.| 

## <a name="response-object"></a>Objekt odpovědi

Odpověď obsahuje záhlaví protokolu HTTP a objekt webové stránky s atributy, jak je znázorněno v `name`následujícím `url`příkladu `description`: `isFamilyFriendly`,, `primaryImageOfPage`, a.

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>Další kroky
- [Rychlý start pro jazyk C#](csharp.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro JavaScript](javascript.md)
- [Rychlý start pro Node](node-quickstart.md)
- [Rychlý start pro Python](python-quickstart.md)
