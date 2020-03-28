---
title: 'Úvodní příručka: Překlad řeči do více jazyků – služba Speech Service'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74981551"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k interaktivnímu překladu řeči z jednoho jazyka do řeči v jiném jazyce. Po splnění několika předpokladů trvá překlad řeči na text ve více jazycích pouze šest kroků:
> [!div class="checklist"]
> * Vytvořte ````SpeechTranslationConfig```` objekt z klíče předplatného a oblasti.
> * Aktualizujte ````SpeechTranslationConfig```` objekt a určete zdrojový jazyk rozpoznávání řeči.
> * Aktualizujte ````SpeechTranslationConfig```` objekt a určete více cílových jazyků překladu.
> * Vytvořte ````TranslationRecognizer```` objekt ````SpeechTranslationConfig```` pomocí objektu shora.
> * Pomocí ````TranslationRecognizer```` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte ````TranslationRecognitionResult```` vrácené.
