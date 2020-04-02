---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: dapine
ms.openlocfilehash: bb177963b824e6d5d80d5f60e8b9f995b6ecd121
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80537007"
---
## <a name="speech-modes"></a>Režimy řeči

**Interaktivní**
- Určeno pro scénáře velení a řízení.
- Má hodnotu časového času segmentace X.
- Na konci jednoho rozpoznaného utterance služba zastaví zpracování zvuku z tohoto ID požadavku a ukončí otočení. Připojení není uzavřeno.
- Maximální limit pro rozpoznávání je 20s.
- Typické Carbon volání `RecognizeOnceAsync`vyvolat je .

**Konverzace**
- Určeno pro delší rozpoznávání.
- Má hodnotu časového času segmentace Y. (Y != X)
- Zpracuje více úplných promluv bez ukončení tahu.
- Ukončí obrat pro příliš mnoho ticha.
- Carbon bude pokračovat s novým ID požadavku a přehrávání zvuku podle potřeby.
- Služba se po 10 minutách rozpoznávání řeči násilně odpojí.
- Carbon se znovu připojí a přehraje nekvitovaný zvuk.
- Vyvolána v `StartContinuousRecognition`uhlíku s .

**Diktování**
- Umožňuje uživatelům zadat interpunkci mluvením.
- Vyvolána v Carbon `EnableDictation` zadáním `SpeechConfig` na objekt bez ohledu na volání rozhraní API, který spustí rozpoznávání.
- Cluster 1<sup>st</sup> `speech.fragment` party vrací zprávy pro zprostředkující výsledky, třetí<sup>strany</sup> vrátí `speech.hypothesis` zprávy.