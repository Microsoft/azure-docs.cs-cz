---
title: 'Rychlý Start: Převod řeči na text – služba pro rozpoznávání řeči'
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
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980513"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro interaktivní převod řeči z jednoho jazyka na text v jiném jazyce. Po splnění několika požadavků se převod řeči na text provede jenom pět kroků:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Aktualizujte objekt ````SpeechConfig```` a určete zdrojové a cílové jazyky.
> * Vytvořte objekt ````TranslationRecognizer```` pomocí výše uvedeného objektu ````SpeechConfig````.
> * Pomocí objektu ````TranslationRecognizer```` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené ````TranslationRecognitionResult````.
