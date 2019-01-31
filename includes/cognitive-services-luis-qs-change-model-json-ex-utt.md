---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 46b588d6977aa6ffeb9d473e6bfe149cde7147ba
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478744"
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
