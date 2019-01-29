---
title: Projekt ve verzi Preview se adresy URL koncového bodu
titlesuffix: Azure Cognitive Services
description: Přehled koncového bodu adresy URL náhledu.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 8ed28ef356a4e884fca61b950c59506762fb8bdc
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099658"
---
# <a name="project-url-preview-endpoint"></a>Projekt ve verzi Preview se adresy URL koncového bodu

Rozhraní API ve verzi Preview adresa URL obsahuje jeden koncový bod.

## <a name="endpoint"></a>Koncový bod
Pokud chcete získat adresu URL ve verzi Preview, odesílejte požadavek na následující koncový bod. Použijte pro jiné specifikace hlaviček a parametrů adresy URL.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Parametry dotazu
|Name|Value|Type|Požaduje se|  
|----------|-----------|----------|--------------|  
|q|Adresa URL ve verzi preview|Řetězec |Ano|
|safeSearch|Neplatný obsah pro dospělé nebo nelegální obsah blokovaný s kódem chyby 400 a *isFamilyFriendly* příznak nevrátí. <p>Platný obsah pro dospělé níže je chování. Vrátí stavový kód 200 a *isFamilyFriendly* je příznak nastaven na hodnotu false.<ul><li>safeSearch=strict: Název, popis, adresu URL a image se nezobrazí.</li><li>bezpečné hledání = střední; Získejte název, adresu URL a popis, ale není popisný obraz.</li><li>bezpečné hledání = off; Získejte odpovědi objekty/všeho – název, URL, popis a obrázek.</li></ul> |Řetězec|Není nutné. </br> Výchozí hodnota je bezpečné hledání = strict.| 

## <a name="response-object"></a>Objekt odpovědi

Odpověď obsahuje hlavičky protokolu HTTP a objekt webovou stránku s atributy, jak je znázorněno v následujícím příkladu: `name`, `url`, `description`, `isFamilyFriendly`, a `primaryImageOfPage`.

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

## <a name="next-steps"></a>Další postup
- [Rychlý start pro jazyk C#](csharp.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro JavaScript](javascript.md)
- [Rychlý start pro Node](node-quickstart.md)
- [Rychlý start pro Python](python-quickstart.md)
