---
title: 'Rychlý Start: rozpoznávání řeči pomocí služby mikrofon-Speech'
titleSuffix: Azure Cognitive Services
description: Bude doplněno
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/07/2019
ms.author: erhopf
ms.openlocfilehash: f3f28cdd8c3c77a5cde2ead86c49b41d54bab5d7
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960892"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k interaktivnímu rozpoznávání řeči ze zvukových dat zachycených z mikrofonu. Po splnění několika požadavků se rozpoznávání řeči pouze v mikrofonu provede čtyřmi kroky:

> [!div class="checklist"]
> * Vytvořte objekt `SpeechConfig` z klíče a oblasti předplatného.
> * Vytvořte objekt `SpeechRecognizer` pomocí výše uvedeného objektu `SpeechConfig`.
> * Pomocí objektu `SpeechRecognizer` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené `SpeechRecognitionResult`.
