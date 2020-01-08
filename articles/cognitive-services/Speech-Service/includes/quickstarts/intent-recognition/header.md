---
title: 'Rychlý Start: rozpoznávání řeči, záměrů a entit – Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 1/02/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a833d39ab91cd803f066d707306a6ff648d37e8f
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660467"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) a službu Language UNDERSTANDING (Luis) k rozpoznávání záměrů ze zvukových dat zachycených z mikrofonu. Konkrétně použijete sadu Speech SDK k zachycení řeči a předem vytvořenou doménu z LUIS k identifikaci záměrů pro domácí automatizaci, jako je zapnutí a vypnutí světla. 

Po splnění několika požadavků se rozpoznávání řeči a identifikace záměrů z mikrofonu dá jenom několik kroků:

> [!div class="checklist"]
>
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Vytvořte objekt ````IntentRecognizer```` pomocí výše uvedeného objektu ````SpeechConfig````.
> * Pomocí objektu ````IntentRecognizer```` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené ````IntentRecognitionResult````.
