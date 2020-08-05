---
title: 'Rychlý Start: Převod řeči na text – služba pro rozpoznávání řeči'
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980513"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro interaktivní převod řeči z jednoho jazyka na text v jiném jazyce. Po splnění několika požadavků se převod řeči na text provede jenom pět kroků:
> [!div class="checklist"]
> * Vytvořte ````SpeechConfig```` objekt z klíče a oblasti předplatného.
> * Aktualizujte ````SpeechConfig```` objekt a určete zdrojové a cílové jazyky.
> * Vytvořte ````TranslationRecognizer```` objekt pomocí ````SpeechConfig```` výše uvedeného objektu.
> * Pomocí ````TranslationRecognizer```` objektu spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte ````TranslationRecognitionResult```` vrácenou.
