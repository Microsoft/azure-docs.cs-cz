---
title: 'Rychlý Start: syntetizace řeči – služba Speech'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak pomocí sady Speech SDK syntetizovat řeč
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503077"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro převod textu na syntetizované rozpoznávání řeči. Po splnění několika požadavků vykreslení syntetizované řeči pro výchozí reproduktory provede jenom čtyři kroky:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Vytvořte objekt ````SpeechSynthesizer```` pomocí výše uvedeného objektu ````SpeechConfig````.
> * Použití objektu ````SpeechSynthesizer```` pro mluvený text.
> * Podívejte se na ````SpeechSynthesisResult```` vrátil chyby.
