---
title: 'Rychlý Start: Převod řeči na řeč – služba Speech'
titleSuffix: Azure Cognitive Services
description: Bude doplněno
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74981459"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro interaktivní převod řeči z jednoho jazyka na řeč v jiném jazyce. Po splnění několika požadavků se převod řeči na řeč provede jenom šest kroků:
> [!div class="checklist"]
> * Vytvořte ````SpeechTranslationConfig```` objekt z klíče a oblasti předplatného.
> * Aktualizujte ````SpeechTranslationConfig```` objekt a určete zdrojové a cílové jazyky.
> * Aktualizujte ````SpeechTranslationConfig```` objekt zadáním hlasového názvu výstupu řeči.
> * Vytvořte ````TranslationRecognizer```` objekt pomocí ````SpeechTranslationConfig```` výše uvedeného objektu.
> * Pomocí ````TranslationRecognizer```` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte ````TranslationRecognitionResult```` vrácenou.
