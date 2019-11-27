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
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: bee3b64ece3faac6258fe4d017f12833b12e370d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536358"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k interaktivnímu rozpoznávání řeči od vstupu mikrofonu a získáte přepis textu ze zachyceného zvuku. Tato funkce se dá snadno integrovat do aplikací nebo zařízení pro běžné úlohy rozpoznávání, jako jsou zdlouhavého přepisování konverzace. Dá se použít také pro složitější integrace, jako je použití bot frameworku se sadou Speech SDK pro vytváření hlasových asistentů.

Po splnění několika požadavků se rozpoznávání řeči pouze v mikrofonu provede čtyřmi kroky:

> [!div class="checklist"]
> * Vytvořte objekt `SpeechConfig` z klíče a oblasti předplatného.
> * Vytvořte objekt `SpeechRecognizer` pomocí výše uvedeného objektu `SpeechConfig`.
> * Pomocí objektu `SpeechRecognizer` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené `SpeechRecognitionResult`.
