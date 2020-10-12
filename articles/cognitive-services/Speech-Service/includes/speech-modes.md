---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81422037"
---
## <a name="speech-modes"></a>Režimy řeči

**Interaktivit**
- Určeno pro scénáře příkazů a ovládacích prvků.
- Má časový limit segmentace hodnotu X.
- Na konci jednoho rozpoznaného utterance služba zastaví zpracování zvuku z tohoto ID žádosti a ukončí. Připojení není uzavřeno.
- Maximální limit pro rozpoznávání je 20s.
- Typické volání metody Invoke je `RecognizeOnceAsync` .

**Konverzace**
- Určeno pro delší dobu nečinnosti.
- Hodnota časového limitu segmentace Y. (Y! = X)
- Zpracuje více úplných projevy, aniž by bylo potřeba ukončit.
- Ukončí příliš mnoho netichého vypnutí.
- Uhlí bude pokračovat s novým ID žádosti a přehráním zvuku podle potřeby.
- Služba se nuceně odpojí po 10 minutách rozpoznávání řeči.
- Uhlí se znovu připojí a znovu dohraje nepotvrzené zvuky.
- Vyvoláno v uhlíku s `StartContinuousRecognition` .

**Diktování**
- Umožňuje uživatelům zadat interpunkční znaménka tím, že je mluví.
- Vyvoláno v uhlíku zadáním `EnableDictation` `SpeechConfig` objektu bez ohledu na volání rozhraní API, které spouští rozpoznávání.
- Cluster s 1<sup>St</sup> . vrátí `speech.fragment` zprávy pro mezilehlé výsledky, 3 návratové zprávy služby<sup>RD</sup> stran `speech.hypothesis` .