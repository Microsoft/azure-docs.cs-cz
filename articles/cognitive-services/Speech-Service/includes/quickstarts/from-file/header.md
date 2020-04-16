---
title: 'Úvodní příručka: Rozpoznávání řeči ze zvukového souboru – služba Řeč'
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400541"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k rozpoznání řeči ze zvukového souboru. Po splnění několika předpokladů trvá rozpoznání řeči ze souboru pouze několik kroků:
> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče předplatného a oblasti.
> * Vytvořte `AudioConfig` objekt, který určuje . Název souboru WAV.
> * Vytvořte `SpeechRecognizer` objekt `SpeechConfig` pomocí `AudioConfig` objektů a shora.
> * Pomocí `SpeechRecognizer` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte `SpeechRecognitionResult` vrácené.
