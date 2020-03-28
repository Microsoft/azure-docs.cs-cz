---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900340"
---
V tomto rychlém startu použijete sadu [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) a službu Language Understanding (LUIS) k rozpoznání záměrů ze zvukových dat zachycených z mikrofonu. Konkrétně budete používat sady Speech SDK k zachycení řeči a předem připravenou doménu z LUIS k identifikaci záměrů pro domácí automatizaci, jako je zapnutí a vypnutí světla. 

Po splnění několika předpokladů, rozpoznání řeči a identifikace záměrů z mikrofonu trvá jen několik kroků:

> [!div class="checklist"]
>
> * Vytvořte `SpeechConfig` objekt z klíče předplatného a oblasti.
> * Vytvořte `IntentRecognizer` objekt `SpeechConfig` pomocí objektu shora.
> * Pomocí `IntentRecognizer` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte `IntentRecognitionResult` vrácené.
