---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400375"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro převod textu na syntetizované rozpoznávání řeči. Služba převod textu na řeč poskytuje řadu možností pro syntetizované hlasy v části [Podpora jazyků pro převod textu na mluvené slovo](../../../language-support.md#text-to-speech). Po splnění několika požadavků vykreslení syntetizované řeči pro výchozí reproduktory provede jenom čtyři kroky:
> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče a oblasti předplatného.
> * Vytvořte `SpeechSynthesizer` objekt pomocí výše uvedeného `SpeechConfig` objektu.
> * Použití `SpeechSynthesizer` objektu pro mluvený text.
> * Podívejte se `SpeechSynthesisResult` na vrácené chyby.
