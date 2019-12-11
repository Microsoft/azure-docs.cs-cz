---
title: 'Rychlý Start: Převod řeči na řeč – služba Speech'
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
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981459"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro interaktivní převod řeči z jednoho jazyka na řeč v jiném jazyce. Po splnění několika požadavků se převod řeči na řeč provede jenom šest kroků:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechTranslationConfig```` z klíče a oblasti předplatného.
> * Aktualizujte objekt ````SpeechTranslationConfig```` a určete zdrojové a cílové jazyky.
> * Aktualizujte objekt ````SpeechTranslationConfig```` a určete hlasový název výstupu řeči.
> * Vytvořte objekt ````TranslationRecognizer```` pomocí výše uvedeného objektu ````SpeechTranslationConfig````.
> * Pomocí objektu ````TranslationRecognizer```` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené ````TranslationRecognitionResult````.
