---
title: 'Rychlý Start: syntetizace řeči do zvukového souboru – Speech Service'
titleSuffix: Azure Cognitive Services
description: Bude doplněno
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502944"
---
V tomto rychlém startu použijete [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro převod textu na syntetizované rozpoznávání řeči ve zvukovém souboru. Po splnění některých požadavků vysyntetizuje řeč do souboru jenom pět kroků:
> [!div class="checklist"]
> * Vytvořte objekt ````SpeechConfig```` z klíče a oblasti předplatného.
> * Vytvořte objekt konfigurace zvuku, který určuje. Název souboru WAV.
> * Vytvořte objekt ````SpeechSynthesizer```` pomocí výše uvedených objektů konfigurace.
> * Pomocí objektu ````SpeechSynthesizer```` převeďte text na syntetizované rozpoznávání řeči a uložte ho do zadaného zvukového souboru.
> * Zkontrolujte, ````SpeechSynthesizer```` vrátil chyby.
