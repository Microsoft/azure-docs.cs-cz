---
title: 'Rychlý Start: Převod řeči na více jazyků – Speech Service'
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
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74981551"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro interaktivní převod řeči z jednoho jazyka na řeč v jiném jazyce. Po splnění několika požadavků se převod řeči na text v několika jazycích provede jenom šest kroků:
> [!div class="checklist"]
> * Vytvořte ````SpeechTranslationConfig```` objekt z klíče a oblasti předplatného.
> * Aktualizujte ````SpeechTranslationConfig```` objekt a určete zdrojový jazyk rozpoznávání řeči.
> * Aktualizujte ````SpeechTranslationConfig```` objekt tak, aby určoval více cílových jazyků překladu.
> * Vytvořte ````TranslationRecognizer```` objekt pomocí výše uvedeného ````SpeechTranslationConfig```` objektu.
> * Pomocí ````TranslationRecognizer```` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte ````TranslationRecognitionResult```` vrácenou.
