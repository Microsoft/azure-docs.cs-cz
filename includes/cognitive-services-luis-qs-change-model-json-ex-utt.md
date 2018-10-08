---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: a1b0afce31d7202c38b049addf546350ff347719
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044137"
---
Soubor ukázkových promluv s názvem **utterances.json** má specifický formát. 

Pole `text` obsahuje text ukázkové promluvy. Pole `intentName` musí odpovídat názvu existujícího záměru v aplikaci LUIS. Pole `entityLabels` je povinné. Pokud nechcete označovat žádné entity, zadejte prázdné pole.

Není-li pole entityLabels prázdné, hodnoty `startCharIndex` a `endCharIndex` musí označovat entitu, na kterou odkazuje pole `entityName`. Index je založený na nule, což znamená, že číslo 6 v horní ukázce odkazuje na písmeno S ve slově Seattle, nikoliv na mezeru před ním. Pokud štítek začíná nebo končí na mezeře v textu, volání rozhraní API pro přidání promluv se nezdaří.

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