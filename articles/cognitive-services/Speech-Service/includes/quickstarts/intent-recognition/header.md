---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421887"
---
V tomto rychlém startu použijete sadu [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) a službu Language Understanding (LUIS) k rozpoznání záměrů ze zvukových dat zachycených z mikrofonu. Konkrétně budete používat sady Speech SDK k zachycení řeči a předem připravenou doménu z LUIS k identifikaci záměrů pro domácí automatizaci, jako je zapnutí a vypnutí světla. 

Po splnění několika předpokladů, rozpoznání řeči a identifikace záměrů z mikrofonu trvá jen několik kroků:

> [!div class="checklist"]
>
> * Vytvořte `SpeechConfig` objekt z klíče předplatného a oblasti.
> * Vytvořte `IntentRecognizer` objekt `SpeechConfig` pomocí objektu shora.
> * Pomocí `IntentRecognizer` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte `IntentRecognitionResult` vrácené.
