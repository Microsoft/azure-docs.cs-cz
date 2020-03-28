---
title: 'Úvodní příručka: Rozpoznávání řeči z mikrofonu – služba Řeč'
titleSuffix: Azure Cognitive Services
description: Bude doplněno
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75469805"
---
V tomto rychlém startu použijete sadu [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) k interaktivnímu rozpoznání řeči ze vstupu mikrofonu a k získání přepisu textu ze zachyceného zvuku. Tuto funkci lze snadno integrovat do aplikací nebo zařízení pro běžné úlohy rozpoznávání, jako je přepis konverzací. Lze jej také použít pro složitější integrace, jako je použití rozhraní Bot Framework s sadou Speech SDK k vytvoření hlasových asistentů.

Po splnění několika předpokladů, rozpoznání řeči z mikrofonu trvá pouze čtyři kroky:

> [!div class="checklist"]
> * Vytvořte `SpeechConfig` objekt z klíče předplatného a oblasti.
> * Vytvořte `SpeechRecognizer` objekt `SpeechConfig` pomocí objektu shora.
> * Pomocí `SpeechRecognizer` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte `SpeechRecognitionResult` vrácené.
