---
title: Zarovnání slov – Překladatel
titleSuffix: Azure Cognitive Services
description: Chcete-li získat informace o zarovnání, použijte metodu přeložit a vložte volitelný parametr includeAlignment.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 8368ca0ca4c3f2f0ab3cb88a5d54295d71451636
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98898015"
---
# <a name="how-to-receive-word-alignment-information"></a>Jak přijímat informace o zarovnání slov

## <a name="receiving-word-alignment-information"></a>Příjem informací o zarovnání slov
Chcete-li získat informace o zarovnání, použijte metodu přeložit a vložte volitelný parametr includeAlignment.

## <a name="alignment-information-format"></a>Formát informací o zarovnání
Zarovnání je vráceno jako řetězcová hodnota v následujícím formátu pro každé slovo zdroje. Informace pro každé slovo jsou oddělené mezerou, včetně jazyků neoddělených mezerami (skripty), jako je například čínština:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Příklad řetězce zarovnání: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Jinými slovy, dvojtečka odděluje počáteční a koncový index, pomlčku odděluje jazyky a mezeru odděluje slova. Jedno slovo může být zarovnáno s nula, jedním nebo více slovy v jiném jazyce a zarovnaná slova mohou být nesouvislá. Pokud nejsou k dispozici žádné informace o zarovnání, element zarovnání bude prázdný. Metoda v takovém případě nevrátí žádnou chybu.

## <a name="restrictions"></a>Omezení
Zarovnání se vrátí jenom pro podmnožinu párů jazyků v tomto bodě:
* z angličtiny do jakéhokoli jiného jazyka;
* z jakéhokoli jiného jazyka až po angličtinu, s výjimkou čínského zjednodušené čínštiny, tradiční čínštiny a lotyšské angličtiny
* z japonštiny do korejštiny nebo z korejštiny do japonštiny nebudete dostávat informace o zarovnání, pokud je větu zarovnaným převodem. Příkladem překonzervovaného překladu je "Toto je test", "Miluji vás" a další věty s vysokou frekvencí.

## <a name="example"></a>Příklad

Ukázkový kód JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj&quot;: &quot;0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
