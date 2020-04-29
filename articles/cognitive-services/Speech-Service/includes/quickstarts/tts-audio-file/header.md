---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400340"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro převod textu na syntetizované rozpoznávání řeči ve zvukovém souboru. Služba převod textu na řeč poskytuje řadu možností pro syntetizované hlasy v části [Podpora jazyků pro převod textu na mluvené slovo](../../../language-support.md#text-to-speech). Po splnění některých požadavků vysyntetizuje řeč do souboru jenom pět kroků:
> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče a oblasti předplatného.
> * Vytvořte objekt konfigurace zvuku, který určuje. Název souboru WAV.
> * Vytvořte `SpeechSynthesizer` objekt pomocí výše uvedených objektů konfigurace.
> * Pomocí `SpeechSynthesizer` objektu převeďte text na syntetizované rozpoznávání řeči a uložte ho do zadaného zvukového souboru.
> * Zkontrolujte `SpeechSynthesizer` vrácené chyby.
