---
title: 'Rychlý Start: rozpoznávání řeči pomocí služby mikrofon-Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469805"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k interaktivnímu rozpoznávání řeči od vstupu mikrofonu a získáte přepis textu ze zachyceného zvuku. Tato funkce se dá snadno integrovat do aplikací nebo zařízení pro běžné úlohy rozpoznávání, jako jsou zdlouhavého přepisování konverzace. Dá se použít také pro složitější integrace, jako je použití bot frameworku se sadou Speech SDK pro vytváření hlasových asistentů.

Po splnění několika požadavků se rozpoznávání řeči pouze v mikrofonu provede čtyřmi kroky:

> [!div class="checklist"]
> * Vytvořte objekt `SpeechConfig` z klíče a oblasti předplatného.
> * Vytvořte objekt `SpeechRecognizer` pomocí výše uvedeného objektu `SpeechConfig`.
> * Pomocí objektu `SpeechRecognizer` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené `SpeechRecognitionResult`.
