---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 4725a1a9cf2cb74655a37ac27a0a86f10d7f4bb9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052914"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) a službu Language UNDERSTANDING (Luis) k rozpoznávání záměrů ze zvukových dat zachycených z mikrofonu. Konkrétně použijete sadu Speech SDK k zachycení řeči a předem vytvořenou doménu z LUIS k identifikaci záměrů pro domácí automatizaci, jako je zapnutí a vypnutí světla. 

Po splnění několika požadavků se rozpoznávání řeči a identifikace záměrů z mikrofonu dá jenom několik kroků:

> [!div class="checklist"]
>
> * Vytvořte `SpeechConfig` objekt z klíče a oblasti předplatného.
> * Vytvořte `IntentRecognizer` objekt pomocí `SpeechConfig` výše uvedeného objektu.
> * Pomocí `IntentRecognizer` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte `IntentRecognitionResult` vrácenou.

