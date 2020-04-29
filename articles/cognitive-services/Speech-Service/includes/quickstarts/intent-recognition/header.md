---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 8d70d282ffb1a39f9ffb3eb6addf694ac9d0d060
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81421887"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) a službu Language UNDERSTANDING (Luis) k rozpoznávání záměrů ze zvukových dat zachycených z mikrofonu. Konkrétně použijete sadu Speech SDK k zachycení řeči a předem vytvořenou doménu z LUIS k identifikaci záměrů pro domácí automatizaci, jako je zapnutí a vypnutí světla. 

Po splnění několika požadavků se rozpoznávání řeči a identifikace záměrů z mikrofonu dá jenom několik kroků:

> [!div class="checklist"]
>
> * Vytvořte `SpeechConfig` objekt z klíče a oblasti předplatného.
> * Vytvořte `IntentRecognizer` objekt pomocí výše uvedeného `SpeechConfig` objektu.
> * Pomocí `IntentRecognizer` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte `IntentRecognitionResult` vrácenou.
