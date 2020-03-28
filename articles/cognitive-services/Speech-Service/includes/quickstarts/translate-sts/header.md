---
title: 'Úvodní příručka: Překlad řeči na řeč - služba řeči'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74981459"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k interaktivnímu překladu řeči z jednoho jazyka do řeči v jiném jazyce. Po splnění několika předpokladů trvá překlad řeči na řeč pouze šest kroků:
> [!div class="checklist"]
> * Vytvořte ````SpeechTranslationConfig```` objekt z klíče předplatného a oblasti.
> * Aktualizujte ````SpeechTranslationConfig```` objekt a určete zdrojové a cílové jazyky.
> * Aktualizujte ````SpeechTranslationConfig```` objekt a určete název hlasu výstupu řeči.
> * Vytvořte ````TranslationRecognizer```` objekt ````SpeechTranslationConfig```` pomocí objektu shora.
> * Pomocí ````TranslationRecognizer```` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte ````TranslationRecognitionResult```` vrácené.
