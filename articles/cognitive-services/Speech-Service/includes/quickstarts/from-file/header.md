---
title: 'Rychlý Start: rozpoznávání řeči ze zvukového souboru – Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: trbye
ms.openlocfilehash: a7c91775411f100a92dfcb0a7199dd4b25f6eca4
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400541"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k rozpoznávání řeči ze zvukového souboru. Po splnění několika požadavků se rozpoznávání řeči v souboru provede několika kroky:
> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče a oblasti předplatného.
> * Vytvořte `AudioConfig` objekt, který určuje. Název souboru WAV.
> * Vytvořte `SpeechRecognizer` objekt pomocí objektů `SpeechConfig` a `AudioConfig` výše.
> * Pomocí `SpeechRecognizer` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte `SpeechRecognitionResult` vrácenou.
