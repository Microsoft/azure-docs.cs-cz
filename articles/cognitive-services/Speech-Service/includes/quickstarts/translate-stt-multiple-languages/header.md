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
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981551"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro interaktivní převod řeči z jednoho jazyka na řeč v jiném jazyce. Po splnění několika požadavků se převod řeči na text v několika jazycích provede jenom šest kroků:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechTranslationConfig```` z klíče a oblasti předplatného.
> * Aktualizujte objekt ````SpeechTranslationConfig```` a určete tak zdrojový jazyk rozpoznávání řeči.
> * Aktualizujte objekt ````SpeechTranslationConfig```` a určete více jazyků cíle překladu.
> * Vytvořte objekt ````TranslationRecognizer```` pomocí výše uvedeného objektu ````SpeechTranslationConfig````.
> * Pomocí objektu ````TranslationRecognizer```` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené ````TranslationRecognitionResult````.
