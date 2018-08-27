---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 419f15901b665b43b850922f77bd32d7aac8d3a2
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42920640"
---
Ukázkový soubor v projevy, **utterances.json**, následuje specifickém formátu. 

`text` Pole obsahuje text utterance příklad. `intentName` Pole musí odpovídat názvu existující záměr v aplikaci LUIS. Pole `entityLabels` je povinné. Pokud nechcete, aby jako popisek žádné entity, zadejte prázdné pole.

Pokud pole entityLabels není prázdný, `startCharIndex` a `endCharIndex` potřebovat k označení entity podle `entityName` pole. Index je založený na nule, což znamená, že 6 v horním příkladu odkazuje na "S" Seattle a ne podle mezery před velké S. Pokud začínat ani končit popisek na místa v textu, přidat projevy volání rozhraní API se nezdaří.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```