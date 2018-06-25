---
title: Aplikace Word zarovnání informace, pomocí rozhraní API služby Microsoft překladač Text | Microsoft Docs
description: Z rozhraní API služby Microsoft překladač Text přijímat informace zarovnání aplikace word.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 9d8284db61235284ec0d5a1594c34687c89560e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342773"
---
# <a name="how-to-receive-word-alignment-information"></a>Jak získat informace, zarovnání aplikace word

## <a name="receiving-word-alignment-information"></a>Příjem informací zarovnání aplikace word
Získat informace, zarovnání, použijte metodu přeložit a zahrnout parametr volitelný includeAlignment.

## <a name="alignment-information-format"></a>Informace o formátu zarovnání
Zarovnání se vrátí jako hodnotu řetězce formátu pro každou aplikaci word zdroje. Informace o jednotlivých slov je oddělené mezerou, včetně jazyků jiný oddělených mezerami (skripty) jako čínština:

[[SourceTextStartIndex]: [SourceTextEndIndex] – [TgtTextStartIndex]: [TgtTextEndIndex]] *

Příklad zarovnání řetězce: "0:0-7:10 1:2 – 11:20 3:4-0:3 3:4-4:6 5:5 – 21:21".

Jinými slovy dvojtečkou odděluje počáteční a koncová hodnota Čárka odděluje jazyky a místo odděluje slova. Jedno slovo může zarovnané s nula, jednu nebo více slov v dalších jazyků a může být nesouvislé zarovnaný slova. Pokud nejsou dostupné žádné informace zarovnání, bude prázdný element zarovnání. Metoda vrátí v takovém případě žádná chyba.

## <a name="restrictions"></a>Omezení
Zarovnání je vrácena pouze pro podmnožinu dvojice jazyků v tomto okamžiku:
* z angličtina do dalších jazyků;
* z jakéhokoli jiného jazyka na angličtinu, s výjimkou zjednodušené čínštiny, tradiční čínštině a lotyština na angličtinu
* z japonština, korejština nebo korejština japonské vám neobdrží zarovnání informace, pokud se věta specifických překlad. Je třeba specifických překlad "Toto je test", "I rádi je" a dalších – vysoká frekvence věty.

## <a name="example"></a>Příklad:

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
