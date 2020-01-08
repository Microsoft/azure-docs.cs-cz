---
title: 'Rychlý Start: rozpoznávání řeči ze zvukového souboru – Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466625"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k rozpoznávání řeči ze zvukového souboru. Po splnění několika požadavků se rozpoznávání řeči ze souboru provede jenom pět kroků:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Vytvořte objekt ````AudioConfig````, který určuje. Název souboru WAV.
> * Vytvořte objekt ````SpeechRecognizer```` pomocí výše uvedených objektů ````SpeechConfig```` a ````AudioConfig````.
> * Pomocí objektu ````SpeechRecognizer```` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené ````SpeechRecognitionResult````.
