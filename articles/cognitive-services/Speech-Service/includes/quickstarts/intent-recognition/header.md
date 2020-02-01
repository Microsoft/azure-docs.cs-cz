---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900340"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) a službu Language UNDERSTANDING (Luis) k rozpoznávání záměrů ze zvukových dat zachycených z mikrofonu. Konkrétně použijete sadu Speech SDK k zachycení řeči a předem vytvořenou doménu z LUIS k identifikaci záměrů pro domácí automatizaci, jako je zapnutí a vypnutí světla. 

Po splnění několika požadavků se rozpoznávání řeči a identifikace záměrů z mikrofonu dá jenom několik kroků:

> [!div class="checklist"]
>
> * Vytvořte objekt `SpeechConfig` z klíče a oblasti předplatného.
> * Vytvořte objekt `IntentRecognizer` pomocí výše uvedeného objektu `SpeechConfig`.
> * Pomocí objektu `IntentRecognizer` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené `IntentRecognitionResult`.
