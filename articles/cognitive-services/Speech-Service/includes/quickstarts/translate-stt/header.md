---
title: 'Úvodní příručka: Překlad řeči na text - služba Řeči'
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
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980513"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k interaktivnímu překladu řeči z jednoho jazyka do textu v jiném jazyce. Po splnění několika předpokladů trvá překlad převod řeči na text pouze pět kroků:
> [!div class="checklist"]
> * Vytvořte ````SpeechConfig```` objekt z klíče předplatného a oblasti.
> * Aktualizujte ````SpeechConfig```` objekt a určete zdrojové a cílové jazyky.
> * Vytvořte ````TranslationRecognizer```` objekt ````SpeechConfig```` pomocí objektu shora.
> * Pomocí ````TranslationRecognizer```` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte ````TranslationRecognitionResult```` vrácené.
