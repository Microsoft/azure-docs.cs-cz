---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: 36243fd5db76a4706318f41b3e2cb3f557c17189
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400780"
---
V tomto rychlém startu pomocí [sady Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) můžete interaktivně rozpoznat řeč ze vstupu mikrofonu a získat přepis textu ze zachyceného zvuku. Tuto funkci lze snadno integrovat do aplikací nebo zařízení pro běžné úlohy rozpoznávání, jako je přepis konverzací. Lze jej také použít pro složitější integrace, jako je použití rozhraní Bot Framework s sadou Speech SDK k vytvoření hlasových asistentů.

Po splnění několika předpokladů, rozpoznání řeči z mikrofonu trvá pouze čtyři kroky:

> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče předplatného a oblasti.
> * Vytvořte `SpeechRecognizer` objekt `SpeechConfig` pomocí objektu shora.
> * Pomocí `SpeechRecognizer` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte `SpeechRecognitionResult` vrácené.
