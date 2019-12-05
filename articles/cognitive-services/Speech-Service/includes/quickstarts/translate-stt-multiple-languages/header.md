---
title: 'Rychlý Start: Převod řeči na více jazyků – Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: bc35c1efcad2ca9ebb5eaf23dd84f7189858a159
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817318"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro interaktivní převod řeči z jednoho jazyka na řeč v jiném jazyce. Po splnění několika požadavků se převod řeči na text v několika jazycích provede jenom šest kroků:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechTranslationConfig```` z klíče a oblasti předplatného.
> * Aktualizujte objekt ````SpeechTranslationConfig```` a určete tak zdrojový jazyk rozpoznávání řeči.
> * Aktualizujte objekt ````SpeechTranslationConfig```` a určete více jazyků cíle překladu.
> * Vytvořte objekt ````TranslationRecognizer```` pomocí výše uvedeného objektu ````SpeechTranslationConfig````.
> * Pomocí objektu ````TranslationRecognizer```` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené ````TranslationRecognitionResult````.
