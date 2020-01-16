---
title: 'Rychlý Start: rozpoznávání řeči ze zvukového souboru – Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037733"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k rozpoznávání řeči ze zvukového souboru. Po splnění několika požadavků se rozpoznávání řeči v souboru provede několika kroky:
> [!div class="checklist"]
> * Vytvořte objekt `SpeechConfig` z klíče a oblasti předplatného.
> * Vytvořte objekt `AudioConfig`, který určuje. Název souboru WAV.
> * Vytvořte objekt `SpeechRecognizer` pomocí výše uvedených objektů `SpeechConfig` a `AudioConfig`.
> * Pomocí objektu `SpeechRecognizer` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené `SpeechRecognitionResult`.
