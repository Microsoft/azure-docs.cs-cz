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
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a87ed9355a5939393fd5e20f395cc96f35e7f150
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816078"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k interaktivnímu rozpoznávání řeči ze zvukových dat zachycených z mikrofonu. Po splnění několika požadavků se rozpoznávání řeči pouze v mikrofonu provede čtyřmi kroky:
> [!div class="checklist"]
>
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Vytvořte objekt ````IntentRecognizer```` pomocí výše uvedeného objektu ````SpeechConfig````.
> * Pomocí objektu ````IntentRecognizer```` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené ````IntentRecognitionResult````.
