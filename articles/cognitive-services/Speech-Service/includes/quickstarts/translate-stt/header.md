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
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: cd45657beacd04eb2376af3e4297eae051157778
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816928"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro interaktivní převod řeči z jednoho jazyka na text v jiném jazyce. Po splnění několika požadavků se převod řeči na text provede jenom pět kroků:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Aktualizujte objekt ````SpeechConfig```` a určete zdrojové a cílové jazyky.
> * Vytvořte objekt ````TranslationRecognizer```` pomocí výše uvedeného objektu ````SpeechConfig````.
> * Pomocí objektu ````TranslationRecognizer```` spusťte proces rozpoznávání pro jeden utterance.
> * Zkontrolujte vrácené ````TranslationRecognitionResult````.
