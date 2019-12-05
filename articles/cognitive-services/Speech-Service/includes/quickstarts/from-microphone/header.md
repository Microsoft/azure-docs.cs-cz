---
title: 'Rychlý Start: rozpoznávání řeči pomocí služby mikrofon-Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: a72597163a8c11cd8b515d052dc69992784e61e0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818767"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k interaktivnímu rozpoznávání řeči od vstupu mikrofonu a získáte přepis textu ze zachyceného zvuku. Tato funkce se dá snadno integrovat do aplikací nebo zařízení pro běžné úlohy rozpoznávání, jako jsou zdlouhavého přepisování konverzace. Dá se použít také pro složitější integrace, jako je použití bot frameworku se sadou Speech SDK pro vytváření hlasových asistentů.

Po splnění několika požadavků se rozpoznávání řeči pouze v mikrofonu provede čtyřmi kroky:

> [!div class="checklist"]
> * Vytvořte objekt `SpeechConfig` z klíče a oblasti předplatného.
> * Vytvořte objekt `SpeechRecognizer` pomocí výše uvedeného objektu `SpeechConfig`.
> * Pomocí objektu `SpeechRecognizer` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené `SpeechRecognitionResult`.
