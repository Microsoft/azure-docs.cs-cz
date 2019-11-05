---
title: 'Rychlý Start: rozpoznávání řeči ze zvukového souboru – Speech Service'
titleSuffix: Azure Cognitive Services
description: Bude doplněno
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504134"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k rozpoznávání řeči ze zvukového souboru. Po splnění několika požadavků se rozpoznávání řeči ze souboru provede jenom pět kroků:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Vytvořte objekt ````AudioConfig````, který určuje. Název souboru WAV.
> * Vytvořte objekt ````SpeechRecognizer```` pomocí výše uvedených objektů ````SpeechConfig```` a ````AudioConfig````.
> * Pomocí objektu ````SpeechRecognizer```` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené ````SpeechRecognitionResult````.
