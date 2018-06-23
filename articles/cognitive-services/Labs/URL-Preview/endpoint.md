---
title: Projekt koncového bodu adresy URL náhledu - kognitivní služby Microsoft | Microsoft Docs
description: Souhrn Preview adresu URL koncového bodu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: ddd53aa49db01d7a6db397eb285d0854edc59388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343236"
---
# <a name="project-url-preview-endpoint"></a>Koncového bodu náhledu adresa URL projektu

Rozhraní API Preview adresa URL obsahuje jeden koncový bod.

## <a name="endpoint"></a>Koncový bod
Chcete-li získat adresu URL náhledu, odeslat požadavek na následující koncový bod. Použijte pro jiné specifikace hlaviček a parametrů adresy URL.

GET:
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

````

### <a name="query-parameters"></a>Parametry dotazu
|Název|Hodnota|Typ|Požaduje se|  
|----------|-----------|----------|--------------|  
|OTÁZKY|Adresa URL pro zobrazení náhledu|Řetězec |Ano|
|bezpečné hledání|Neplatný obsah pro dospělé nebo nelegální obsahu, je blokovaný s kódem chyby 400 a *isFamilyFriendly* příznak nevrátí. <p>Pro právní obsah pro dospělé je nižší než chování. Stavový kód vrátí hodnotu 200 a *isFamilyFriendly* příznak nastaven na hodnotu false.<ul><li>bezpečné hledání = strict: název, popis, adresa URL, bitové kopie nejsou k dispozici.</li><li>bezpečné hledání = mírný; Získáte název, URL a popis ale není popisný bitové kopie.</li><li>bezpečné hledání = vypnuto; Získáte všechny odpovědi objekty nebo prvky – název, URL, popis a bitové kopie.</li></ul> |Řetězec|Není požadováno. </br> Výchozí nastavení je bezpečné hledání = strict.| 

## <a name="response-object"></a>Objekt odpovědi

Odpověď obsahuje hlavičky protokolu HTTP a objekt webová stránka s atributy, jak je znázorněno v následujícím příkladu: `name`, `url`, `description`, `isFamilyFriendly`, a `primaryImageOfPage`.

````
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

````

## <a name="next-steps"></a>Další postup
- [Rychlý start C#](csharp.md)
- [Rychlý start Java](java-quickstart.md)
- [Rychlý start JavaScript](javascript.md)
- [Rychlé spuštění uzlu](node-quickstart.md)
- [Rychlý start Python](python-quickstart.md)
