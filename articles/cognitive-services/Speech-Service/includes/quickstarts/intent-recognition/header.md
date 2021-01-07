---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a752b723980c45e1488f1471ee8139190853b54c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97966716"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) a službu Language UNDERSTANDING (Luis) k rozpoznávání záměrů ze zvukových dat zachycených z mikrofonu. Konkrétně použijete sadu Speech SDK k zachycení řeči a předem vytvořenou doménu z LUIS k identifikaci záměrů pro domácí automatizaci, jako je zapnutí a vypnutí světla. 

Po splnění několika požadavků se rozpoznávání řeči a identifikace záměrů z mikrofonu dá jenom několik kroků:

> [!div class="checklist"]
>
> * Vytvořte `SpeechConfig` objekt z klíče a oblasti předplatného.
> * Vytvořte `IntentRecognizer` objekt pomocí `SpeechConfig` výše uvedeného objektu.
> * Pomocí `IntentRecognizer` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte `IntentRecognitionResult` vrácenou.

> [!NOTE]
> LanguageUnderstandingModel můžete vytvořit předáním adresy URL koncového bodu do metody FromEndpoint.
> Sada Speech SDK podporuje pouze koncové body LUIS v 2.0 a koncové body LUIS v 2.0 se vždy řídí jedním z těchto dvou vzorů:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
