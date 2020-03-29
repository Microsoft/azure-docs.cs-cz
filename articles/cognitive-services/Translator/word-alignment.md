---
title: Zarovnání slov – překladač textového rozhraní API
titleSuffix: Azure Cognitive Services
description: Chcete-li získat informace o zarovnání, použijte metodu Translate a zahrňte volitelný parametr includeAlignment.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: dd4ff1e39c062910f4627973c801dc3c51f345e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837230"
---
# <a name="how-to-receive-word-alignment-information"></a>Jak získat informace o zarovnání slov

## <a name="receiving-word-alignment-information"></a>Příjem informací o zarovnání slov
Chcete-li získat informace o zarovnání, použijte metodu Translate a zahrňte volitelný parametr includeAlignment.

## <a name="alignment-information-format"></a>Formát informací o zarovnání
Zarovnání je vrácena jako řetězcová hodnota následujícího formátu pro každé slovo zdroje. Informace pro každé slovo jsou odděleny mezerou, včetně jazyků (skriptů) neoddělených mezerami, jako je čínština:

[[SourceTextStartIndex]:[SourceTextEndIndex]-[TgtTextStartIndex]:[TgtTextEndIndex]] *

Příklad zarovnání řetězce: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Jinými slovy dvojtečka odděluje počáteční a koncový index, pomlčka odděluje jazyky a prostor odděluje slova. Jedno slovo může být zarovnáno s nulou, jedním nebo více slovy v jiném jazyce a zarovnaná slova mohou být nesouvislá. Pokud nejsou k dispozici žádné informace o zarovnání, prvek Zarovnání bude prázdný. Metoda vrátí žádnou chybu v tomto případě.

## <a name="restrictions"></a>Omezení
Zarovnání je vrácena pouze pro podmnožinu páry jazyků v tomto okamžiku:
* z angličtiny do jiného jazyka;
* z jakéhokoli jiného jazyka do angličtiny s výjimkou zjednodušené čínštiny, tradiční čínštiny a lotyštiny do angličtiny
* z japonštiny do korejštiny nebo z korejštiny do japonštiny Neobdržíte informace o zarovnání, pokud je věta přeložena do konzerv. Příklad konzervovaného překladu je "Toto je test", "Miluji tě" a další vysokofrekvenční věty.

## <a name="example"></a>Příklad

Příklad JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
